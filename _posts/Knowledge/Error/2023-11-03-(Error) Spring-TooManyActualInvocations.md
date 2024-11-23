---
title: "[Error] Spring-InvalidDataAccessApiUsageException"
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

`org.mockito.exceptions.verification.TooManyActualInvocations: `

TDD 작성 중 verify() 에러이다.

검증하는 서비스 메소드가 여러 번 불리는 경우라면 이를 times()로 몇 번째 메소드인지 지정해줘야 에러를 방지할 수 있다.
