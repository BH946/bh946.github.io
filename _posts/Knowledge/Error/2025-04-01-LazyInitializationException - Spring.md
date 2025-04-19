---
title: "LazyInitializationException - Spring"
categories : [Err]
tag : [JPA]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**DTO에서 lazy 강제 초기화 해야한다.** 

안하면 `LazyInitializationException` 에러

- 강제 초기화를 안하면 fetch join으로 모두 조회 했어도 해당 엔티티를 찾을 수 없게 되어서 null이 응답!! -> "영속성에 등록하지 않아서. 즉, 메모리에 올려두지 않아서."
