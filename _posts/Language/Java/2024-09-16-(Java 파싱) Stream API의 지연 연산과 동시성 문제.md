---
title:  "[Java 파싱] Stream API의 지연 연산과 동시성 문제"
categories : Java
tag : [Java, StreamAPI, LazyEvaluation, Concurrency, ThreadSafety]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**Stream API는 지연 연산(lazy evaluation) 방식을 사용하며, 이로 인해 발생할 수 있는 동시성 문제와 해결 방안을 설명합니다.**

<br>

<br>

## Stream의 지연 연산과 동시성 문제

**Stream은 실행시점에 값을 읽는게 아니라 참조(주소)를 읽어 둔다. 즉, 지연 연산을 사용한다.**

**따라서 이미 데이터를 다 읽은 상태(즉시 연산)가 아니라서 문제가 발생할 수 있는것이다.**

1. `names.stream()`이 호출되면 스트림이 생성되지만, 아직 데이터는 처리X
2. `.filter(...)`와 같은 연산은 중간 연산이고, 최종 연산(`.collect(...)`)이 호출될 때 실제로 데이터를 처리!
3. 이 과정에서 스트림은 데이터 소스를 반복(iterate)하며 필요한 작업을 수행

<br>

ArrayList 는 수정(추가, 삭제 등)될 경우, 반복 중에 ConcurrentModificationException 예외를 던지도록 설계되어 있다.

**따라서 Stream 연산은 내부적으로 반복(iteration)을 수행하기 때문에, 구조적 수정이 감지되면 예외가 발생하고, 예외가 발생 안 해도 순서나, 데이터가 꼬여서 비정상적인 결과가 나타날 수 있다.**

```java
// 반복 중 데이터 제거로 인한 예외 발생 (다른 스레드에서 수정한 경우도 마찬가지로 예외 발생)
List<String> names = new ArrayList<>(List.of("Alice", "Bob", "Charlie"));
names.stream().forEach(name -> names.remove(name)); // ConcurrentModificationException
```

<br>

<br>

## 해결 방안

**1. 복사본 사용: 스트림 연산 전에 데이터의 복사본을 만들어 사용**

```java
List<String> result = new ArrayList<>(names).stream()
    .filter(name -> name.length() > 3)
    .collect(Collectors.toList());
```

<br>

**2. 불변 객체 사용: 외부 수정 가능성이 원천 차단**

```java
List<String> names = List.of("Alice", "Bob", "Charlie");
List<String> result = names.stream()
    .filter(name -> name.length() > 3)
    .collect(Collectors.toList());
```

<br>

**3. 동기화 사용: 줄 세우기. 단, 성능 저하 발생 가능**

```java
synchronized (names) {
    List<String> result = names.stream()
        .filter(name -> name.length() > 3)
        .collect(Collectors.toList());
}
```

💡 **권장 사항**

- 가능한 불변 객체 사용
- 필요한 경우 복사본 생성
- 동기화는 성능 저하를 고려하여 신중히 사용

<span style="color:#777777">스트림의 지연 연산 특성을 이해하고 적절한 동시성 제어 방법을 선택하는 것이 중요합니다.</span>
