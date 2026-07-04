# Personal Color Diagnosis Algorithm

> Domain reference for the core logic that classifies a user into one of **12 personal color types** — the heart of the "Litmus Face / OMCT" service.

---

## Overview of the flow

> ☝🏻 **How the final type is decided**
>
> The user goes through **9 rounds of the Basic Stage**, and — only if that isn't enough to resolve a single winner — a follow-up **Bonus Stage** round.

### Basic Stage

- **9 rounds × 4 options per round = 36 color exposures total.**
- 12 types exist, so each type is exposed exactly 3 times across the session.
- The 9 rounds are three passes of the same three-round pattern:
  1. `spring warm / summer cool / autumn warm / winter cool` — the "True" quadrant. If the user is a True type, this round separates them sharply by season.
  2. `spring light / summer mute / autumn deep / winter bright` — no season or tone overlap.
  3. `spring bright / summer light / autumn mute / winter deep` — no season or tone overlap.
- Every round shows four colors from the **same family** (e.g. four pinks, four blues). Orange and purple are the exceptions to this rule.
- After all 9 rounds, we count the mode of `(season, tone)` picks — most-frequently-selected wins.
- All users see the same 9 questions in the same order. Only the horizontal order of the 4 options in a round is shuffled (`src/pages/choice-color/index.page.tsx:59`).
- **Single winner after 9 rounds → done.** No bonus round needed.
- **Tie → bonus round.** The top-tied types (2–4 of them) are re-tested with hand-picked chips.

### Bonus Stage

- Triggered only when the Basic Stage produces a tie.
- One additional round, 4 options, resolves the tie with one pick.
- The 4 options are drawn from the color palettes of the tied types (see §5 for the composition rule).

---

## Domain vocabulary

```ts
season: ['spring', 'summer', 'autumn', 'winter']
tone:   ['light', 'deep', 'warm', 'cool', 'mute', 'bright']
```

> Type definitions live in [`@types/color.d.ts`](../../@types/color.d.ts).

## The 12 personal color types

The final space is `season × tone` (a curated subset — not all 24 combinations are used):

| Season | Type key       | Display name    |
| ------ | -------------- | --------------- |
| Spring | `springwarm`   | True Spring     |
| Spring | `springbright` | Bright Spring   |
| Spring | `springlight`  | Light Spring    |
| Summer | `summercool`   | True Summer     |
| Summer | `summermute`   | Soft Summer     |
| Summer | `summerlight`  | Light Summer    |
| Autumn | `autumnwarm`   | True Autumn     |
| Autumn | `autumnmute`   | Soft Autumn     |
| Autumn | `autumndeep`   | Dark Autumn     |
| Winter | `wintercool`   | True Winter     |
| Winter | `winterbright` | Bright Winter   |
| Winter | `winterdeep`   | Dark Winter     |

---

## Algorithm details (implementation view)

### 1. Question data

- File: [`src/data/choiceColorData.ts`](../../src/data/choiceColorData.ts)
- 9 color themes (rounds) × 4 options each = 36 color chips total.
- Each option carries `season`, `tone`, and `type` (`type = season + tone`).

**Round order and comparison axis**

| # | Theme         | Axis         | Types shown (spring / summer / autumn / winter)       |
| - | ------------- | ------------ | ----------------------------------------------------- |
| 1 | pink          | warm/cool    | springwarm / summercool / autumnwarm / wintercool     |
| 2 | green         | bright/mute  | springbright / summermute / autumnmute / winterbright |
| 3 | brown/grey    | light/deep   | springlight / summerlight / autumndeep / winterdeep   |
| 4 | blue          | warm/cool    | springwarm / summercool / autumnwarm / wintercool     |
| 5 | red           | bright/mute  | springbright / summermute / autumnmute / winterbright |
| 6 | pink          | light/deep   | springlight / summerlight / autumndeep / winterdeep   |
| 7 | orange/purple | warm/cool    | springwarm / summercool / autumnwarm / wintercool     |
| 8 | skyblue       | bright/mute  | springbright / summermute / autumnmute / winterbright |
| 9 | mint          | light/deep   | springlight / summerlight / autumndeep / winterdeep   |

> The pattern `(warm/cool → bright/mute → light/deep)` repeats three times. Every user gets the same round order; only the left-to-right order of the 4 options within a round is randomly shuffled per session (`src/pages/choice-color/index.page.tsx:59`).

### 2. Recording answers

