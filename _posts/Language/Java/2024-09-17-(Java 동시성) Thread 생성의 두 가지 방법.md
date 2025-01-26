---
title:  "[Java 동시성] Thread 생성의 두 가지 방법"
categories : Java
tag : [Java, Thread, Runnable, Multithreading, Concurrency]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**Java에서 스레드를 생성하는 두 가지 주요 방법인 Thread 클래스 상속과 Runnable 인터페이스 구현 방식을 설명합니다.**

<br>

<br>

## Thread 클래스 상속 방식

**Thread 클래스를 상속하여 새로운 Thread 클래스를 정의하고, run() 메서드를 오버라이드하여 스레드가 실행할 작업을 정의**

```java
class MyThread extends Thread {
    @Override
    public void run() {
        // 스레드가 실행할 작업 정의
        System.out.println("Thread running: " + Thread.currentThread().getName());
    }
}

// 사용 예시
MyThread thread = new MyThread();
thread.start(); //run()
```

<br>

<br>

## Runnable 인터페이스 구현 방식

**Runnable 인터페이스를 구현하여 run() 메서드를 오버라이드하여 새로운 스레드의 작업 내용을 정의하고, Thread 클래스로 객체를 생성하여 Runnable 인스턴스를 실행**

```java
class MyRunnable implements Runnable {
    @Override
    public void run() {
        // 스레드가 실행할 작업 정의
        System.out.println("Runnable running: " + Thread.currentThread().getName());
    }
}

// 사용 예시
MyRunnable runnable = new MyRunnable();
Thread thread = new Thread(runnable); //매개변수에 넣기
thread.start(); //run()
```

<br>

💡 **선택 기준**

- 단순 작업: Runnable 인터페이스 구현
  - 단순히 run() 메소드 구현하는 경우
- 확장 필요: Thread 클래스 상속
  - Thread 클래스의 기능 확장 필요한 경우

<span style="color:#777777">대부분의 경우 Runnable 인터페이스 구현 방식이 더 유연하고 권장됩니다.</span>
