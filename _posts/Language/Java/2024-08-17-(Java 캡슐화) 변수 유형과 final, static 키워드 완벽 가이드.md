---
title:  "[Java 캡슐화] 변수 유형과 final, static 키워드 완벽 가이드"
categories : Java
tag : [Java, 변수, final, static, 멤버변수, 지역변수, 초기화블록]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**Java의 변수 유형, final 키워드, static 블록의 특징과 활용법을 상세히 설명하는 기술 문서입니다. - 멤버변수,지역변수,static블록,인스턴스블록**

<br>

<br>

## 변수의 종류와 스코프

### 멤버변수

멤버변수는 클래스 영역에서 선언되며 두 가지 유형이 있습니다.

```java
class Example {
    static int classVar = 1;    // 클래스 변수
    int instanceVar;            // 인스턴스 변수(그외)
}
```

<br>

💡 **특징**

- 클래스 변수(static): 모든 인스턴스가 공유
- 인스턴스 변수: 각 객체마다 별도 존재
- 참조 타입의 기본값은 null

<br><br>

### 지역변수

메소드 내부에서 선언되는 변수로, 해당 메소드 내에서만 사용 가능합니다.

```java
void method() {
    int localVar = 0;    // 지역변수
}
```

<br>

<br>

## static 메소드 호출

```java
class Calculator {
    static int add(int a, int b) {
        return a + b;
    }
}

// 두 가지 호출 방법
Calculator.add(1, 2);      // 권장되는 방법
Calculator calc = new Calculator();
calc.add(1, 2);           // 경고 발생(사용은 가능)
```

<br>

<br>

## final 키워드의 활용

### 클래스와 메소드

```java
final class FinalClass { }          // 상속 불가(=오버라이딩 불가)
class Parent {
    final void finalMethod() { }    // 오버라이딩 불가
}
```

<br><br>

### 변수와 상수

```java
class Example {
    final int BLANK_FINAL;          // Blank final(=값이 할당되지 않은)
    static final double PI = 3.14;  // 진정한 상수(ex: Math.PI)
    
    public Example() {
        BLANK_FINAL = 100;          // 생성자에서 초기화
    }
}
```

<br>

⚡ **static final**

- 객체와 무관한 공용 상수
  - `Example.PI` 로 바로 사용 및 값 변경 불가 -> **진정한 상수**
- 일반 static보다 먼저 초기화

<br>

⚡ **blank final**

- 생성자에서 1회 초기화 가능
- 물론, 바로 선언과 동시에 초기화해도 가능 → `final int num=1;`

<br>

<br>

## 초기화 블록 - static, 인스턴스

**static 블록**

```java
class Example {
    static {
        // 클래스 로딩 시 1회 실행
    }
}
```

<br>

**인스턴스 블록**

```java
class Example {
    {
        // 객체 생성 시마다 실행
    }
}
```

<br>

**같이 사용한 예시(비교)**

```java
public class Test {
    public static void main(String[] args){
        MyClass mc = new MyClass();
        MyClass mc2 = new MyClass();
    }
}

public class MyClass {
    static { //static block
        System.out.println("static"); //class 가 메모리에 load 될 때 동작
    }
    { //instance block
        System.out.println(this.name); //null
    }
    String name;
    static int y;
    static {
        System.out.println("static2"); //마찬가지
    }
    //기본 메소드
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}

/*
출력:
static
static2
null
null
*/
```

<span style="color:#777777">각 블록은 초기화 순서와 용도가 다르므로 상황에 맞게 적절히 사용해야 합니다.</span>

