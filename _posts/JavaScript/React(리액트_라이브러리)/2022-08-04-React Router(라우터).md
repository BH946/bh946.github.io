---
title: "React Router(라우터)"
categories : React_JS
tag : [Javascript, 리액트]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..

---



## 라우터

node에서는 `express`라이브러리에서 라우터함수 사용했다.

리액트에서는 `react-router-dom` 이라고 라이브러리가 있으며, 이를 활용하겠다.

* `yarn add react-router-dom`

* [공식문서](https://reactrouter.com/docs/en/v6/getting-started/overview) - 참고해서 현재 문법으로 꼭 사용할것

<br>

## 사용법

참고 : 라우터에서 "/" 경로부분때매 다른것보다 먼저 렌더링 되는 문제가 있는데 `exact 속성` 넣어주면 해결

* 문법 바뀌면 또 안될수도 있기때문에 공식문서도 꼭 참고할것



### 기본

```jsx
const App = () => {

  // const user = false // test용
  const user = useSelector(state => state.user.currentUser)
  return (
    <BrowserRouter>
      <Routes>
        <Route exact path="/" element={<Home />}/>
        <Route path="/products/:category" element={<ProductList />}/>
        <Route path="/product/:id" element={<Product />}/>
        <Route path="/cart" element={<Cart />}/>
        {/* 로그인 성공여부에 따른 페이지 전환 */}
        <Route path="/login" element={(
          user ? (<Navigate replace to="/"/>) : (<Login/>)
        )}/>
        <Route path="/register" element={(
          user ? (<Navigate replace to="/"/>) : (<Register/>)
        )}/>
      </Routes>
    </BrowserRouter>
  );
```





### useLocation

url의 경로를 가져올 수 있어서 유용한 리액트 훅이다

이런식으로 활용해서 URI에 파라미터로 넘긴 id값도 간편히 가져올 수 있다.

```jsx
const location = useLocation();
const id = location.pathname.split("/")[2];
```











