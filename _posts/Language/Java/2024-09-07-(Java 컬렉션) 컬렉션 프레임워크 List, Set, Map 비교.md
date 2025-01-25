---
title:  "[Java 컬렉션] 컬렉션 프레임워크 List, Set, Map 비교"
categories : Java
tag : [Java, Collection, List, Set, Map, DataStructure]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**Java의 컬렉션 프레임워크는 데이터 저장과 처리를 위한 List, Set, Map 인터페이스를 제공하며, 각각 고유한 특징과 사용 목적을 가집니다. 특히, 사용 자료구조도 다릅니다. 예를들어 TreeSet, TreeMap의 경우 RBT(레드블랙트리) 자료구조를 사용합니다.**

<br>

**List, Set, Map 중 무엇을 사용할 지 고민 후 HashSet, TreeSet, LinkedHashSet 처럼 사용할 자료구조를 선택 하자.**

- HashSet은 해시 자료구조를 사용
- TreeSet은 RBT 자료구조를 사용
- 이런식으로 각자 구현된 자료구조도 다르므로 원하는걸 선택하자.
  - 정렬이 필요하다면 TreeSet을 쓰겠죠?

<br>

<br>

## List 인터페이스

**특징**

- **순차적** 데이터 저장
- 중복 허용
- null 값 허용

```java
List<String> list = new ArrayList<>();
list.add("apple");
list.add("apple");  // 중복 허용
list.add(null);     // null 허용
```

<br>

<br>

## Set 인터페이스

**특징**

- **순서없이** Key로만 데이터를 저장
- 중복 불허용
- null 값 불허용

```java
Set<String> set = new HashSet<>();  // 해시테이블 기반
set.add("apple");
set.add("apple");  // 중복 무시

// TreeSet은 레드블랙트리 기반으로 정렬 지원
Set<String> treeSet = new TreeSet<>();
```

<br>

<br>

## Map 인터페이스

**특징**

- **순서없이** Key, Value로 데이터를 저장
- 키의 중복 불허용 (값은 중복 허용)
- 키로 null 사용 불허용

```java
Map<String, Integer> map = new HashMap<>();
map.put("apple", 1);
map.put("banana", 2);
map.put("apple", 3);  // 키 중복 시 값 덮어쓰기
```

<br>

**한 눈에 보기**

| 특성 | List | Set  | Map        |
| ---- | ---- | ---- | ---------- |
| 순서 | O    | X    | X          |
| 중복 | O    | X    | 키:X, 값:O |
| null | O    | X    | 키:X       |

<span style="color:#777777">각 인터페이스의 특징을 이해하고 사용 목적에 맞는 적절한 구현체를 선택하는 것이 중요합니다.</span>
