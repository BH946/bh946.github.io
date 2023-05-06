---
title:  "[개념] NP(Nondeterministic Polynomial)"
categories : Algorithm
tag : [알고리즘]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



## Intro..

**지금까지 모든 알고리즘은 Deterministic(결정적) 알고리즘이였다면,  
이번에 알아볼 개념은 Nondeterministic(비결정적) 알고리즘이다.**

* `Polynomial-time Algorithm` : 다차(다항식) 시간 알고리즘 – 즉, 다항(다차) 함수

* `다루기 힘든 정도(Intractability)` : 다차 시간 알고리즘을 찾을 수 없는 문제
  * 반례 : 연쇄행렬곱셈문제
    * 왜냐하면 브루트포스는 지수함수지만, DP로 풀면 n^3의 다차 함수로 복잡도 나오기 때문
* 참고로 다차 함수는 로그도 당연 포함

<br><br>

## 문제의 일반적인 분류

* **다차시간 알고리즘을 찾은 문제**
* **다루기 힘들다고 증명된 문제**
* **다루기 힘들다고 증명되지 않았고, 다차시간 알고리즘도 찾지 못한 문제**

<br>

### **다차시간 알고리즘을 찾은 문제**

* **모든 알고리즘들이 다차시간 알고리즘**
  * 정렬 문제
  * 정렬된 배열 검색 문제
  * 행렬곱셈 문제

* **다차시간이 아닌 알고리즘도 있으나, 다차시간 알고리즘을 찾은 경우**

  * 연쇄행렬곱셈 문제

  * 최단경로 문제

  * 최소비용신장트리(MST) 문제

<br>

### **다루기 힘들다고 증명된 문제**

* **`비다항식(nonpolynomial)` 크기의 결과를 요구하는 비현실적인 문제**
  * 모든 해밀토니안 회로를 결정하는 문제
    * 만일 모든 정점들 간에 에지가 있으면, (n-1)!개의 답을 얻기 때문에 필요 이상으로 많은 답을 요구
    * 따라서 비현실적이고 다루기 힘든 문제로 분류

* **요구가 현실적이나, 다차시간에 풀 수 없다고 증명된 문제**

  * 종료 문제(Halting Problem)

  * 종료 문제는 예로 A라는 코드가 B라는 코드를 읽어들여 분석해서 무한루프를 도는지 아니면 종료를 정상적으로 하는지 이런걸 알아보는 알고리즘     
    => 이 알고리즘은 존재하지 않는다고 증명했다는 것.

    ```
    algorithm 말도안돼
     if Halt(말도안돼) == “예” then
       while true do print “야호”
    ```

    * 지금 실행하는 알고리즘이 B라고 했을때.  
      B코드를 Halt알고리즘이 읽어들여서 정상종료시?? "예" 및 종료.   
      그런데, B코드에선 "예"니까 while문으로 무한루프에 빠짐(종료X)    
      => 이러니까 상반된다고하고 존재할 수 없다는것.

<br>

### 다루기 힘들다고 증명되지 않았고, 다차시간 알고리즘도 찾지 못한 문제

* 0-1냅색, 외판원, m-색칠, 해밀토니안 회로 등등 많음
  * 이러한 문제들은 NP(Nondeterministic(비결정적) Polynomial) 문제에 속함
    * P는?? **다차시간** 알고리즘으로 풀 수 있는 **결정 문제**들의 집합!!
    * NP는?? **다차시간 비결정적** 알고리즘이여 **검증단계가 다차시간**에 수행되는 비결정적 알고리즘
      * 검증(Verification) : 결정 문제의 답이 “예” 인지를 검증하는 절차
      * 예로 S가 모든 정점을 돌았는지 먼저 체크, 모든 가중치 합이 d이하인지 확인.  
        맞으면 true, 아니면 false로 검증(verify)
        => 따라서 검증은 충분히 다차 시간 안에 수행 가능
    * P는 NP에 속함(포함관계)
    * NP는 추측단계(비결정적), 검증단계(결정적)로 구성 가능
    * 사실 이 NP관련해서는 이론부터 복잡하고 쉽지 않기 때문에 좀 더 궁금한 점은 구글링을 추천



<img src="https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/capture_markup_images/aac3ded48ac44d4e868e4e67b13b452a/74694790-55c6-46ab-8086-7c33be1afa9d.png" alt="screen captures" style="zoom: 80%;" />

<img src="https://slid-users-assets-v1-seoul.s3.ap-northeast-2.amazonaws.com/public/image_upload/aac3ded48ac44d4e868e4e67b13b452a/b0ea4931-63e6-48db-8aeb-a061b9b76b24.png" alt="screen captures" style="zoom: 50%;" />

<br><br>

## 최적화 문제 vs 결정 문제

* 최적화 문제 : 최적의 해를 찾아야 한다.

* 결정 문제 : 대답이 "예" 또는 "아니오" 로 이루어지는 문제

<br>

**최적화 문제 알고리즘으로 결정문제 알고리즘 만드는 모습**

```
Alogorithm DecTSP(G, k)
 if (OpTSP(G)<=k) return ‘YES’
 else return ‘No’
```

<br>

**결정 문제 알고리즘으로 최적화 알고리즘 만드는 모습**

```
// 일반적
Alogorithm OpTSP(G)
 for(k=1; ; k++) {
  if(DecTSP(G,k)==’Yes’) return k
 }
 
// 개선 version
Alogorithm OpTSP(G) // 개선된 version
 // 바이너리 서치를 활용
```

