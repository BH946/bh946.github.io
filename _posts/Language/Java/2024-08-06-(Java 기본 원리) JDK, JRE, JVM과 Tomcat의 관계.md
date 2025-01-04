---
title:  "[Java 기본 원리] JDK, JRE, JVM과 Tomcat의 관계"
categories : Java
tag : [java, jdk, jre, jvm, tomcat, was]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**Java 개발 환경은 JDK, JRE, JVM이 계층적 구조를 이루며, Tomcat은 이 환경 위에서 동작하는 Java 기반 WAS다.**

<br>

<br>

## JDK / JRE / JVM 포함 관계 💡

![JDK/JRE/JVM](https://github.com/user-attachments/assets/aab8afff-44b6-49a5-a19a-683b0cebc40b) 

- **JDK (Java Development Kit)**
  - 가장 큰 범위의 개발 도구

- **JRE (Java Runtime Environment)**

  - Java 실행 환경

  - 실제 서버 구동할 때 JRE만 있어도 충분(javac 없어도 됨) → `.class`파일 바로 구동 가능
  - Java Class Library → 자바 기본 제공 클래스 (ex:String)

<br>

<br>

## Tomcat과 JVM의 관계 ⚡

**Tomcat의 특징**

- Java로 작성된 WAS(Web Application Server)
  - 톰캣이란 이름의 자바 클래스로 구성된 로직으로 볼 수 있겠다.
- JVM 위에서 동작하여 Java 웹 애플리케이션 관리

<br>

**동작 방식**

- 단일 프로세스로 실행
- 요청 처리를 스레드로 할당
- 기본 스레드 풀: 200개
