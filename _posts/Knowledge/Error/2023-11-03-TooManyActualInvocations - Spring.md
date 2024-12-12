---
title: "TooManyActualInvocations - Spring"
categories : [Err]
tag : [spring, 스프링, 스프링부트]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..

---



`org.mockito.exceptions.verification.TooManyActualInvocations: `

TDD 작성 중 발생한 verify() 에러이다.

검증 대상 서비스 메서드가 여러 번 호출되는 경우, 이를 times()를 사용하여 호출 횟수를 명시해야 에러를 방지
