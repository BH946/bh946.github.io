---
title: "React_Project_Movie_외부 API 이용"
categories : React_JS
tag : [Javascript, 리액트, AJAX, fetch API, concat, filter, map]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..

---



## 기본 필요 지식

**`React` 컴포넌트 내에서 수정시(state, props) 재렌더를 할텐데, 
만약 어떠한 함수가 선언 되어 있다면 이 함수는 컴포넌트가 렌더링 될때마다 
새로운 함수를 생성한다** 라는 등의 단점들을 `최적화`를 통해 예방  

* `함수 예`로 **useCallback**은 **지정한 배열 값 변화**에만 **재렌더**를 하고, 나머진 재렌더 하지 않으니 최적화
  * **지정값이 빈배열**일시 기존 **함수** 그대로 사용한다는 뜻. 새로 **함수** 생성될일이 없다.
* `변수 예`로 **useMemo**는 **지정한 배열 값 변화**에만 **재렌더**를 하고, 나머진 재렌더 하지 않으니 최적화
  * **지정값이 빈배열**일시 기존 **변수** 그대로 사용한다는 뜻. 새로 **변수** 생성될일이 없다.
* `props 예`로 **memo**는 **지정한 props(매개변수)의 변화에만 재렌더**를 하고, 나머진 재렌더 하지 않으니 최적화
* **useState**는  **컴포넌트 상태**를 관리. **재렌더시** 변수 새로 다시 선언이 아니라 관리중인 상태값으로 사용. 또한 해당 **상태(state)** 변경시 Virtual Dom에서 재렌더 여부 결정
* **useEffect**는 진짜 LifeCycle의 재렌더(Update)와 유사. **지정한 배열 값의 변화에 실행을함.**



**`LifeCycle`은 Mount, Update, UnMount 로 크게 3가지로 분류**

* 참고로 Ctrl+S 이나, 웹 새로고침은 아마 첨부터 코드 실행하는것 같다. mount과정을 계속 거치길래 그렇게 판단 하였음.(useEffect로 확인해봄)
* 맨 처음 컴포넌트가 **render** 될때는 **마운트** 과정을 거침
  그러나 그 이후에 props, state가 변경되어 **render** 될 때는 마운트를 거치지 않음
  그래서 **마운트(Mount)는 DOM 객체가 생성되고 브라우저에 나타나는 것을 의미**



**참고로 import 문제는 default 로 export 아니면 무조건 {} 써줄것.**

<br>

## 초기 폴더 구성

**git bash**로 **npx create-react-app** 폴더명 으로 설치

```bash
npx create-react-app react_project
```

폴더 구성에서 **src 폴더 하위에 새로운 코드들 작성**할것. 

* import시 **src 폴더를 기본으로 경로** 잡기 때문

public 폴더에는 이미지 등 정적 파일들 작성할 것.

<br>

## `Material UI` 라이브러리 `@mui/material` 설치

- 간단하게 `Material UI`를 사용할 수 있게 해주는 라이브러리로, `@emotion`를 정식 지원함.
- `@mui/material`과 `@emotion`을 설치.

```shell
npm install @mui/material @emotion/react @emotion/styled
```

또는

```shell
yarn add @mui/material @emotion/react @emotion/styled
```

<br>

## 외부 `API` 요청을 위해 네이버 개발자 센터 등록 및 키 발급

