---
title:  "[수업]Card Selection"
categories : [AlgorithmTest]
tag : [백준, 프로그래머스, 알고리즘 문제풀이]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
typora-root-url: ../../..
---



## Intro..

이 문제는 `DP` 관련 문제이다.

`DP` 관련해서 정리해둔 게시글이 있기 때문에 참고할 것.

<br>

## 문제

![screen captures](/images/2023-01-17-(수업)Card Selection/8f3fee7f-131a-43b1-a02f-a1feb53e741a.png)



![screen captures](/images/2023-01-17-(수업)Card Selection/bf39f6f2-bc7b-4639-bf5f-15fb29b73f88.png)

<br>

## 풀이

열을 `i-2,i-3,i-4`와 행을 `1,2,3,4....`로 해서 행,열그려서 확인!!(그림그려보면 방식 자체는 간단)

* 제대로 그렸던 그림을 버려서 이전에 그렸던 그림 참고(이거 맞는지 확인은 안해봤음. 참고정도만!)

<img src="/images/2023-01-17-(수업)Card Selection/image-20230111174839184.png" alt="image-20230111174839184" style="zoom: 50%;" />



**DP로 풀면되고, 개인적으로 재귀보단 for문으로 푸는게 더 간단했다(이전경로 기억하면서 푸는방식)**  

* **for문으로 dp배열**을 2차원으로 접근해서 이전경로들에 대응되는 값들로 배열에 저장되게 코드를 짜면 되고,   
* `i-2` 처리가 제일 중요한데 이는 `i-4와 i-3` 중에 큰값과 더하면 된다.   
* 기본 아이디어는 `i-2,i-3,i-4` 중 큰값과 더하는거임. 
  * **i-2만 따로 예외처리를 해야한다는 것(중요)**



**교수님 코드도 참고해보면 방식을 이해할 수 있을 것이다.**

![image-20230111175029620](/images/2023-01-17-(수업)Card Selection/image-20230111175029620-16734270323574.png)



![image-20230111175043966](/images/2023-01-17-(수업)Card Selection/image-20230111175043966-16734270452435.png)
