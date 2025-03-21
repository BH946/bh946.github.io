---
title: "로그인 방식-쿠키,세션과 토큰방식(jwt with redis)"
categories : [Knowledge]
tag : [spring, 스프링, 스프링부트, 로그인, cookie, session, token, jwt, redis]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**로그인 기능의 인증 방식을 쿠키-세션 방식과 토큰 방식을 둘 다 경험해보면서 느낀 차이점을 간단하게 정리**

<br>

<br>

## 쿠키-세션 방식 vs 토큰 방식(jwt with redis)

* [JWT 정리 추천 문서](https://www.popit.kr/jwt-%EC%9D%B8%EC%A6%9D%EC%9D%80-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%82%AC%EC%9A%A9%ED%95%B4%EC%95%BC-%ED%95%A0%EA%B9%8C/)

* **세션 쿠키 로그인 방식**:
  - 서버에서 세션을 메모리에 기록해 관리.
  - 서버에 부하가 갈 수 있음.
* **JWT 인증 토큰(JSON) 방식**:
  - JWT 자체를 디코딩해 사용자 ID 같은 인증 정보를 구할 수 있음.
  - 별도로 서버 메모리에 세션을 기록할 필요가 없어 서버 부하를 줄일 수 있음.
* **장단점 비교**:
  - **세션 쿠키 방식**: 로그아웃 시 서버 메모리의 세션을 제거하면 되므로 간단.
  - **JWT 방식**: 로그아웃 시 강제로 토큰 만료를 시킬 수 없다는 단점이 있음.
* **JWT의 로그아웃 해결 방법**:
  - 블랙리스트를 활용해 해결 가능.
  - 로그아웃 API로 들어온 토큰을 블랙리스트로 DB나 메모리에 기록.
  - 평생 기록할 필요가 없으므로 메모리 기반 저장소 **Redis** 추천.
* **Redis 특징**:
  - In-Memory 구조의 오픈소스 NoSQL DBMS.
  - 메모리 사용이지만 영속성을 보장.
    - 일부 디스크를 사용해 AOF나 RDB 방식으로 기록.

<br>

<br>

## 그럼 언제 사용할까?

**상황에 따라 다르지만 간단히 판단하자면**

- **웹**: 쿠키-세션 방식 추천.
- **앱**: JWT(토큰) 방식 추천.

<br>

**왜??**

- 웹은 기본적으로 stateless이므로 서버에서 관리(메모리에 세션 저장)하는 게 더 적합.
- 앱은 네이티브 환경이므로 stateful하게 토큰을 저장하며 관리하는 게 유리함.

- **물론, 이것이 정답은 아니고 큰 틀에서 보면 이렇게 나눠볼 수 있다는 것**

<br>

**분명한 건 "앱"에서도 당연히 쿠키-세션 방식을 사용할 수 있다.**

**"웹"의 경우 통신 시 자동으로 웹브라우저가 쿠키를 요청 헤더에 포함해주는데,  
"앱"의 경우 웹브라우저를 가지고 있는 게 아니다 보니 HTTP라이브러리가 이를 해결한다.**

* 예로 HTTP라이브러리의 axios를 사용한다면, 일반 웹에서 HTTP통신하는 것처럼 자동으로 
  요청 헤더에 쿠키를 포함해준다. (웹브라우저가 처리하는 방식과 유사하게 처리 해주는게 POINT)

