---
title: "React Redux(리덕스)"
categories : React_JS
tag : [Javascript, 리액트]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..

---



## 리덕스(redux)란

매번 렌더링하면서 데이터를 새로 가져와야하는 그런 경우를 대비한 아주 유용한 툴이다.

한번 가져오면 공용으로 사용할 수 있고, 다시 데이터 가져올 필요도 없다.

[redux-toolkit](https://ko.redux.js.org/introduction/getting-started/#redux-toolkit ) - 공식 문서

* `yarn add @reduxjs/toolkit` 
  * userRedux, store 즉, 리덕스 준비단계에 필요한 도구
* `yarn add react-redux`
  * 리덕스를 직접 사용할때 필요한 도구
* 즉, `userSlice->store->Provider` 로 감싸는 흐름
* 크롬 확장프로그램의 `Redux DevTools`를 추천한다. Redux 실행해서 테스트할때 유용!

<br>

## 구조

redux폴더 생성해서 해당 폴더에 모아둘것

**userRedux.js, store.js, apiCalls.js** 보통 이런식의 구조이다.

* userRedux.js의 경우는 productRedux.js 라던지 cartRedux.js 라던지 등등 추가되지만
* store.js의 경우 하나라고 보면 된다.
* store.js까지 생성하고 나면 사용만 하면 된다.
* apiCalls.js 없이도 userRedux.js에서 한번에 가능한데 createAsyncThunk 함수를 사용 해야한다.
  * 이것은 별루 추천하지는 않는다. (그래도 아래에 정리해두겠음)





### userRedux.js

* redux-toolkit의 createSlice 함수를 사용

* 주 속성들 : name, initialState, reducers
* 다른곳의 데이터가 reducers에 payload 형태로 보낼수 있기 때문에  
  reducers에서 action.payload를 통해서 데이터 가져와 저장
* 아래 export도 중요

```js
import {createSlice} from "@reduxjs/toolkit"

const userSlice = createSlice({
    name: "user",
    initialState: {
        currentUser : null,
        isFetching : false,
        error : false
    },
    reducers: {
        loginStart:(state) => {
            state.isFetching = true;
        },
        loginSuccess:(state,action) => {
            state.isFetching = false;
            state.error = false;
            state.currentUser = action.payload;
        },
        loginFailure:(state) => {
            state.isFetching = false;
            state.error = true;
        },
        logout:(state) => {
            state.currentUser = null;
            state.isFetching = false;
            state.error = false;
        }
    }
})

export const {loginStart, loginSuccess, loginFailure, logout} = userSlice.actions;
export default userSlice.reducer;
```





### store.js

* redux-toolkit의 configureStore 함수를 사용
* 하지만 이렇게 해도 새로고침하면 데이터가 다 날라간다.
  * persist 적용시 데이터를 날라가지 않게 할 수 있는데 아래에서 다시 정리하겠다.

```js
import { configureStore } from "@reduxjs/toolkit";
import userReducer from "./userRedux";

export default configureStore({ // reducer로 구성
  reducer: {
    user: userReducer, // state.user
  },
});
```





### store.js - persist 활용방법

* `yarn add redux-persist`

* 마찬가지로 redux-toolkit의 configureStore 함수를 사용하지만 persistStore() 로 한번 감싼다.
* 추가로 combineReducers 함수는 여러가지 Redux들 합쳐 사용할 수 있으므로 유용한 함수다.

```js
import { combineReducers, configureStore } from "@reduxjs/toolkit";
import cartReducer from "./cartRedux";
import userReducer from "./userRedux";
import {
    persistStore,
    persistReducer,
    FLUSH,
    REHYDRATE,
    PAUSE,
    PERSIST,
    PURGE,
    REGISTER,
  } from 'redux-persist'
import storage from 'redux-persist/lib/storage'

const persistConfig = {
key: 'root',
version: 1,
storage,
}

const rootReducer = combineReducers({user: userReducer, cart: cartReducer})

const persistedReducer = persistReducer(persistConfig, rootReducer)

export const store = configureStore({
    reducer: persistedReducer,
    middleware: (getDefaultMiddleware) =>
        getDefaultMiddleware({
            serializableCheck: {
                ignoredActions: [FLUSH, REHYDRATE, PAUSE, PERSIST, PURGE, REGISTER],
            }
        })
});

export let persistor = persistStore(store);
```





### apiCalls.js

* 여기서 바로 axios로 api호출하면 되는데, 토큰관련해서 인증하려고 한번더 requestMethods.js로 나눔

```js
import { publicRequest } from "../requestMethods";
import { loginFailure, loginStart, loginSuccess } from "./userRedux"


export const login = async (dispatch, user) => {
    dispatch(loginStart());
    try {
        const res = await publicRequest.post("/auth/login", user);
        dispatch(loginSuccess(res.data));

    } catch(err) {
        dispatch(loginFailure())
    }
}
```





### requestMethods.js

* 토큰은 F12 Application으로 localstorage들 확인. TOKEN 가져옴.

```js
import axios from "axios";

const BASE_URL = "/proxy/api";
// const BASE_URL = "http://localhost:5000/api";
const TOKEN = JSON.parse(JSON.parse(localStorage.getItem("persist:root")).user).currentUser.accessToken;

export const publicRequest = axios.create({
    baseURL: BASE_URL,
});

// user요청에는 헤더에 토큰이 필요
export const userRequest = axios.create({
    baseURL: BASE_URL,
    headers: { token: `Bearer ${TOKEN}`},
});
```



=> 이렇게 여기까지 리덕스와 api호출을 연동해봤다.

<br>

## 사용을 위한 세팅



### 기본

`<App/>` 주변을 `<Provider store={store}></Provider>` 로 감싸주면 바로 적용이 가능하다.





### persist 활용

아래 구조로 구성

* PersistGate를 추가 및 속성에 persistor을 넣어주는것이다.
  * persistor은 persist를 적용한 store.js 를 의미한다.

```js
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <Provider store={store}>
    <PersistGate loading={null} persistor={persistor}>
      <App />
    </PersistGate>
  </Provider>
);
```





## 사용법



### useSelector

useSelector는 리덕스 스토어의 상태를 조회하는 Hook이다. 
즉, 리덕스 스토어가 변경되면 감지해서 리렌더링 하는 최적화 훅이다.

=> 예로 리덕스꺼 감지하다가 api호출통해 새로 데이터 들고오면? 그때 리렌더링이 될것이란 것이다.   
리렌더링 하는건 useState와 유사해 보인다.

이것을 사용해 리덕스로 잘 저장되어있던 reducer의 `변수`들을 가져올 수 있다.  
state.user는 store.js의 reducer의미하고, userRedux.js의 name을 잘 가져온다.

`const name = useSelector((state) => state.user.name);`

또는

`const {isFetching, error } = useSelector((state) => state.user);`

이런식으로 활용해서 리덕스에 저장한 변수들 가져옴





### useDispatch

이것을 사용해 reducer의 `함수` 만들었던 것들 사용.

예시

```js
const dispatch = useDispatch();

const handleClick = (e) => {
  e.preventDefault(); // 이벤트 기본동작 방지
  dispatch(loginStart()); // dispatch는 useDispatch()로 만든 객체.
}; // loginStart() 는 userRedux.js에서 만든 함수!
```

<br>

## createAsyncThunk 사용법

apiCalls.js 의 내용을 userRedux.js에서 한번에 다 처리하는 즉, api호출까지 같이 하는 방법이다.

pending, fulfilled, rejected 이것만 가능하므로 더 자유롭게 할 수 있는 apiCalls.js 방식을 추천하기는 한다.

* 이전에 updateStart,...Success,...Failure 이런식으로 userRedux.js에 함수 만들었는데
* Thunk만의 pending, fulfilled, rejected속성을 이용해 바로 위 함수처럼 사용할 수 있다.
* 그리고 자동으로 실행되는거기 때문에 apiCalls.js 처럼 따로 할 필요가 없는것이다.
* 자세한 내용은 - [createAsyncThunk 공문]( https://redux-toolkit.js.org/api/createAsyncThunk)



```js
import { createSlice, createAsyncThunk } from "@reduxjs/toolkit";
import axios from "axios";

export const updateUser2 = createAsyncThunk("users/update", async (user) => {
  const response = await axios.post(
    "http://localhost:8800/api/users/1/update",
    user
  );
  return response.data;
});

export const userSlice = createSlice({
  name: "user",
  initialState: {
    userInfo: {
      name: "john",
      email: "john@email.com",
    },
    pending: null,
    error: null,
  },
  reducers: {},
  extraReducers: {
    [updateUser2.pending]: (state) => { // 업데이트 시작
      state.pending = true;
      state.error = false;
    },
    [updateUser2.fulfilled]: (state, action) => { // 업데이트 성공
      state.userInfo = action.payload;
      state.pending = false;
    },
    [updateUser2.rejected]: (state) => { // 업데이트 실패
      state.pending = false; // null로 하던데 왜 그런지는 몰라
      state.error = true;
    },
  },
});

export const { updateStart, updateSuccess, updateFailure } = userSlice.actions;
export default userRedux.reducer;
```

<br>

## 참고

[git_redux](https://github.com/safak/youtube/tree/redux)







