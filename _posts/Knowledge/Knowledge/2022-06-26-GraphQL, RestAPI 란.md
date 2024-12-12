---
title:  "GraphQL, RESTAPI 란?"  
categories : Knowledge
tag : [GraphQL, RESTfull, rest, API]  
toc: true  
toc_sticky: true  
author_profile: false  
sidebar:  
 nav: "docs"    
---



**REST 방식의 Over Fetching, Under Fetching 문제와 이를 해결하는 GraphQL 질의 언어를 알아보자.**

<br>

<br>

## API

* 애플리케이션 프로그래밍 인터페이스(=API)
* 소프트웨어와 소프트웨어 사이를 소통하기 위한 인터페이스. 즉, **정보**들을 **요청**해서 **응답** 받는데 지정된 **표준 형식**
  * 형식이 지정되어 있지 않아도 요청에 응답을 받을 수야 있겠지만 개발이나 협	업할 때 힘들 것이다. 따라서 지정된 형식이 필요해서 나온 것이 API이다.

<br>

<br>

## Rest

* URI가 도메인`/classes/2/students/15..` 이런 형태에 명사만 가지는 것이 Rest 형식이다.
* URI만 봐도 어떤 작업인지 대충 알 수 있다.
  * 예: "2반 15번 학생 정보 조회"가 `/classes/2/students/15`라는 URI에 다 들어 있다.
* HTTP 규약에 맞춰서 동작하고, 주로 쓰는 메서드는 다음과 같다:
  * **GET**: 데이터 조회
  * **POST**: 데이터 전송
  * **PUT**: 데이터 수정
  * **DELETE**: 데이터 삭제
* 또한 다양한 규칙들이 존재하므로 자세한 것은 구글링!!
* 하지만 Rest에는 단점이 있다:
  - **Over Fetching**: 필요한 것보다 데이터를 너무 많이 받는 문제.
  - **Under Fetching**: 필요한 데이터를 얻으려면 여러 번 요청해야 하는 문제.

<br>

<br>

## GraphQL

* GraphQL은 API 서버랑 데이터 주고받는 질의 언어다.
* 페이스북(=메타)이 2012년에 개발하여 2015년에 GraphQL-spec을 오픈소스
  * GraphQL-spec이란 GraphQL이 작성된 규칙의 종류를 뜻하며 이를 오픈소스로!
* GraphQL은 아이디어(개념)이기 때문에 다운받을 수 있는 건 implementation(구현체)뿐이다.
  * implementations는 GraphQL의 spec규칙을 코드로 바꿔준 것이다.
* 쿼리SQL처럼 질의어로 필요한 데이터만 골라서 요청할 수 있다.
  * 그래서 Rest의 **Over Fetching, Under Fetching 문제를 해결**할 수 있다.

<br><br>

### Over Fetching

필요한 정보보다 더 많은 데이터를 받는 상황이다.

GraphQL은 요청할 데이터만 딱 골라서 받을 수 있다.

<br><br>

### Under Fetching

필요한 데이터를 얻으려 여러 번 요청해야 하는 상황이다.

GraphQL은 한 번 요청으로 여러 데이터를 가져올 수 있다.

<br>

<br>

## 정리하자면

Rest는 서버가 중심이고, 클라이언트가 그에 맞춰 개발해야 한다.

GraphQL은 클라이언트가 필요한 데이터를 정확히 요청할 수 있다. (서버가 sql문 쓰는 것처럼 프론트도 sql문 쓰는 것)

그래도 초기 설정이나 학습이 어려워서 Rest를 아직 많이 쓴다.

<br>

<br>

## 참고 자료

[REST API](https://www.youtube.com/watch?v=iOueE9AXDQQ)
[GraphQL API](https://www.youtube.com/watch?v=N-81mS2vldIa)
