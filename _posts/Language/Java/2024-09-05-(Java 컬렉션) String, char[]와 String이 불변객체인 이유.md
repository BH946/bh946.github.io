---
title:  "[Java 컬렉션] String, char[]와 String이 불변객체인 이유"
categories : Java
tag : [Java, String, StringBuilder, CharArray, StringPool, Immutable]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**String은 불변 객체로 설계되어 있으며, 효율적인 문자열 처리를 위해서는 적절한 메서드와 클래스를 선택해야 합니다.**

<br>

<br>

## String의 불변성(Immutability)

**특징과 이점**

- 생성 후 상태 변경 불가능	
  - read-only 메소드만 제공
- String Constant Pool(상수 풀)을 통한 메모리 최적화
  - 리터럴로 선언한 String의 경우 Pool이 따로 관리해줘서 힙 메모리 절약
  - String을 new 연산자가 아닌 리터럴로 선언하는걸 추천하는 이유

```java
// 리터럴 방식 권장 (String Pool 사용)
String str1 = "hello";
String str2 = "hello";  // str1과 같은 메모리 참조

// new 연산자 사용 시 새로운 객체 생성
String str3 = new String("hello");  // 새로운 메모리 할당 (str1,str2와 주소 다름)
```

<br>

<br>

## 주요 문자열 처리 메서드

**문자열 변환**

- **char[] to string to int**
  - `char[] val = {'a,b,c'};`
  - `int num = Integer.parseInt(new String(val));`
- **string to char[]**
  - `char[] inChar = inStr.toCharArray();`
- **int to string**
  - `int num = Integer.toString(300);`
- **char length**
  - `val.length`
- **string length**
  - `val.length()

<br>

**문자열 조작**

| 메서드      | 설명                                                         | 예시                   |
| ----------- | ------------------------------------------------------------ | ---------------------- |
| length()    | 길이 반환                                                    | str.length()           |
| charAt()    | 특정 위치 문자(char 반환)                                    | str.charAt(2)          |
| substring() | 부분 문자열                                                  | str.substring(2,4)     |
| replace()   | 문자 치환(새로운 객체 생성)                                  | str.replace('a','b')   |
| indexOf()   | 특정 문자 첫번째 인덱스 반환                                 | str.indexOf("c")       |
| equlas()    | 비교(기존 ==은 주소를 비교)                                  | str.equlas("abcde")    |
| contains()  | 포함 여부                                                    | str.contains("bc")     |
| compareTo() | 동일:0, str이 사전순 앞:-1, str이 사전순 뒤:1, <br />마지막 문자만 다르면 마지막 문자의 사전순 차이 반환 -> 즉, str이 "abcde" 라면 1 반환 <br />TIP : str - "abcdd" = ? 로 보면 간단<br />예로 5 - 10 = -5로 음수이므로 앞을 의미 | str.compareTo("abcdd") |
| split()     | 특정 문자 기준 나눔(배열 반환)                               | str.split(" ")         |
| trim()      | 앞 뒤 공백 제거                                              | str.trim()             |

<br>

<br>

## 효율적인 문자열 처리 - StringBuilder

C++처럼 java도 string은 문자열을 이어붙일 때 잘못 사용하면 O(N^2) 복잡도를 발생  

- 예로: `s+"a"` 의 경우 `s+"a"`라는 새 문자열 객체를 만든 후 기존 `s`에 더하기 때문에 `s+"a"` 만큼 매번 길이가 추가

- 해결 방안으로 StringBuilder 활용! - O(N) 복잡도
  - `'a'` 만 생성되어서 `s` 에 더하기 때문에 `a` 만큼 길이만 추가로 필요

```java
// 비효율적인 방식 O(N^2)
String result = "";
for (int i = 0; i < n; i++) {
    result += "a";  // 매번 새로운 객체 생성
}

// 효율적인 방식 O(N)
StringBuilder sb = new StringBuilder();
for (int i = 0; i < n; i++) {
    sb.append("a");
}
String result = sb.toString();
```

<span style="color:#777777">문자열 처리 시 상황에 맞는 적절한 도구를 선택하여 성능과 가독성을 모두 확보해야 합니다.</span>
