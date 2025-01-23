---
title:  "[Java 상속] equals 오버라이딩 가이드 - equals(), hashCode(), toString()"
categories : Java
tag : [Java, Object, equals, hashCode, toString, Override, HashSet, HashMap]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**equals()를 오버라이드 하기 위해 Object 클래스의 기본 메서드인 equals(), hashCode(), toString()의 재정의를 설명한다.**

<br>

<br>

## 비교할 때 equlas() 재정의 이유는?

**아래 비교 예시를 보면 "=="만 으로 정확히 비교하기 어렵다는 것을 알 수 있다.**

**String equals vs ==**

```java
String s1 = new String("test");
String s2 = new String("test");
System.out.println(s1.equals(s2));  // true (값 비교)
System.out.println(s1 == s2);       // false (주소 비교)
System.out.println("test" == "test"); // true (리터럴은 동일 주소 사용)
```

<br>

<br>

## equals(), hashCode() 오버라이딩

**왜 hashCode() 까지 오버라이딩을 하냐면,**  
**HashSet, HashMap 등은 hashCode() 먼저 비교 후 equals() 비교하기 때문.**

참고) 동일 클래스 객체인 cn1, cn2 가 있을 때 hashcode를 오버라이딩 하지않으면 다른 코드값을 가지고 있어서 HashSet에서 cn1, cn2가 전부 사용하게 됨.

<br>

### equals()

- Object 클래스의 기본 equals()는 "객체의 주소"가 비교
- 값 비교를 위해서는 반드시 오버라이드 필요

```java
public class Product {
    String sn;
    public Product(String sn) {
        this.sn=sn;
    }
    
    @Override
    public boolean equals(Object obj) {
        if (obj != null && obj instanceof Product) {
            Product casted = (Product) obj;
            return sn.equals(casted.sn);
        }
        return false;
    }
}
```

<br><br>

### hashCode()

- Object의 기본 hashCode()는 객체의 메모리 주소 기반 해시값 반환
- equals() 재정의 시 반드시 hashCode()도 재정의 필요

```java
@Override
public int hashCode() {
    return sn.hashCode(); // String의 hashCode 활용 권장
}
```

<br><br>

### equals()와 hashCode()의 관계

💡 **주의사항**

- equals()가 true면 hashCode()도 같은 값 반환
- HashSet, HashMap 등은 hashCode() 먼저 비교 후 equals() 비교

```java
Set<Example> set = new HashSet<>();
Example e1 = new Example("test");
Example e2 = new Example("test");

// hashCode() 미재정의 시
set.add(e1);
set.add(e2); // 두 객체 모두 저장됨

// hashCode() 재정의 시
set.add(e1);
set.add(e2); // 하나만 저장됨 (중복 제거)
```

<br>

<br>

## toString()

**구현 예시: 디버깅 할 때 출력하기 좋음**

```java
@Override
public String toString() {
    return "Product{sn='" + sn + "'}";
}
//사용 예시
System.out.println(해당 객체); // toString이 반환 됨
```

<br>

<span style="color:#777777">equals()와 hashCode()는 반드시 함께 재정의되어야 하며, 특히 Hash 기반 컬렉션 사용 시 주의가 필요합니다.</span>
