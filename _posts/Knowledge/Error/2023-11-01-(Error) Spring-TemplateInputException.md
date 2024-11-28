---
title: "[Error] Spring-TemplateInputException"
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

`org.thymeleaf.exceptions.TemplateInputException: Error resolving template [/spring/swagger/swagger], template might not exist or might not be accessible by any of the configured Template Resolvers`

리눅스의 경로 문제!!  
html 리턴할 때 **return /studio;** 형태가 아닌 **return studio;**   
=> 절대 경로 사용하지 말라는 것

다음으로 **spring.thymeleaf.prefix: classpath:/templates/**   
=> 리눅스용 템플릿 위치 설정인데 이것도 반드시 필요!!

물론 **spring.thymeleaf.prefix: file:src/main/resources/templates/**  
=> 윈도우용 템플릿 위치 설정!
