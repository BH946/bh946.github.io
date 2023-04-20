---
title:  "[고급개념] Swap wtih XOR"
categories : Algorithm
tag : [알고리즘, XOR]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



# Intro..

**Swapping을 하는 방법은 정말 간단하다. 하지만 여기서는 일반적인 방법과 XOR 연산을 통해서 교환하는것을 소개하려고한다.**

<br><br>

# swapping - 기본

**보통 아래와 같은 구조로 간단히 변수 값을 교환할 수 있다.**

* 핵심 아이디어는 중간에 값을 따로 기록해두는 `temp` 변수이다.

```c++
// swap
int a = 22;
int b = 4;
int temp = -1;
a = temp;
a = b;
b = temp;
```

<br><br>

# swapping with XOR

**`^` 기호는 XOR 연산을 하는 기호이며 아래와 같은 방법으로 변수 값을 교환할 수 있다.**

```c++
// swap
int a = 22;
int b = 4;
a = a ^ b; // xor
b = a ^ b;
a = a ^ b;
```

<br>

**좀 더 자세히 주석을 달아두겠다.**

* 주석의 숫자는 2진수로 나타낸 bit이다.

```c++
// swap
int a = 22;	// a = 10110
int b = 10; // b = 01010 
a = a^b		// a = 11100
b = a^b		// b = 10110 
a = a^b		// a = 01010
```

