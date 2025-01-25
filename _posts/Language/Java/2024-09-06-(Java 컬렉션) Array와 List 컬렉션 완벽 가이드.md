---
title:  "[Java 컬렉션] Array와 List 컬렉션 완벽 가이드"
categories : Java
tag : [Java, Array, ArrayList, LinkedList, Collection, DataStructure]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**배열(Array)과 리스트(List)는 Java의 핵심 데이터 구조로, 각각 정적/동적 길이라는 특징을 가집니다. - 추가로 ArrayList 와 LinkedList를 비교해 보겠습니다.**

<br>

<br>

## 배열 (Array) vs 리스트(List)

**배열(Array) 특징과 초기화**

- **고정 길이**
- **연속된 메모리 할당**
- length 속성으로 길이 확인 -> String은 length(), char[]는 length 였음

```java
// 배열 초기화 방법
int[] arr1 = {1, 2, 3};
int[] arr2 = new int[3];
Arrays.fill(arr2, 0);  // 배열 전체 초기화
```

<br>

**리스트(List) 특징과 초기화**

- **동적 길이**
- **메모리 할당이 인터페이스 구현체에 따라 다름**
  - List는 인터페이스 이고, 이의 대표적인 구현체가 ArrayList, LinkedList가 있음
  - 자세한 내용은 아래 ArrayList vs LinkedList 파트 참고
- size() 메서드로 길이 확인

<br><br>

## ArrayList vs LinkedList

**ArrayList**

- 내부적으로 배열 사용 -> 즉, **무작위 접근 가능**
  - 배열을 1차로 쓰고, 연결리스트를 2차로 각 배열 인덱스에 연결되어 있는 구조!
- 랜덤(무작위) 접근 O(1)
- 삽입/삭제 O(n) -> 배열(연속된 메모리)로 인해 느림

```java
List<String> list = new ArrayList<>();
list.add("apple");
list.get(0);      // 랜덤(무작위) 접근
list.set(0, "banana");  // 값 변경
```

<br>

**LinkedList**

- 노드 기반 연결 구조 -> 즉, **무작위 접근 불가능**
- 랜덤 접근 O(n) -> 연결리스트만으로 인해 느림
- 삽입/삭제 O(1)

```java
List<Integer> list = new LinkedList<>();
list.add(1);          // 끝에 추가
list.addFirst(0);     // 시작에 추가
list.addLast(2);      // 끝에 추가
list.remove(1);       // 인덱스 1 제거
```

<br>

<br>

## Array ↔ List 변환

```java
// 문자열 배열을 List로 변환
String[] temp = "abcde";
List<String> list = new ArrayList<>(Arrays.asList(temp));

// List를 문자열 배열로 변환
List<String> list = new ArrayList<>();
String[] temp = list.toArray(new String[list.size()]);

// 정수 배열을 List로 변환
int[] temp = { 123, 789, 456 };
List<Integer> list = new ArrayList<>(Arrays.asList(temp));

// List를 정수 배열로 변환
List<Integer> list = new ArrayList<>();
int[] temp = list.stream().mapToInt(i->i).toArray();

// 한번에 초기화하는 fill 메소드 - Arrays, Collections
int[] myArray = new int[10];
Arryas.fill(myArray, 0); // 0으로 init
List<Integer> myList = new ArrayList<>(10);
Collections.fill(myList, null); // null로 init
```

💡 **유용한 팁**

- 중복 검사: `list.indexOf(value) < 0` => 중복 없으면 -1 리턴
- 전체 초기화: `Collections.fill(list, value)`
- 스트림 변환: `list.stream().mapToInt(i->i).toArray()`

<span style="color:#777777">상황에 따라 적절한 자료구조를 선택하여 사용하는 것이 성능 최적화의 핵심입니다.</span>
