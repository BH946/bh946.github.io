---
title: "[Error] XML-xmlParseEntityRef no name"
categories : [Err]
tag : [xml, 검색엔진, 경로에러, 특수문자]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..

---



# Error

**`xmlParseEntityRef no name`**

XML에서는 특수문자 `&,<,>,;` 등은 다르게 치환해서 사용해야한다.

예를 들어 '<'  는 ASCII 코드값(16진수)으로 3C이다. 이것을 문자 참조형태로 표현하면 `&#x3C;` 이다.

이런식으로 사용해야 에러를 방지할 수 있다.

**필자의 경우 XML에 게시물 경로와 제목까지 불러오는 코드가 있었는데 게시물 경로상에 `&`를 사용해서 해당 에러를 직면했다. 그래서 해당 문자를 제거했고, 에러가 해결되었다.**
