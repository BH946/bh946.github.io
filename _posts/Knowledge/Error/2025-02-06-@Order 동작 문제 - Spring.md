---
title: "@Order 동작 문제 - Spring"
categories : [Err]
tag : [spring, 검색엔진, 경로에러, 특수문자]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---





**@Order(1)** 애노테이션이 안되는것 같을 때 `import org.junit.jupiter.api.Order;` 로 import한게 맞는지 체크하자.

안되는 경우는 core쪽 import 했을때 안되는걸 확인했다.
