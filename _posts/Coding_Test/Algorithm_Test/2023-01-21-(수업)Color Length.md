---
title:  "[수업]Color Length"
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

이 문제는 `DP`로 풀수있는  문제이다.

`DP` 관련해서 정리해둔 게시글이 있기 때문에 참고할 것.

참고로 백준에 있는 문제이다.

<br>

## 문제

**백준에서 확인!!**

<br>

## 풀이

![screen captures](/images/2023-01-21-(수업)Color Length/a3aa30b8-0bd7-44cf-8cb3-21ec331fc032.png)



![screen captures](/images/2023-01-21-(수업)Color Length/c2fceba7-73c3-45ac-a55a-05124b58cdb2.png)

Si,j의 마지막은 당연히 두차선 a,b중에 한개일것.   

* 따라서 Si-1,j ai 또는 Si,j-1 bj   
  => 여기서 MIN이 Si,j값이 되는것.

그리고 색의 개수를 구하여 그 개수만큼 길이에 더해 줄 수있는 이유는, 차들 거리는 1이기 때문



![screen captures](/images/2023-01-21-(수업)Color Length/9fc778ae-4a6e-4f48-8494-be9721805ef9.png)



**색이 계속되는 경우??**   

* 색의 맨첫과 맨끝의 차를 비용으로 구하는 거기 때문에, 계속되는 경우는 길이를 더해줄 필요가 없음. 
* 그래서 이전 N값을 그대로 사용하고, 더해주는것.

**bj의 색이 시작되는 경우??**   

* 기존값과는 별개로 새로운 색이 탄생한거기 때문에 +1로 길이 추가해줘야함.

**bj의 색이 종료되는 경우??**

* **A**BCAC**A**의 경우 A의 차는 4이다. 그런데, 색 시작할때 했던 +1을 그대로 뒀으면?   
* A의 차는 5로 저장되어 있을것이다. 따라서 종료때는 -1을 해줘야 정확한 A의 차인 4를 구할 수 있다.   => 이런 느낌
