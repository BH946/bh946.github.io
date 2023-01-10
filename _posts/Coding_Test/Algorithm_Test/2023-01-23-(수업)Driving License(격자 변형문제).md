---
title:  "[수업]Driving License(격자 변형문제)"
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

이 문제는 `격자 유형의 DP` 관련 문제이다.

`DP` 관련해서 정리해둔 게시글이 있기 때문에 참고할 것.

<br>

## 문제

![screen captures](/images/2023-01-23-(수업)Driving License(격자 변형문제)/69a7ef87-9c0f-4136-b09d-ef391b186bf2.png)



![screen captures](/images/2023-01-23-(수업)Driving License(격자 변형문제)/a6de84d5-0e3d-4d45-8c5b-c54bb964e19d.png)



![screen captures](/images/2023-01-23-(수업)Driving License(격자 변형문제)/ed17eb2f-65d9-4a03-94ed-baa93032dd97.png)



![screen captures](/images/2023-01-23-(수업)Driving License(격자 변형문제)/b68a000a-1f50-4983-ba41-25aab49d741d.png)

<br>

## 풀이

**회전을 최대한 최소하면서 주어진 연료내로 도착하는걸 구하는것(회전수 중요)**

* 제일 최소의 시간일때를 식으로 구해보면? **{(N-1)+(M-1)}\*L+1**  
* 뒤에 +1은 회전 한번만 했다고 가정한건데, 이처럼 회전 수만 최소로 구하면 시간은 바로 구할 수 있다.



**자세한 알고리즘 흐름은 아래 그림을 참고!**

![screen captures](/images/2023-01-23-(수업)Driving License(격자 변형문제)/a65f75fc-5d01-4371-a892-7ea7049b1eab.png)

* 그림을 보면, 파란 볼펜으로 쓴 번호 1~5까지를 순서대로 보면 흐름을 이해할 수 있다.
* DP로 수직과 수평은 따로 기록해둬서 비교해 나가면 회전 구별이 더욱 용이하다.(2개 배열 추천!)
* 여기에 회전까지 총 3차원으로 구성하는것을 추천한다.
  * EX : `vertical[N][M][200]` 와 `horizental[N][M][200]` 
