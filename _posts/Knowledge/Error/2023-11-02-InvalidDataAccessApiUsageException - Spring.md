---
title: "InvalidDataAccessApiUsageException - Spring"
categories : [Err]
tag : [spring, 스프링, 스프링부트]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..

---



`InvalidDataAccessApiUsageException org.hibernate.TransientPropertyValueException`

보통 FK 오류이다.. FK를 줘야할 객체의 id가 null이라서 이런 문제가 발생!!

영속성 전이를 위해 cascade type을 지정해서 해결

본인은 보통 먼저 생성해서 쓰고있긴 하다.