- [네이버 개발자 센터](https://developers.naver.com/main/)
- [네이버 영화 검색 API 문서](https://developers.naver.com/docs/search/movie/)

<br>

## `API` 키를 저장하는 방법

- `/.env`

  - 프로젝트의 환경 변수 등 민감한 정보를 저장하는 파일
  - `REACT_APP_`으로 시작하는 변수를 사용할 수 있음
  - [/.gitignore](../.gitignore)과 같이 저장소에 업로드 되지 않는 방법을 사용하여 관리
  - `React`에서는 `process.env.REACT_APP_이름`으로 접근할 수 있음 (보통 이름 대문자)
  - 해당 파일에 다음 내용 작성

  ```
  REACT_APP_NAVER_CLIENT_ID="<Client ID>"
  REACT_APP_NAVER_CLIENT_SECRET="<Client Secret>"
  ```

  - `<Client ID>`와 `<Client Secret>`에 네이버 개발자 센터에서 발급받은 키 입력

<br>

## `CORS` 문제를 임시로 해결하기 위한 방안

**서버가 아닌 클라이언트에서 데이터 요청시 자동으로 차단당하는 경우가 CORS 문제이다.**

- `http-proxy-middleware` 모듈을 사용하여 해결할 수 있음

```shell
npm install http-proxy-middleware --save-dev
```

또는

```shell
yarn add -D http-proxy-middleware
```

- 모듈 설치 후 [`/src/setupProxy.js`](./src/setupProxy.js) 파일 생성 후 코드 작성
- 개발 서버 재실행

### setupProxy.js

* **이번 API호출에서도 CORS문제가 발생하기 때문에 이부분 추가해서 임시로 사용하자**

```javascript
// "/api"로 접근시 https://openapi.naver.com/api.. 로 호출하게 된다.
// 따라서 url을 fetch로 접근할 때 /api/v1/... 이런식으로 입력하면 된다는 것이다.
const { createProxyMiddleware } = require("http-proxy-middleware");

module.exports = function (app) {
  app.use(
    "/api",
    createProxyMiddleware({
      target: "https://openapi.naver.com",
      changeOrigin: true,
      pathRewrite: { "^/api/": "/" },
    })
  );
};
```

<br>

## 배열을 상태로 관리할 때 응용하기 좋은 내장 함수

- 추가: `Array.prototype.concat`
  * 배열을 합침
- 변경: `Array.prototype.map`
  * 산술된 인자를 받아 배열을 만듬
- 삭제: `Array.prototype.filter`
  * 불리언이 true인 값만 가지고 배열을 만듬
- 그 외 추가 계산을 위한 함수
  - `Array.prototype.reduce`, `Array.prototype.every`, `Array.prototype.some` 등

<br>

## 컴포넌트 스타일링

### 디자인 초안확인

![image-20220704164612149](/images/2022-07-03-외부 API 호출한 것 React.js로 디자인까지/image-20220704164612149.png)



#### RequestTest.jsx(API 호출 테스트용)

단순히 API 호출 테스트를 위해서 지원하는 여러가지 **Tool(도구)**를 이용해도 되지만 여기선 **간단히 코드 작성**해서 요청에 응답을 받아 보겠다.

* **테스트 방법** : 단순히 App.js에서 RequestTest태그를 추가해서 test하면 된다.
  * 테스트 중 **index.js**에 **\<React.StrictMode>**를 사용해서 useEffect를 써도 console 두 번 찍히는 문제 발견했으며, 해당 태그 지우면 해결된다.

```jsx
// RequestTest.jsx
import { useCallback, useEffect, useState } from "react";

const URL = "/api/v1/search/movie.json?query=어벤져스";
// https://openapi.naver.com/v1/search/movie.json?query=어벤져스
// => API 요청할 주소이다.
// 참고로 cors 문제 임시로 해결. 따라서 /api/로 접근.

// API 요청 테스트하는 컴포넌트
const RequestTest = () => {
  // fetch API 요청 test(async/await으로 비동기 요청을 동기처럼 사용)
  const callApiFetch = async () => {
    const response = await fetch(URL, {
      headers: {
        "Content-Type": "application/json",
        "X-Naver-Client-Id": process.env.REACT_APP_NAVER_CLIENT_ID,
        "X-Naver-Client-Secret": process.env.REACT_APP_NAVER_CLIENT_SECRET,
      },

    })
    console.log(response)
    const data = await response.json();
    console.log(data.items)
  }
  useEffect(() => {
    // mount 시 API 요청
    callApiFetch();
    console.log("rendering~")
  }, []);

  return (<></>);
}

export default RequestTest;
```

![image-20220706235423572](/images/2022-07-03-외부 API 호출한 것 React.js로 디자인까지/image-20220706235423572.png)



### 컴포넌트 뼈대 구성

**배치를 위해 요소를 우선 설계**

#### /apis/fetch.js

* API 호출이 잘되는건 알았기 때문에 **fetch.js로 api호출은 따로 분류해서 관리**하자

```js
import qs from "qs";

const URL = "/api/v1/search/movie.json";
// https://openapi.naver.com/v1/search/movie.json
// API를 요청할 주소
// 참고로 cors 문제는 임시로 해결

// offset : 데이터 시작 위치, limit : 데이터 개수 제한
export const requestGetMovieList = async (offset =1, limit = 5) => {
  const query = qs.stringify({
    query: "어벤져스",
    start: offset,
    display: limit,
  })
  const URI = `${URL}?${query}`;

  const response = await fetch(URI, {
    headers: {
      "Content-Type": "application/json",
      "X-Naver-Client-Id": process.env.REACT_APP_NAVER_CLIENT_ID,
      "X-Naver-Client-Secret": process.env.REACT_APP_NAVER_CLIENT_SECRET,
    },
  });

  return await response.json();
};
```



#### components/Movie.jsx

* **컴포넌트 단위**로 구분하기 위해 따로 분류해서 관리하자

```jsx
import {memo} from "react";

// 최적화
// 지정한 props의 변화 외의 불필요한 컴포넌트 재렌더 방지하는 memo() 사용

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
// memo 함수를 통해 불필요한 컴포넌트 재렌더 방지
// movieList 이외의 다른 값이 변경되어도 렌더링에 영향을 주지 않음

export default Movie; // 기본값
```



#### containers/MovieContainer.jsx

* **컴포넌트의 모음으로 컨테이너**로 구분해서 분류해서 관리하자
* 뒤에 추가버튼, 체크박스 버튼 등등 기능 추가하게 되면 당연히 이 코드에서 더 수정이 될 예정이다

```jsx
import { useState, useCallback, useEffect } from "react";

import { requestGetMovieList } from "../apis/fetch";
import { Movies } from "../components/Movie";

// 최적화
// movieList가 변할때만 함수 새로 생성하기 위해 useCallback() 사용
// 재렌더마다 API 요청이 아닌 mount 시에만 API 요청하기 위해 useEffect() 사용

const MovieContainer = () => {
    const [movieList, setMovieList] = useState([]);
    
    const callApiGetMovieList = useCallback(async () => {
        const data = await requestGetMovieList()
        // data.items => [{},{},...] 로 구성
        setMovieList(data.items)
        console.log(data.items)
    }, [movieList])

    useEffect(() => {
        // mount 시에만 API 요청
        callApiGetMovieList();
    }, [])

    return (
        <div>
            <Movies movieList={movieList}></Movies>
        </div>
    );
}

export default MovieContainer;
```

![image-20220707013003821](/images/2022-07-03-외부 API 호출한 것 React.js로 디자인까지/image-20220707013003821.png)



### 컴포넌트 스타일 요소 구성

#### HTML 뼈대를 보고 태그에 이름을 붙이는 단계

**Movie.jsx를 구성한 뼈대**를 확인

```jsx
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
```



**components/Movie.style.js** 로 태그에 이름 붙혀 스타일 지정

```js
import styled from "@emotion/styled";

export const StyleMovieItem = styled.div``;
export const StyleMovieImageBox = styled.div``;
export const StyleMovieImage = styled.img``;
export const StyleMovieContentBox = styled.div``;
export const StyleMovieAnchor = styled.a``;
export const StyleMovieTitle = styled.h3``;
export const StyleMovieSubtitle = styled.h4``;
export const StyleMovieDirector = styled.p``;
export const StyleMovieList = styled.div``;
```



#### 왼쪽 이미지와 오른쪽 텍스트 영역 스타일링

**Movie.jsx**는 이름 붙힌 태그로 수정

```jsx
<StyleMovieItem>
    <StyleMovieImageBox>
        <StyleMovieImage src={image} alt="movie-thumbnail"></StyleMovieImage>
    </StyleMovieImageBox>
    <StyleMovieContentBox>
        <StyleMovieAnchor href={link}>
            <StyleMovieTitle>{title}</StyleMovieTitle>
            <StyleMovieSubtitle>{subtitle}</StyleMovieSubtitle>
            <StyleMovieDirector>감독 : {director}</StyleMovieDirector>
        </StyleMovieAnchor>
    </StyleMovieContentBox>
</StyleMovieItem>

// 중간부분 생략...
// 여기 div도 수정한것(Movie보다 상위태그이며 좌우 padding 주기위함)
<StyleMovieList> 
    {movieList.map((movieItem) => {
        return(
            <Movie 
                key={movieItem.title} // key도 보내줘야 안전
                {...movieItem}>
            </Movie>
        )
    })}
</StyleMovieList>
```



**Movie.style.js** 에서 스타일들 수정

```js
import styled from "@emotion/styled";

export const StyleMovieItem = styled.div`
    // display는 화면을 배치하는 방법
    display: flex; // flex란 유연성을 뜻함
    flex-direction: row; // align(가로)와 justify(세로) 반대로 생각할 것
    align-items: center;
    
    width:100%;
    border: solid 1px #777; // 실선, 1px크기, #777색깔
    border-radius: 20px; // 둥글게
    // border-radius에 의해 끝쪽이 줄었지만 내부 요소는 오버할 수 있음
    overflow: hidden; // 오버한 부분 hidden으로 숨김
    box-shadow: 0px 3px 15px #0003; // 이 그림자 속성값을 추천

    // scss문법이며, 두번째 요소부터 margin-top이 먹히는 특징
    & + & {
        margin-top: 20px;
    }
`;

export const StyleMovieImageBox = styled.div`
    display: flex;
    width:40%;
    height: 100%;
`;

export const StyleMovieImage = styled.img`
	// img 태그는 기본적으로 inline-block 요소이므로 display: block; 으로 해도됨.
    // block은 어떤 특정 구역을 차지해버림(빈부분없이 채움)
    width:100%;
`;

export const StyleMovieContentBox = styled.div`
    width: 60%; // 그림영역 40% 였었음
    padding: 12px;
`;

export const StyleMovieAnchor = styled.a`
    color:#222;
    font-size: 12px;
    text-align: center; // text 정렬
    word-break: keep-all; // 텍스트들 줄 바꿈 속성(단어)
    // break-all : 문자 단위로 줄바꿈
    // keep-all : 단어 단위로 줄바꿈

    text-decoration: none; // 밑줄 X
    &:hover { // 마우스 hover때(&는 this로 생각할것)
        text-decoration: underline; // 밑줄 O
    }
`;

export const StyleMovieTitle = styled.h3`
    color: #000;
`;

export const StyleMovieSubtitle = styled.h4`
    color : #555;
    margin-top: 8px;
`;

export const StyleMovieDirector = styled.p`
    color: #999;
    margin-top: 40px;
`;

export const StyleMovieList = styled.div`
    padding: 0 80px; // 좌우 padding
`;
```



### 문자열 내 태그 렌더링

* 사진을 보면 title 부분이 처음봤던 디자인 도안과 다름. 이부분을 수정.

![image-20220707221945764](/images/2022-07-03-외부 API 호출한 것 React.js로 디자인까지/image-20220707221945764.png)

```jsx
<StyleMovieTitle>{title}</StyleMovieTitle>
// 부분을 아래 코드로 변환
<StyleMovieTitle dangerouslySetInnerHTML={{ __html: title}}></StyleMovieTitle>
```

<br>

## 데이터 추가 요청하기

### 추가하는 버튼 컴포넌트 작성

#### components/AppendButton.jsx

* **상위 태그에서 추가하는 이벤트 리스너를 넘겨줄 예정이르모 onClick props를 선언**

```jsx
import { StyleAppendButton } from "./AppendButton.style";

const AppendButton = ({onClick}) => {
    return(
        <StyleAppendButton onClick={onClick}>추가</StyleAppendButton>
    )
}

export default AppendButton;
```



#### components/AppendButton.style.js

* styled 객체에 .HTML태그명 이 아닌 괄호로 컴포넌트를 넣어주면 **해당 컴포넌트에 스타일 지정**

* ButtonBase의 경우, 형태는 없고 클릭 시 Ripple(물결) 효과만 발생할 수 있는 요소를 만듬

* 거기에 스타일을 지정하여 커스텀 물결 이벤트를 쉽게 만들 수 있는 장점이 있음

```js
import styled from "@emotion/styled";
import {ButtonBase} from "@mui/material";

export const StyleAppendButton = styled(ButtonBase)`
    width: 100%;
    height: 40px;

    font-size: 20px;
    color: #fff;
    background-color: #06f;
`;
```



### MovieContainer.jsx에 버튼 추가

```jsx
// 생략..
<div>
    <Movies movieList={movieList}/>
    <AppendButton/>
</div>
```

![image-20220707231309233](/images/2022-07-03-외부 API 호출한 것 React.js로 디자인까지/image-20220707231309233.png)



### 요청 로직 수정 - fetch.js

* 미리 작성한 부분인데 설명하자면
* **마지막 리스트 기준으로 5개씩 추가**하여 받아오는 함수(**offset** 활용)
* **URL은 주소**, **URI는 질의어까지** 포함
* **qs 모듈** : 객체를 URI에 첨부하게끔 변경 or URI에 있는 데이터를 객체로 변환

```js
// fetch.js
import qs from "qs";

const URL = "/api/v1/search/movie.json";
export const requestGetMovieList = async (offset =1, limit = 5) => {
  const query = qs.stringify({
    query: "어벤져스",
    start: offset,
    display: limit,
  })
  const URI = `${URL}?${query}`; // 객체를 URI에 첨부하게 변경
// 생략...
```



### 요청 로직 수정 - MovieContainer.jsx

* **API를 호출**할 때 start 매개변수를 **movieList.length + 1**로 넘김
* **넘어온 data.items**는 **concat 메서드**로 **불변성**을 지키며 **movieList에 추가**
  * 참고로 **data.items** 는 [{},{},...] 로 구성. 그런데도 **다시 배열 생성한것은 불변성 때문**
  * **data.items는 5개의 값일 뿐 movieList는 이 추가되는 값들을 concat으로 계속 추가해야함**
* **useCallback** 함수의 **deps 배열**에는 **movieList**가 추가 되어야 **두 번째 요청부터 정상 작동**함
  * movieList 추가 안하면 u**seCallback은 빈배열일시 처음 만든 함수값만 사용**하기 때문에 1부터 5개만 계속 요청(요청했던 1~5개 데이터 또 요청.. 계속..반복..)
  * 물론 useCallback을 사용안했다면 매번 렌더링때 새로 함수 만들테니 문제없겠지만, 최적화를 함께 하기 위해서 이부분도 자세히 알아둘것.
* **useEffect의 deps 배열에 callApiGetMovieList 함수가 있다면**
  * mount시 callApiGetMovieList함수 실행 -> movieList 변경 -> callApiGetMovieList함수 변경 -> useEffect 실행 -> 다시 callApiGetMovieList함수 실행... **무한 루프**가 발생
  * 따라서 처음 mount시에만 실행위해 빈배열 넣었던것.

```jsx
// MovieContainer.jsx
// 생략...
const MovieContainer = () => {
    const [movieList, setMovieList] = useState([]);
    
    const callApiGetMovieList = useCallback(async () => {
        const data = await requestGetMovieList(movieList.length + 1); // 수정
        const newMovieList = movieList.concat(...data.items); // 수정
        // setMovieList(data.items) 이걸로하면 5개 값 추가가 아닌 아예 바뀜
        setMovieList(newMovieList) // 수정
    }, [movieList]) // useCallback

    useEffect(() => {
        // mount 시에만 API 요청
        callApiGetMovieList();
    }, []) // mount!!

    return (
        <div>
            <Movies movieList={movieList}/>
            <AppendButton onClick={callApiGetMovieList}/> <!--수정-->
        </div>
    );
}
```

<br>

## 데이터 체크하기

### Checkbox 컴포넌트 만들기

#### components/Checkbox.jsx

* 모든 props를 객체로 통합하여 하위로 전달

```jsx
import { StyleCheckbox } from "./Checkbox.style";

const Checkbox = (props) => {
  // { checked, onChange }로 받는 방법이 풀어서 쓰면
  // const { checked, onChange } = props; 와 동일함
    return <StyleCheckbox {...props} />;
}

export default Checkbox;
```



#### components/Checkbox.style.js

```js
import styled from "@emotion/styled";
import { Checkbox } from "@mui/material";
// Material UI에 스타일을 적용하기 위해 가져옴

export const StyleCheckbox = styled(Checkbox)`
  // styled 객체에 .HTML태그명 이 아닌 괄호로 컴포넌트를 넣어주면 해당 컴포넌트에 스타일 지정
  width: 48px;
  height: 48px;
`; // Material UI의 Checkbox에서, 크기를 48px로 변경한 스타일 컴포넌트(체크박스)

```



### Checkbox 추가하기

#### Movie.style

* **position 속성 사용**
  * 체크박스 위치를 오른쪽 위로 위치 지정 간편히 하기 위함!

```js
// 생략...
export const StyleMovieItem = styled.div`
  position: relative;
  // 이 요소를 상대적인 좌표로 지정할 수 있게 해줌
  // 여기서 top, left, right, bottom 속성을 통해 값을 지정해주면 현재 위치 기준으로 이동
  // 이 요소 하위 요소(자식, 손자 요소)에 position: absolute;를 적용하면 이 요소의 위치를 기준으로 적용
// 생략...`;

// 생략...
export const StyleMovieCheckboxArea = styled.div`
  position: absolute;
  // absolute 요소는 position: relative; 가 적용되어 있는 가장 가까운 상위 요소를 기준으로 위치를 지정함

  top: 6px;
  right: 6px;
  // 가장 가까운 상위 요소(StyleMovieItem)의 박스 내에, 위쪽에서 6px, 오른쪽에서 6px만큼 떨어진 곳에 위치시킴
`; // Movie에서 Checkbox의 위치를 지정하기 위해 감싸는 스타일 컴포넌트
```



#### Movie

```jsx
<Checkbox /> 추가하기
```



### 데이터 구조 수정

#### MovieContainer.jsx

* 기존의 data.items에는 **checked**라는 프로퍼티 없으므로 **추가**
* **props**로 전달할 **함수** 작성 후 **전달**
  * link는 Movie.jsx에서 줄거임
  * 일반적으로 아래로 전달 함수는 on이 아닌 handle 접두어 사용

```jsx
// MovieContainer.jsx
// const callApiGetMovieList 함수 내부 
        const newItems = data.items.map((item) => {
            return {...item,
            checked: false,}
        });

const handleChangeCheck = useCallback(
    (link) => {
        // link의 값을 가진 요소의 checked를 반대로 바꾸는 함수
        // link를 유일한 값이라고 생각하고 사용
        const newMovieList = movieList.map((movieItem)=>
              link === movieItem.link
              ? { ...movieItem, checked: !movieItem.checked }
              : movieItem
		)// movieItem의 link가 매개변수로 넘어온 link와 같다면 그 요소의 checked를 반대로 바꾸는 로직
        setMovieList(newMovieList)
    },
    [movieList]
);
// 생략...
<Movies movieList={movieList} onChangeCheck = {handleChangeCheck}/>
```



#### Movie.jsx

리액트에도 이벤트 함수들이 있음. Checkbox에서 onChange, checked를 사용할거임.

* **onChange 이벤트로 넣을 때 link 전달 위해 함수 미리 만들어 놓기**
* **checked 이벤트 속성도 이용할것임**

```jsx
const Movie = ({ title, subtitle, image, link, director, checked, onChange}) => {
    return (
        <StyleMovieItem>
			<!--생략...-->
            <StyleMovieCheckboxArea>
                <Checkbox checked={checked} onChange={onChange}/>
            </StyleMovieCheckboxArea>
        </StyleMovieItem>
    );
}

const MovieList = ({movieList, onChangeCheck}) => {
    return (
        <StyleMovieList>
            {movieList.map((movieItem) => {
                return(
                    <Movie 
                    key={movieItem.title} // key도 보내줘야 안전
                    {...movieItem}
                    onChange={() => onChangeCheck(movieItem.link)}>
                    </Movie>
                    // onChangeCheck 함수에는 link 매개변수를 넣어주어야 하는데,
                    // 클릭 이벤트로 넣을 때 link 매개변수를 전달하기 위해선 함수를 미리 만들어 놓아야 함
                )
            })}
        </StyleMovieList>
    )
}
```

<br>

## 데이터 전체 체크/해제

### MovieContainer에 전체 체크박스 추가

* 참고로 체크박스시 내부 체크유무 값은 잘 변경되지만, UI에 체크 유무 표시는 전부 변경시키거나 하진 않음. 그러나 내부 체크값은 잘 동작하다는점 참고

#### containers/MovieContainer.style.js

```js
import styled from "@emotion/styled/macro";

export const StyleMovieAllCheckArea = styled.div`
  display: flex;
  align-items: center; // 현재 flex의 배치 방향(가로)에 수직(세로) 배치를 중앙 정렬 => 수직 중앙 정렬
  justify-content: flex-end; // 뒤쪽에서부터 요소를 위치시킴
`; // 전체 선택이 위치한 영역의 스타일 컴포넌트

export const StyleMovieAllCheckText = styled.p`
  font-size: 14px;
  color: ${(props) => (props.checked ? "#222" : "#999")};
  // 텍스트의 props인 checked가 true이면 진한 검정, flase이면 연한 회색을 띔
`; // 전체 선택 텍스트 스타일 컴포넌트

```



#### MovieContainer.jsx

* **이벤트 함수까지 전부 작성**
  * **AllChecked, onChangeAllCheck**

```jsx
const AllChecked = useMemo(() => {
    return movieList.every((movieItem) => movieItem.checked);
}, [movieList]); // movieList의 모든 요소가 갖고 있는 checked가 true인지 판단하는 useMemo
// every 함수를 통해 모든 요소의 checked가 true이면 true를 AllChecked에 할당

const onChangeAllCheck = useCallback(() => {
    // movieList의 모든 요소의 checked를 AllChecked 상태의 반대로 바꾸는 함수
    const newMovieList = movieList.map((movieItem) => ({
        ...movieItem,
        checked: !AllChecked,
    })); // movieList의 모든 요소의 checked를 AllChecked 상태의 반대로 바꾸는 로직
    setMovieList(newMovieList);
}, [AllChecked, movieList]);

<StyleMovieAllCheckArea>
    <StyleMovieAllCheckText checked={AllChecked}>
        전체 선택
    </StyleMovieAllCheckText>
    <Checkbox checked={AllChecked} onChange={onChangeAllCheck}/>
</StyleMovieAllCheckArea>
```

<br>

## 체크한 데이터 삭제하기

* **체크박스 선택 안되어있으면 비활성화 할것임**



### 삭제하는 버튼 컴포넌트 작성

#### components/DeleteButton.style.js

```jsx
import styled from "@emotion/styled";
import { ButtonBase } from "@mui/material";
// Material UI에 스타일을 적용하기 위해 가져옴

export const StyleDeleteButton = styled(ButtonBase)`
  // styled 객체에 .HTML태그명 이 아닌 괄호로 컴포넌트를 넣어주면 해당 컴포넌트에 스타일 지정
  // ButtonBase의 경우, 형태는 없고 클릭 시 Ripple(물결) 효과만 발생할 수 있는 요소를 만듬
  // 거기에 스타일을 지정하여 커스텀 물결 이벤트를 쉽게 만들 수 있는 장점이 있음
  width: 120px;
  height: 40px;

  font-size: 20px;
  color: #fff;

  background-color: ${(props) => (props.disabled ? "#f007" : "#f00")};
  // Delete Button은 checked된 영화 목록이 없으면 비활성화 되는데, 시각적인 표시를 추가하기 위해서 사용
  // props로 넘기면 @emotion/styled에서 접근 가능함
  // disabled가 true이면 반투명한 빨간색, false이면 진한 빨간색을 띔
`; // checked된 영화 목록을 삭제하는 버튼 스타일 컴포넌트

```



#### components/DeleteButton.jsx

```jsx
import { StyleDeleteButton } from "./DeleteButton.style";

const DeleteButton = ({ onClick, disabled }) => {
  // 삭제 함수를 클릭 이벤트로 사용하기 위해 onClick props 받아옴
  // 또한, checked 상태인 영화 목록이 없으면 비활성화하기 위해 disabled props 받아옴
  return (
    <StyleDeleteButton onClick={onClick} disabled={disabled}>
      삭제
    </StyleDeleteButton>
  );
}; // checked 상태인 영화 목록을 삭제하기 위해 만든 버튼 컴포넌트

export default DeleteButton;
```



### 체크된 요소만 삭제하는 함수 작성

* **filter함수를 이용해 check된 것들은 제외하고 새로 movieList를 만드는것**

```jsx
// MovieContainer.jsx
const onDeleteChecked = useCallback(() => {
    const newMovieList = movieList.filter((movieItem) => !movieItem.checked);
    setMovieList(newMovieList)
},[movieList]);
// 생략...
<DeleteButton onClick={onDeleteChecked} />
```



### 하나도 선택되어있지 않을 때 버튼 비활성화

* **every함수를 통해 전부 체크되어 있지 않다면 allNoneChecked 변수에 true 반환**
  * disabled 속성값에 true를 줘서 비활성화

```jsx
const allNoneChecked = useMemo(() => {
    return movieList.every((movieItem) => !movieItem.checked);
}, [movieList]); 
// 생략...
<DeleteButton disabled={allNoneChecked} onClick={onDeleteChecked} />
```



## 최종 결과 확인

![image-20220708042628303](/images/2022-07-03-외부 API 호출한 것 React.js로 디자인까지/image-20220708042628303.png)

<br>

## `useState`의 동작 순서

**MovieSetCallback.jsx 으로 최종 수정(최적화)**

- `useState`로 반환받은 `setter` 함수는 **완전히 동기적으로(순차적으로) 동작하지 않습니다.**
- `setState` 함수에 값을 매개변수로 넣어서 호출하면, 이는 `React`에 해당 값으로 변경하라는 동작을 요청하는 행위와 같습니다.
  - 따라서 `setState`가 연속해서 호출되면, `React`에서 첫 번째 동작 요청 행위와, 두 번째 동작 요청 행위가 모두 실행되지만 `setState`에 넘어간 값이 한 상태를 기준으로 동작하기 때문에, 두 번쨰 동작 요청 행위만 실행된 것으로 판단합니다.

- 이에 `setState` 함수에 콜백 함수를 매개변수로 넣어서 호출하는 방법이 있습니다.
  - 콜백 함수의 인수로는 이전 상태가 넘어오며, 이전 상태를 통해 다음 상태를 연산하는 로직을 여러 번 호출하여도 정상적으로 동작합니다.
  - 이를 `Functional Update`라고 합니다.

> 이는 상태를 유지해야 할 권리를 사용자가 아닌 `React`로 전환할 수 있게 만드는 기법입니다.

> 또한 `setState`에 값을 매개변수로 넣으면, `React`에서 기존 값과 비교하는 연산이 발생할 수 있기 때문에 성능적인 측면에서 콜백 함수를 매개변수로 넣는 것이 유리합니다.

### 참고

* \- [`React` `Hook` `setState`에 함수 전달](https://developer-talk.tistory.com/m/252)

- [`/src/infos/MovieSetCallback.jsx`](./src/infos/MovieSetCallback.jsx)
- `useState`가 완전히 `동기적`으로 작동하는것이 아니니까 혹시 모를 에러를 대비 및 성능 향상을 위해 `setState` 에 **값이 아닌 콜백함수를 매개변수**로 넣어서 호출한다.
- 또한 **값이 아닌 콜백함수를 매개변수**로 넣었을때의 특징은 매개변수가 **자동으로 이전의 값**을 의미하게 된다. 그래서 상태 유지를 사용자가 아닌 **React**로 완전 전환할 수 있는것이다.

```jsx
import { useState, useCallback, useEffect, useMemo } from "react";

import Loading from "../components/Loading";
import { Movies } from "../components/Movie";
import AppendButton from "../components/AppendButton";
import DeleteButton from "../components/DeleteButton";
import Checkbox from "../components/Checkbox";
// 컴포넌트 가져옴

import { requestGetMovieList } from "../apis/fetch";
// api 요청 함수 가져옴

import {
  StyleMovieAllCheckArea,
  StyleMovieAllCheckText,
} from "../containers/MovieContainer.style";

/**
 * 영화의 목록을 관리하는 컨테이너 컴포넌트
 * setState에서 값이 아닌 콜백 함수를 매개변수로 넘기는 컴포넌트
 */
const MovieContainer = () => {
  const [loading, setLoading] = useState(false);
  // 로딩 상태 state로 정의
  const [movieList, setMovieList] = useState([]);
  // 요청한 영화 리스트 state로 정의

  const AllChecked = useMemo(() => {
    return movieList.every((movieItem) => movieItem.checked);
  }, [movieList]);

  const allNoneChecked = useMemo(() => {
    return movieList.every((movieItem) => !movieItem.checked);
  }, [movieList]);

  const callApiGetMovieList = useCallback(async () => {
    // 영화 목록을 가져오는 API를 요청하는 함수
    setLoading(true);
    try {
      const data = await requestGetMovieList(movieList.length + 1);
      // 이 함수는 콜백 함수를 전달하는 setMovieList를 사용하여도
      // 여기에서 movieList를 사용하기 때문에 deps 배열에 movieList를 넣어주어야 함
      const newItems = data.items.map((item) => ({
        ...item,
        checked: false,
      }));

      setMovieList((prevMovieList) => {
        const newMovieList = prevMovieList.concat(...newItems);
        return newMovieList;
        // 기존 movieList.concat 코드는 이 컴포넌트의 상태에 의존하는 것
        // prevMovieList를 사용하면 React의 상태에 의존하게 됨
        // 따라서 movieList를 사용할 필요가 없어짐
      });
    } catch (e) {
      console.error(e);
    }
    setLoading(false);
  }, [movieList]);

  const handleChangeCheck = useCallback((link) => {
    setMovieList((prevMovieList) => {
      const newMovieList = prevMovieList.map((movieItem) =>
        link === movieItem.link
          ? { ...movieItem, checked: !movieItem.checked }
          : movieItem
      ); // prevMovieList를 참조하게 되어 더 이상 이 컴포넌트의 movieList에 의존하지 않기 때문에
      // movieList의 값을 감지해야할 필요 또한 사라짐
      return newMovieList;
    });
  }, []); // 이 함수는 mount 이후 단 한 번만 생성됨

  const onChangeAllCheck = useCallback(() => {
    // movieList의 모든 요소의 checked를 AllChecked 상태의 반대로 바꾸는 함수

    setMovieList((prevMovieList) => {
      const newMovieList = prevMovieList.map((movieItem) => ({
        ...movieItem,
        checked: !AllChecked,
      })); // prevMovieList를 참조하게 되어 더 이상 이 컴포넌트의 movieList에 의존하지 않기 때문에
      // movieList의 값을 감지해야할 필요 또한 사라짐
      // 하지만 여전히 AllChecked의 값은 감지해야 함
      return newMovieList;
    });
  }, [AllChecked]); // 이 함수는 movieList의 변경에는 생성되지 않으며, mount 시와 AllChecked가 변경되었을 때만 생성됨

  const onDeleteChecked = useCallback(() => {
    setMovieList((prevMovieList) => {
      const newMovieList = prevMovieList.filter(
        (movieItem) => !movieItem.checked
      );
      return newMovieList;
    });
  }, []); // 이 함수는 mount 이후 단 한 번만 생성됨

  useEffect(() => {
    // mount 시에만 API 요청
    callApiGetMovieList();
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);

  return (
    <div>
      <StyleMovieAllCheckArea>
        <StyleMovieAllCheckText checked={AllChecked}>
          전체 선택
        </StyleMovieAllCheckText>
        <Checkbox checked={AllChecked} onChange={onChangeAllCheck} />
        <DeleteButton disabled={allNoneChecked} onClick={onDeleteChecked} />
      </StyleMovieAllCheckArea>
      <Movies movieList={movieList} onChangeCheck={handleChangeCheck} />
      <Loading isLoading={loading} />
      <AppendButton onClick={callApiGetMovieList} />
    </div>
  );
};

export default MovieContainer;
```

<br>

## `useCallback`, `useEffect`, `useMemo`의 효율성

- 불필요한 데이터, 함수의 생성과, 값의 변화를 감지하는 점에서 굉장히 용이하고 효율적으로 사용할 수 있는 장점이 있습니다.
- 하지만 이를 과다하게 사용할 경우 `React`에서 값을 비교, 체크해야할 요소가 늘어나 오히려 그 요소를 비교 연산하는데 시간과 비용을 더 많이 사용하게 되는 경우가 발생할 수 있습니다.

> 항상 적절히 사용해야 하며, 특히 값의 변화를 감지하고 새로운 로직을 작성하는 `useEffect`의 사용을 최소화 해야 합니다.

### 참고

- [`React`에서 `Hook`의 올바른 사용](https://sangcho.tistory.com/entry/React-useEffect-4Tip)
- [`Hook`이 실패한 설계인 이유?](<https://jong-hui.github.io/devlog/2021/01/08/(React)%ED%9B%85%EC%9D%B4-%EC%8B%A4%ED%8C%A8%ED%95%9C-%EC%84%A4%EA%B3%84%EC%9D%B8-%EC%9D%B4%EC%9C%A0-4%EA%B0%80%EC%A7%80/>)

<br>

## (추가) 로딩 부분

* 백드롭(backdrop)이란 새로운 화면이 뜨는 방식의 UI 형태라고 생각하면 됩니다.
* 중간에 코드가 에러 발생하면 무한로딩이 되버리기 때문에 이를 방지하기 위해 try,catch구문 사용
  * 만약, 로딩화면이 너무빨리지나가서 확인해보고 싶다면 예외처리 제거하고 일부러 에러코드 적용해서 로딩화면을 확인할 것.

```jsx
// Loading.jsx
import { Backdrop, CircularProgress } from "@mui/material";
// 로딩 컴포넌트에서 사용할 Material UI 컴포넌트 가져옴

const Loading = ({ isLoading }) => {
  // isLoaing props의 값이 true면 로딩 상태 렌더
  // false면 로딩 상태 렌더하지 않음
  return (
    <Backdrop open={isLoading}>
      <CircularProgress />
    </Backdrop>
  );
};

export default Loading;
```

```jsx
// MovieContainer.jsx
const callApiGetMovieList = useCallback(async () => {
    // 영화 목록을 가져오는 API를 요청하는 함수
    setLoading(true); // loading 시작
    try { // 무한로딩 방지(try,catch)
      const data = await requestGetMovieList(movieList.length + 1);
      const newItems = data.items.map((item) => ({
        ...item,
        checked: false,
      }));

      setMovieList((prevMovieList) => {
        const newMovieList = prevMovieList.concat(...newItems);
        return newMovieList;
      });
    } catch (e) {
      console.error(e);
    }
    setLoading(false); // loading 끝
  }, [movieList]);

// 생략...
<Loading isLoading={loading} />
```

<br>

## (추가) index, App 스타일링

* 기존은 index.css, App.css로 구성이 되어있는데 이것을
* index.style.js, App.style.js로 새로 만들어 스타일링 해보겠다.
* 추가로 @emotion라이브러리의 Global 컴포넌트를 활용해 전역 CSS 작성할 코드 등록하겠다

### 기존 index.js

```js
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  //<React.StrictMode>
    <App />
  //</React.StrictMode>
);

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
reportWebVitals();
```



### 교체 index.js

* **index.css**를 사용안하니까 import에서 **제외**
* 참고로 div로 감싼 이유는 React.StrictMode는 useEffect가 자꾸 2번 실행되어서 처리하였다.

```js
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import reportWebVitals from './reportWebVitals';

// 추가
import { Global } from "@emotion/react";
// 전역 CSS를 등록할 컴포넌트 가져옴
import { GlobalStyles } from "./index.style";
// Global 컴포넌트에 등록할 전역 CSS 작성한 코드 가져옴

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <div>
    <App />
    <Global styles={GlobalStyles}/>
  </div>
);

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
reportWebVitals();
```



### 추가 index.style.js

```js
import { css } from "@emotion/react";
// CSS 코드를 작성하는 모듈 가져옴

export const GlobalStyles = css`
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  // 모든 요소에 CSS 적용함
`;
```



### 기존 App.js

```js
import logo from './logo.svg';
import './App.css';
import MovieContainer from './infos/MovieSetCallback';

function App() {
  return (
    <div className="App">
      <MovieContainer></MovieContainer>
    </div>
  );
}

export default App;
```



### 교체 App.js

* **App.style.js**를 새로 **만들어**서 구성하였고, 기존 **App.css**는 import에서 **제외**

```js
import { StyleApp, StyleAppContent } from "./App.style";
import MovieContainer from './infos/MovieSetCallback';

const App = () => {
  return (
    <StyleApp>
      <StyleAppContent>
        <MovieContainer />
      </StyleAppContent>
    </StyleApp>
  );
}

export default App;
```



### 추가 App.style.j.s

```js
import styled from "@emotion/styled/macro";

export const StyleApp = styled.main`
  width: 100%;
  min-height: 100vh; // 화면의 높이를 가져옴
  // 1vh는 화면의 높이 / 100
  background-color: #ebebeb;
`;

export const StyleAppContent = styled.article`
  width: 600px;
  height: 100%;
  margin: 0 auto;
  // 이 요소를 지정하면 상위 요소의 크기 기준으로 세로 축 중앙 정렬
  background-color: #fff;
`;
```













