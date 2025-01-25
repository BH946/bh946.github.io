---
title:  "[Java 컬렉션] Java Collection 정렬의 3가지 방법"
categories : Java
tag : [Java, Collections, Sort, Comparator, Lambda, Comparable]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**Java에서 컬렉션을 정렬하는 세 가지 주요 방법과 각각의 특징을 설명합니다. - Comparable Interface, Anonymous Class(Comparator), Lambda(Comparator)**

<br>

<br>

## 1. Comparable 인터페이스 구현

**사용자 클래스에 정렬 기준 정의**  
Comparable 인터페이스는 @Fun... 가지며, compareTo 를 추상메소드로 가짐

```java
public class Student implements Comparable<Student> {
    private int id;
    private String name;
    
    @Override
    public int compareTo(Student other) {
        return this.id - other.id;  // id 기준 오름차순
    }
}

Collection.sort(객체); //로 사용
```

<br>

<br>

## 2. Anonymous Class를 사용한 정렬

**Comparator 구현을 익명 클래스로**

```java
Collections.sort(a1, new Comparator<Item>() {
    @Override
    public int compare(Item o1, Item o2) {
        return o1.itemId  - o2.itemId;
    }
})
```

<br>

<br>

## 3. Lambda를 사용한 정렬

**Comparator 구현을 람다로 간략히**

```java
List<Student> students = new ArrayList<>();
// id 기준 정렬
Collections.sort(students, (s1, s2) -> s1.getId() - s2.getId());

// 이름 기준 정렬
Collections.sort(students, (s1, s2) -> s1.getName().compareTo(s2.getName()));

// 복합 조건 정렬 (삼항 연산자 사용)
Collections.sort(students, (s1, s2) -> 
    s1.getId() == s2.getId() ? 
    s1.getName().compareTo(s2.getName()) : 
    s1.getId() - s2.getId()
);
```

<br>

💡 **실무 추천 방식**

- 1번 정렬: Collections.sort() 로 사용 -> Student 이 스스로 정렬 규칙을 가짐
- 3번 정렬: Lambda 표현식 사용 (코드 간결성)

<span style="color:#777777">상황에 따라 적절한 정렬 방식을 선택하되, 가독성과 유지보수성을 고려하여 Lambda 표현식 사용을 권장합니다.</span>
