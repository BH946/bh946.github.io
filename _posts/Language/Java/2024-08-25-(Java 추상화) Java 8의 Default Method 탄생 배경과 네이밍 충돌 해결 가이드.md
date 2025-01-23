---
title:  "[Java 추상화] Java 8의 Default Method 탄생 배경과 네이밍 충돌 해결 가이드"
categories : Java
tag : [Java8, Interface, DefaultMethod, AbstractClass, MouseListener, 다중상속]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**Java 8에서 도입된 default method의 탄생 배경과 네이밍 충돌 시 해결 방법을 설명하는 문서입니다.**

<br>

<br>

## Default Method 이전의 구현 방식

**default method를 구현하기 위해서는 abstract class가 중간 과정에 추가!**  

- abstract class MouseAdaptor 는 implements MouseListener 를 구현
  - MouseListener 는 interface 입니다. -> 제일 기본 틀 역할
- Interface 를 implements 하면서 default method를 만들 수 있는 곳!
  - abstract class 는 구현체(default mehtod)를 만들 수 있으니까!

**class MouseHandler2 는 extends MouseAdaptor(앞에서 만든) 를 상속.** 

- 핵심: 여기서 오버라이딩을 하지 않는다면 abstract MouseAdaptor에서 만든 method가 **default method 역할이 되는 것!**
- 오버라이딩 안했으니까 abstract MouseAdaptor에서 만든 기본 메소드를 사용하기 때문!

**아래 Test 코드에서 click() 메소드를 사용하는데, 이 메소드가 default method !**

자세한 것은 코드와 주석을 함께보면 이해 가능할 것이다.

참고) 물론 Java8 부터는 default method가 아예 추가가 되었음!

![Image](https://github.com/user-attachments/assets/d50c476a-ef19-4a6b-8f88-38871c5dabb0) 

![Image](https://github.com/user-attachments/assets/0ad5e85a-4cbc-4526-88e5-3d54ba0594c1)  

![Image](https://github.com/user-attachments/assets/7c51519a-59de-436a-9fc6-5f0ee6f98adf) 

![Image](https://github.com/user-attachments/assets/a5c90e21-b0ed-417e-ad9d-ab268b59a124)  

<br>

<br>

## Default Method 네이밍 충돌

**충돌 해결 방법**

```java
interface Interface1 {
    default void m3() {
        System.out.println("Interface1's m3");
    }
}

interface Interface2 {
    default void m3() {
        System.out.println("Interface2's m3");
    }
}

class MyClass implements Interface1, Interface2 {
    @Override
    public void m3() {  // 반드시 오버라이드 필요
        System.out.println("override m3");
    }
}
```

💡 **주의사항**

- 동일한 이름의 default method가 여러 인터페이스에 존재할 경우 반드시 오버라이드 필요
- 단일 인터페이스의 default method는 오버라이드 선택적

<span style="color:#777777">default method의 도입으로 인터페이스 확장이 더욱 유연해졌으며, 기존 코드의 하위 호환성도 유지할 수 있게 되었습니다.</span>
