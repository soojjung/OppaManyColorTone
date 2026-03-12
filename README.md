<p align="center">
  <a href="https://omct.web.app/" rel="noopener" target="_blank"><img src="./readme/omct-thumbnail.png" alt="logo"></a>
</p>

# Personal Color Self-Diagnosis Service

## 🧐 About Project

<p>
Hello, we are SaekKkanDa.

Have you ever wondered what your personal color is?<br>
Getting a professional personal color diagnosis is expensive... and such a hassle.<br>
With just one photo of yourself, find your personal color — quickly, easily, and for free!

</p>

> Deployment Link: https://omct.web.app/

> Github Link: https://github.com/SaekKkanDa/OppaManyColorTone

## ✨ Installation

```bash
nvm use
yarn
yarn run dev
```

## 📙 Guide

- [1 - Error Handling Design](https://github.com/SaekKkanDa/OppaManyColorTone/wiki/1.-%EC%97%90%EB%9F%AC-%ED%95%B8%EB%93%A4%EB%A7%81-%EB%94%94%EC%9E%90%EC%9D%B8)
- [2 - Deployment Automation with Github Actions](https://github.com/SaekKkanDa/OppaManyColorTone/wiki/2.-Github-Actions%EC%9D%84-%ED%99%9C%EC%9A%A9%ED%95%98%EC%97%AC-%EB%B0%B0%ED%8F%AC-%EC%9E%90%EB%8F%99%ED%99%94)
- [3 - OMCT Folder Structure](https://github.com/SaekKkanDa/OppaManyColorTone/wiki/3.-OMCT-%ED%8F%B4%EB%8D%94-%EA%B5%AC%EC%A1%B0)
- [4 - Internationalization (next‐i18next)](<https://github.com/SaekKkanDa/OppaManyColorTone/wiki/4.-%EB%8B%A4%EA%B5%AD%EC%96%B4-%EC%A7%80%EC%9B%90-(next%E2%80%90i18next)>)

## 🙏 Contributors

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<table>
  <tbody>
    <tr>
      <td align="center" valign="top">
        <a href="https://github.com/soojjung">
          <img src="https://avatars.githubusercontent.com/u/89066676?v=4" width="100px" alt="Soojin" />
          <br />
          <sub>
            <b>Soojin</b>
          </sub>
        </a>
        <br />
      </td>
      <td align="center" valign="top">
        <a href="https://github.com/seoltang">
          <img src="https://avatars.githubusercontent.com/u/91963656?v=4" width="100px" alt="Seoltang" />
          <br />
          <sub>
            <b>Seoltang</b>
          </sub>
        </a>
        <br />
      </td>
      <td align="center" valign="top">
        <a href="https://github.com/zwonkim">
          <img src="https://avatars.githubusercontent.com/u/103507999?v=4" width="100px" alt="Coco" />
          <br />
          <sub>
            <b>Coco</b>
          </sub>
        </a>
        <br />
      </td>
      <td align="center" valign="top">
        <a href="https://github.com/hyeongjun3">
          <img src="https://avatars.githubusercontent.com/u/37447578?v=4" width="100px" alt="Jun" />
          <br />
          <sub>
            <b>Jun</b>
          </sub>
        </a>
        <br />
      </td>
      <td align="center" valign="top">
        <a href="https://github.com/jjsk109">
          <img src="https://avatars.githubusercontent.com/u/39641848?v=4" width="100px" alt="Nick" />
          <br />
          <sub>
            <b>Nick</b>
          </sub>
        </a>
        <br />
      </td>
    </tr>
  </tbody>
</table>

<!-- ALL-CONTRIBUTORS-LIST:END -->

## 🛠 Stack

- Next.js
- TypeScript
- styled-components
- Recoil
- Firebase
- Github actions
- Sentry
- spline
- i18next

## 🧑‍💼 Roles & Responsibilities

### [Soojin](https://github.com/soojjung)

- Personal Color Content Planning
  Expanded to 12 personal color types, including color choices and descriptive text for each type

- [Internationalization (i18n) Support🔗](<https://github.com/SaekKkanDa/OppaManyColorTone/wiki/4.-%EB%8B%A4%EA%B5%AD%EC%96%B4-%EC%A7%80%EC%9B%90-(next%E2%80%90i18next)>)
  Migrated to a new internationalization library (next-i18next)

- [SEO Meta Tags🔗](https://github.com/SaekKkanDa/OppaManyColorTone/pull/247)

### [Seoltang](https://github.com/seoltang)

- [Color Selection Result Logic🔗](https://github.com/SaekKkanDa/OppaManyColorTone/pull/125)

- [SEO Improvements🔗](https://github.com/SaekKkanDa/OppaManyColorTone/pull/247)

- [Next.js Image & Font Optimization🔗](https://github.com/SaekKkanDa/OppaManyColorTone/pull/231)

- 3D Image Integration
  Added 3D images to the landing page (using Spline library)

### [Coco](https://github.com/zwonkim)

- Color Selection Logic
  Designed and implemented the color selection logic, a core feature of the personal color diagnosis service
- [Internationalization (i18n) Support🔗](https://github.com/SaekKkanDa/OppaManyColorTone/pull/226)
  Added multi-language support to enable easy access for international users, with global expansion in mind

- [Color Chip Selection Interaction🔗](https://github.com/SaekKkanDa/OppaManyColorTone/issues/264)
  Added animations to the color selection process to reduce mid-session drop-off rates and enhance user engagement

### [Jun](https://github.com/hyeongjun3)

- Firebase Communication Module
  Developed modules for communicating with Firebase Storage and APIs

- [Personal Color Proxy Test Feature🔗](https://github.com/SaekKkanDa/OppaManyColorTone/issues/217)
  Developed a feature allowing others to take the personal color test on your behalf using your photo

- [CI/CD🔗](https://github.com/SaekKkanDa/OppaManyColorTone/wiki/2.-Github-Actions%EC%9D%84-%ED%99%9C%EC%9A%A9%ED%95%98%EC%97%AC-%EB%B0%B0%ED%8F%AC-%EC%9E%90%EB%8F%99%ED%99%94)
  Built CI/CD pipeline using Firebase CLI and Github Actions

- [Error Handling Design🔗](https://github.com/SaekKkanDa/OppaManyColorTone/wiki/1.-%EC%97%90%EB%9F%AC-%ED%95%B8%EB%93%A4%EB%A7%81-%EB%94%94%EC%9E%90%EC%9D%B8)
  Designed an architecture where handleable errors are dealt with contextually, while unhandled errors are collected via Sentry following defined conventions

## 🐞 Troubleshooting

- [html2canvas Library Save Issue🔗](https://github.com/SaekKkanDa/OppaManyColorTone/issues/221)
  Celebrity photos breaking when saving result images

- i18next & Firebase CLI Deployment Internal Server Error
  Documentation to be written

- Cross-Browser Issues
  Issues with features like Native Share API and saving HTML as images behaving differently across devices and browsers

## 📌 Improvements (TODO)

### Google AdSense

- Service monetization and revenue generation
- Seamlessly integrated ads that blend naturally into the UX
