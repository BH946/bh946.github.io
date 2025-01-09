---
title:  "[Java 기본 원리] 자바는 값 전달 방식일까? Call by value?"
categories : Java
tag : [java, parameter, call-by-value, memory]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**Java는 항상 Call by Value 방식으로 동작하며, 객체의 경우 참조값을 복사하여 전달하는데 이 조차도 Call by Value 방식이다.** 🔍

<br>

<br>

## Call by Value vs Call by Reference ⚡

### C언어 포인터는 참조 전달 아냐?

**C언어의 포인터를 이용한 값 전달은 Call by Reference가 아닐까?**  
아니다!!  
"주소값을 복사" 했을 뿐이지 참조 자체를 바꾼게 아니다.

`*a` 를 보면 굳이 에스터리스크(*)연산자를 통해 메모리에 ‘접근’ 한다. 만약 값 복사가 아니면 `a`로 바로 접근이 되어야 할 것 아닌가!   
**C에서는 "주소 값"이 출력된다. 즉, Call by Value이다. "값"이다.**

```c
// Call by Reference 같은 Call by Value 예시
void swap(int *a, int *b) {  // 주소값을 복사하여 전달
    int temp = *a;
    *a = *b;
    *b = temp;
}

int main() {
    int x = 10, y = 20;
    swap(&x, &y);  // 주소값을 전달
}
```

<br><br>

### Java 객체들은 참조 전달 아냐?

**Java도 C와 마찬가지로 Call by value다.**  
Java에서는 기본 데이터 유형에 대해서는 `Call by value`, 객체에 대해서는 `Call by reference`와 유사한 동작을 할 수 있도록 참조 변수를 활용

아래 코드는 **참조 변수를 활용해 원본 배열로 바로 접근하는 예**이다.  
해당 참조 변수(arr)엔 `Call by value`로 numbers배열 "주소 값"이 복사된 것!  
따라서 `arr = new int[]{200, 2, 3}` 처럼 새로운 주소를 가진 배열로 할당이 가능!   
=> 마치 변수에 "일반 값" 넣는것 처럼!

```java
public static void main(String[] args) {
    int[] numbers = {1, 2, 3};
    modifyArray(numbers);
    System.out.println(numbers[0]);  // 100 출력 (배열 내부 값 변경됨)

    reassignArray(numbers);
    System.out.println(numbers[0]);  // 100 출력 (참조 자체는 변경되지 않음)
}

static void modifyArray(int[] arr) {
    arr[0] = 100;  // 원본 배열 수정 가능
}

static void reassignArray(int[] arr) {
    arr = new int[]{200, 2, 3};  // 새로운 배열 할당 (원본에 영향 없음)
}
```

<br>

<br>

## 그래서 결론은?

- Java, C는 기본적으로 Call by value → 애초에 우리가 아는 대부분 언어들은 call by value
  - call by reference 를 흉내낼 수 있을뿐!
  - 객체나 포인터의 경우 call by reference로 느껴지는 이유는 매개변수에서 주소(참조)를 복사해서 넘기기 때문
- 따라서 실제론 **call by object reference** 라고 이해하는 것이 더 정확

- 여담이지만 C++에서 참조자라는 개념이 들어오면서 이 참조 자체를 복사할 수 있는 방법이 생겼다. 따라서, a를 참조한 참조자 c변수가 있다고 한다면 c는 a와 (내부적으로)완전히 같은 변수로 ‘이름’은 다르지만 같은 참조값을 지닌다는 의미에서 별칭(alias)라고 부르기도 한다.  
  - C++ 은 참조 전달이 되는듯 하다?
