---
title:  "React로 스타일링(@emotion,SCSS,SASS)"
categories : [React_JS, Css]
tag : [JavaScript, 리액트, React, jsx, scss, sass, 스타일, emotion, export 키워드, emotion/styled/macro 라이브러리]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---

## 리액트의 스타일링 방식 결정

스타일링 방식을 크게 보자면, `styled(사용자 스타일 태그)` 사용과 `css` 사용 방식이 있다.  

`styled(사용자 스타일 태그)`의 [emotion-2번째-방식](#emotion-2번째-코드-작성-방식) 을 사용하는걸 추천한다.

`css`는 기본적으로 하는법은 꼭 알고 넘어가야하므로 [css 방식](#3-css-scss-로드---import사용) 을 참고하는걸 추천한다.

[11](#"html에서-스타일-적용")

[11](#html에서-스타일-적용)

<br>

## HTML에서 스타일 적용

* css정리 포스팅글 참고 - [CSS_기초](/_posts/Style_Sheet/CSS/CSS_기초)

 [CSS_기초](/css/CSS_기초)



### 인라인 스타일

**HTML 문서의 스타일을 지정할 `태그에 직접` 적용**

* 가장 높은 우선순위로 적용하므로 디버깅/테스트 용으로 좋음





### 스타일 태그

**HTML 문서의 스타일을 모아두어 관리**

* `<style>` 태그를 활용





### .css 파일 로드

**.css 파일을 통해 스타일을 외부에서 관리**

* **%PUBLIC_URL%** 는 index.html있는 폴더를 잡아준 키워드
* 이것 말고도 경로 **상대경로든 절대경로든 아무거나 사용해도 상관없음**  
  예로 "./expand_style.css"

```html
<link rel="stylesheet" href="%PUBLIC_URL%/expand_style.css"/>
```





### 자주 쓰는 CSS 요소

- `display`
- `position`
  - `top`, `left`, `bottom`, `right`
- `width`, `height`
- `color`, `font-weight`, `font-size`
- `background-color`, `background-image`
- `padding`, `margin`
- `border`, `border-radius`, `box-shadow`
- `transform`
- `transition`

<br>

## CSS 우선순위

**!important -> 인라인 -> #id -> .class -> 태그들**

#과 .은 각각 id와 class 의미

<br>

## React의 네이밍

- `React`에서는 일반적인 `HTML`, `JavaScript`에서 사용하는 어트리뷰트, 프로퍼티의 이름을 사용하지 않고 카멜 케이스(`camelCase`)의 이름을 사용합니다.
- 컴포넌트의 이름의 경우 파스칼 케이스(`PascalCase`)를 사용합니다.
- 또한 `JavaScript` 예약어(`class`, `for` 등)의 경우, 이름을 그대로 사용하지 않고 변환된 이름을 사용합니다.

### 참고

- [이름 표기법](https://velog.io/@leyuri/%ED%91%9C%EA%B8%B0%EB%B2%95-%EC%8A%A4%EB%84%A4%EC%9D%B4%ED%81%AC-%EC%BC%80%EC%9D%B4%EC%8A%A4-%ED%8C%8C%EC%8A%A4%EC%B9%BC-%EC%BC%80%EC%9D%B4%EC%8A%A4-%EC%B9%B4%EB%A9%9C-%EC%BC%80%EC%9D%B4%EC%8A%A4)

<br>

## React에서 스타일 작성법

### 1. 스타일 어트리뷰트

* **JSX**의 HTML 요소 또한 **style 어트리뷰트** 존재

- `HTML`에서 작성하는 것처럼 `style` 어트리뷰트를 사용하지만, 문자열이 아닌 `React`의 프로퍼티 네이밍 규칙을 준수하는 `JavaScript` 객체를 전달합니다.
- 스타일에 알맞지 않은 프로퍼티는 **파싱하지 않음**



**html**

```html
<div style="속성: 속성값;"></div>
```



**JSX**

```jsx
// StyleAttributeComponent.jsx
// 스타일 어트리뷰트를 사용한 컴포넌트 선언
const StyleAttributeComponent = () => {
  return (
    <div
      style={{
        width: 100, // width에서 숫자의 경우 px 단위로 변경
        height: "100px", // 문자열로 100px를 입력해도 무방함
        padding: 32,
        backgroundColor: 12, // background-color를 카멜 케이스로 변경
        // 12는 backgroundColor에 유효하지 않으므로 적용되지 않음
        // background: "#fff",
        background: "rgba(0, 0, 0, 0.6)",
      }}
    >
      Style Attribute
    </div>
  );
};

export default StyleAttributeComponent;

```





### 2. CSS in JavaScript

- `JavaScript`에서 `CSS` 코드 작성 사용하는 방법입니다
- CSS 파일 로드 방식에 비해 느린 성능이지만, **하나의 컴포넌트** 작성하는 것처럼 JS에서 사용 가능합니다.
  * `사용자 스타일 태그`를 활용하는 이유는   
    `<div className='container'...`처럼 이름 주면서 css파일로 적용하다보면   
    간단한 프로젝트는 상관없지만 큰 프로젝트의 경우 `className`의 이름을  
    정말 길게 적어서 구분해야할 필요가 생긴다. 즉, 가독성이 좋지 않아진다.
- `styled-components`와 `@emotion`이 가장 유명한 **라이브러리**입니다.
  * `@emotion` 추천



#### `@emotion` 설치

* `사용자 스타일 태그`를 사용하기 위해선 `styled`가 필요해서 아래 설치작업이 꼭 필요한것

```shell
npm install @emotion/react @emotion/styled
```

또는

```shell
yarn add @emotion/react @emotion/styled
```

- 설치 명령어(`install`, `add`)를 입력 후 모듈을 여러 개 나열하면 한 번에 모두 설치합니다.
- [`package.json`](./package.json)의 `dependencies`에 해당 모듈의 이름이 있는 경우 `npm install` 혹은 `yarn` 명령어만 입력하면 됩니다.



#### `@emotion` 1번째 코드 작성 방식

* **styled.div`** 처럼 작성하면 div 컴포넌트를 생성한것! **StyleEmotionDiv태그가 div 커스텀한 태그**라고 생각하고 사용하면 됨

```jsx
// EmotionComponent.jsx
import styled from "@emotion/styled";
// @emotion의 styled 라이브러리를 가져옴

// styled 라이브러리로 스타일이 지정된 div 컴포넌트 생성
const StyleEmotionDiv = styled.div`
  display: flex;
  width: 100px;
  height: 100px;
  padding: 32px;

  background-color: #ddd; // CSS 파일처럼 속성 이름 적용

  &::after {
    content: "Emotion";
  } // SCSS 사용 가능
`;

// @emotion을 사용한 컴포넌트 선언
const EmotionComponent = () => {
  // @emotion으로 스타일링한 div 컴포넌트 렌더링
  return <StyleEmotionDiv />;
};

export default EmotionComponent;
```



#### `@emotion` 2번째 코드 작성 방식

* **중요** : jsx로 바로 스타일 구성한게 아닌 한번더 나눠서 구성한다면?  
  **Circle.style.js** 이런 형태로 네임 지정할것**(보통 이렇게함)**
* **중요** : **export default**가 아닌 **export 키워드**는 해당 이름 가진 변수명으로 외부로 내보내기 함

```jsx
// Circle.jsx
import { StyleCircleBox, StyleCircleText } from "./Circle.style";
// @emotion/styled 라이브러리로 스타일이 지정된 컴포넌트 파일에서 내보낸 컴포넌트들을 가져옴

const Circle = () => {
  return (
    <>
      <StyleCircleBox>
        <StyleCircleText>Inner Box</StyleCircleText>
      </StyleCircleBox>
      <StyleCircleText red>Outer Box</StyleCircleText>
      {/* 스타일 컴포넌트에 매개변수를 넘겨줌. 값이 지정되어 있지 않으면 암묵적으로 true 할당 */}
      {/* 이 경우 red={true}와 같음 */}
      <StyleCircleBox color="#0f6"></StyleCircleBox>
      {/* 스타일 컴포넌트에 매개변수를 넘겨줌 */}
    </>
  );
};

export default Circle;
```



* **@emotion/styled/macro : 컴포넌트에 주입할 매개변수(어트리뷰트)를 사용**

```js
// Circle.style.js

import styled from "@emotion/styled/macro";
// 컴포넌트에 주입할 매개변수(어트리뷰트)를 사용
// 매개변수를 사용하기 위해선 외부 설정을 하거나, @emotion/styled/macro 라이브러리 호출

// styled 라이브러리로 스타일이 지정된 p 컴포넌트 생성
// export default가 아닌 export 키워드는 해당 이름을 가진 변수(여기서는 컴포넌트)를 동일한 이름으로 외부에 내보내기 함
// 따라서 다른 파일에서 이 변수(컴포넌트)를 사용하기 위해선 내보낸 변수(컴포넌트)와 동일한 이름으로 불러와야 함
export const StyleCircleText = styled.p((props) => ({
  color: props.red && "#f00", // red = true임
  // 컴포넌트에 주입할 매개변수(어트리뷰트)를 사용
}));

// styled 라이브러리로 스타일이 지정된 div 컴포넌트 생성
export const StyleCircleBox = styled.div`
  display: flex;
  align-items: center;
  justify-content: center;

  width: 200px;
  height: 200px;

  background-color: ${(props) => props.color || "#06f"};
  // 컴포넌트에 주입할 매개변수(어트리뷰트)를 사용

  border-radius: 50%;

  box-shadow: 0px 3px 15px #0001;

  ${StyleCircleText} {
    // SCSS 문법으로 현재 스타일 컴포넌트 하위(자식, 손자)에 있을 StyleCircle 스타일 컴포넌트에 스타일 지정
    font-size: 40px;
  }
`;
```



#### CSSApp.js - 3개의 예시 합침

* **StyleAttributeComponent, EmotionComponent, Circle**를 모음  
  **index.js**에선 **CSSApp 컴포넌트**로 불러와 사용하는 것
* 참고로 컴포넌트 다루는 **흐름을 이해하기 위해** 추가로 적은것일 뿐

```js
import StyleAttributeComponent from "./components/StyleAttributeComponent";
import EmotionComponent from "./components/EmotionComponent";
import Circle from "./components/Circle";
// 스타일 어트리뷰트 및 @emotion/styled로 작성한 컴포넌트 가져옴

import logo from "./logo.svg";
import "./CSSApp.css";

// CSS 로드 방식을 사용한 컴포넌트
function CSSApp() {
  return (
    <div className="App">
      <StyleAttributeComponent />
      <EmotionComponent />
      <Circle />
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
      </header>
      <div className="div">
        <p>이건 외부 스타일 시트의 스타일 요소가 적용된 태그입니다.</p>
        <p>이 요소는 public/index.html 파일에서 불러온 expand_style.css가 적용됨.</p>
      </div>
    </div>
  );
}
export default CSSApp;
```





### 3. CSS, SCSS 로드 - import사용

#### React.js의 CSS 파일로드

- `import "<CSS 파일 경로>";`를 통해 `CSS` 파일을 불러올 수 있습니다.
  
  - 이는 `CRA` 환경에서 암묵적으로 `webpack`이라는 도구를 통해 `css-loader`를 사용하기 때문입니다.
  
  

**js**

```js
import "./CSSApp.css";
...~~~
    <header className="App-header">
등등..
```



**css**

```css
// 위의 className의 App-header를 해당 css파일에서 스타일링 하는것이다.
.App {
  text-align: center;
}

.App-header {
  background-color: #282c34;
  min-height: 100vh;
}
등등..
```



#### React.js의 module.css 파일 로드

- `import <스타일 변수> from "<CSS module 파일 경로>";`를 통해 `CSS Module` 파일을 불러올 수 있습니다.
  - `CSS`의 클래스 이름이 **중첩되는 것을 방지**하기 위해 중간에 임의의 이름을 추가해 줍니다.

* 원래처럼 해석할게 아니라 **styles**라는 변수에 from뒤의 '~'안의 내용들을 **담아와서 사용**한다고 생각.
* **파일명이 module.css** 이런식으로 지정하는게 일반적임



**js** (css는 유사해서 생략)

```js
import styles from "./CSSModuleApp.module.css";
// Module CSS 파일의 클래스 명을 객체로 가져옴

// CSS Module 로드 방식을 사용한 컴포넌트
function CSSModuleApp() {
  return (
    <div className={styles.App}>
      <header className={styles["App-header"]}>
          ... 등등 생략
```



#### SASS, SCSS 사용(CSS 전처리기들)

**Syntactically Awesome Style Sheets : 가장 유명한 CSS 전처리기**

* // 주석을 사용 가능
* 데이터 타입과 변수가 있음

* 실제로 코드 작성할때 SCSS같은 형식으로 많이 작성한다고함. 가독성이 좋아서.

- `SCSS`는 `node-sass` 모듈이 설치되어 있어야 불러올 수 있다.

  ```shell
  npm install node-sass
  ```

  또는

  ```shell
  yarn add node-sass
  ```

  - 그리고 위의 `CSS`, `CSS Module` 불러오는 것과 같이 `SCSS` 파일 불러올 수 있습니다.

![image-20220701031554954](/images/2022-06-30-(JSX)React로 스타일링(@emotion,SCSS,SASS)/image-20220701031554954.png)

![image-20220701031840399](/images/2022-06-30-(JSX)React로 스타일링(@emotion,SCSS,SASS)/image-20220701031840399.png)

![image-20220701031902168](/images/2022-06-30-(JSX)React로 스타일링(@emotion,SCSS,SASS)/image-20220701031902168.png)



**리스트를 렌더링할 때 요소간 간격을 주기 좋은 방식**

```js
import styled from "@emotion/styled/macro";

export const StyleCheckboxWrapper = styled.div`
  padding: 0 12px;

  box-shadow: 0px 3px 15px #0001;
  // 요소에 그림자 음영을 추가

  & + & {
    // SCSS 문법으로, 첫 번째 요소부터 margin이 적용되지 않고,
    // 두 번째 요소부터 위 요소와 margin이 적용됨
    // 리스트를 렌더링할 때 요소간 간격을 주기 좋음
    margin-top: 20px;
  }
`;
```



**padding을 주는 다른 방법**

```js
export const StyleCheckboxDetail = styled.div`
  width: 100%;

  padding: 24px 0;
  // 이렇게 입력하면 y축에만 padding이 적용됨
  // 반대로 0 24px; 라면 x축에만 padding 적용
`;

```





### 4. 참고) html은 link 태그

* public 폴더는 정적 폴더이고 새로고침 때마다 src 폴더의 소스들만 가능
* **public 폴더는 서버를 껏다 켜야 적용을 함**

```html
<link rel="stylesheet" href="%PUBLIC_URL%/expand_style.css" />
<!-- 외부 스타일 시트 불러옴 -->
```

<br>

## index.js의 조건부 렌더링

* selector 변수의 값에 따라 예제 컴포넌트를 선택
* switch 조건부로 사용
* **ReactDom.render() 사용한 부분을 잘 확인**

```js
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";

import CSSApp from "./CSSApp";
import CSSModuleApp from "./CSSModuleApp";
import SCSSApp from "./SCSSApp";
import SCSSModuleApp from "./SCSSModuleApp";
// 예제 컴포넌트들을 가져옴

import reportWebVitals from "./reportWebVitals";

const selector = "CSS";
// 선택할 예제 컴포넌트

const getRenderApp = (selector) => {
  // selector 변수를 통해 예제 컴포넌트를 반환하는 함수
  switch (selector) {
    case "CSS":
      return <CSSApp />;
    case "CSSModule":
      return <CSSModuleApp />;
    case "SCSS":
      return <SCSSApp />;
    default:
      return <SCSSModuleApp />;
  }
};

ReactDOM.render(<React.StrictMode>{getRenderApp(selector)}</React.StrictMode>, document.getElementById("root"));
// getRenderApp은 컴포넌트를 반환하며,
// 반환된 컴포넌트를 기존의 App 렌더 위치에 넣어 selector에 따라 다른 App 렌더

reportWebVitals();
```



**보통의 기존 코드**

```js
ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root') // 이 root ID는 public/index.html의 <div id="root"></div>와 동일해야 함.
);
```

**즉, 차이점은 { } 를 통해 getRenderApp() 함수 사용해서 원하는 태그 리턴받아 사용했다.**

