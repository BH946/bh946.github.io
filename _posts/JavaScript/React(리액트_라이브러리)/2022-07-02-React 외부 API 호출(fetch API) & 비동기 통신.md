---
title: "React로 외부API 호출(fetch API) & 비동기 통신"
categories : React_JS
tag : [Javascript, 리액트, AJAX, XMLHttpRequest, fetch API, API 키, env, 무한로딩, cros]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..

---



## JavaScript 비동기 통신

**JavaScript에서는 XMLHttpRequest, fetch를 지원**

### AJAX

**Asynchronous JavaScript And XML**

* JS와 XML을 이용한 **비동기적 정보 교환 기법**
* XML, Text, JSON 등 데이터 사용 가능
* AJAX를 사용하지 않으면 브라우저가 처음부터 전부 렌더링



### XMLHttpRequest(XHR)

**과거 JavaScript에서 AJAX를 지원하기 위한 기술**

* [MDN 문서](https://developer.mozilla.org/ko/docs/Web/API/XMLHttpRequest)

* 과거의 방식이라 코드가 난잡하여 가독성이 나쁨
* **에러 발생을 핸들링해주지 않아**서 직접 작성
* Promise가 아닌 비동기 콜백 방식이라 최신 JS와의 궁합이 좋지 않음
* **XMLHttpRequest 객체를 생성하여 객체의 정보를 주입한 후 통신 요청**



#### 코드

* [API 요청 사이트](https://reqres.in/)

```jsx
import { useCallback, useEffect } from "react";

const URL = "https://reqres.in/api/users";
// API를 요청할 주소

// API 요청 테스트하는 컴포넌트
const RequestTest = () => {
  // XMLHttpRequest 방식으로 요청하는 함수
  const callApiGetXhr = useCallback(() => {
    const xhr = new XMLHttpRequest();
    // XMLHttpRequest 객체 생성
    xhr.open("GET", URL, true);
    // GET 방식으로 URL에 데이터 요청하도록 설정
    // 마지막 인수가 true이면 비동기식으로 요청함

    xhr.onreadystatechange = () => {
      // 요청이 변경됐을 때 실행할 콜백 함수
      if (xhr.readyState === XMLHttpRequest.DONE) {
        // 요청 상태가 완료됐을 때 실행할 코드
        const { status } = xhr;
        // HTTP status 코드를 가져옴
        // 이는 객체의 프로퍼티를 직접 가져오는 디스트럭처링 할당
        console.log(xhr);
        if (status === 0 || (status >= 200 && status < 400)) {
          // 오류가 나지 않았을 때 데이터 출력
          console.log(JSON.parse(xhr.responseText));
          // xhr.responseText는 string 값을 반환함
          // JSON.parse 함수는 string 타입 데이터를 JSON 타입으로 바꾸어 주는 함수
          // JSON 문법에 어긋난 string은 파싱 에러 발생
        }
      }
    };
    xhr.send();
    // 위에 설정한 XMLHttpRequest 객체로 실제 요청을 보내는 실행을 함
    // 이 코드가 없으면 요청하지 않음
  }, []);

  useEffect(() => {
    // mount 시에만 API 요청
    callApiGetXhr();
  }, [callApiGetXhr]);
  return <></>;
};
export default RequestTest;
```

#### 결과

![image-20220702024743486](/images/2022-07-02-React로 외부 API 호출(fetch API) & 비동기 통신/image-20220702024743486.png)



### fetch

**브라우저에서 정식 지원하는 HTTP 요청 인터페이스**

* [MDN 문서](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API)
* XMLHttpRequest보다 강력하고 유연한 조작 가능
* XML, Text, JSON 모두 지원
* Promise에서 에러가 구현되어 try catch로 **에러 핸들링 가능**
* **Promise**로 구현되었기 때문에 최신 JS와 궁합이 좋음
* XMLHttpRequest처럼 객체를 생성해야하는게 아님.  
  객체 생성할 필요 없이 바로 요청 가능
* **Promise 덕분에 async/await을 활용 가능하단 장점**

#### 코드

* [API 요청 사이트](https://reqres.in/)
* **Promise**는 .**then(콜백), .catch(콜백)**과 같은 비동기 기능을 제공해주는 객체이다.
* fetch API는 콜백 지옥이 나타나기 쉬워서 **async/await** 까지 주로 사용한다.

```jsx
import { useCallback, useEffect } from "react";
const URL = "https://reqres.in/api/users";
// API를 요청할 주소

// API 요청 테스트하는 컴포넌트
const RequestTest = () => {
  // fetch API 방식으로 요청하는 함수
  const callApiFetch = useCallback(() => {
    // GET 방식으로 URL에 데이터 요청
    fetch(URL)
      .then((response) => {
        // 요청이 완료됐을 때 콜백 함수의 인수 response로 응답 결과 전달함
        response.json().then((data) => console.log(data));
        // 결과 값을 JSON으로 파싱함
        // 파싱된 결과를 콜백 함수의 인수 data로 전달함
      })
      .catch((error) => {
        // 오류가 발생했을 때 오류 객체가 error로 넘어옴
        console.error(error);
      });

    // 이처럼 .then(콜백), .catch(콜백)과 같은 비동기 기능을 제공해주는 객체를 Promise라고 함
    // Promise는 완료 상태에 따라 then의 콜백을 실행할 수도 있고 catch를 실행할 수도 있음
    // fetch API의 경우 콜백 지옥이 나타나기 쉬움(async await 사용)
  }, []);
    
  useEffect(() => {
    // mount 시에만 API 요청
    callApiFetch();
  }, [callApiFetch]);

  return <></>;
};
export default RequestTest;
```

<br>

## `Promise`와 `async` / `await ` 체크하고 넘어가기

- `Promise`는 최신 문법으로 비동기를 사용하기 위해 정한 비동기 작성법
- `async` / `await`는 `Promise` 사용 시 발생하는 콜백 지옥(중첩) 코드를 해결하기 위해 동기 코드인 것처럼 보이게 하는 키워드
- `async` 함수는 `Promise`를 반환하는 함수로 바뀌게 됨
- **async**는 **비동기** 객체인 Promise를 **동기처럼** 사용하는 키워드
- 비동기 객체인 Promise 앞에 **await를 붙이면 동기처럼** 동작

### 참고

- [`MDN` - `Promise`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [`MDN` - `async`와 `await`](https://developer.mozilla.org/ko/docs/Learn/JavaScript/Asynchronous/Async_await)
- [`Promise`와 `async`, `await` 차이](https://velog.io/@pilyeooong/Promise%EC%99%80-asyncawait-%EC%B0%A8%EC%9D%B4%EC%A0%90)
- [`async`와 `await`](https://ko.javascript.info/async-await)

<br>

## 외부 API 요청

* [네이버 개발자 센터](https://developers.naver.com/main/)

* 영화 오픈 API를 해당 사이트에서 검색해서 접근  
  => [네이버 영화 검색 API 문서](https://developers.naver.com/docs/search/movie/)



### 네이버 애플리케이션 등록 방법

* 애플리케이션 이름 입력
* 사용 API 검색 등록
* 환경 추가 -> Web
* Web 환경의 웹 서비스 URL에 React 개발 서버 주소 (localhost:3000) 입력
* 등록



### API 키 발급

**Client ID와 Client Secret라는 키를 활용해 API요청.** 이런 API 키는 외부에 노출되면 안됌.



### API 요청 로직 작성

**API 요청과 같은 로직은 웹 서비스와 관심사가 분리되어 있으므로 폴더를 따로 만들어 모아두는 것이 좋음**

* **/src/apis/fetch.js** 파일 생성 후 코드 작성
* URL에 GET형식으로 어벤져스 정보 요청하게 작성
* fetch로 따로 객체 생성 없이 요청 하자마자, await으로 response 인자 반환한 형태
* 요청 때 headers안에 API키도 같이 보내서 인증절차 통과
* **await**이후 .then코드 내부라 생각하면됨. 따라서 중첩해서 사용한 .then은 콜백지옥인데 await으로 표현시 겉으로 보이는 콜백지옥이 사라진것처럼 보이는것
  * **추가**로 **await**은 promise에 사용
* **async**는 promise를 반환
* **fetch**는 promise를 반환

```js
const URL = "/api/v1/search/movie.json?query=어벤져스";
// 요청할 URL과 query
// query는 질의어로, 어떠한 정보를 검색하여 반환 받을 것인지 전달하는 것

export const requestGetMovieList = async () => {
  const response = await fetch(URL, {
    headers: {
      "Content-Type": "plain/text",
      "X-Naver-Client-Id": process.env.REACT_APP_NAVER_CLIENT_ID,
      "X-Naver-Client-Secret": process.env.REACT_APP_NAVER_CLIENT_SECRET,
    },
  });
  // fetch의 두 번째 인자는 옵션을 전달하는 객체로, HTTP 요청에 필요한 추가 정보를 전달
  // fetch는 Promise를 반환하므로 await를 사용할 수 있음
  // awiat를 사용하면 기존 .then(response 콜백)에서의 response 인자가 반환

  return await response.json();
  // 응답 객체를 JSON으로 변환하는 작업 비동기로, 또한 Promise를 반환하므로 await 사용 가능
};


// Async 함수와 Promise의 관계
export const requestAsync = async () => {
  // async 함수는 자동으로 Promise를 반환하는 함수가 됨
  const response = await fetch(URL, {
    headers: {
      "Content-Type": "plain/text",
      "X-Naver-Client-Id": process.env.REACT_APP_NAVER_CLIENT_ID,
      "X-Naver-Client-Secret": process.env.REACT_APP_NAVER_CLIENT_SECRET,
    },
  });
  // await 키워드는 async 내에서만 사용할 수 있고, Promise에다가 사용하면 그 Promise가 끝날 때까지 기다림
  // await 키워드를 사용하면 그 뒤에 코드들은 .then 메서드에 코드가 들어가는 것과 같음
  console.log(response);
  const a = 10;
  // 여기까지 첫 번째 .then의 코드
  const data = await response.json();
  // 다시 await를 만났으므로 이 뒤에 코드들은 .then 메서드에 코드가 들어가는 것과 같아짐(중첩)
  console.log(data);
  const b = a * 2;
  console.log(b);
  // 여기까지 두 번째 .then의 코드
  const secondResponse = await fetch("https://reqres.in/api/users");
  // 세 번째 중첩
  return await secondResponse.json();
  // 네 번째 중첩 후 resolve 함수에 결과 값 넣어서 실행한 것과 같음
};

// 위 Async 함수와 같은 코드
export const requestPromise = new Promise((resolve, reject) => {
  fetch(URL, {
    headers: {
      "Content-Type": "plain/text",
      "X-Naver-Client-Id": process.env.REACT_APP_NAVER_CLIENT_ID,
      "X-Naver-Client-Secret": process.env.REACT_APP_NAVER_CLIENT_SECRET,
    },
  }).then((response) => {
    console.log(response);
    const a = 10;
    response.json().then((data) => {
      console.log(data);
      const b = a * 2;
      console.log(b);

      fetch("https://reqres.in/api/users").then((secondResponse) => {
        secondResponse.json().then((secondData) => {
          resolve(secondData);
        });
        // 마지막 소스는
        // secondResponse.json().then(resolve);
        // 와 같이 작성할 수 있음
      });
    });
  });
});

// 두 함수 모두 Promise를 반환하므로
requestAsync.then(secondData => console.log(secondData));
requestPromise.then(secondData => console.log(secondData));
// 이렇게 then을 사용하여 반환 받을 수 있음
// then 안에 들어가는 매개변수는 resolve를 내에 넣은 값이 전달
```



### MovieContainer.jsx 작성 후 요청

**이 또한 구분하기 좋게 containers로 따로 폴더 만들어서 모으는 것을 추천**

* **전체 데이터**를 가지고 있는 컴포넌트를 일반적으로 **컨테이너**라 부름

* **/src/containers/MovieContainer.jsx** 파일 생성

```jsx
import { useCallback, useEffect } from "react";
import { requestGetMovieList } from "../apis/fetch";
// api 요청 함수를 가져옴

const MovieContainer = () => {
  const callApiGetMovieList = useCallback(async () => {
    // async는 비동기 객체인 Promise를 동기처럼 사용하는 키워드
    // 비동기 객체인 Promise 앞에 await를 붙이면 동기처럼 동작
    const data = await requestGetMovieList();
    // 원래라면 비동기로 실행되어야 하는데, await 키워드로
    // 해당 함수가 동기처럼 실행되어 아래 코드가 API 요청 후 순차 실행
    // requestGetMovieList 함수가 Promise를 반환하기 때문에 await 사용 가능
    console.log(data);
  }, []);

  useEffect(() => {
    // mount 시에만 API 요청
    callApiGetMovieList();
  }, [callApiGetMovieList]);

  return (
    <div>
    </div>
  );
};
export default MovieContainer;
```

![image-20220702032401117](/images/2022-07-02-React로 외부 API 호출(fetch API) & 비동기 통신/image-20220702032401117.png)

<br>

## `API` 키를 저장하는 방법

**해당 키를 외부에 노출 시키지 않게 관리할 팁**  
**해당 키를 깃 저장소에 올리지 않는 것!**

- `/.env` 파일을 프로젝트 루트 폴더에 생성

  - 프로젝트의 환경 변수 등 민감한 정보를 저장하는 파일
  - `REACT_APP_`으로 시작하는 변수를 사용할 수 있음
  - [/.gitignore](../.gitignore)과 같이 **저장소에 업로드 되지 않는 방법을 사용**하여 관리
  - `React`에서는 `process.env.REACT_APP_이름`으로 접근할 수 있음
  - 해당 파일에 다음 내용 작성

  ```
  REACT_APP_NAVER_CLIENT_ID="<Client ID>"
  REACT_APP_NAVER_CLIENT_SECRET="<Client Secret>"
  ```

  - `<Client ID>`와 `<Client Secret>`에 네이버 개발자 센터에서 발급받은 키 입력

<img src="/images/2022-07-02-React로 외부 API 호출(fetch API) & 비동기 통신/image-20220702031031220.png" alt="image-20220702031031220" style="zoom:50%;" /> ![image-20220702031056042](/images/2022-07-02-React로 외부 API 호출(fetch API) & 비동기 통신/image-20220702031056042.png)

<br>

## `CORS` 문제를 임시로 해결하기 위한 방안

* [MDN 문서](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)
* 개발 서버와 같이 클라이언트에서 요청을 하는 경우, 서버에서 이런 비정상 요청을 막는 경우가 있음

- `http-proxy-middleware` 모듈을 사용하여 해결할 수 있음

```shell
npm install http-proxy-middleware --save-dev
```

또는

```shell
yarn add -D http-proxy-middleware
```

- 모듈 설치 후 [`/src/setupProxy.js`](./src/setupProxy.js) 파일 생성 후 코드 작성, 개발 서버 재실행

```js
// setupProxy.js
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

## API 요청 시 로딩

### 로딩 컴포넌트 작성

* **/src/components/Loading.jsx** 파일 생성
* 로딩 컴포넌트에서 사용할 Material UI 컴포넌트 가져와서 로딩 UI사용

```jsx
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



### useState를 통한 로딩 관리

* **/src/containers/MovieContainer.jsx 코드 수정**
* 로딩창 비동기로 할텐데, **try catch 잘 활용**해야 **무한로딩 안걸림.**
  * try...catch로 에러 처리 하면 현재 함수는 반드시 끝까지 실행은 될거니까 에러 발생해도 두번째 **setLoading(false)**까지 반드시 실행하므로, 이것을 실행하지 않아서 무한 로딩 상태가 유지되는 버그 발생하지 않음

```jsx
import { useState, useCallback, useEffect } from "react";
// ... 중략
const MovieContainer = () => {
  const [loading, setLoading] = useState(false);
  // 로딩 상태 state로 정의(처음 false로 정의)
  const [movieList, setMovieList] = useState([]);
  // 요청한 영화 리스트 state로 정의

  const callApiGetMovieList = useCallback(async () => {
    // 함수 실행하자마자 loading 상태를 true로 변경
    setLoading(true);
    try {
      const data = await requestGetMovieList();
      setMovieList(data.items);
      // 받아온 items를 영화 리스트 state로 변경
    } catch (e) {
      // 에러 발생 시 에러 로그 출력
      console.error(e);
    }
    // 위의는 동기로 동작하는 것처럼 보이기 때문에 요청이 모두 끝나야만 loading 상태가 false가 됨
    // 따라서 요청을 보낸 후 응답 받을 때까지 loading이 true이므로 로딩 상태가 렌더링
    // try ... catch (e) ... 문으로 에러 처리를 했기 때문에
    // 반드시 loading 상태가 true가 됐다가 false로 돌아옴
    setLoading(false);
  }, []);
```

<br>

## 외부 데이터 렌더링(state로 관리)

### Movie.jsx

* **/src/components/Movie.jsx** 파일 생성
* **memo** 함수 활용해서 최적화
* **movieList**는 **MovieContainer.jsx에서 인수**로 줄거임

```jsx
import { memo } from "react";
// 불필요한 컴포넌트 재렌더 방지하는 memo 함수 가져옴

const Movie = ({ title, subtitle, image, link, director }) => {
  // 영화의 정보를 출력하는 컴포넌트
  return (
    <div>
      <a href={link}>
        <img src={image} alt="movie-thumbnail" />
        <h3>{title}</h3>
        <h4>{subtitle}</h4>
        <p>감독: {director}</p>
      </a>
    </div>
  );
};

const MovieList = ({ movieList }) => {
  // 영화 데이터 리스트를 반복 렌더링 하는 컴포넌트
  return (
    <div>
      {movieList.map((movieItem) => {
        return <Movie key={movieItem.title} {...movieItem} />;
        // 이렇게 props로 {...movieItem}을 넣어주면, movieItem 객체가 키의 이름 그대로 props를 전달.
      })}
    </div>
  );
};

export const Movies = memo(MovieList);
// memo 함수를 통해 불필요한 컴포넌트 재렌더 방지
// movieList 이외의 다른 값이 변경되어도 렌더링에 영향을 주지 않음

export default Movie;
```



### MovieContainer.jsx

* **/src/containers/MovieContainer.jsx 파일 수정**

```jsx
// 추가로 수정된 부분
  return (
    <div>
      <Movies movieList={movieList} />
      <Loading isLoading={loading} />
    </div>
  );
```

* **실행화면(Loading은 제외)**

![image-20220702035715545](/images/2022-07-02-React로 외부 API 호출(fetch API) & 비동기 통신/image-20220702035715545.png)

* **총 정리된 코드(MovieContainer.jsx)**

```jsx
import { useState, useCallback, useEffect } from "react";

import { Movies } from "../components/Movie";
import Loading from "../components/Loading";

import { requestGetMovieList } from "../apis/fetch";

const MovieContainer = () => {
  const [loading, setLoading] = useState(false);
  const [movieList, setMovieList] = useState([]);

  const callApiGetMovieList = useCallback(async () => {
    setLoading(true);
    try {
      const data = await requestGetMovieList(); // api 요청한 함수
      setMovieList(data.items); // movieList가 여기서 데이터 얻은것
    } catch (e) {
      console.error(e);
    }
    setLoading(false);
  }, []);

  useEffect(() => {
    callApiGetMovieList();
  }, [callApiGetMovieList]);

  return (
    <div>
      <Movies movieList={movieList} />
      <Loading isLoading={loading} />
    </div>
  );
};
export default MovieContainer;
```

