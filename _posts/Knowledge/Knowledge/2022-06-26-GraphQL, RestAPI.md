---
title:  "GraphQL, REST, API"  
categories : Knowledge
tag : [GraphQL, RESTfull, rest, API]  
toc: true  
toc_sticky: true  
author_profile: false  
sidebar:  
 nav: "docs"    
---



## API

* 애플리케이션 프로그래밍 인터페이스(=API)
* 소프트웨어와 소프트웨어 사이를 소통하기위한 인터페이스
  즉, **정보**들을 **요청**해서 **응답** 받는데 지정된 **형식**
  * 형식이 지정되어 있지 않아도 요청에 응답을 받을 수야 있겠지만 개발이나 협업할 때 힘들것이다. 따라서 지정된 형식이 필요해서 나온 것이 API이다.

<br><br>

## Rest

* URI가 도메인`/classes/2/students/15..` 이런 형태에 명사만 가지는것이 Rest 형식이다.
* 각 요청의 모습 자체로 어떤 요청인지 추론이 가능하다는 장점이 있다.
  * 예로 위 URI의 경우 2반의 15번 학생 정보 조회라는것을 간단히 알 수 있다.
* HTTP 규약에 따라 신호를 전송하며 주로 사용 메소드는 GET, POST이며, PUT, DELETE 등등이 있다.
  * 조회는 GET, 전송은 POST 처럼 목적에 따라 구분해서 사용한다.
* 또한 다양한 규칙들이 존재하므로 자세한것은 구글링!!
* Rest API 문제점은 `over Fetching`과 `Under Fetching`이다.
  * 아래에서 설명하겠다.


<br><br>

## GraphQL

* **GraphQL이란 쿼리 언어이며, API 서버 런타임으로서 데이터를 제공합니다.**
* 페이스북(=메타)이 2012년에 개발하여 2015년에 GraphQL-spec을 오픈소스
  * GraphQL-spec이란 GraphQL이 작성된 규칙의 종류를 뜻하며 이를 오픈소스로!
* GraphQL은 아이디어(개념)이기 때문에 다운받을 수 있는건 implementation(구현)뿐이다.
  * implementations는 GraphQL의 spec규칙을 코드로 바꿔준 것이다.
* 쿼리 언어이기 때문에 SQL처럼 질의어 사용이 가능하다는 장점이 있다.
  * 따라서 `Over Fetching, Under Fetching`을 해결이 가능하다.

<br>

### Over Fetching

`Over Fetching` 은 원하는 정보를 한번 요청했는데, 다량의 필요없는 정보들을 함께 받는 경우이다.

그러나 이를 쿼리 언어를 사용해 정확히 필요한 정보만 가져올 수 있는 장점

<br>

### Under Fetching

`Under Fetching` 은 특정 정보를 얻기위해 요청을 여러번 하는 경우이다.

그러나 이를 쿼리 언어를 사용해 여러 정보들도 한번의 요청으로 가져올 수 있는 장점

<br><br>

## 정리하자면,

기존 `Rest` 는 서버쪽에 맞추어서 프론트가 개발을 해야한다면, 
`GraphQL` 은 서버가 sql문 쓰는것처럼 프론트도 sql문 쓰는것이 가능해서 개발하기가 더 편하다.

다만, 단점들도 있기 때문에 아직까지도 Rest를 많이 사용한다. 자세한 내용은 구글링!!

<br><br>

## 참고한 자료

[REST API](https://www.youtube.com/watch?v=iOueE9AXDQQ)
[GraphQL API](https://www.youtube.com/watch?v=N-81mS2vldIa)
