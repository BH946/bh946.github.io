---
title:  "[수업]재활용 수집통(Recycling Bins)"
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

![screen captures](/images/2023-01-22-(수업)재활용 수집통(Recycling Bins)/41c4e849-0b34-4a02-b8ec-84094ce9beca.png)



![screen captures](/images/2023-01-22-(수업)재활용 수집통(Recycling Bins)/3fc6aef7-0a63-4042-bc4f-186eb32b083d.png)

<br>

## 풀이

**이 문제는 top-down 방식으로 문제를 해결했었다.**

m=1때 규칙은 손으로 그려보면 금방 구할 수 있어서 간단히 구하였고, m>=2 이상이 문제이다.

* m은 쓰레기통 수이다.

* m>=2 이상은 min을 구해야 함.

  * n m이 6 2 의경우? 6개 집, 2개쓰레기통 이니까 쓰레기통 2,3,4,5,6개때 경우 전부 비교   
  * 왜냐면 이때 통위치 다 달라지니까 그때 비교중 제일 min값 사용

  

**또한 아래는 bottom-top 방식으로 교수님이 푸신 풀이가 있으니 이 방식을 참고해도 좋다.**

![screen captures](/images/2023-01-22-(수업)재활용 수집통(Recycling Bins)/2a6ea96c-6bca-4b91-a202-249d1ab9b5bb.png)

