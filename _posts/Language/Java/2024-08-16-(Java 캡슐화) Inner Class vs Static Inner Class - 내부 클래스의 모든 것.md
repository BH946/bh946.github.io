---
title:  "[Java 캡슐화] Inner Class vs Static Inner Class - 내부 클래스의 모든 것"
categories : Java
tag : [java, inner-class, static-class, memory, performance]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**Java의 내부 클래스는 Inner Class와 Static Inner Class로 구분되며, 메모리 관리와 성능 측면에서 중요한 차이를 보인다.** 🔍

<br>

<br>

## 내부 클래스의 기본 구조 💡

**클래스 선언 방식 -> Inner, Static Inner**

1. Inner: 코드에서 `InnerClass`는 외부 클래스 `OuterClass`의 멤버이므로, `OuterClass`의 인스턴스가 생성된 상태라면 내부 클래스에서 `OuterClass`의 메서드와 필드에 접근할 수 있다.

2. Static Inner: 코드에서 `StaticInnerClass`는 정적(static)으로 선언되었기 때문에 외부 클래스의 인스턴스 메서드나 필드에 접근할 수 없다. **`OuterClass.this` 참조가 없기 때문**

```java
class OuterClass {
    void outerMethod() {
        System.out.println("Outer class method");
    }
    // 일반 내부 클래스
    class InnerClass {
        void method() {
            OuterClass.this.outerMethod(); // 외부 클래스 참조 가능
        }
    }
    
    // 정적 내부 클래스
    static class StaticInnerClass {
        void method() {
            OuterClass.this.outerMethod(); // 컴파일 에러 -`OuterClass.this`참조X
        }
    }
}

//인스턴스 생성 방식
// 일반 내부 클래스 생성
OuterClass outer = new OuterClass();
OuterClass.InnerClass inner = outer.new InnerClass();

// 정적 내부 클래스 생성
OuterClass.StaticInnerClass staticInner = new OuterClass.StaticInnerClass();
```

<br>

<br>

## 실제 활용 예시

**외부 클래스의 참조가 없으므로 안전하다.**

```java
public class UserDTO {
    // 정적 내부 클래스로 Request/Response 구현
    public static class Request {
        private String username;
        private String password;
    }
    
    public static class Response {
        private Long id;
        private String username;
    }
}
```

<br>

<br>

## Static Inner Class 권장 이유 ⚡

1. 메모리 효율성

   - 일반 내부 클래스: 외부 클래스 참조를 저장

   - 정적 내부 클래스: 외부 참조 없음

2. 가비지 컬렉션

```java
class OuterClass {
    private BigObject bigObject;
    
    class InnerClass {
        // bigObject에 대한 참조 유지로 GC 방해
    }
    
    static class StaticInnerClass {
        // 외부 참조 없어 GC 효율적
    }
}
```

<span style="color:#777777">정적 내부 클래스는 **메모리 효율성, 가비지 컬렉션 최적화, 멀티스레드 안전성 등**의 이점을 제공하므로, 특별한 이유가 없다면 정적 내부 클래스 사용을 권장한다.</span>