- Files: [`src/pages/choice-color/index.page.tsx`](../../src/pages/choice-color/index.page.tsx), [`src/pages/choice-color/BasicStage/index.tsx`](../../src/pages/choice-color/BasicStage/index.tsx)
- On every pick, the selected option's `ColorType` is pushed into `selectedTypes: ColorType[]`.
- At the end of the Basic Stage, `selectedTypes.length === 9`.

### 3. Scoring — mode calculation

- File: [`src/hooks/useSelectBonusColorTypes.ts`](../../src/hooks/useSelectBonusColorTypes.ts) (lines 41–56)
- A plain-vote algorithm: return the `ColorType`(s) that appeared most often among the 9 picks.

```ts
function getModeTypes(selectedTypes: ColorType[]) {
  const count: { [key: string]: number } = {};
  let maxFreq = 0;

  selectedTypes.forEach((selectedType) => {
    count[selectedType] = count[selectedType] + 1 || 1;
    if (count[selectedType] > maxFreq) maxFreq = count[selectedType];
  });

  return Object.entries(count)
    .filter(([, value]) => value === maxFreq)
    .map(([key]) => key);
}
```

### 4. Branching — single winner vs tie

`useSelectBonusColorTypes.ts` lines 24–35

- `modeTypes.length === 1` → route directly to `/result?colorType=<type>`.
- `modeTypes.length >= 2` → enter the **Bonus Stage**.

### 5. Bonus Stage option composition

- Files: [`src/utils/getBonusColorOptions.ts`](../../src/utils/getBonusColorOptions.ts) (lines 12–57), [`src/pages/choice-color/BonusStage/index.tsx`](../../src/pages/choice-color/BonusStage/index.tsx)
- How the 4 bonus options are built depends on how many types are tied:

| Tied count | Option composition                                                                 |
| ---------- | ---------------------------------------------------------------------------------- |
| 2          | Each type's `firstColors` + `secondColors` (2 × 2)                                 |
| 3          | Each type's `firstColors` (3) + the first type's `secondColors` (1)                |
| 4          | Each type's `firstColors` (4)                                                      |
| 5+ (edge)  | Fallback set: `['autumnmute', 'summercool', 'autumndeep', 'summerlight']`          |

- The user picks one of the 4 → that option's `type` is the final classification.

### 6. Result lookup

- File: [`src/data/resultColorData.ts`](../../src/data/resultColorData.ts)
- The final `ColorType` is used as a key into a table of per-type presentation data:
  - `name` (display name)
  - `textColor` (brand color for headings)
  - `gridColors` / `bestColors` / `worstColors` (palettes)
  - `tags` (trait tags)
  - `celebrities` (people identified with the same type)
  - `secondaryType` / `worstType` (neighboring / opposing types)

---

## Flow diagram

```
┌────────────────────────────────┐
│  Basic Stage (9 rounds)        │
│  4 options × 9 = 36 exposures  │
│  → selectedTypes: ColorType[]  │
└──────────────┬─────────────────┘
               │
               ▼
       ┌───────────────┐
       │ getModeTypes()│  (mode calculation)
       └───────┬───────┘
               │
       ┌───────┴────────┐
       │                │
       ▼                ▼
  Single winner     Tie (2–4 types)
       │                │
       │                ▼
       │      ┌──────────────────────┐
       │      │  Bonus Stage         │
       │      │  Pick 1 of 4 colors  │
       │      └────────┬─────────────┘
       │               │
       ▼               ▼
   ┌──────────────────────────────┐
   │ Final ColorType decided      │
   │ → /result?colorType=...      │
   └──────────────────────────────┘
```

---

## Key files

| File | Role |
| ---- | ---- |
| `@types/color.d.ts` | `ColorSeason` / `ColorTone` / `ColorType` type definitions |
| `src/data/choiceColorData.ts` | 9-round × 4-option color data (Basic Stage) |
| `src/data/bonusColorData.ts` | Per-type representative palettes (Bonus Stage source) |
| `src/data/resultColorData.ts` | Per-type result data (palettes, tags, celebrities, etc.) |
| `src/pages/choice-color/index.page.tsx` | Basic ↔ Bonus stage routing and shared state |
| `src/pages/choice-color/BasicStage/index.tsx` | Basic Stage UI |
| `src/pages/choice-color/BonusStage/index.tsx` | Bonus Stage UI |
| `src/hooks/useSelectBonusColorTypes.ts` | **Core scoring logic** (mode function) |
| `src/utils/getBonusColorOptions.ts` | Bonus Stage option composition |
| `src/pages/result/index.logic.ts` | Result page routing and data lookup |
