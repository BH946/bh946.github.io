---
title:  "[개념] Greedy Algorithm(탐욕적인 알고리즘)"
categories : Algorithm
tag : [알고리즘, MST, Minimum Spanning Tree, Prim, Kruskal, 프림, 크루스칼, Dijkstra, 다익스트라, Fractional Knapsack, Huffman Code]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



## Intro..

DP 방법 말고도 다양한 기법들이 존재한다.  
그 중에서 탐욕적인(Greedy) 기법을 소개하겠다.

<br>

## 탐욕적인 알고리즘(Greedy alogrithm)

**매 순간의 선택을 최적의 선택을 함으로써 최종적인 해답에 도달하는 형식**



### 최적의 해를 얻지 못하는 경우

**그리디로 최적의 해를 얻지 못하는 경우들이 존재한다.  **
그 순간의 선택이 최적의 선택이 아니여야 최종적인 해답에 도달하는 경우도 있기 때문

* 카드, 보드 게임 등은 적용 불가
  * 예로 `거스름돈 문제`에서 16원의 경우??(액면가 1원, 5원, 10원, 12원)
    * 12원 1개, 1원 4개 => 동전개수 5개(그리디 방법)
    * 10원 1개, 5원 1개, 1원 1개 => 동전개수 3개(최적의 해)
  * 또 다른 예로 `연쇄행렬곱셈` 그리디 2가지 방법 + DP 방법
    * 1번 : ![img](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/DRW00000d985f1e-16726634559823.gif)차원 중 p 값이 가장 큰 두 행렬부터 계산
    * 결과 :  ![img](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/DRW00000d985f21.gif)   , 곱셈 수 :996
    * 2번 : n*m 값이 가장 작은 두 행렬부터 계산
    * 결과 :  ![img](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/DRW00000d985f25.gif)   , 곱셈 수 : 990
    * DP 방법 : 곱셈 수 904
  * 또 다른 예로 `격자에서 수 선택` 이것은 당연히 DP를 써야 최적이 된다.



### MST(최소비용 신장트리)

MST(Minimum Spanning Tree)는 최소의 가중치로 만든 신장트리이다.  
이를 구하는 알고리즘은 그리디 방식인 `Prim, Kruskal`이 있다.

* 신장트리(Spanning Tree) : Connected Acyclic Graph **(Acyclic : 사이클X)**

* 완전그래프(Complete Graph) : 모든 정점이 연결 (K1,K2,K3...)

* MST 특징 : 같은 비용인데 다른 모양은 여러 개 나올 수도 있음
* 응용 : 도로건설, 통신, 배관(파이프) 등에 적용
* 노드 수 : N, 에지 수 : N-1



#### Prim

**시작정점 아무거나 선택해서 시작하며 동일 비용의 노드들은 아무거나 택하고,   
젤 최소 비용인 노드들을 우선 선택해 나가는 방식의 알고리즘이다.**

* 사이클 신경 안써도 됨
* 인접 행렬, nearest 배열, distance 배열 사용**(최소비용 노드 선택 후 이 배열들 계속 갱신 해줘야함)**

![image-20230103011331435](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/image-20230103011331435.png)

* 시작노드 0로 시작 한 상태의 모습



![image-20230103011405178](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/image-20230103011405178.png)

* 다음 가중치가 젤 작은 노드1을 선택하고 배열도 갱신한 모습



![image-20230103011501914](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/image-20230103011501914.png)

* 다음 같은 가중치인 2번과 7번 노드중에서 아무거나(2번) 선택한 후 배열 갱신한 모습
  * 여기까지 이해 되었다면, 전체 노드 선택할때까지 반복해서 최종 답을 구할 수 있다.



![image-20230103011623234](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/image-20230103011623234.png)

* 참고용 Prim 알고리즘의 코드 구조



#### Kruskal

**에지들의 가중치로 비내림차순 정렬을 먼저 한 후 작은 것들을 순서대로 선택해 나가는 알고리즘이다.**

* 사이클이 생성되는 경우가 있기 때문에 사이클 구분이 매우 중요
  * 서로소 집합 추상 데이터 타입(disjoint set abstract data type) 형태를 사용
    * 이를 직접 만들어 사용하거나 이미 지원해주는 언어는 바로 사용하면 됨
    * 예로 C++은 지원하며, C는 지원하지 않음



![image-20230103011716628](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/image-20230103011716628.png)

* 먼저 에지들 정렬하고 젤 작은 에지부터 선택해 나가는 모습



![image-20230103011807970](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/image-20230103011807970.png)

* 위 상황처럼 파란색 6 (6,8)은 사이클을 생성시키므로 pass한다.
  * **이처럼 사이클을 구분하게끔 알고리즘을 만들어야함.**



![image-20230103012211204](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/image-20230103012211204.png)

* Kruskal 알고리즘의 코드 구조
  * `initial(n)`은 전체를 자기자신을 루트로 가지게 9개의 트리가 독립적으로 만들어진 상태   
  * `find(i)`는 자신이 속한 트리의 루트가 누구인지 찾음   
  * `equal(p,q)`는 동일한지 확인   
  * `merge(p,q)`는 p집합, q집합을 합병(q의 root가 p의 root를 가리키게 바꾸는것)



![image-20230103012410736](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/image-20230103012410736.png)

