---
title:  "[개념] Branch-and-Bound(분기한정법)"
categories : Algorithm
tag : [알고리즘, 0-1 Knapsack, 0-1 배낭채우기, TSP, 외판원 문제, Traveling Saleswoman Problem]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



## Intro..

백트래킹과 분기한정법을 같이 알아두면 좋다.

백트래킹은 깊이우선검색을 기반으로 하지만, 분기한정법은 여러 검색 방법들을 지원하는 차이가 있다.

여기선 분기한정법을 알아보도록 하자.

<br><br>

## Branch-and-Bound(분기한정법)

**Backtrack +** **다른 탐색들**

* **Bound** : 마디가 유망한지 여부를 결정하기 위해서 한계치(bound) 계산
* **So far the best** : 현재까지 찾은 최적의 답

<br>

### 0-1 배낭채우기(깊이, 너비, 최고)

**여기서만 검색방식 3가지를 다 보여주려고한다.**

**0-1 배낭채우기는 앞서 정리를 여러번 했기 때문에 자세한 설명은 생략하고 푸는 방식을 보겠다.**

* **우선 여기선 Bound를 구해준다**
  * 앞으로 얻을 수 있는 최대 가능한 이익(단위 무게당 이익)
  * Fraction 냅색 방식으로 이부분은 구한다.
* **다음으로 입력데이터를 단위 무게당 이익이 감소하는 순으로 정렬(최대 이익을 구하기 위함)**
* **따라서 노드는 3가지 정보를 기록 : 현재까지 이익, 현재까지 무게, 앞으로 가능한 최대 이익**
* **상태 공간트리는 단순히 1, 0 방식**

<br>

#### 깊이우선 방식

참고할 코드는 [Backtracking](https://bh946.github.io/algorithm/(%EA%B0%9C%EB%85%90)-Backtracking(%EB%90%98%EC%B6%94%EC%A0%81-%EA%B8%B0%EB%B2%95)/) 게시물에서 확인

![screen captures](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/66ad63ec-7ff6-478a-a916-0f0f321c9822-16727213310084.png)

<br>

#### 너비우선 방식

이 케이스는 깊이가 더 좋은 케이스였던것(물론 무슨 경우에서도 다 좋은건아님. 이 케이스에서 좋았던것)

![screen captures](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/b576733b-f667-4b66-a7a3-997caad96ff4.png)

![screen captures](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/a0a0a9d9-a4e0-4590-8309-7509acbe597e.png)

* 참고 코드

<br>

#### 최고우선 방식

우선순위 큐라고 생각하면 됨

![screen captures](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/1acd9ce1-f877-4d01-a085-64dd4b7ca9b7.png)

<br>

### 외판원 문제(Traveling Saleswoman Problem)

TSP라고 매우 유명한 문제이고, 해밀토니안 사이클과 유사하지만 가중치가 있고 제일 최소를 찾는다는 것이다.

* DP방식과 분기한정법 방식 소개

<br>

#### DP

  ![img](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/DRW00002a2c1c12.gif)  란?   ![img](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/DRW00002a2c1c14.gif)  에서 A에 속한 정점 3개를 한 번씩 만 거쳐서   ![img](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/DRW00002a2c1c16.gif)  으로 가는 최단경로의 길이

D의 목표는?   ![img](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/DRW00002a2c1c18.gif)   인것!   ![img](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/DRW00002a2c1c1a.gif)  에서   ![img](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/DRW00002a2c1c1c.gif)  모두 지나서   ![img](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/DRW00002a2c1c1e.gif)  으로 가는 최소길이를 의미하니까

공식 :   ![img](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/DRW00002a2c1c20.gif)  

![screen captures](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/3f142e0e-26b6-43b1-b3a1-c70db5ceea48.png)

* 하나의 예시이며 풀이를 잘 참고

<br>

![screen captures](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/f29b89ca-a5ec-4304-8c47-da73810ae07a.png)

* 코드 참고

<br>

#### 분기한정법

**outcome, income방식이 존재하며 outcome 방식을 소개하겠다.**

* 나가는 에지들 중 가장 비용이 싼 것들의 합 -> Lower Bound
* 사이클 생성했을 때 젤 작은 Value가 the best!
* 우선순위큐로 BOUND가 젤 작은 것들을 우선 dequeue

<br>

![screen captures](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/86652e30-33b3-4db0-a11a-93d27c310632.png)

* outcome 방식이므로 위에 2차원 배열을 보면 가로로 젤 작은 값들을 각각 구해서 합해주는것이  
  Bound 값이 된다.
* **나가는 에지들이 다 최소일경우? 젤 최적일것.(하지만 당연히 그 경우는 사이클이 형성 안될가능성이 높음)**
  * **그래도 이것을 Lower Bound로 사용할수가 있다는것**

<br>

![screen captures](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/26555c18-14a6-4c4c-a71d-1c936b971e88.png)

* v1->v2로 간 이 상황에선 필기처럼 각각 저렇게 갈 수 있는 경로들이 존재
  * 이것은 잘 생각을해보면 이해가 가능
  * v1->v2로 간건 확실하니까 v2, v3, v4, v5의 경우들만 구하면되고  
    v2는 v1로는 가면안되니까 v3,v4,v5  
    v3은 v2에서 온 상황일테니까 v2를 제외한 v1,v4,v5로 가는게 자명함.
  * 이런 형태로 갈 수 있는 경로들을 모두 따져서 Bound값을 구해줘야한다.

<br>

![screen captures](/images/2023-01-03-(개념) Branch-and-Bound(분기한정법)/12c5748e-7e02-4c63-b481-68da2877dbce.png)

* 최종 결과이며 best값 보다 큰 값들은 당연히 볼필요도 없다.
* 또한 우선순위 방식을 사용한 상태이다.

<br><br>

## 최적화 문제의 SST 비교

* 최적화 문제의 SST 비교 최소화 문제(예: TSP)
  * 트리 깊어질수록 BOUND값이 절대 작아질 수 없음(Low Bound를 구함)

* 최적화 문제의 SST 비교 최대화 문제(예: 0-1냅색)
  * 트리 깊어질수록 BOUND값이 절대 커질 수 없음(Upper Bound를 구함)



