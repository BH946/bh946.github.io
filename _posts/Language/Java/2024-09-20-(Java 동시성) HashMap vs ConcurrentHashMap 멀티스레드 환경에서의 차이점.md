---
title:  "[Java 동시성] HashMap vs ConcurrentHashMap 멀티스레드 환경에서의 차이점"
categories : Java
tag : [Java, HashMap, ConcurrentHashMap, ThreadSafety, Concurrency]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**HashMap과 ConcurrentHashMap은 멀티스레드 환경에서 동시성 처리 방식에 큰 차이가 있습니다.**

<br>

<br>

## HashMap vs ConcurrentHashMap

1. HashMap

   ```java
   // 멀티스레드 환경에서 위험한 예시
   Map<String, String> hashMap = new HashMap<>();
   // 여러 스레드에서 동시 접근
   hashMap.put("key", "value");  // 데이터 무결성 보장 불가
   ```

   - 스레드 안전하지 않음

   - 동시 수정 시 데이터 손실 가능

2. ConcurrentHashMap

   ```java
   Map<String, String> concurrentMap = new ConcurrentHashMap<>();
   // 여러 스레드에서 안전하게 동시 접근 가능
   concurrentMap.put("key", "value");  // 동기화 보장
   ```

   - 스레드 안전 -> 동기화 지원

   - 세그먼트 단위 락 사용

<br>

💡 **성능 고려사항**

- 단일 스레드: HashMap이 더 빠름
- 멀티 스레드: ConcurrentHashMap이 안전하고 효율적

<span style="color:#777777">멀티스레드 환경에서는 데이터 정합성을 위해 ConcurrentHashMap 사용을 권장합니다.</span>
