---
title:  "[Java 예외] 예외 처리 시스템 Checked와 Unchecked Exception"
categories : Java
tag : [Java, Exception, Error, CheckedException, UncheckedException, Throwable]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**Java의 예외 처리 시스템은 Checked와 Unchecked로 나뉘며, Object와 Throwable을 최상위 클래스로 하여 Error와 Exception으로 구분합니다.**

<br>

<br>

## 예외 계층 구조

**최상위는 Object 클래스, 그 다음은 Throwable 클래스 -> 그 다음은 Error, Exception 클래스**

```java
try {
    // 예외 발생 가능 코드
} catch (Throwable t) {
    // Error와 Exception 모두 처리 가능
}
```

<br>

![예외처리계층](https://github.com/user-attachments/assets/7965b798-9067-4b85-b322-f5418963b9c4) 

<br>

<br>

## Error

**오류**

- 개발자가 예측/처리 불가능
- 시스템 레벨의 심각한 문제
- 예: OutOfMemoryError, StackOverflowError

<br>

<br>

## Exception

**예외**

- 개발자가 예측/처리 가능한 문제
- 개발자 구현 로직 같은 곳에서 발생
- 두 가지 유형으로 구분:

| 구분           | Checked Exception         | Unchecked Exception               |
| -------------- | ------------------------- | --------------------------------- |
| 처리 여부      | 컴파일러가 처리 강제      | 컴파일러가 처리 강제X             |
| 대표 예시      | IOException, SQLException | RuntimeException과 하위 클래스    |
| 주요 발생 원인 | 외부 리소스 접근          | 프로그래밍 로직 오류(개발자 실수) |

<br><br>

### Checked, Unchecked Exception

```java
// Checked Exception 예시
try {
    FileReader file = new FileReader("file.txt");
} catch (IOException e) {  // 반드시 처리해야 함
    e.printStackTrace();
}

// Unchecked Exception 예시
String str = null;
str.length();  // NullPointerException (처리 선택적)
```

<br>

![Image](https://github.com/user-attachments/assets/ee582d2d-1e5a-4c79-bd53-888c0820b4b4) 

![Image](https://github.com/user-attachments/assets/e2b792ce-6335-4cac-b439-6dd9a27e5913) 

![Image](https://github.com/user-attachments/assets/fbc9244f-12ce-402d-a57a-375800badbdd) 

<span style="color:#777777">적절한 예외 처리는 프로그램의 안정성과 유지보수성을 높이는 핵심 요소입니다.</span>
