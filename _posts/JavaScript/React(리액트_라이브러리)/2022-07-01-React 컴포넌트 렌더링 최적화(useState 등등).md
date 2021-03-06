---
title:  "React 컴포넌트 렌더링 최적화(useState 등등)"
categories : React_JS
tag : [Javascript, 리액트, Material, mui, checkbox, 컴포넌트 생명주기, useEffect, useState, 마운트, 언마운트, 재렌더, 반복 렌더링, useMemo, useCallback, memo]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---

## `Material UI` 라이브러리 `@mui/material` 설치

- 간단하게 `Material UI`를 사용할 수 있게 해주는 라이브러리로, `@emotion`를 정식 지원합니다.
- `@mui/material`과 `@emotion`을 설치합니다.
- checkbox 사용할거임

```shell
npm install @mui/material @emotion/react @emotion/styled
```

또는

```shell
yarn add @mui/material @emotion/react @emotion/styled
```

- [`@mui/material` 공식 웹사이트](https://mui.com/)
- [`@mui/material` 공식 문서](https://mui.com/getting-started/usage/)

<br>

## 재렌더와 실제 **`DOM`**에 리페인트 되는 것은 다름

\- 재렌더(업데이트)는 `Virtual DOM`에 가해지는 행위로 실제 `DOM`에 반영되지 않더라도 내부적으로 `React`가 렌더 함수를 호출합니다.

\- 렌더 함수가 실행되어도 이전 `DOM`과 `Virtual DOM`이 다르지 않으면 리페인트 하지 않습니다.

\- 따라서 리페인트가 안된다고 성능에 이슈가 발생하는 것이 아니므로 쓸데 없이 많은 렌더 함수를 호출하는 것 자체를 관리해야 합니다.(최적화)

<br>

## `JavaScript`(`ES6`)의 `Spread` 연산자(전개 연산자)

- [MDN 문서 - 전개 구문](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
- [Velog - 전개 연산자](https://velog.io/@recordboy/%EC%A0%84%EA%B0%9C-%EC%97%B0%EC%82%B0%EC%9E%90Spread-Operator)
- `...` 연산을 사용하면 배열이나 객체의 요소를 펼칠 수 있습니다.
- 이를 사용하여 객체나 배열의 내용을 손 쉽게 복사할 수 있습니다.
- 여기서는 `FunctionCheckboxList`에서 하위 컴포넌트인 `FunctionCheckbox`로 이벤트 리스너 함수를 전달할 때, 배열의 값을 `map` 함수로 변경할 때 용이하게 사용하였습니다.

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

<br>

## `JavaScript`(`ES6`)의 `Destructuring` 연산자(구조 분해 할당, 비구조화 할당)

- [MDN 문서 - 구조 분해 할당](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)
- [Guidebook - 비구조화 할당](https://learnjs.vlpt.us/useful/06-destructuring.html)
- `const { }`, `const []` 연산을 사용하여 객체에서 특정 값만 가져올 수 있습니다.
- 이를 사용하여 객체나 배열의 내용 중 일부를 손 쉽게 가져올 수 있습니다.
- 객체의 경우 `const { property1, property2 }` 연산을 사용하면 전달받는 객체에 `property1`의 키를 가진 값과, `property2`의 키를 가진 값이 할당됩니다. 키의 이름이 객체에 존재하지 않으면 `undefined`가 할당됩니다.
- 배열의 경우 `const [name1, name2]` 연산을 사용하면 `name1`에 첫 번째 요소, `name2`에 두 번째 요소가 할당되며, 그 이상도 가능합니다.
- 여기서는 `FunctionCheckbox`에서 `props`를 가져올 때와, `useState` 함수에서 상태와 `setState` 함수를 가져올 때 사용합니다.

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

<br>

## React.js의 생명주기

![image-20220701190414194](/images/2022-07-01-React 컴포넌트 렌더링 최적화/image-20220701190414194.png)



### 함수형 컴포넌트의 생명주기

![image-20220701190452999](/images/2022-07-01-React 컴포넌트 렌더링 최적화/image-20220701190452999.png)

* **생명주기의 때에 따라 어떤 작업을 처리해야 하는지 지정**해줘야 불필요한 업데이트를 방지할 수 있다.
* **클래스 컴포넌트** : **LifeCycle**메서드 사용
* **함수형 컴포넌트** : **Hook**을 사용(예 : **useEffect**, **useState**)
* **연관성 : 마운트 - 재렌더 - 언마운트 <-> 생성 - 업데이트 - 제거**



### useEffect - 변화감지(훅)

**React에서 제공하며, Side Effect를 다루어 LifeCycle과 비슷한 조작을 가능하게 해주는 특별한 함수**

* 컴포넌트에서 **값(상태)의 변화를 감지**함

  * 컴포넌트에서 관리하는 **값(props, state)의 변화**를 **mount**
  * 이후 지속적으로 감지하여 값이 변화했을 경우 콜백 함(이벤트 리스너와 유사)

* **클래스에서는 LifeCycle**을 이용해 조작

  * 클래스에서는 생명주기 메서드를 상속받아 사용하기 때문에 코드가 난잡해지지만, **useEffect는 이를 간단하게 구현**함
  * **감지할 값이 없으면 mount와 unmount만 감지**

* **clean-up** **함수**

  * useEffect의 인자로 넘어가는 콜백함수에서 return 값으로 함수를 넘겨주는 함수로, 이 함수는 **값의 변화가 발생하기 직전**, 그리고 **unmount가 발생하기 직전에 실행**됨
  * **아래 예시는 감지할 값이 있을 때의 예시이며 clean-up 함수는 return 부분**

  ```jsx
  // 이건 감지할 값이 있을 때의 예시
  // clean-up 함수는 return 부분을 의미
  useEffect(() => {
      // count를 넣은 배열일 경우 이 함수는 mount될 때, count 값이 변할 때만 실행
      console.log("count update");
      return () => {
          // count를 넣은 배열 내의 return 함수는 unmount될 때와, count 값이 변하기 직전에만 실행
          console.log("count will update");
      };
  }, [count]);
  ```

* **useEffect**는 먼저 **선언한 순서대로 호출**됨



### 마운트와 언마운트(생성, 제거)

**컴포넌트의 시작과 끝에 한 번 씩 발생**

#### 마운트

맨처음 컴포넌트가 render 될때는 마운트과정을 거친다.  
그러나 props, state가 변경되어 render 될 때는 마운트를 거치지 않는다.(재렌더)  
그래서 마운트(Mount)는 **DOM 객체가 생성되고 브라우저에 나타나는 것을 의미**

* state, defaultProps, context 등을 저장하고 설정하는 작업
* 초기에는 렌더링을 하고 나서 DOM에 접근 가능 하므로, 마운트 단계가 끝나기 전에는 DOM에 접근할 수 없음
* **useEffect에 빈 배열을 넣은 후 실행될 때가 마운트 단계가 끝났을 때임**



#### 언마운트

* 주로 DOM에 직접 등록했든 이벤트를 제거하고 외부 라이브러리를 dispose 해주거나, setTimeout, setInterval 등을 clear 함
* **useEffect에 빈 배열을 넣은 후 cleanup함수가 실행될 때 언마운트 단계임**



#### 빈 배열 예시(감지할 값X 상황)

* 감지할 값이 있는상황은 위에서 확인(예시 들어둠)
* **그런데 만약, 빈 배열조차 안 넣으면?** 그냥 **무엇이든 값 변경될 때마다** useEffect내에 코드 **실행**.  
  **해결** : 따라서 **빈 배열**을 넣어서 **mount, unmount때만 실행** 시키게 만든것.

```jsx
useEffect(() => {
    // 빈 배열일 경우 이 함수는 mount될 때만 실행
    console.log("mount");
    return () => {
        // 빈 배열 내의 return 하는 함수는 unmount될 때만 실행
        console.log("unmount");
    };
}, []);
```



### 재렌더(업데이트)

**컴포넌트의 props, state가 변경될 때마다 연산** 

* **상위 컴포넌트에서 전달한 props의 값이 바뀌거나, 현재 컴포넌트의 sate가 변경되었을 때,** 함수형 컴포넌트는 **함수 그 자체(함수 자체가 render 메서드)가 실행**됨
* 실행되면서 함수 내(자식)에 정의된 함수들(**onClick 리스너 등) 혹은 변수**들은 **새로 선언**됨
* 때문에 함수 내에서 **변수는 거의 무의미하게 되기 때문에 useState 등의 훅 메서드**를 사용함

![image-20220701201736571](/images/2022-07-01-React 컴포넌트 렌더링 최적화/image-20220701201736571.png)



### 재렌더와 마운트 구분

예로 input태그의 값 입력해서 내부 데이터 수정될때 재렌더가 되는것이지 마운트가 되는건 아님.   

보통 마운트는 처음 한번만 하는걸로 판단 하면 됨.  

서버 구동해서 개발시 저장이나 웹 새로고침 할때 그때는 항상 마운트과정도 거침

이처럼 햇갈릴 수 있기 때문에 잘 구분해서 useEffect나 등등 메소드 잘 사용할것

```jsx
// input태그 예시
import { useCallback, useEffect, useState } from "react";

const URL = "https://reqres.in/api/users";
// API를 요청할 주소

// API 요청 테스트하는 컴포넌트
const RequestTest = () => {
  let [data, changeData] = useState("")
  // fetch API 요청 test(async/await으로 비동기 요청을 동기처럼 사용)
  const callApiFetch = async () => {
    const response = await fetch(URL)
    console.log(response)
    const data = await response.json();
    console.log(data)
    
  }
  // callApiFetch()
  useEffect(() => {
    // mount 시 API 요청
    callApiFetch();
    console.log("rendering~")
  }, []); // 빈 배열 지우면 mount때만이 아니라 재렌더시 항상 실행함

  return (<><input onChange={(e)=>{changeData(e.target.value)}}></input></>);
}
```

<br>

## 커스텀 컴포넌트 작성

* 클래스 보다는 **함수 컴포넌트**가 주를 이룬다
* 함수 작성시 function보단 **화살표 함수**로 작성 추천

### style.js 따로 작성

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

export const StyleCheckboxContent = styled.div`
  display: flex;
  align-items: center;
  // display: flex는 좀 더 유연하고 편하게 요소를 배치시킬 수 있게 해 줌
  // IE에서는 제대로 동작하지 않음

  height: 40px;
`;

export const StyleCheckboxMore = styled.div`
  display: flex;
  align-items: center;

  width: 100%;
  height: 30px;

  color: #777;
  font-size: 12px;
`;

export const StyleCheckboxDetail = styled.div`
  width: 100%;

  padding: 24px 0;
  // 이렇게 입력하면 y축에만 padding이 적용됨
  // 반대로 0 24px; 라면 x축에만 padding 적용
`;
```



### 함수형 컴포넌트

```jsx
import { Checkbox } from "@mui/material";

import {
  StyleCheckboxContent,
  StyleCheckboxDetail,
  StyleCheckboxMore,
  StyleCheckboxWrapper,
} from "./Checkbox.style";

// 함수형 컴포넌트 작성
const FunctionCheckbox = () => {
    return (
      <StyleCheckboxWrapper>
        <StyleCheckboxContent>
          <Checkbox checked={false} />
        </StyleCheckboxContent>
          {true ? ( // true니까 더보기 부분 글이 나옴. false면 대충 긴 내용? 부분임
            <StyleCheckboxMore>
              사람을 화나게하는 방법은 두가지가 있다고 합니다. 그 첫번째는 말을
              하다가 마는 것이고... 더보기
            </StyleCheckboxMore>
          ) : (
            <StyleCheckboxDetail>대충 긴 내용?</StyleCheckboxDetail>
          )}
      </StyleCheckboxWrapper>
    );
  }
);
```



### 클래스형 컴포넌트

```jsx
import { Component } from "react";
import { Checkbox } from "@mui/material";
import {
  StyleCheckboxContent,
  StyleCheckboxDetail,
  StyleCheckboxMore,
  StyleCheckboxWrapper,
} from "./Checkbox.style";

// 클래스형 컴포넌트 선언
class ClassCheckbox extends Component {
  // Component를 상속 받음
  render() {
    // 렌더/재렌더시 이 함수 실행
    return (
      <StyleCheckboxWrapper>
        <StyleCheckboxContent>
          <Checkbox checked={false} />
        </StyleCheckboxContent>
        {true ? (
          <StyleCheckboxMore>
            사람을 화나게하는 방법은 두가지가 있다고 합니다. 그 첫번째는 말을
            하다가 마는 것이고... 더보기
          </StyleCheckboxMore>
        ) : (
          <StyleCheckboxDetail>대충 긴 내용?</StyleCheckboxDetail>
        )}
      </StyleCheckboxWrapper>
    );
  }
}
export default ClassCheckbox;
```



### 실행화면

* 함수형, 클래스형 둘다 동일

**true일때 실행화면**

![image-20220701205407074](/images/2022-07-01-React 컴포넌트 렌더링 최적화/image-20220701205407074.png)

**false일때 실행화면**

![image-20220701205526248](/images/2022-07-01-React 컴포넌트 렌더링 최적화/image-20220701205526248.png)

<br>

## useState와 조건부 렌더링

### useState - 상태관리(훅)

**React에서 제공하며, 클래스형 컴포넌트 없이 상태를 사용할 수 있게 해주는 특별한 함수**

* 컴포넌트의 **상태를 관리**함
* **렌더링에 영향**을 줌
  * 이 State가 변하면 React는 변화를 인지하고 Virtual DOM에서 **재 렌더 여부를 결정**
* **훅 메서드**
  * 함수형 컴포넌트에서, 함수 내에 함수를 선언한다고 Virtual DOM의 재 렌더 여부를 결정하는 곳까지 영향을 줄 수 없으나, 훅 메서드를 통해 **React 내부와 연결**

* **useEffect**가 **상태변화를 감지**했다면, **useState**는 useEffect의 상태 변화 감지로 인한 재렌더 때문에 무의미한 **변수 새로 다시 선언되는걸 방지**하기 위해 사용



### setState를 통한 조건부 렌더링

* **setState형식으로 함수** 만들어서 활용

```jsx
import {useState} from 'react';
// ...생략
const FunctionCheckbox = ({checked}) => {
    const [hideMore,setHideMore] = useState(true); // 상태 true로
    const onViewMore = () => {
        console.log("test");
        setHideMore(false); // 위에서 만든 setState형식 함수 사용하는 모습
    };
    const onHideMore = () => {
        setHideMore(true);
    };

    const onToggleMore = () => {
        setHideMore(!hideMore)
    };

    return(
        <StyleCheckboxWrapper>
            <StyleCheckboxContent>
                <Checkbox checked={checked}/>
            </StyleCheckboxContent>
            
        {hideMore ?( // 삼항연산자
            <StyleCheckboxMore onClick={onToggleMore}>사람을 화나게하는 방법은 두가지가 있다고 합니다. 그 첫번째는 말을 하다가 마는 것이고... 더보기</StyleCheckboxMore>            
        ):(
            <StyleCheckboxDetail onClick={onToggleMore}>대충 긴 내용?</StyleCheckboxDetail>
        )}
        </StyleCheckboxWrapper>
    );
};
```



### 실행화면

* 클릭마다 두 화면이 바뀜.
* 초기 상태는 true로 줬음
* checkbox는 아직 따로 설정안해서 체크해도 반응 없음

**true일때 실행화면**

![image-20220701205407074](/images/2022-07-01-React 컴포넌트 렌더링 최적화/image-20220701205407074.png)

**false일때 실행화면**

![image-20220701205526248](/images/2022-07-01-React 컴포넌트 렌더링 최적화/image-20220701205526248.png)

<br>

## map 함수를 이용한 반복 렌더링

* **map 함수** : 배열 순회하여 새로운 값들을 return하여 모아서 새로운 배열을 반환
* map 함수로 렌더하게 되면 렌더되는 컴포넌트에는 **key라는 매개변수를 추가적으로 전달**해야 함
* key 매개변수는 리스트 렌더링에 효율적인 사용을 위해 **다른 리스트와 겹치지 않는 고유의 값**을 전달

```jsx
const FunctionCheckbox = ({isHide,checked}) => {
// ... 
    
export const FunctionCheckboxList = () => {
    const [checkboxList, setCheckboxList] = useState([
        {
            id: 1,
            isHide: true,
            checked: false,
        },
        {
            id:2,
            isHide:true,
            checked:false,
        },
    ]);
    
    return (
        <>
            {checkboxList.map((checkboxItem)=>( // map함수 사용
                <FunctionCheckbox key={checkboxItem.id} isHide = {checkboxItem.isHide} checked={checkboxItem.checked} />
            ))}
        </>
    );
};
```

<br>

## 상태 최적화 관리

**`React` 컴포넌트 내에서 수정시(state, props) 재렌더를 할텐데, 만약 어떠한 함수가 선언 되어 있다면 이 함수는 컴포넌트가 렌더링 될때마다 새로운 함수를 생성한다** 라는 등의 단점들을 최적화를 통해 예방  
=> **함수 예**로 **useCallback**은 새로 생성말고, **기존 함수를 반환**해서 최적화  
=> **변수 예**로 **useMemo**는 새로 생성말고, **기존 변수를 반환**해서 최적화  
=> **props 예**로 **memo**는 **지정한 props(매개변수)의 변화에만 재렌더**를 하고, 나머진 X를 통해 최적화

* checkbox를 변경에 반응하는 함수를 추가하는 것을 최적화 할것이다.
* 전체 선택 기능 또한 구현할 것이다.
* 그리고 여기 부분은 전부 **FunctioinCheckbox.jsx** 파일에서 다루는 내용이다.

### FunctionCheckboxList 수정(최적화로 수정전)

![image-20220701223245020](/images/2022-07-01-React 컴포넌트 렌더링 최적화/image-20220701223245020.png)

![image-20220701223317330](/images/2022-07-01-React 컴포넌트 렌더링 최적화/image-20220701223317330.png)



### 전체 체크 유무, 기능(최적화로 수정전)

**체크유무는 useMemo로 최적화, 기능은 useCallback로 최적화 하였음**

* **참고 : every 메서드는 배열 순회하며 각 요소들이 모든 조건에 부합하는지 확인**

![image-20220701223346987](/images/2022-07-01-React 컴포넌트 렌더링 최적화/image-20220701223346987.png)

![image-20220701223524297](/images/2022-07-01-React 컴포넌트 렌더링 최적화/image-20220701223524297.png)



### useMemo로 최적화 - 변수 생성

**React에서 제공하며, Side Effect를 다루어 변화에만 반응하는 변수 생성을 가능하게 해주는 특별한 함수**

* useMemo에는 변화를 감지하면 실행하여 **값을 반환할 콜백 함수와, 감지할 값들의 배열**을 전달
* **감지할 값**들이 **변화될 때만 콜백 함수가 실행되어 값이 변경**됨
  * 즉, **배열에 요소들이 없을 시** 처음 기억(생성)해둔 변수를 끝까지 사용하는 것
* 상태를 통해 **추가적으로 값을 연산해야 하는 경우 사용**하기 좋음
* 다른 변화로 인한 **불필요한 렌더링을 제거**할 수 있어 **성능 향상**에 도움
  * 아래 예시로, 재렌더마다 **AllCheck**가 새로 생성되는데 **useMemo** 사용시 **checkboxList만 변경될 때마다 재렌더**를 하기 때문에 불필요한 렌더링을 하지않아 **성능 향상**

```jsx
const AllCheck = useMemo(
    () => checkboxList.every((checkboxItem) => checkboxItem.checked),
    [checkboxList]
);
// const AllCheck = checkboxList.every((checkboxItem) => checkboxItem.checked);
// useMemo를 사용하지 않으면 재렌더가 될 때마다 AllCheck가 새로이 선언되며, every 메서드가 계속 실행됨
// useMemo의 두 번째 인수에 있는 배열 내부의 값이 변경되면 첫 번째 인수인 콜백 함수를 실행하여 AllCheck를 새로 계산함
// 여기서는 checkboxList가 변경될 때마다 AllCheck를 계산
```



### useCallback로 최적화 - 함수 생성

**React에서 제공하며, Side Effect를 다루어 변화에만 반응하는 함수 생성을 가능하게 해주는 특별한 함수**

* useCallback에는 변화를 감지하면 **생성할 함수와, 감지할 값들의 배열**을 전달
* **감지할 값**들이 **변화될 때만 함수가 생성**됨
  * 즉, **배열에 요소들이 없을 시** 처음 기억(생성)해둔 함수를 끝까지 사용하는 것
* 다른 변화로 인한 **불필요한 렌더링을 제거**할 수 있어 **성능 향상**에 도움
  * 아래 예시로, 재렌더마다 **onChangeCheckbox**함수가 매번 새로 생성되는 단점.  
    **useCallback**을 사용한다면, 감지할 배열 값인 **checkboxList**가 변화할 시에만 재렌더하는 것이기 때문에 다른 변화에는 **onChangeCheckbox**함수가 새로 생성되지 않아서 **성능 향상**
* **useCallback 사용방식 및 만든 onChangeCheckbox함수를 적용하는 방식을 확인(코드에서)**
  * **onChangeCheckbox**를 매개변수 id를 넘기기위해 **onChangeCheckbox(checkboxItem.id)**로 넘기면? 함수가 바로 실행이되며, 우리는 함수자체를 넘기고 싶기 때문에 **()=>{onChangeCheckbox(checkboxItem.id)}**이런 형태로 **화살표 함수로 한번 감싸는 것**이다.

```jsx
// 함수형 컴포넌트로 Checkbox를 반복적으로 렌더링하는 컴포넌트 작성
export const FunctionCheckboxList = () => {
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
  // 렌더할 Checkbox에 대한 데이터를 useState로 선언(상태)

  // 한 Checkbox 컴포넌트의 checked 상태를 반대로 변경하는 함수
  const onChangeCheckbox = useCallback(
    (id) => {
      // id를 인자로 받음
      const newCheckboxList = checkboxList.map(
        (checkboxItem) =>
          checkboxItem.id === id
            ? // 인자로 받은 아이디와 순회하던 도중 만난 id 값이 일치한 checkbox 데이터의 값을 변경
              { ...checkboxItem, checked: !checkboxItem.checked } // checkbox 데이터의 다른 값은 유지하고, checked 프로퍼티만 값을 반대로 바꿈
            : checkboxItem // 일치하지 않은 id 값은 그대로 반환함
      ); // 상태는 불변성을 유지하기 위해 이전의 값은 변경하지 않고 새로운 배열을 반환하는 map 함수 사용
      setCheckboxList(newCheckboxList);
    },
    [checkboxList]
  );
  // useCallback을 사용하지 않으면 재렌더가 될 때마다 onChangeCheckbox가 새로이 선언되며, 함수 생성이 계속 실행됨
  // useCallback의 두 번째 인수에 있는 배열 내부의 값이 변경되면 첫 번째 인수의 함수를 새로 생성하여 반환
  // 여기서는 checkboxList가 변경될 때마다 함수 생성

// 중간 생략..(아래는 onChangeCheckbox함수 넣은 방식 확인)
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
          // onChangeCheckbox 함수와 onClickChangeView 함수에는 id 매개변수를 넣어주어야 하는데,
          // 클릭 이벤트로 넣을 때 id 매개변수를 전달하기 위해선 함수를 미리 만들어 놓아야 함
        />
      ))}
      {/* 배열 내장 함수인 map으로 컴포넌트를 반환하게 되면 리스트로 출력 */}
    </>
  ); // return..
}; // export..
```



### memo로 최적화

**React에서 제공하며, props이외의 변화가 발생해도 재렌더 하지 않는 컴포넌트 생성**

* memo 함수는 컴포넌트를 인자로 받아, 해당 컴포넌트에 전달되는 **props 이외의 상태가 변화하더라도 재렌더 하지 않는 컴포넌트를 반환**하는 함수이다.
* 다른 변화로 인한 **불필요한 렌더링을 제거**할 수 있어 **성능 향상**에 도움된다.
* **memo함수 사용법 확인(아래코드)**

```jsx
import { useState, useCallback, useMemo, memo } from "react";
import { Checkbox } from "@mui/material";

import {
  StyleCheckboxContent,
  StyleCheckboxDetail,
  StyleCheckboxMore,
  StyleCheckboxWrapper,
} from "./Checkbox.style";

// 함수형 컴포넌트 작성
const FunctionCheckbox = memo(
  // memo 함수는 안의 컴포넌트가 props 이외의 변화에 재렌더 되지 않도록 해줌
  // 최적화를 위해 자주 사용되는 함수
  // 재렌더 되지 않는 컴포넌트를 반환함
  ({ isHide, checked, label, onChangeCheckbox, onClickChangeView }) => {
    // props는 다음과 같이 받아올 수 있음
    // 이름이 같지 않으면 받아올 수 없음
    return (
      <StyleCheckboxWrapper>
        <StyleCheckboxContent>
          <Checkbox onChange={onChangeCheckbox} checked={checked} />
          {/* Material UI에 정의된 Checkbox 컴포넌트 사용 */}
          <p>{label}</p>
        </StyleCheckboxContent>
        <div onClick={onClickChangeView}>
          {/* 이 div가 클릭되면 보여지는 요소가 Toggle됨 */}
          {isHide ? (
            <StyleCheckboxMore>
              사람을 화나게하는 방법은 두가지가 있다고 합니다. 그 첫번째는 말을
              하다가 마는 것이고... 더보기
            </StyleCheckboxMore>
          ) : (
            <StyleCheckboxDetail>대충 긴 내용?</StyleCheckboxDetail>
          )}
        </div>
      </StyleCheckboxWrapper>
    );
  }
);
```

<br>

## `useState`의 동작 순서 - Functional Update

- `useState`로 반환받은 `setter` 함수는 **완전히 동기적으로(순차적으로) 동작하지 않습니다.**
- `setState` 함수에 값을 매개변수로 넣어서 호출하면, 이는 `React`에 해당 값으로 변경하라는 동작을 요청하는 행위와 같습니다.
  - 따라서 `setState`가 연속해서 호출되면, `React`에서 첫 번째 동작 요청 행위와, 두 번째 동작 요청 행위가 모두 실행되지만 `setState`에 넘어간 값이 한 상태를 기준으로 동작하기 때문에, 두 번쨰 동작 요청 행위만 실행된 것으로 판단합니다.

- 이에 `setState` 함수에 콜백 함수를 매개변수로 넣어서 호출하는 방법이 있습니다.
  - 콜백 함수의 인수로는 이전 상태가 넘어오며, 이전 상태를 통해 **다음 상태를 연산하는 로직을 여러 번 호출하여도 정상적으로 동작합니다.**
  - 이를 `Functional Update`라고 합니다.

> 이는 상태를 유지해야 할 권리를 사용자가 아닌 `React`로 전환할 수 있게 만드는 기법입니다.

> 또한 `setState`에 값을 매개변수로 넣으면, `React`에서 기존 값과 비교하는 연산이 발생할 수 있기 때문에 성능적인 측면에서 콜백 함수를 매개변수로 넣는 것이 유리합니다.

### 참고

- [`React` `Hook` `setState`에 함수 전달](https://developer-talk.tistory.com/m/252)

```jsx
import { useState, useCallback } from "react";

const FunctionalUpdate = () => {
  const [count, setCount] = useState(0);

  // 값을 매개변수로 전달한 setCount 함수
  const onClick = useCallback(() => {
    setCount(count + 1);

    console.log(count); // count의 값이 변하지 않음

    setCount(count + 2);
    // 두 번째로 실행한 setState만 실제로 적용
  }, [count]);
  // deps 배열에 count를 넣어 주지 않으면, onClick 함수는 count의 값을 계속해서 0으로 기억하게 됨

  // 콜백 함수를 매개변수로 전달한 setCount 함수
  const onClickFunctionalUpdate = useCallback(() => {
    setCount((prevCount) => {
      // 이전 count의 상태가 넘어옴
      return prevCount + 1;
    }); // 콜백 함수의 인자로는 이전 상태 값이 전달됨

    // 콜백 함수에서 다음으로 설정할 상태 값을 return 하면 적용됨
    // console.log(count); // count의 값이 변하지 않음

    setCount((prevCount) => {
      return prevCount + 2;
    }); // 첫 번째로 전달한 setState와 두 번째로 전달한 setState 모두 실행
  }, []);
  // deps 배열에 count를 넣어 주지 않아도, onClickFunctionalUpdate 함수는 이 컴포넌트의 count를 사용하는게 아닌,
  // React에서 전달한 prevCount를 사용하기 때문에 문제가 되지 않음
  // 상태를 관리해야 할 책임을 React로 전가한 것

  return <div onClick={onClick}>카운터 값: {count}</div>;
};

// 현재 count의 값이 0일 때, 값을 매개변수로 전달한 setCount 함수 실행 과정
//
// 1. 다음 count의 상태를 1(count + 1 === 0 + 1)로 만들라고 React에 전달
// 2. console.log 함수 실행
// 3. 다음 count의 상태를 2(count + 2 === 0 + 2)로 만들라고 React에 전달
// 4. 함수 종료

// 이에 React는 처음에 1로 상태를 변경한 후에 즉시 2로 상태를 변경하므로
// 두 번째 setCount만 적용된 것처럼 보임

// 현재 count의 값이 0일 때, 콜백 함수를 매개변수로 전달한 setCount 함수 실행 과정

// 1. 다음 count의 상태를 이전 상태 + 1(prevCount + 1 === 0 + 1 === 1)로 변경하는 함수를 실행하라고 React에 전달
// 2. console.log 함수 실행
// 3. 다음 count의 상태를 이전 상태 + 2(prevCount + 2 === 1 + 2 === 3)로 변경하는 함수를 실행하라고 React에 전달
// 4. 함수 종료

// 이에 React는 처음에 1로 상태를 변경한 후에 두 번째로 실행할 함수에서,
// prevCount에 1을 넣고 실행하기 때문에 두 setCount 모두 적용

export default FunctionalUpdate;
```

<br>

## `input` 요소의 관리

- `input` 요소를 상태(`state`)로 관리하여 변화마다 리렌더링 할 경우, 항상 최선의 선택은 아닙니다.
- 꼭 실시간으로 변화를 감지해야 하는 경우가 아니라면, 값이 변경될 때마다 다른 컴포넌트의 리렌더 로직에도 영향을 끼치는 `input` 요소는 성능 저하로 작용하기 쉽습니다.
- **즉, 실시간 변화를 감지하는 경우가 아닌경우는 무분별하게 useState로 관리하지 말자는 것입니다.**
- 이런 경우 `form` 태그의 `onSubmit`를 요소를 사용한 후 넘어오는 이벤트 객체 `e`로부터 `input` 요소를 추출하여 값에 접근하는 방식이나, `useRef`를 사용하는 등의 다른 방식을 통해서 충분히 구현할 수 있습니다.

### 참고

```jsx
import { useState, useRef } from "react";

const InputManage = () => {
  const inputRef = useRef(null);
  const [value, setValue] = useState("");

  const onChange = (e) => setValue(e.target.value);
  // 이런 작성 방식은 onChange 이벤트가 발생할 때마다 React에게 리렌더 로직을 요청하는 것과 같음
  // 실제로 input 요소는 onChange 이벤트마다 리렌더가 됨

  const onSubmit = (e) => {
    // e 객체에 하위 로직을 전달 받을 수 있는 방법이 여러 가지 존재함
    // 이를 통해 꼭 상태로 관리하여 리렌더하는 방식을 취하지 않아도 무방
    // inputRef.current. 이처럼 useRef를 사용하여 ref로 접근 가능함
  };

  return (
    <form onSubmit={onSubmit}>
      <input type="text" value={value} onChange={onChange} />
      <input type="text" ref={inputRef} />
      <button type="submit">전송</button>
    </form>
  );
};
export default InputManage;
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
