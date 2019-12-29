---
title: Gatsby 스타일링 입문
typora-copy-images-to: ./
disableTableOfContents: true
---

<!-- Idea: Create a glossary to refer to. A lot of these terms get jumbled -->

<!--
  - Global styles
  - Component css
  - CSS-in-JS
  - CSS Modules

-->

Gatsby 튜토리얼 파트 2에 오신 것을 환영합니다!

## 이번 튜토리얼에서는 무엇을 다루나요?

이번 파트에서 여러분은 Gatsby 사이트의 스타일링을 위한 옵션들을 살펴보고 사이트를 만들기 위한 React 컴포넌트의 사용법을 좀 더 자세히 알아볼 것입니다.

## 전역 스타일의 사용

모든 사이트는 일종의 전역 스타일을 가지고 있습니다. 타이포그래피와 배경색 같은 것들을 포함해서 말이죠. 이러한 스타일이 사이트의 전반적인 느낌을 만들어줍니다 — 마치 벽의 색상과 텍스처가 방의 느낌을 만들 듯이요.

### 일반적인 CSS 파일을 이용해서 전역 스타일 만들기

사이트에 전역 스타일을 추가하는 가장 직관적인 방법 중의 하나는 전역 `.css` 스타일시트를 이용하는 것입니다.

#### ✋ 새로운 Gatsby 사이트 만들기

새로운 Gatsby 사이트를 만들어줍시다. (여러분이 만약 커맨드 라인에 입문한지 얼마되지 않았다면) [파트 1](/tutorial/part-one/)에서 사용한 터미널 윈도우를 끄고 파트 2를 위한 새로운 터미널 세션을 시작하는 것이 아마도 가장 좋은 방법일 것입니다. 

새로운 터미널 윈도우를 열고, "hello world" gatsby 사이트를 만들고, 개발 서버를 작동시키세요:

```shell
gatsby new tutorial-part-two https://github.com/gatsbyjs/gatsby-starter-hello-world
cd tutorial-part-two
```

이제 여러분은 ("hello world" 스타터를 기반으로 한) 새로운 Gatsby 사이트가 있을 것이고 다음과 같은 구조를 가질 것입니다:

```text
├── package.json
├── src
│   └── pages
│       └── index.js
```

#### ✋ css 파일에 스타일 추가하기

1. 새로운 프로젝트에 `.css` 파일을 추가하세요:

```shell
cd src
mkdir styles
cd styles
touch global.css
```

> Note: 만약 에디터를 선호한다면, 코드 에디터에서 파일과 디렉토리를 추가해도 전혀 문제가 없습니다.

이제 여러분은 다음과 같은 구조를 가질 것입니다:

```text
├── package.json
├── src
│   └── pages
│       └── index.js
│   └── styles
│       └── global.css
```

2. Define some styles in the `global.css` file:

```css:title=src/styles/global.css
html {
  background-color: lavenderblush;
}
```

> Note: `/src/styles/` 폴더 안에 예제 css 파일을 배치하는 것은 임의입니다.

#### ✋ `gatsby-browser.js`에 스타일시트를 포함시키기

1. `gatsby-browser.js`를 만드세요.

```shell
cd ../..
touch gatsby-browser.js
```

이제 여러분의 프로젝트의 파일 구조는 다음과 같습니다:

```text
├── package.json
├── src
│   └── pages
│       └── index.js
│   └── styles
│       └── global.css
├── gatsby-browser.js
```

> 💡 `gatsby-browser.js`란? 아직 이것에 대해서 너무 염려하지 말고, `gatsby-browser.js`는 Gatsby가 (이 파일이 있으면) 찾아서 사용하는 몇 안되는 특별한 파일 중의 하나라는 것만 알아두세요. 파일 이름이 매우 중요합니다. 더 자세한 내용을 살펴보려면 [the docs](/docs/browser-apis/)를 참고하세요.

2. `gatsby-browser.js` 파일에 방금 여러분이 만든 스타일시트를 불러오세요:

```javascript:title=gatsby-browser.js
import "./src/styles/global.css"

// or:
// require('./src/styles/global.css')
```

> Note: CommonJS (`require`)와 ES 모듈 (`import`) 문법 둘다 작동합니다. 어떤 것을 고를지 모르겠다면, 우리는 대부분 `import`를 사용합니다.

3. 개발 서버를 작동시키세요:

```shell
gatsby develop
```

브라우저에서 여러분의 프로젝트를 살펴보면, "hello world" 스타터에 라벤더 배경색이 나타날 것입니다:

