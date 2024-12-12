---
title: "xmlParseEntityRef no name - XML"
categories : [Err]
tag : [xml, 검색엔진, 경로에러, 특수문자]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..

---



`xmlParseEntityRef no name`

XML에서는 특수문자 `&,<,>,;` 등은 다르게 치환하여 사용해야 한다.

예를 들어 '<'는 ASCII 코드값(16진수)으로 3C이다. 이것을 문자 참조 형태로 표현하면 `&#x3C;`이다.

이와 같은 방식으로 사용해야 오류를 방지할 수 있다.

**필자의 경우, XML에서 게시물 경로와 제목을 불러오는 코드가 있었는데, 게시물 경로에 `&`를 사용하여 해당 오류를 직면했다. 이후, 해당 문자를 제거하여 오류를 해결했다.**
