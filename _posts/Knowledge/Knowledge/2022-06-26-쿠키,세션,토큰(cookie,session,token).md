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



## 1. http는 stateless이다

**http는 통신규약인데, stateless(상태저장 안함) 라고 한다.**

* 서버로 가는 모든 요청들이 독립적이며 서로 연결이 없고, 메모리가 없다.
* 즉, 요청이끝나면 서버는 그냥 요청한 녀석이 누구였는지 잊어버린다.

<br><br>

## 2. 쿠키(Cookie)

**쿠키는 클라이언트 로컬에 저장되는 데이터(쿠키는 서버에서 처음 만들어 전송함)**

* 서버에서 쿠키를 만들어 클라이언트에 같이 전송했다면,  
  해당 웹을 방문할때마다 자동으로 클라->서버 요청에 쿠키도 포함해서 서버로 전송한다.
* 클라이언트 - 서버 사이의 **매개체 역할**을 한다.

<br><br>

## 3. 세션(Session)

**세션은 쿠키와 달리 서버 측에서 관리한다(세션DB의 세션ID 형식으로 관리)**

* 참고로 쿠키 기반이기 때문에, **세션은 쿠키를 통해 전송**이 된다.

<br><br>

## 토큰(Token)

**토큰은 그저 문자열로 이루어져있다.**

* 이것을 서버로 보내서 로그인 인증을 할 수 있다(예: JWT)
* 참고로 쿠키는 브라우저에만 있기 때문에 네이티브 앱에선 토큰을 활용!!
  * **JWT**( JSON Web Token ) 는 토큰 기반. 서버가 인증완료한 토큰을 클라이언트로 보내주면,  
    클라이언트는 요청 때마다 서버로 해당 토큰을 보내고, 서버는 해당 토큰 유효한지만 확인하면 인증 완료.

<br><br>

## 추천하는 참고자료

[조금 자세히 잘 정리](https://interconnection.tistory.com/74)
[간략히 잘 정리](https://www.youtube.com/watch?v=tosLBcAX1vk&list=PL7jH19IHhOLN4_YTF__6y3ongLcYCZtNL&index=8)