* **기본 동작 구조 : find(root) -> equal(p,q) -> merge(q->p) 흐름**



![image-20230103012459397](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/image-20230103012459397.png)

* **사이클을 감지하는 순간의 모습(핵심)**



#### 두 알고리즘 복잡도 관련

* **sparse graph** : Prim(  ![img](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/DRW00000d985f4c.gif)  ), Kruskal(  ![img](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/DRW00000d985f4e.gif)  **)**

* **dense graph** : Prim(  ![img](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/DRW00000d985f50.gif)  ), Kruskal(  ![img](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/DRW00000d985f52.gif)  **)**

* **Heap** : Prim+sparse –   ![img](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/DRW00000d985f54.gif)  , Prim+dense -   ![img](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/DRW00000d985f56.gif)  

* **Fibonacci heap** : Prim+sparse –   ![img](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/DRW00000d985f58.gif)  , Prim+dense -   ![img](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/DRW00000d985f5a.gif)  

Prim도 자료구조가 다르면 이렇게 복잡도가   ![img](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/DRW00000d985f5c.gif)   까지 가능



### Dijkstra(다익스트라)

**SSSP(Single Source Shortest Path Dijkstra Algorithm) : 단일출발점 최단경로 문제(다익스트라)**

* 각 정점에 최단경로 길이를 레이블에 기억 후 젤 작은 레이블 값부터 색칠해가는 형태

* **Label correctioin** 알고리즘이라고도 부름
* distance(=label), from(=nearest) 배열 사용
  * 이 두 배열은 앞서 위에서 봤듯이 Prim 알고리즘에서 사용한 배열과 매우 유사하다
  * 심지어 알고리즘 방식도 조금 유사하지만 차이점이 있다.
  * 최단경로 길이를 기억해나가는 것이고, 이 길이는 만약 다른 노드 경유를 한 경우거나 하면 그 경유한 길이도 꼭 다 합산한 총 길이를 기록해나가야한다.
  * 자세한건 아래 그림을보고 이해하자



![image-20230103013926087](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/image-20230103013926087.png)

* Prim알고리즘과 유사하게 배열들 초기값이 형성되는 모습이다.



![image-20230103014026475](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/image-20230103014026475.png)

* 시작을 1노드로 하고 배열들 갱신한 모습이다.



![image-20230103014139546](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/image-20230103014139546.png)

* 현재 다음으로 작은 가중치인 5번노드를 택한 경우이고, 6번 노드 길이가 22가 된것을 보자.
  * **Prim과 차이점**이 여기선 v1->v5->v6 길이로 22를 기록한것이고
  * Prim의 경우 그저 v5->v6으로 가는 가중치 15를 기록했을것이다.
  * **이처럼 출발지점인 v1노드 기준으로 각각 노드들의 길이들을 판단하게 된다는것이 중요하다.**



#### Dijkstra vs Floyd

* Dijkstra(  ![img](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/DRW00000d985f64.gif)  ) - 단일 출발점, 임의의 도착점(Greedy)
* Floyd(  ![img](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/DRW00000d985f67.gif)  ) - 임의의(복수가능) 출발점, 임의의 도착점(DP)

물론 Dijkstra도 임의의 출발점을 위해 n번 사용하면   ![img](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/DRW00000d985f6a.gif)  으로 가능



### Heap(힙)

자세히는 다른 게시물에 정리를 해뒀으니 여기선 넘어가겠다.



### Fractional Knapsack(Greedy)

**Fractional Knapsack란 배낭을 빈틈없이 채우는 알고리즘이다.   
여러 품목(item)들 중에서 정해진 무게(W)만큼 채워서 최대한 많은 이득을 취할 수 있는 문제를 다룬다.**

**무게 큰거부터 차례로 넣어가고, 남은건 쪼개서 담으면 되기때문에 탐욕(그리디)방법으로 풀 수 있다.**   
하지만 물건을 쪼갤수 없는 상태면?? `0-1 Knapsack`의 문제가 되는것이다.  
`Fractional Knapsack`과 `0-1 Knapsack`을 구별할 것

* Greedy는 `Fractional Knapsack` 가능, `0-1 Knapsack` 불가
* 따라서 `0-1 Knapsack`은 DP로 구현



![image-20230103015258286](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/image-20230103015258286.png)

* 그리디 방식으로 풀면 되고, 값어치를 구해서 젤큰 품목(item)부터 차례로 넣으면 된다.
* 220만원이 최적이며 간단하므로 더이상 설명은 생략하겠다.



### Huffman Code(그리디 방법으로 가능)

**메시지를 직접 전송하는 대신 이진 code를 전송할건데 빈도수 높은 메시지는 이진 code를 짧게해서 총 전송 비용을 최소로 하는것이 목표인 알고리즘**

* **빈도 수 정렬 후 이진트리 생성 및 이진 code로 읽는 방식으로 진행**



![image-20230103023152804](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/image-20230103023152804.png)

* f는 문자들 빈도수를 적어둔 것
* 그리고 이진트리를 구해줌
* 다음으로 이 이진트리를 차례대로 읽어줘서 이진 코드로 변경
* 이후 총 전송 비용을 계산(아래그림)



![image-20230103023300950](/images/2023-01-01-(개념) Greedy Algorithm(탐욕적인 알고리즘)/image-20230103023300950.png)
