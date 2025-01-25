---
title:  "[Java 예외] Java 7의 try-with-resources와 AutoCloseable 인터페이스"
categories : Java
tag : [Java7, TryWithResources, AutoCloseable, ResourceManagement, Exception]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**Java 7부터 도입된 try-with-resources는 자원을 자동으로 닫아주는 기능으로, AutoCloseable 인터페이스를 구현한 클래스에서만 사용 가능합니다.**

<br>

<br>

## try-with-resources 기본 구문

### 단일 리소스 사용

**괄호()를 사용하는게 큰 특징!**

```java
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    String line = br.readLine();
    // 리소스 사용 코드
} catch (IOException e) {
    e.printStackTrace();
    //br.close(); 가 필요없음. 자동으로 해줌!
}
```

<br><br>

### 다중 리소스 사용

**세미콜론(;) 으로 구분하는게 큰 특징!**

```java
try (FileInputStream fis = new FileInputStream("input.txt");
     BufferedInputStream bis = new BufferedInputStream(fis)) {
    // 여러 리소스 사용 코드
}
```

<br>

<br>

## 주의사항

**오히려 자동으로 자원 반환해서 필요할 때 해당 자원을 사용 못 할수도 있으니, 삽질하지말고 이 부분은 항상 유의하고 사용하길 권장!**

💡 **장점**

- 자원 누수 방지
- 코드 간결성 향상
- 예외 처리 용이

<br>

⚡ **주의점**

- 자동 close()로 인한 리소스 재사용 불가
- AutoCloseable 구현체만 사용 가능
- close() 시점 정확히 인지 필요

<span style="color:#777777">try-with-resources는 효율적인 자원 관리를 제공하지만, 리소스의 생명주기를 정확히 이해하고 사용해야 합니다.</span>
