---
title: "[JSX]React & Node.JS(npm, yarn, npx, jsx)"
categories : React_JS
tag : [Javascript, 리액트, node.js, cra, virtual DOM, state, lifecycle, 컴포넌트, 조건부 렌더링, spread, Destructuring]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..

---

## Node.js 사용 매커니즘(npm)

**사용 방식을 설명. 그리고 Node.js는 런타임 환경이다(Node.js 특징 게시글 참고)**

* **npm, yarn을 전역으로 설치 했기 때문에 어디서든 터미널에서 접근가능**

### npm 명령어

**npm init**은 현재 폴더를 **npm**으로 관리하는 프로젝트 폴더로 설정해줌  
**npm install, uninstall** 경우 **npm**서버로부터 패키지 받아오거나 삭제해줌   
**npm run <커스텀 명령어>**를 사용해 **package.json**의 **scripts** 영역 안의 명령어를 실행할 수 있음.



### 1. 설치(윈도우 기준)

* [Node.js 홈페이지](https://nodejs.org/ko/download/)

* 최신 버전은 기능이 불안정할 수 있으므로 LTS 버전 권장

* NVM(버전매니저)를 통해 다른 버전의 Node.js를 쉽게 설치, 전환 가능

* 설치한 노드 버전은 터미널에서 node -v 또는 npm -v로 확인

### 2. npm

* **Node.js 공식 패키지 매니저**
* cmd에서 cd를 통해 원하는 폴더 접근 후 npm init 으로 npm설치
  * **npm init**을 통해 방대하게 흩어져 있는(github) 자바스크립트 모듈들을 다운받아서 관리(**package.json 생성**)
  * **code . 입력** : VS코드 해당폴더 경로로 바로 실행
  * **mkdir 폴더명** : 폴더명으로 폴더 생성
  * **cd 경로** : 해당 경로로 이동  
    **cd /** : 제일 상위로 이동  
    **cd ..** : 한칸 상위로 이동

#### package.json / node_modules

* **npm 프로젝트에 대해 설정하는 명세서 / 의존 모듈 저장 폴더**
* package.json에 의존성 설정하고, node_modules에 모듈들 실제 저장되는 것

![image-20220628194040629](/images\2022-06-28-(JS)React & Node.js란\image-20220628194040629.png)

### 3. npm install, uninstall : 모듈 추가/제거

![image-20220628194300068](/images\2022-06-28-(JS)React & Node.js란\image-20220628194300068.png)

### 4. npm run, npm start : 실행

* **npm 커스텀 명령어를 실행하는 명령어**
* 터미널에 **node index.js** 입력이 기존 실행방식이였음. 이 명령어를 커스텀이 가능하단 장점
* **node** : Node.js파일 실행  
* **npm run** : scripts의 커스텀 명령어 실행  
  * **npm run start** : **node index.js** 실행  

![image-20220628194454503](/images\2022-06-28-(JS)React & Node.js란\image-20220628194454503.png)

* **예: (npm run) start, async, sync, robot 이런식으로 실행 명령어를 커스텀해서 활용**

![image-20220630145611137](/images\2022-06-28-(JS)React & Node.js란\image-20220630145611137.png)

<br>

## Node.js 사용 매커니즘(yarn)

**페이스북에서 발표한 비공식 패키지 매니저**

**npm을 사용하던 yarn을 사용하던 자유**

[npm과 yarn 비교](https://dongmin-jang.medium.com/npm-vs-yarn-7b699c5d6034)

![image-20220628194957977](/images\2022-06-28-(JS)React & Node.js란\image-20220628194957977.png)

![image-20220628195011378](/images\2022-06-28-(JS)React & Node.js란\image-20220628195011378.png)

<br>

## React.js를 위한 CRA환경(npx)

**CRA는 React.js를 개발하기 위한 환경이다.**

* **npx : npm에 속해있는 npm 패키지 실행 도구이다**
  * **다른 사람의 package.json의 scripts 중 하나를 실행하고 싶을 때 : npx와 스크립트를 적으면 패키지 설치를 하지 않고 실행가능**

* **CRA(Create React App) 환경 구성 가능 (아래 3가지는 동일한 동작 명령어)**
  * **npx create-react-app 폴더명**
  * **npm init react-app 폴더명**
  * **yarn create react-app 폴더명**

### CRA의 구성

* **yarn으로 설치시? package-lock.json이 아닌 yarn.lock으로 파일 구성**
* **index.js가 시작 소스파일, App.js가 App컴포넌트 파일**
* **index.html의 id가 "root"와 index.js의 getElementById('root')가 동일해야 실행이 됨.  **
  **=> 당연한거지만 확인 잘 할것**
* **/public :** React와 관련없는 추가 정적 파일은 여기에 추가
* **/src :** 개발 소스 코드 JS가 포함되는 폴더이다. 해당 파일의 변화를 감지하여 새로고침하며, 후에 빌드/배포될 때 내부 파일이 하나의 파일로 묶여(번들링) ['index.html']에 주입하게 된다.

![image-20220630163936325](/images/2022-06-28-(JS)React & Node.js란/image-20220630163936325.png)



### 참고) index.js

* 모듈 이름만 사용하면 npm을 통해 의존 중인 외부 패키지 모듈을 의미
* 경로를 지정 했다면 프로젝트 내의 모듈을 의미

![image-20220630180909702](/images/2022-06-28-(JS)React & Node.js란/image-20220630180909702.png)

<br>

## CRA환경을 npm, yarn로 개발 서버 실행

- **cd <프로젝트 명>**을 통해 프로젝트 폴더로 이동한 후, **npm start** 혹은 **yarn start**를 입력하면 **localhost:3000**으로 브라우저가 열리면서 **CRA** 프로젝트의 개발 환경이 실행됩니다.
- 개발 환경은 **localhost:3000**에 개발 서버를 열고, **react-hot-loader**를 통해 프로젝트 [**/src**](./src) 폴더 내의 파일이 수정되면 새로고침합니다.
- **localhost:3000**에서 응답 받는 **HTML** 파일은(브라우저로 접속하는 행위는 **GET** 방식으로 요청하는 행위와 같으므로) [**/public**](./public) 폴더 내의 [**index.html**](./public/index.html)에, [**/src**](./src) 폴더 내의 있는 [**index.js**](./src/index.js) 파일을 임시 빌드한 **JavaScript** 코드를 주입한 **HTML** 파일입니다.

> **Node.js**와 **npm** 그리고 **yarn**의 버전이 제각각인 경우 **yarn**과 제대로 호환되지 않을 수 있습니다.  
> 이 경우를 방지하기 위해, 가급적 최신 버전을 사용하시는 것이 좋습니다.



### 명령어

- **npm run start**(**npm start**, **yarn start**) 명령어는 현재 프로젝트의 **React** 개발 서버를 실행합니다.
- **npm run build**(**yarn build**) 명령어는 현재 프로젝트를 빌드하여 배포 가능한 파일을 생성합니다.
- **npm run test**(**yarn test**) 명령어는 현재 프로젝트에 테스트 코드([**/src/App.test.js**](./src/App.test.js))를 실행합니다.
- **npm run eject**(**yarn eject**) 명령어는 현재 프로젝트의 숨겨져 있는 구성 파일을 꺼냅니다.(취소하기 힘듭니다.)

<br>

## React.js의 특징

* **안드로이드 스튜디오처럼 MVC같은 프레임워크와 달리 오직 V(View)만 고려한다.(장점)**
* **자바스크립트를 확장한 문법인 JSX 문법을 사용한다.**

### Virtual DOM

**가상 DOM을 사용함으로써 실제 DOM에 바로 반영하지 않는다.** 

**실제 DOM과 비교해서 차이가 생긴 요소만 가상 DOM에 렌더링함으로써 렌더링 연산을 줄인다.**

* **이 말의 즉슨, 재렌더(업데이트)시 내부적으론 react가 렌더함수 호출해서 렌더링을 하는데, 리페인팅만 실제 DOM과 비교해서 차이가 생긴 요소만 가상 DOM에 리페인팅 한다.**



### 컴포넌트와 단방향 데이터 전달

**React는 컴포넌트라는 단위의 집합으로 구성된 트리 구조이다.**

**각 컴포넌트는 state를 통해 현재 상태를 나타내며, props를 통해 하위 컴포넌트로 데이터 전달(단방향)**



### state와 lifecycle

React의 렌더링 프로세스(알고리즘)은 state와 깊게 연관되어 있다.  
**state가 변경되면 React 컴포넌트 트리에서 리렌더링 여부를 결정**한다.

**크게 컴포넌트 생성(마운트), 변경(업데이트), 제거(언마운트)인 생명주기가 있다.**  
state가 변경됐을 때, state와 연관된 컴포넌트(props등)은 생명주기알고리즘 통해 리렌더링 여부  
판단 후 DOM에 반영한다.

<br>

## JSX 문법

* JS를 확장한 문법으로, **문자열 + HTML**의 결합  
  JS에서 **HTML을 직관적**으로 다룰 수 있는 표현식  
  **리액트의 Element(컴포넌트의 렌더링 요소)**를 생성

* 리액트는 JSX 사용이 필수는 아니지만, 대부분 적용
* 컴포넌트는 JSX 문법을 통해 Element의 집합을 반환  
  JSX는 후에 JS코드로 컴파일됨



### 중괄호 내에 변수 사용 가능

* 변수 : name

```jsx
const element = <h1>Hello, {name}</h1>
```



### 한 줄 이상의 JSX 코드는 대괄호

* 다만, **두개의 최상위 Element는 사용 불가**해서 **꼭 하나의 요소로 감싸**줘야함  
* div, input처럼 최상위 2개를 사용 할 수 는 없음  

```jsx
const multiLine = (
	<div>
    	<h1>Hello, {name}</h1>
    </div>
    <input/> // 문제
); // 한 줄 이상 대괄호
```

* 또한 한가지 태그로 감싸지 않고 return하는 경우도 JSX가 오류 발생(빈태그 가능)
* 빈태그의 활용

```jsx
return (
    <>
    	{element}
		{multiLine}
	</>
);
// 태그 없이 감싸야할 경우 위와 같이 빈 태그로 감싸주면 됨.
```



### HTML 속성 정의 가능

* 문자

```jsx
const element = <div tabIndex = "0"></div>
// tab-index 요소와 같이 '-' 있는 속성은 뒤 문자를 대문자로 연결
```

* 숫자

```jsx
const element = <div tabIndex = {0}></div>
// 이것은 숫자타입인 0
```



### 컴포넌트 표현(import, export, 함수형)

**1. 컴포넌트 명은 일반적으로 단어의 시작마다 대문자인 파스칼 케이스로 작성하며, 파일 이름과 동일하게 함.**

**2. 컴포넌트 파일의 확장자는 .js나 .jsx 아무거나 상관 없지만 리액트의 컴포넌트는 jsx 문법으로 표현하므로 .jsx가 가독성에 좋음**

* **import보다 from먼저 작성시 원하는 메소드 얻기 쉬움**

```jsx
import "./App.css";
// CSS 파일을 불러옴
import ConditionRender from "./ConditionRender";
import InputComponent from "./InputComponent";
// 커스텀 컴포넌트를 작성함
// .js(jsx) 파일이지만 경로 입력 시 .js를 제외하더라도 상관 없음
// default로 export 했기 때문에 {} 없이 불러오는중
```

* **코드내에서 커스텀 컴포넌트 작성(예시는 화살표함수)**

```jsx
// 화살표 함수를 통해 함수형 컴포넌트 선언
const SingleMultiLine = () => {
  const name = "SingleMulti";

  const element = <h1>Hello. {name}</h1>;
  const multiLine = (
    <div tabIndex={0}>
      <h1>Hello, MultilLine {name}</h1>
    </div>
  );
  return (
    <>
      {element}
      {multiLine}
    </>
  );
};
```

* **컴포넌트들 활용해서 생성한 새로운 함수형 컴포넌트**

```jsx
// 함수형 컴포넌트로 선언
function App() {
  return (
    <div className="App">
      <SingleMultiLine />
      <InputComponent />
      <ConditionRender></ConditionRender>
    </div>
  );
  // 커스텀 컴포넌트 또한 태그 이름으로 사용 가능
}
```

* **내보내기(export)**

```jsx
// 현재의 컴포넌트를 바깥에서 사용할 수 있게 내보내기 함.

// default는 1개만 가능하며 이 경우 import를 {}없이 가능함.
export default App;

// default없이 export 한다면 import {App} from... 이런식으로 {}가 필요함
export {App};
import {App} from ...

// export를 함수 앞에서 바로 사용도 가능
export const App = () => {} // 화살표 함수의 경우
export function A() {}; // 일반 함수의 경우

// export default를 함수 앞에서는 일반 함수만 바로 사용 가능
export default function A() {}; // 일반 함수의 경우
export default const App = () => {} // error
```



### 조건부 렌더링

* JSX 내에는 if, switch 문을 사용할 수 없음(제한적).  
  때문에 조건식을 사용하여 렌더링 또는 &&와 ||연산자를 통한 렌더링

```jsx
const ConditionRender = () => {
  const isRender = false; // 임시 조건식

  if (isRender) {
    return <>if 문으로도 조건부 가능하나, 응용할 상황이 제한적</>;
  }

  return (
    <>
      <div>{isRender ? <h1>렌더 가능</h1> : <p>렌더 불가</p>}</div>
      {/* 삼항 연산자를 통해 조건을 골라 렌더링 가능 */}
      <div>
        {isRender && <h1>true면 렌더</h1>}
        {/* && 연산자의 경우 앞의 조건식이 true이면 뒤의 컴포넌트를 렌더 */}
        {isRender || <h1>false면 렌더</h1>}
        {/* || 연산자의 경우 앞의 조건식이 false이면 뒤의 컴포넌트를 렌더 */}
      </div>
    </>
  );
};
export default ConditionRender;
// 현재의 컴포넌트를 바깥에서 사용할 수 있게 내보내기 함.
```



## 문법에서 괄호 등등 (추가 설명)

### {() => 함수()} 형태

**onChangeCheckbox**를 매개변수 id를 넘기기위해 **onChangeCheckbox(checkboxItem.id)**로 넘기면? 함수가 바로 실행이되며, 우리는 함수자체를 넘기고 싶기 때문에 **()=>{onChangeCheckbox(checkboxItem.id)}**이런 형태로 **화살표 함수로 한번 감싸는 것**이다.

```jsx
  return (
    <>
      <FunctionCheckbox
        checked={AllCheck}
        onChangeCheckbox={onChangeAllCheckbox}
        label="전체 체크 여부"
      />
      {checkboxList.map((checkboxItem) => (
        <FunctionCheckbox
          key={checkboxItem.id}
          isHide={checkboxItem.isHide}
          checked={checkboxItem.checked}
          onChangeCheckbox={() => onChangeCheckbox(checkboxItem.id)}
          onClickChangeView={() => onClickChangeView(checkboxItem.id)}
          // onChangeCheck 함수와 onClickChangeView 함수에는 id 매개변수를 넣어주어야 하는데,
          // 클릭 이벤트로 넣을 때 id 매개변수를 전달하기 위해선 함수를 미리 만들어 놓아야 함
        />
      ))}
      {/* 배열 내장 함수인 map으로 컴포넌트를 반환하게 되면 리스트로 출력 */}
    </>
  );
```



### `JavaScript`(`ES6`)의 `Spread` 연산자(전개 연산자)

- [MDN 문서 - 전개 구문](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
- [Velog - 전개 연산자](https://velog.io/@recordboy/%EC%A0%84%EA%B0%9C-%EC%97%B0%EC%82%B0%EC%9E%90Spread-Operator)
- `...` 연산을 사용하면 배열이나 객체의 요소를 펼칠 수 있습니다.
- 이를 사용하여 객체나 배열의 내용을 손 쉽게 복사할 수 있습니다.

```jsx
// checkboxItem은 {id:~, isHide:~, checked:~} 로 이루어진 객체임
// ...을 통해 객체를 풀었고, 객체 내 요소인 checked로 쉽게 수정을 한것
const onChangeAllCheckbox = useCallback(() => {
    const newCheckboxList = checkboxList.map((checkboxItem) => ({
        ...checkboxItem, 
        checked: !AllCheck,
    }));
    setCheckboxList(newCheckboxList);
}, [AllCheck, checkboxList]);
```



### `JavaScript`(`ES6`)의 `Destructuring` 연산자(구조 분해 할당, 비구조화 할당)

- [MDN 문서 - 구조 분해 할당](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)
- [Guidebook - 비구조화 할당](https://learnjs.vlpt.us/useful/06-destructuring.html)
- `const { }`, `const []` 연산을 사용하여 객체에서 특정 값만 가져올 수 있습니다.
- 이를 사용하여 객체나 배열의 내용 중 일부를 손 쉽게 가져올 수 있습니다.
- 객체의 경우 `const { property1, property2 }` 연산을 사용하면 전달받는 객체에 `property1`의 키를 가진 값과, `property2`의 키를 가진 값이 할당됩니다. 키의 이름이 객체에 존재하지 않으면 `undefined`가 할당됩니다.
- 배열의 경우 `const [name1, name2]` 연산을 사용하면 `name1`에 첫 번째 요소, `name2`에 두 번째 요소가 할당되며, 그 이상도 가능합니다.

```jsx
// 함수형 컴포넌트 작성에서 props를 가져올 때
const FunctionCheckbox = memo(
  ({ isHide, checked, label, onChangeCheckbox, onClickChangeView }) => {
    return (
//      <StyleCheckboxWrapper>
// ... 생략
            
// useState에서 setState 함수를 가져올 때
const [checkboxList, setCheckboxList] = useState([
    {
        id: 1, // 해당 Checkbox의 id
        isHide: true, // 자세히 보기 숨겨짐 여부
        checked: false, // check 상태
    },
    {
        id: 2,
        isHide: true,
        checked: false,
    },
]);
```

* 또다른 예시

```js
// 1. 함수의 파라미터에서도 비구조화 할당
// 함수 print에서 파라미터의 이름이 전달받은 object 객체의 키 값과 같아야 해당 키 값을 얻을 수 있는것임. 동일 이름 없으면 undefined 값 가져오는것.
const object = { a: 1, b: 2 };

function print({ a, b }) { 
  console.log(a);
  console.log(b);
}
print(object);

// 2. 객체 안에 있는 값을 추출해서 변수 혹은 상수로 바로 선언
const object = { a: 1, b: 2 };

const { a, b } = object;

console.log(a); // 1
console.log(b); // 2
```

* 또다른 예시

```jsx
// 1. 함수의 파라미터에서도 비구조화 할당의 예시인데 movieList로 이름이 동일하단걸 체크
// 서로 다른 파일
const MovieList = ({movieList}) => {
    return (
        <div>
            {movieList.map((movieItem) => {
                return(
                    <Movie 
                    key={movieItem.title} // key도 보내줘야 안전
                    {...movieItem}>
                    </Movie>
                )
            })}
        </div>
    )
}
export const Movies = memo(MovieList);
// 다른 파일
return (
    <div>
        <Movies movieList={movieList}></Movies>
    </div>
);
```

* 또다른 예시 **{}가 아닌 (props)로 하나의 객체로 가져오는 예시**
  * props로 가져오는 방법이고, {}로 했을때와 똑같이 값을 바로 가져오며, props는 배열로 이루어져 있어서 props[0] 이런식으로도 사용가능하다.

```jsx
// const Movie = ({ title, subtitle, image, link, director}) => {
const Movie = ((props)) => {
    return (
        <div>
            <div>
                <img src={props.image} alt="movie-thumbnail"></img>
            </div>
            <div>
                <a href={props.link}>
                    <h3>{props.title}</h3>
                    <h4>{props.subtitle}</h4>
                    <p>감독 : {props.director}</p>
                </a>
            </div>
        </div>
    );
}
```



### [ES6] Spread와 Destructuring을 같이 사용한 예시

* **Spread X 일때** : 할당될 객체 키 값과 동일한 이름 맞춰가기 위해 아래처럼 코드 작성했었음  
  \<Movies **movieList1**={movieList}>\</Movies> // 다른 파일  
  const MovieList = ({**movieList1**}) => {...}
* **Spread와 Destructuring을 같이 활용한 경우**  
  <Movie {**...movieItem**}/> 와  
  const Movie = ({ **title, subtitle, image, link, director** }) => {...}  
  이런식으로 넘기고 받아올 수 있다는 차이점
* **결론** : **movieList같은 배열을 통째로 넘기**고 싶을땐 **Spread X** 인 방식을 활용,  
  **movieList를 분해해서 넘기**고 싶을땐 **Spread와 함께**하는 방식을 활용

```jsx
const Movie = ({ title, subtitle, image, link, director}) => {
    return (
        <div>
            <div>
                <img src={image} alt="movie-thumbnail"></img>
            </div>
            <div>
                <a href={link}>
                    <h3>{title}</h3>
                    <h4>{subtitle}</h4>
                    <p>감독 : {director}</p>
                </a>
            </div>
        </div>
    );
}

const MovieList = ({movieList1}) => {
    return (
        <div>
            {movieList1.map((movieItem) => {
                return(
                    <Movie 
                    key={movieItem.title} // key도 보내줘야 안전
                    {...movieItem}>
                    </Movie>
                )
            })}
        </div>
    )
}
export const Movies = memo(MovieList);
// 다른 파일
return (
    <div>
        <Movies movieList1={movieList}></Movies>
    </div>
);
```
