---
title:  "[개념] Divide_and_Conquer(분할정복법)"
categories : Algorithm
tag : [알고리즘, 꼬리 재귀 호출, 이분검색, 합병정렬, 빠른정렬, 쉬트라쎈, top-bottom, top-down]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**분할정복법(Divide and Conquer)은 문제를 작은 부분으로 나누고(Divide), 각 부분을 해결한 후(Conquer), 필요시 해답을 통합(Combine)하는 알고리즘 설계 방법입니다. 이 글에서는 하향식 접근법의 개념, 꼬리 재귀 호출, 이분검색, 합병정렬, 빠른정렬, 쉬트라쎈 알고리즘 등 다양한 분할정복 방식과 분할정복을 사용하지 말아야 할 경우에 대해 설명합니다.**

<br>

**분할정복 해결법**을 **하향식(top-down) 접근법**이라고도 한다.

* 보통 재귀를 많이 의미하며, 좀 더 의미를 분명히 해둬야 할 것이 있다.
  * **top-bottom, bottom-top, top-bottom:Momoization 의 의미 구분**
  * top-bottom : 하향식 접근법이며, 재귀를 보통 사용한다.
  * bottom-top : 상향식 접근법이며, for문을 보통 사용하고 흔히 DP(동적계획법)을 의미한다.
  * top-bottom(+Momoization) : top-bottom과 동일한데 추가로 DP처럼 리턴값을 따로 기억해서 재사용하는 구조를 가진다.

<br><br>

## 분할정복(Divide-and-Conquer)식 설계

분할(Divide) : 문제를 작은 부분으로 나눔

정복(Conquer) : 작은 부분을 각각 해결

통합(Combine) : (필요하다면) 해결된 해답을 모음

**=> 하향식(top-down) 접근 방법이라고 한다.**

<br>

### 다양한 분할정복 방식들

**하향식(top-down) 접근법이며, 보통 재귀 형태이다.**

* **꼬리 재귀 호출(tail recursion)** => for문으로 변환 가능(무조건)
  * 여기서 바꾼 반복 알고리즘과 재귀 알고리즘의 시간복잡도는 동일
  * 다만, 반복 알고리즘이 상수적으로 좀 더 좋음(재귀처럼 호출하고 리턴하는 복잡도가 없어서)
* **이분검색** : 재귀 알고리즘(정렬된 배열에 사용)
  * 입력 배열을 전역으로 선언해서 낭비되는 재귀 파라미터를 막으면 더 효율적
  * [이분검색 & 피보나치](https://bh946.github.io/algorithm/(%EA%B0%9C%EB%85%90)-%EC%88%9C%EC%B0%A8,-%EC%9D%B4%EB%B6%84%EA%B2%80%EC%83%89%EA%B3%BC-%ED%94%BC%EB%B3%B4%EB%82%98%EC%B9%98-%EC%88%98(%EC%9E%90%EC%84%B8%ED%9E%88)/)
* **합병정렬** : 대표적인 분할정복식 방법
  * [합병정렬 참고](https://bh946.github.io/algorithm/(%EA%B0%9C%EB%85%90)-Sort(%EC%A0%95%EB%A0%AC)/#mergesort%ED%95%A9%EB%B3%91%EC%A0%95%EB%A0%AC)
* **빠른정렬** : 이 또한 분할정복식 방법
  * [퀵정렬 참고](https://bh946.github.io/algorithm/(%EA%B0%9C%EB%85%90)-Sort(%EC%A0%95%EB%A0%AC)/#quicksort%ED%80%B5%EC%A0%95%EB%A0%AC)
* **쉬트라쎈** : 행렬 곱 알고리즘 인데, 2x2 크기로 분할해서 계산하는 특징이 있음
  * [행렬곱셈(쉬트라쎈)](https://bh946.github.io/algorithm/(%EA%B0%9C%EB%85%90)-%ED%96%89%EB%A0%AC%EA%B3%B1%EC%85%88(%EC%89%AC%ED%8A%B8%EB%9D%BC%EC%8E%88)/)

* **등등 다양한 알고리즘들이 존재**

<br><br>

## 분할정복 사용하지 말아야 할 경우들

크기가 n인 "입력"이 **2개 이상**으로 분할, "분할 크기"가 **거의** **n**에 가까울 때 -> 지수 시간

크기가 n인 "입력"이 **거의 n개의 조각**으로 분할, "분할 크기"가 **n/c** 경우 -> ![image-20221228193004758](/images/2022-12-29-(개념) Divide_and_Conquer(분할정복법)/image-20221228193004758.png)시간

**예시로 "병합 정렬과 퀵 정렬"을 생각해보자.**

- 병합 정렬은 문제의 크기를 두 부분으로 나눌 때 `c=2` 로, 각 분할된 문제의 크기가 `n/2` 가 된다.   
  이를 재귀적으로 반복하다 보면 문제의 크기가 점점 작아져서 결국에는 매우 작은 크기로 쪼개지고, 더 이상 분할할 수 없는 작은 크기까지 분할한 후 다시 병합하면서 정렬한다.
- **만약1)** 병합 정렬에서 분할 단계에서 잘못된 알고리즘으로 각 부분 문제의 크기가 `n/1.1` 정도로 나뉘었다고 가정하면, 문제를 충분히 작게 나누지 못해 분할의 이점이 없어지고 전체 시간 복잡도가 크게 늘어날 수 있다.
  - **입력으로 2개**가 분할되었지만, **분할크기가 n/1.1**로 기존의 크기n과 유사하기 때문에 계속해서 분할 해야한다.
  - 따라서 문제를 충분히 작게 나누지 못하였고, 충분히 작게 나누기 위해서 시간 복잡도는 크게 늘어나는 것이다.
- **만약2)** 퀵 정렬은 피벗 선택이 불량한 경우에는 성능이 급격하게 떨어질 수 있다. 이미 정렬된 배열을 퀵소트로 정렬하려고 할 때, **피벗**을 항상 배열의 **첫 번째 요소로 선택**하면??
  -  **입력으로 거의 n개**로 분할된다고 볼 수 있고, 각 분할이 **n/c 크기**(ex:n/n=1)의 부분 문제로 분할되면서 깊이가 `n`에 가까워진다.
  - 이 경우 배열이 이미 정렬되어 있기 때문에 첫 번째 요소를 피벗으로 선택하면 나머지 요소들은 전부 피벗보다 큰 값을 가지게 된다. 하나의 부분 문제의 크기가 1이 되며 나머지 부분의 크기는 n−1이 되어 배열이 거의 나누어지지 않게 된다는게 큰 특징이다.
  - 따라서 n, n-1, n-2... 로 흘러가다보니 퀵정렬의 최악 복잡도는 n^2으로 알려져 있다.



