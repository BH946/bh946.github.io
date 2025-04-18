---
title:  "URI, URL, URN 및 REST API 완벽 가이드"
categories : [Computer_Network]
tag : [URI,URL,URN,REST,API,웹개발,네트워크]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**URI, URL, URN의 개념적 차이와 REST API의 원리 및 설계 규칙에 대한 종합적인 설명이다.**

<br>

<br>

## URI, URL, URN의 이해

인터넷 상의 자원을 식별하고 접근하는 방법에는 URI, URL, URN이라는 세 가지 개념이 있다. 이들은 서로 연관되어 있지만 각각 다른 목적을 가지고 있다.

<img src="/images/2024-06-08-URI, URL, URN 및 REST API 완벽 가이드/image-20250418185646988.png" alt="image-20250418185646988" style="zoom:80%;" /> 

**URI (Uniform Resource Identifier)**

URI는 인터넷 상의 자원을 식별하기 위한 통합 자원 식별자다.

- **정의**: 인터넷에 있는 자원을 식별하는 유일한 방법
- **구성**: `scheme:[//authority]path[?query][#fragment]`
- **특징**: URL과 URN을 모두 포함하는 상위 개념

<span style="color:#ff9300">URI는 자원을 고유하게 식별할 수 있는 문자열로, 인터넷의 모든 자원에 대한 참조 방식이다.</span>

<br>

**URL (Uniform Resource Locator)**

URL은 인터넷 상의 자원 위치를 나타내는 웹 주소다.

- **정의**: 네트워크 상에서 자원이 어디 있는지 위치를 알려주기 위한 규약
- **구성**: `scheme://host[:port]/path[?query][#fragment]`
- **특징**: 자원에 접근하는 방법(프로토콜)과 위치를 모두 포함

💡 **URL의 핵심 요소**:

- **scheme**: 자원에 접근하는 프로토콜(http, https, ftp 등)
- **host**: 자원이 위치한 서버의 도메인 이름 또는 IP 주소
- **port**: 서버에 접속하기 위한 포트 번호(생략 가능)
- **path**: 서버 내 자원의 경로

<br>

**URN (Uniform Resource Name)**

URN은 자원의 위치에 관계없이 이름으로 식별하는 방식이다.

- **정의**: 자원의 위치에 독립적인 이름을 부여하는 방식
- **구성**: `urn:namespace:specific-identifier`
- **예시**: `urn:isbn:8960777331` (특정 책의 ISBN)
- **특징**: 자원의 위치가 변경되어도 동일한 자원을 식별 가능

<span style="color:#ff9300">URN은 자원의 위치가 아닌 고유한 이름을 통해 자원을 식별하지만, 실제로는 URL에 비해 사용 빈도가 낮다.</span>

<br><br>

### URI, URL, URN 관계 및 예시

URI는 URL과 URN을 포함하는 상위 개념이다. 대부분의 웹 환경에서는 URL을 주로 사용하기 때문에 URI와 URL을 혼용하여 사용하는 경우가 많다.

- URI : 인터넷 상 자원 식별 개념
- URL : 인터넷 상 자원의 **위치(Locator)**
- URN : 인터넷 상 자원의 **이름(Name)**
- 예시 : http://www.naver.com/191?name=tt
  - URL : http://www.naver.com/191?name=t
  - URN : urn:isbn:8960777331 (어떤 책의 isbn URN)

<br>

<br>

## REST API의 이해

REST API는 웹 서비스 개발에서 널리 사용되는 아키텍처 스타일로, 자원 기반의 구조적 접근 방식을 제공한다.

**REST(Representational State Transfer)**는 자원을 이름으로 구분하여 자원의 상태를 주고받는 웹 아키텍처 스타일이다.

- **정의**: 자원을 이름으로 구분하고 HTTP 메서드를 통해 자원의 상태를 전송하는 방식
- **특징**: HTTP URI를 통해 자원을 명시하고, HTTP 메서드로 CRUD 연산을 수행
- **목적**: 클라이언트-서버 간 통신을 위한 구조적이고 일관된 인터페이스 제공

<span style="color:#ff9300">REST는 웹의 기존 기술과 HTTP 프로토콜을 그대로 활용하므로 웹의 장점을 최대한 활용할 수 있는 아키텍처다.</span>

<br>

**API(Application Programming Interface)**는 소프트웨어 간 상호작용을 위한 인터페이스다.

- **정의**: 애플리케이션 소프트웨어를 구축하고 통합하기 위한 정의와 프로토콜 세트
- **목적**: 서로 다른 소프트웨어 구성 요소가 서로 통신할 수 있게 하는 메커니즘
- **필요성**: 지정된 형식 없이는 개발이나 협업이 어려우므로, 표준화된 인터페이스가 필요

<br><br>

### REST API의 구성 요소

REST API는 다음 세 가지 주요 구성 요소로 이루어진다.

1. **Resource(자원, URI)**
   - 모든 자원은 고유한 URI로 식별
   - 예: `/users`, `/products/123`
2. **Method(요청 방식)**
   - HTTP 메서드를 사용하여 자원에 대한 행위 정의
   - 주요 메서드: GET(조회), POST(생성), PUT(수정), DELETE(삭제)
3. **Representation of Resource(자원의 표현)**
   - 클라이언트와 서버가 데이터를 주고받는 형태
   - 일반적으로 JSON, XML 등의 형식 사용

<span style="color:#ff9300">REST API는 이 세 가지 요소를 통해 웹 서비스의 자원을 명확하게 정의하고 접근하는 방법을 제공한다.</span>

<br><br>

### REST API 설계 규칙

REST API를 설계할 때는 다음과 같은 규칙을 준수하는 것이 좋다.

#### **URI 설계 규칙**

1. **명사 사용**: URI는 동사보다 명사를 사용하여 자원을 표현

   ```
   ❌ /getUsers
   ✅ /users
   ```

2. **소문자 사용**: URI는 대문자보다 소문자를 사용

   ```
   ❌ /Users
   ✅ /users
   ```

3. **슬래시 사용**: 마지막에 슬래시('/')를 포함하지 않음

   ```
   ❌ /users/
   ✅ /users
   ```

4. **하이픈 사용**: 언더바('_') 대신 하이픈('-') 사용

   ```
   ❌ /user_profiles
   ✅ /user-profiles
   ```

5. **파일 확장자 제외**: 파일 확장자는 URI에 포함하지 않음

   ```
   ❌ /users/photo.jpg
   ✅ /users/photo
   ```

<br>

#### **HTTP 메서드 활용**

REST API에서는 HTTP 메서드를 통해 자원에 대한 행위를 정의한다.

- **GET**: 자원 조회
- **POST**: 자원 생성
- **PUT**: 자원 전체 수정
- **PATCH**: 자원 부분 수정
- **DELETE**: 자원 삭제

```
GET /users         # 사용자 목록 조회
GET /users/123     # 특정 사용자 조회
POST /users        # 새 사용자 생성
PUT /users/123     # 특정 사용자 정보 전체 수정
PATCH /users/123   # 특정 사용자 정보 일부 수정
DELETE /users/123  # 특정 사용자 삭제
```

⚡ **RESTful API 설계의 핵심**은 자원을 명확하게 식별하고, 적절한 HTTP 메서드를 사용하여 자원에 대한 행위를 표현하는 것이다.

<span style="color:#777777">URI, URL, URN의 개념을 이해하고 REST API 설계 원칙을 준수하면 더 명확하고 일관된 웹 서비스를 구축할 수 있다. 이는 개발자 간의 협업을 용이하게 하고 API 사용자에게 직관적인 인터페이스를 제공한다.</span>
