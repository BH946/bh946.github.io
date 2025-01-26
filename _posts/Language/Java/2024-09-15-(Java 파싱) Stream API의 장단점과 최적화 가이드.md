---
title:  "[Java 파싱] Stream API의 장단점과 최적화 가이드"
categories : Java
tag : [Java, StreamAPI, Functional, Performance, Optimization]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**Stream API는 컬렉션 데이터를 함수형 프로그래밍 방식으로 처리할 수 있게 해주는 Java 8의 핵심 기능입니다.**

**자바 IO의 Stream과 아래의 Stream API는 다르다. 아래 사진은 Stream 인터페이스 모습과 “사용 모습”**

![Stream](https://github.com/user-attachments/assets/d09f26a1-42a9-4ab0-bf1b-6fb33244ab03) 

<br>

<br>

## Stream API 기본 특징

### 생성 방법

```java
// 배열로부터 스트림 생성
String[] arr = {"a", "b", "c"};
Stream<String> stream = Arrays.stream(arr);

// 컬렉션으로부터 스트림 생성
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
Stream<String> stream = names.stream();

// 스트림 활용 예시
List<String> result = names.stream()
                           .filter(name -> name.length() > 3)
                           .collect(Collectors.toList());
```

<br><br>

### 장점

- 코드 가독성 향상
- 함수형 프로그래밍 지원
- 병렬 처리 용이

<br><br>

### 단점

- 잘못된 사용 시 성능 저하 
  - 불 필요한 중간 연산, 데이터 셋 작을 때 병렬, 가변 객체 사용
- 높은 추상화로 실수 발생 가능

<br>

<br>

## 최적화 예시

### 중간 연산 최적화

```java
// 1-1.불 필요한 중간 연산
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
names.stream()
     .filter(name -> name.length() > 3)
     .filter(name -> name.startsWith("A"))
     .map(String::toUpperCase)
     .forEach(System.out::println);
     
// 1-2.중간 연산 최소화
names.stream()
     .filter(name -> name.length() > 3 && name.startsWith("A"))
     .map(String::toUpperCase)
     .forEach(System.out::println);
```

<br><br>

### 병렬 처리 최적화

```java
// 2.데이터 셋이 큰 경우 병렬 스트림 사용 (작은 경우 비추)
List<String> largeList = new ArrayList<>(10000);
largeList.parallelStream()
         .forEach(System.out::println);
```

<br><br>

### 불변 객체 사용

```java
// 3.불변 객체 사용 -> names 가 불변 객체가 아니라면? 동기화 비용으로 성능 저하
List<String> result = names.stream()
                           .filter(name -> name.length() > 3)
                           .collect(Collectors.toList());
```

<br>

💡 **성능 최적화 팁**

- 불필요한 중간 연산 제거
- 적절한 데이터 크기에서만 병렬 스트림 사용
- 불변 객체 사용으로 동기화 비용 감소

<span style="color:#777777">Stream API는 강력하지만 상황에 맞는 적절한 사용이 중요합니다.</span>

