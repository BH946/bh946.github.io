---
title: "NoSuchBeanDefinitionException - Spring"
categories : [Err]
tag : [Spring, Java, Bean, 빈]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**빈을 못 찾는 NoSuchBeanDefinitionException 에러주의:** 예로 테스트코드에서 Mock사용할 때 주의해야 한다!

컨트롤러에서 사용한 레포,서비스 등등 은 꼭 전부 Mock해주자. 테스트코드에는 없고 컨트롤러에서만 사용중이어도 무조건 **@MockBean 등록은 일단 필수다!!**

- **예로 Mock을 활용한 컨트롤러 테스트 코드를 작성할때 @Test register() {...} 로직에 memberService만 사용**하고 exp, character서비스는 사용하지 않았다.
- 근데, 실제 register() 로직인 "회원가입 컨트롤러 메서드" 코드를 보면 exp, character서비스 전부 사용한다.
- **따라서 exp, character서비스 빈을 찾을 수 없다는 에러가 발생한다.**

