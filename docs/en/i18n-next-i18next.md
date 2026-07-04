# Internationalization with next-i18next

## Motivation

Google Analytics traffic acquisition data showed a non-trivial share of sessions coming from outside Korea — enough to make the existing i18n layer a bottleneck worth fixing rather than a nice-to-have. English was picked as the first language to stabilize because the US was the second-largest country in the demographics report and English also lets us onboard visitors from countries where Korean support is not viable.

The prior stack used **`react-intl`**, which had two problems in this codebase:

1. **State loss on refresh.** The selected language reset to Korean whenever the page reloaded.
2. **SSR incompatibility.** After the Next.js migration, `react-intl`'s render path did not play well with server-side rendering.

Both issues motivated a swap. **`next-i18next`** was selected as the replacement.

## Why `next-i18next`

### 1. First-class Next.js compatibility

Next.js apps have to accommodate both SSR and CSR — the same tree needs to render on the server (for crawlable HTML, per-locale `<head>`, SEO) and hydrate cleanly on the client. `next-i18next` handles this with minimal configuration; the alternative was hand-rolling server preloading for `react-intl`.

### 2. Fills the gap in Next.js built-in i18n

Next.js ships with [internationalized routing](https://nextjs.org/docs/advanced-features/i18n-routing) — locale detection and URL-based routing — but **no translation layer**. It manages the URL and does not touch string resolution.

`next-i18next` provides the missing pieces: translation file management and React components / hooks that resolve strings against the active locale. Combined, they cover both routing and translation without additional glue code.

## Setup

### 1. Install

```bash
yarn add next-i18next react-i18next i18next
```

### 2. Locale file layout

`next-i18next` loads translation JSON files from a directory tree on the local filesystem (both server-side and client-side). The convention:

```
.
└── public
    └── locales
        ├── en
        |   └── common.json
        └── ko
            └── common.json
```

### 3. Project config

Create `next-i18next.config.js` at the project root. This is what tells `next-i18next` which locales exist, which is the default, and where to preload from on the server.

**`next-i18next.config.js`**

```js
/* eslint-disable @typescript-eslint/no-var-requires */
const path = require('path');

/** @type {import('next-i18next').UserConfig} */
module.exports = {
  i18n: {
    locales: ['en', 'ko'],
    defaultLocale: 'en',
  },
  localePath: path.resolve('./public/locales'),
  fallbackLng: 'en',
  ns: ['common'],
  defaultNS: 'common',
  debug: process.env.NODE_ENV === 'development',
};
```

Wire the same config into `next.config.js` so Next.js's URL routing knows about the locale set:

**`next.config.js`**

```js
const { i18n } = require('./next-i18next.config.js');

const nextConfig = {
  // ...
  i18n,
};

module.exports = withSentryConfig(nextConfig);
```

## API surface

Three `next-i18next` primitives cover every case in this project.

### 1. `appWithTranslation` — wrap the app root

```tsx
import { appWithTranslation } from 'next-i18next';

const App = ({ Component, pageProps }: AppProps) => (
  <Component {...pageProps} />
);

export default appWithTranslation(App);
```

### 2. `serverSideTranslations` — preload for SSR

```ts
import { serverSideTranslations } from 'next-i18next/serverSideTranslations';

export const getServerSideProps: GetServerSideProps = async ({ locale }) => {
  return {
    props: {
      ...(await serverSideTranslations(locale ?? 'en', ['common'])),
    },
  };
};
```

### 3. `useTranslation` — resolve strings in components

```tsx
import { useTranslation } from 'next-i18next';

export const Footer = () => {
  const { t } = useTranslation('common');
  return <p>{t('description')}</p>;
};
```

Full docs: [next-i18next on GitHub](https://github.com/i18next/next-i18next).

## Troubleshooting: ESM vs CJS

The wiring above looks routine, but a subtle module-system mismatch cost more time than it should have. Worth writing down so the next person doesn't repeat it.

### Symptoms

1. After adding `next-i18next.config.js` and switching `next.config.mjs` → `next.config.js`, `next-i18next` failed to load the config file.
2. Renaming to `.cjs` or `.mjs` produced a different error — syntax mismatch inside the file.
3. Alternating extensions and module-loading style just cycled between the two errors.

### Root cause

`package.json` had `"type": "module"`, which flips the whole project to ESM by default. **`next-i18next` did not support ESM config files at the time.** The two errors were the same error surfacing at different points in the load chain — the config was being parsed under the wrong module system either way.

### Fix

- Remove `"type": "module"` from `package.json` — this reverts the default interpretation to CommonJS.
- Keep both `next-i18next.config.js` and `next.config.js` as `.js` under the CJS default.

### Takeaway

Node's default is CJS, and `package.json`'s `type` field flips the entire project's interpretation. When a library declares CJS-only support, ESM-by-default at the project level will fail in ways that look like syntax errors but are actually loader errors. Related deep-dive I wrote up separately: [JavaScript modules — CJS vs ESM](https://medium.com/@sojjung3/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EB%AA%A8%EB%93%88-cjs%EC%99%80-esm%EC%9D%98-%EC%B0%A8%EC%9D%B4-d133660d01a8) (Korean).
