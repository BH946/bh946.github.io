---
title:  "[C++]Base Conversion - 백준11576"
categories : [BaekjoonTest]
tag : [백준, 프로그래머스, 알고리즘 문제풀이]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
typora-root-url: ../../..
---



## 문제

* **[Base Conversion(백준11576)](https://www.acmicpc.net/problem/11576)**

<br><br>

## 풀이

**문제 해석을 하자면,**

* A진법 -> B진법으로 변환하는 문제



**풀이**

* A진법에서 바로 B진법으로 가는건 해보진 않았지만,  
* A진법-> 10진법과 10진법 -> B진법 가보는건 이미 경험했던 문제들이다.
  * '진법 변환', '진법 변환 2' 풀이 게시글 참고

* **따라서 A진법->10진법->B진법 으로 가게 작성해보겠다.**



<br><br>

## 코드

```c++
#include <iostream>
#include <stack>
#include <math.h>
using namespace std;

int A, B, m;
int i, j, k;
int inputArr[25]; // 양의 정수이며 2^20 미만(0도 생각)

long long calTemp, result;
stack <long long> st;

int main() {
	// input
	cin >> A >> B >> m;
	for (i = 0; i < m; i++)cin >> inputArr[i];

	// run
	// A->10
	for (i = m-1; i >= 0; i--) {
		calTemp += inputArr[i] * pow(A, j++);
	}
	// 10->B
	while (calTemp!=0) {
		result = calTemp% B;
		st.push(result);
		calTemp /= B;
	}

	// output
	while (!st.empty()) {
		cout << st.top() << ' ';
		st.pop();
	}

	return 0;
}
```

<br><br>

## 느낀점

`A진법 -> 10진법 -> B진법` 변환하는 방법만 알고있으면 풀 수 있는 문제이다.
