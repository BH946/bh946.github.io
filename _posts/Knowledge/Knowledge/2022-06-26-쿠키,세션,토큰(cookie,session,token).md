---
title:  "쿠키,세션,토큰(cookie,session,token)"  
categories : Knowledge
tag : [쿠키, 세션, 토큰]  
toc: true  
toc_sticky: true  
author_profile: false  
sidebar:  
 nav: "docs"    
---



**헷갈리는 쿠키와 세션과 토큰 용어를 정리해보자.**

<br>

<br>

## http는 stateless이다

* **http는 통신 규약인데, stateless(상태 저장 안 함)라고 한다.**
  - 서버로 가는 모든 요청은 독립적이고 서로 연결이 없으며, 메모리를 사용하지 않는다.
  - 즉, 요청이 끝나면 서버는 요청한 클라이언트가 누구였는지 기억하지 못한다.

<br>

<br>

## 쿠키(Cookie)

**쿠키는 클라이언트 로컬에 저장되는 데이터다. (서버에서 처음 만들어 전송함)**

- 서버에서 쿠키를 만들어 클라이언트에 전송하면, 클라이언트는 웹을 방문할 때마다 자동으로 요청에 쿠키를 포함해 서버로 보낸다.
- 클라이언트와 서버 사이에서 **매개체 역할**을 한다.

<br>

<br>

## 세션(Session)

**세션은 쿠키와 달리 서버 측에서 관리된다. (세션DB의 세션ID 형식으로 저장됨)**

- 참고로 세션은 쿠키를 기반으로 동작하기 때문에, **세션은 쿠키를 통해 전송**된다.

<br>

<br>

## 토큰(Token)

**토큰은 단순히 문자열로 이루어진 데이터다.**

- 토큰을 서버로 보내 로그인 인증을 할 수 있다. (예: JWT)
- 참고로 쿠키는 브라우저에만 있으므로, 네이티브 앱에서는 토큰을 활용한다.
  - **JWT**(JSON Web Token)는 토큰 기반 인증 방식이다. 서버가 인증 완료된 토큰을 클라이언트로 보내면, 클라이언트는 요청 때마다 해당 토큰을 서버로 보내고, 서버는 토큰의 유효성만 확인하면 인증이 완료된다.

<br>

<br>

## 추천하는 참고자료

[조금 자세히 잘 정리](https://interconnection.tistory.com/74)

[간략히 잘 정리](https://www.youtube.com/watch?v=tosLBcAX1vk&list=PL7jH19IHhOLN4_YTF__6y3ongLcYCZtNL&index=8)
