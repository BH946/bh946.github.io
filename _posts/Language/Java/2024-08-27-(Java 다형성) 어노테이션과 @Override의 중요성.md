---
title:  "[Java 다형성] 어노테이션과 @Override의 중요성"
categories : Java
tag : [Java, Annotation, Override, Metadata, 오버라이딩]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**어노테이션(Annotation)은 코드에 대한 메타데이터를 제공하는 Java의 중요한 기능이며, 특히 @Override는 메소드 오버라이딩의 안전성을 보장합니다.**

<br>

<br>

## 어노테이션(Annotation)의 개념

**사전적 의미: 주석**

- 코드에 대한 메타데이터(데이터를 설명하는 데이터) 제공
- 컴파일러, JVM, 프레임워크 등이 보는 주석
  - 예로 @Override의 경우 컴파일러에게 해당 메서드는 오버라이딩한 메서드임을 알려줌

<br>

<br>

## @Override 어노테이션

**핵심 기능: 오버라이딩 "강제" 효과**

```java
class Parent {
    void display() { }
}

class Child extends Parent {
    @Override //반드시 오버라이딩
    void display() { }
}
```

<br>

**사용 이점**

- 의도하지 않은 메소드 추가 방지: @Override 가 없다면, 정상적으로 자식 메소드가 1개 생성 됨
- 실수로 인한 시그니처 불일치 예방: @Override 를 붙이면 부모의 메소드가 변경될 때 자식 메소드에는 오류로 알려 줌
- 코드의 가독성과 유지보수성 향상

<br>

<span style="color:#777777">@Override 어노테이션은 코드의 안정성을 높이고 잠재적인 버그를 조기에 발견할 수 있게 해주는 중요한 도구입니다.</span>
