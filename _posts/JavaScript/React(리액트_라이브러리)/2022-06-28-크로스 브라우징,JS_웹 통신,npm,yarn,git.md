---
title:  "(React들어가기전)크로스 브라우징,JS_웹 통신,npm,yarn,git"
categories : Revise
tag : [JavaScript, 리액트, React, http]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---

## 크로스 브라우징

**브라우저의 엔진이 다름을 고려하여 모든 브라우저에서 동일하게 동작할 수 있도록 작성하는 기법**

* 브라우저마다 엔진이 달라 HTML5, CSS, JS가 작동되지 않을 수 있음
* 이는 특정 브라우저에서 JS코드 에러, CSS 깨짐 등을 의미
  * **예로** IE 등 구형 브라우저에서는 W3C 웹 규격을 완벽히 따르지 않아서 위의 에러 발생 가능
* **Babel** 등의 크로스 브라우징 도구가 존재함
* 이와 유사하게 응용 프로그램에서는 **크로스 플랫폼**이라는 이슈가 있음

<br>

## HTTP 통신

### HTTP 통신의 구조

**HTTP는 요청과 응답으로 구성된 데이터 교환 규칙**

* 클라이언트가 HTTP Request를 서버에 보내면, 서버는 HTTP Response를 돌려주는 구조
* HTTP는 **stateless**이기 때문에 상태를 저장하지 않으며, 데이터가 필요하면 계속해서 데이터를 요청하고 응답받는 형태이다. 즉, 연결을 유지하지 않고 통신을 한다.
* 크게 세 부분으로 구성 : **StartLine, Headers, Body**
  * StartLine : 시작라인(Request Line, Status Line..)

![HTTP요청_응답](/images/2022-06-28-크로스 브라우징,JS_웹 통신,npm,yarn,git/req,res.png)

<br>

## HTTP 요청 메소드

**GET, POST, PUT, PATCH, DELETE, OPTIONS 등의 메소드가 있음**  
**메소드 말고도 Headers, Body를 통해 추가적인 데이터와 상태 등 첨부하여 보낼 수 있음**

* HTTP 요청을 할 때, 해당 요청이 어떠한 역할을 정의하는지를 기술하는 이름을 말함
* **GET 요청**은 서버로부터 데이터를 받아(GET, read)올 때 사용하는 메소드
  * 일반적으로 브라우저의 주소 창을 통해 접속하는 행위가 GET 메소드의 통신
* **POST 요청**은 데이터 생성/수정/삭제 등에 주로 사용되는 메소드
  * 회원가입, 로그인 등의 중요 데이터가 포함될 때 많이 사용됨

![req](/images/2022-06-28-크로스 브라우징,JS_웹 통신,npm,yarn,git/req.png)

<br>

## HTTP 응답 구조

**응답 상태 코드와 Headers로 통신에 관한 정보, body를 통해 데이터를 받아옴**

* 응답 상태 코드 존재 - 응답의 상태를 알려줌**(20x ~ 50x)**
  * 200대 : 정상

* **Headers**에는 Server에서 저장시키는 값(**쿠키, 세션 등**)이 포함될 수 있음
* GET 방식으로 요청하면 body에는 HTML, CSS, JS가 오는 것이 일반적
* 요청 메소드, 응답 데이터에 따라 **HTML이 아닐 수 있음**

![res](/images/2022-06-28-크로스 브라우징,JS_웹 통신,npm,yarn,git/res.png)

<br>

## 브라우저에서 요청(행동 예시)

### www.google.co.kr에 접속 때

**브라우저의 주소창에서 접속하는 행위는 GET 방식으로 통신하는 행위와 같음**

1. 브라우저는 www.google.co.kr이라는 주소의 **IP를 DNS에서 받아옴**
   * DNS : 도메인 네임 서비스
2. 해당 주소의 IP로 **HTTP/GET** 메소드를 통해 요청
3. Google 서버는 