![라벤더 Hello World!](global-css.png)

> Tip: 이번 튜토리얼은 가장 빠르고 직관적인 방법으로 Gatsby 사이트에 스타일을 적용하는 방법에 초점을 맞추고 있습니다 — `gatsby-browser.js`를 이용하여 일반적인 CSS 파일을 직접 불러오는 방법. 대부분의 경우 전역 스타일을 추가하는 가장 좋은 방법은 공유 레이아웃 컴포넌트를 사용하는 것입니다. 이 방법에 대해 [관련 문서 확인](/docs/global-css/)

## 컴포넌트-범위 CSS 사용하기

지금까지 우리는 표준 css 스타일시트를 사용하는 더 전통적인 방법에 대해서 이야기했습니다. 이제 컴포넌트 중심으로 CSS를 모듈화하는 다양한 방법에 대해서 살펴보겠습니다.

### CSS 모듈

**CSS 모듈**을 살펴봅시다. [CSS 모듈 홈페이지](https://github.com/css-modules/css-modules)에서 인용하자면:

> **CSS 모듈**은 모든 클래스 이름과 애니메이션 이름의 범위를 기본적으로 로컬로 지정하는 CSS 파일입니다.

CSS 모듈은 매우 인기가 많은데 왜냐면 CSS를 일반적으로 작성할 수 있지만 더욱 안전하기 때문입니다. 이 툴은 유니크한 클래스 이름과 애니메이션 이름을 자동으로 만들어주기 때문에, 이름이 충돌하는 것에 대해서 걱정하지 않아도 됩니다.

Gtasby는 CSS 모듈을 별도의 설치나 설정없이 바로 함께 작동합니다. 이 방법은 Gatsby(그리고 일반적으로 React)를 사용해서 새로 무언가를 만드는 분들에게 매우 권장합니다.

#### ✋ CSS 모듈을 사용해서 새로운 페이지 만들기

이번 섹션에서, 여러분은 CSS 모듈을 사용하는 새로운 페이지 컴포넌트와 스타일을 만들것입니다.

먼저, `Container` 컴포넌트를 만드세요.

1. `src/components`에 디렉토리를 만든 후, 새로 만든 디렉토리 안에서, `container.js`를 만든 후에 다음 것들을 붙여넣기 하세요:

```javascript:title=src/components/container.js
import React from "react"
import containerStyles from "./container.module.css"

export default ({ children }) => (
  <div className={containerStyles.container}>{children}</div>
)
```

여러분이 불러온 css 모듈 파일 이름이 `container.module.css`인 것을 알아차렸을 것입니다. 이제 그 파일을 만들어봅시다.

2. 동일한 디렉토리 (`src/components`)에서, `container.module.css`파일을 만들고 다음 내용을 복사/붙여넣기 합시다:

```css:title=src/components/container.module.css
.container {
  margin: 3rem auto;
  max-width: 600px;
}
```

파일 이름이 `.css`로 끝나지 않고 `.module.css`로 끝나는 것을 알아차렸을 것입니다. 이 방법으로 여러분은 Gatsby에게 해당 CSS파일을 일반적인 CSS로 처리하지않고 CSS 모듈로서 처리하라고 알려줍니다.

3.  `src/pages/about-css-modules.js` 파일을 만들어서 새로운 페이지 컴포넌트를 만드세요:

```javascript:title=src/pages/about-css-modules.js
import React from "react"

import Container from "../components/container"

export default () => (
  <Container>
    <h1>About CSS Modules</h1>
    <p>CSS Modules are cool</p>
  </Container>
)
```

이제 `http://localhost:8000/about-css-modules/`를 방문하면, 여러분의 페이지는 다음과 비슷할 것입니다:

![CSS 모듈 스타일이 적용된 페이지](css-modules-basic.png)

#### ✋ CSS 모듈을 사용한 컴포넌트의 스타일

In this section, you'll create a list of people with names, avatars, and short Latin biographies. You'll create a `<User />` component and style that component using a CSS module.

1. Create the file for the CSS at `src/pages/about-css-modules.module.css`.

2. Paste the following into the new file:

```css:title=src/pages/about-css-modules.module.css
.user {
  display: flex;
  align-items: center;
  margin: 0 auto 12px auto;
}

.user:last-child {
  margin-bottom: 0;
}

.avatar {
  flex: 0 0 96px;
  width: 96px;
  height: 96px;
  margin: 0;
}

.description {
  flex: 1;
  margin-left: 18px;
  padding: 12px;
}

.username {
  margin: 0 0 12px 0;
  padding: 0;
}

.excerpt {
  margin: 0;
}
```

3. Import the new `src/pages/about-css-modules.module.css` file into the `about-css-modules.js` page you created earlier by editing the first few lines of the file like so:

```javascript:title=src/pages/about-css-modules.js
import React from "react"
// highlight-next-line
import styles from "./about-css-modules.module.css"
import Container from "../components/container"

// highlight-next-line
console.log(styles)
```

The `console.log(styles)` code will log the resulting import so you can see the result of your processed `./about-css-modules.module.css` file. If you open the developer console (using e.g. Firefox or Chrome's developer tools) in your browser, you'll see:

![Import result of CSS module in console](css-modules-console.png)

If you compare that to your CSS file, you'll see that each class is now a key in the imported object pointing to a long string e.g. `avatar` points to `src-pages----about-css-modules-module---avatar---2lRF7`. These are the class names CSS Modules generates. They're guaranteed to be unique across your site. And because you have to import them to use the classes, there's never any question about where some CSS is being used.

4. Create a `User` component.

Create a new `<User />` component inline in the `about-css-modules.js` page
component. Modify `about-css-modules.js` so it looks like the following:

```jsx:title=src/pages/about-css-modules.js
import React from "react"
import styles from "./about-css-modules.module.css"
import Container from "../components/container"

console.log(styles)

// highlight-start
const User = props => (
  <div className={styles.user}>
    <img src={props.avatar} className={styles.avatar} alt="" />
    <div className={styles.description}>
      <h2 className={styles.username}>{props.username}</h2>
      <p className={styles.excerpt}>{props.excerpt}</p>
    </div>
  </div>
)
// highlight-end

export default () => (
  <Container>
    <h1>About CSS Modules</h1>
    <p>CSS Modules are cool</p>
    {/* highlight-start */}
    <User
      username="Jane Doe"
      avatar="https://s3.amazonaws.com/uifaces/faces/twitter/adellecharles/128.jpg"
      excerpt="I'm Jane Doe. Lorem ipsum dolor sit amet, consectetur adipisicing elit."
    />
    <User
      username="Bob Smith"
      avatar="https://s3.amazonaws.com/uifaces/faces/twitter/vladarbatov/128.jpg"
      excerpt="I'm Bob Smith, a vertically aligned type of guy. Lorem ipsum dolor sit amet, consectetur adipisicing elit."
    />
    {/* highlight-end */}
  </Container>
)
```

> Tip: Generally, if you use a component in multiple places on a site, it should be in its own module file in the `components` directory. But, if it's used only in one file, create it inline.

The finished page should now look like:

![User list page with CSS modules](css-modules-userlist.png)

### CSS-in-JS

CSS-in-JS is a component-oriented styling approach. Most generally, it is a pattern where [CSS is composed inline using JavaScript](https://reactjs.org/docs/faq-styling.html#what-is-css-in-js).

#### Gatsby에서 CSS-in-JS 사용하기

There are many different CSS-in-JS libraries and many of them have Gatsby plugins already. We won't cover an example of CSS-in-JS in this initial tutorial, but we encourage you to [explore](/docs/styling/) what the ecosystem has to offer. There are mini-tutorials for two libraries, in particular, [Emotion](/docs/emotion/) and [Styled Components](/docs/styled-components/).

#### Suggested reading on CSS-in-JS

If you're interested in further reading, check out [Christopher "vjeux" Chedeau's 2014 presentation that sparked this movement](https://speakerdeck.com/vjeux/react-css-in-js) as well as [Mark Dalgleish's more recent post "A Unified Styling Language"](https://medium.com/seek-blog/a-unified-styling-language-d0c208de2660).

### 그 외의 CSS 옵션

Gatsby supports almost every possible styling option (if there isn't a plugin yet for your favorite CSS option, [please contribute one!](/contributing/how-to-contribute/))

- [Typography.js](/packages/gatsby-plugin-typography/)
- [Sass](/packages/gatsby-plugin-sass/)
- [JSS](/packages/gatsby-plugin-jss/)
- [Stylus](/packages/gatsby-plugin-stylus/)
- [PostCSS](/packages/gatsby-plugin-postcss/)

and more!

## 다음에 할 것은?

Now continue on to [part three of the tutorial](/tutorial/part-three/), where you'll learn about Gatsby plugins and layout components.
