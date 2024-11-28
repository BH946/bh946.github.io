---
title: "[Error] Spring-JdbcSQLNonTransientConnectionException"
categories : [Err]
tag : [spring, 스프링, 스프링부트]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..

---



# Error

`org.h2.jdbc.JdbcSQLNonTransientConnectionException: Connection is broken: "java.net.ConnectException: Connection refused: connect: localhost" [90067-214]`

**이런 에러의 경우 DB와 Connect(연결)이 제대로 안 되었다는 것**

**본인은 H2 DB를 실행하지 않아서 발생한 간단한 문제였다!  
h2를 실행 시 해결 완료**
