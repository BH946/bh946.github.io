---
title:  "[Java 동시성] Vector, ArrayList 차이와 StringBuffer, StringBuilder 차이"
categories : Java
tag : [Java, Vector, ArrayList, StringBuffer, StringBuilder, Synchronization, ThreadSafety]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**컬렉션과 문자열 처리에서 동기화 지원 여부에 따른 클래스들의 특징과 차이점을 설명합니다.**

<br>

<br>

## 컬렉션 클래스 비교

**Vector vs ArrayList**

| 특성          | Vector      | ArrayList   |
| ------------- | ----------- | ----------- |
| 동기화        | 지원        | 미지원      |
| 성능          | 상대적 느림 | 상대적 빠름 |
| 스레드 안전성 | 안전        | 불안전      |

```java
// 스레드 안전한 Vector 사용
Vector<String> vector = new Vector<>();

// 빠른 처리가 필요한 경우 ArrayList 사용
ArrayList<String> arrayList = new ArrayList<>();
```

<br>

<br>

## 문자열 처리 클래스 비교

**StringBuffer vs StringBuilder**

| 특성          | StringBuffer | StringBuilder |
| ------------- | ------------ | ------------- |
| 동기화        | 지원         | 미지원        |
| 성능          | 상대적 느림  | 상대적 빠름   |
| 스레드 안전성 | 안전         | 불안전        |

```java
// 멀티스레드 환경에서 안전한 StringBuffer
StringBuffer buffer = new StringBuffer();
buffer.append("thread").append("safe");

// 단일스레드에서 빠른 StringBuilder
StringBuilder builder = new StringBuilder();
builder.append("fast").append("performance");
```

<br>

💡 **선택 기준**

- 멀티스레드 환경: Vector, StringBuffer
- 단일스레드 환경: ArrayList, StringBuilder

<span style="color:#777777">상황에 맞는 적절한 클래스 선택이 성능과 안정성의 핵심입니다.</span>
