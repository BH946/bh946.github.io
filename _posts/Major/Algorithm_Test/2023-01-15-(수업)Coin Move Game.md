---
title:  "[수업]Coin Move Game"
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

이 문제는 `Nim게임 + DP` 관련 문제이다.

`Nim게임 + DP` 관련해서 정리해둔 게시글이 있기 때문에 참고할 것.

<br>

## 문제

![screen captures](/images/2023-01-15-(수업)Coin Move Game/df29e17f-dac7-46e0-b1a5-91d0e98f370e.png)



![screen captures](/images/2023-01-15-(수업)Coin Move Game/550eb9dd-5219-413b-bde1-4444df1c722e.png)

<br>

## 풀이

이 또한 DP로 풀어주면 된다.   

알고리즘 과정 : 초기값 0, 1정도만 채워주고 뒤부터 규칙에 맞게 채워나가면 됨.   
=> 참고로 뒤에 길게 적다보면 P 주기마다 비슷한 형태로 반복되는것 같음



**아래는 DP배열 예시**

   0 1  2 3 4 5 x 7 8 ...  => `P=6, K=3, S=15`

1 w w L L w x x 

2 w L L L  x L x

3 w L L x w L x



**위 구조를 나타낸 조건들 3가지**

* L, L은 무조건 W => 당연히 이전단계에서 무조건 진 경우면 다음단계는 이기니까   
* W, W는 무조건 L => 당연히 이전단계에서 무조건 이긴 경우면 다음단계는 지는 차례니까   
* L, W는 상대를 무조건 W로 보내는 선택을 할 수 있으니까 L로 설정해야함.   => 상대가 W가 되야 다음턴에 영희가 W될 차례니까



이방식대로 DP 그려나가면 된다.
