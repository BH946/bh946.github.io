---
title:  "[개념] Computational Complexity(계산복잡도)"
categories : Algorithm
tag : [알고리즘, 시간복잡도, 공간복잡도, 최악의복잡도]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



## Intro..

왜 알고리즘에서 복잡도 계산이 필요한가??  
이 질문에 대한 답은 `프로그램 설계 과정` 속에 있다.

* 프로그램 설계 과정 : 문제 -> **알고리즘** -> **(불)만족 판단** -> 프로그램
* 이때 **만족 판단**의 단계는 실제로 프로그램 테스트X
  * 직접 계산복잡도(시간, 공간 등등 복잡도)를 구해서 **예상**을 하는 과정
* 이때 **알고리즘**이란? 
  * 입력 -> 출력 하는 계산 절차
  * **알고리즘 표기법** : 자연어, 프로그래밍언어, 의사코드(Pseudo-code)
    * **의사코드(Pseudo-code) 추천**
    * 자연어는 수학 표현이 어렵고, 프로그래밍언어는 각각의 언어를 알아야하기 때문

<br>

## Computational Complexity(계산복잡도)

`시간복잡도, 공간복잡도, 정확도 분석`을 소개하겠다.(정확도 분석은 간략하게)

* **정확도 분석**
  * 어떠한 입력에 대해서도 답을 출력하면서 멈춤 –> **정확**한 알고리즘
  * 어떤 입력에 대해서 멈추지 않거나, 틀린 답을 출력하면서 멈춤 –> **부정확**한 알고리즘



### 시간복잡도

`입력크기`에 따라서 `단위연산`이 몇 번 수행되는지 결정하는 절차

* 입력크기(input size) : 배열의 크기, 행렬의 크기, 트리에서 마디와 이음선 수 등등
* 단위연산(basic operation) : 비교(comparison), 지정(assignment)



**분석 종류** : 모든, 최악, 평균, 최선의 경우(Every, Worst, Average, Best)

* `Every` 만 **입력 값**과 무관 
  * 덧셈 알고리즘에서 입력 값(숫자)이 커도 속도 동일해서 전혀 상관X

* `나머지(Worst, Average, Best)`는 **입력 값, 입력 크기**에 종속
  * 순차 검색에서 찾을 입력 값에 따라 검색횟수가 달라지므로 `Every`분석은 불가능



### **공간**복잡도

`메모리 사용`과 큰 관련이 있으며, 이 공간을 결정하는 절차

* **제자리정렬(in-place sort)** : 입력 값을 저장하는데 필요한 만큼만 메모리를 사용해서 해당 메모리를 재사용을 하며 정렬하는 알고리즘
  * 예외 : 합병정렬은 입력 배열 S이외에 배열 U, V를 추가 만들어 사용하므로 제자리정렬X
  * 오히려 2n만큼 공간이 더 필요(다만, n으로 코드 개선은 가능! 정렬 포스팅에서 소개!)

<br>

## 복잡도 표기법, 중요 성질 소개

### 표기법

* **큰(Big)O 표기법** – 점근적 상한(Asymptotic **Upper** Bound). 즉, **g(n)<=O(f(n))**
* 정의 : ![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW00002c8c6573.gif)  
  * 한마디로 정의 : `하나 이상 실수 c와 성립`

* 예제 :  ![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW00002c8c6575.gif)

* **작은(Small)o 표기법** – `모든 실수 c와 성립`. 즉, **g(n)<o(f(n))**

* **큰(Big)![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW000012a07483.gif) 표기법(오메가)** – 점근적 하한(Asymptotic **Lower** Bound). 즉, **O와 부등호 반대**
  * 한마디로 정의 : `하나 이상 실수 c와 성립`

* **작은(Small)![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW000012a07485.gif) 표기법(오메가)** – `모든 실수 c와 성립`. 즉, **o와 부등호 반대**

* **![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW000012a07487.gif) 표기법(세타)** – 점근적 상한, 하한의 교집합(Asymptotic **Tight** Bound). 즉, **교집합**



### 차수의 주요 성질

* **빅오, 빅오메가, 세타 기호의 성질**
  *  ![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW00002c8c657b-16721460914321.gif)  

* **로그는 같은 카테고리에 속하는 성질(그래서 세타를 사용)**
  *   ![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW00002c8c657e.gif)  

* **지수 복잡도 함수는 같은 카테고리 아닌 경우 존재(a<b인 경우 소개)**
  *   ![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW00002c8c6582.gif)

* **복잡도 크기순 나열(암기 추천)**
  * ![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW000012a07480.gif)  

* **극한, 로피탈 관련은 생략**

<br>

## 복잡도 문제 풀이

**수업 때 배운 풀이 소개**

<img src="/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/image-20221227221028904.png" alt="image-20221227221028904" style="zoom: 80%;" />

<img src="/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/image-20221227221201616.png" alt="image-20221227221201616" style="zoom:80%;" />

* lg(n!) = nlgn으로 볼 수 있기 때문에 nlnn과 같은 카테고리로 묶음
  * 또한 눈으로 **바로 순서 매길수 있기 때문**에 순서대로 나열
* `파란색 수식`들은 비교해봐야 알 수 있어서 **바로 순서 매기지 못한 식들**
  * 여기선 **(lgn)^2**을 비교하는 과정을 나타냄
  * n=2^k, lgn=k로 치환해서 문제를 풀었다는 특징이 매우 중요!!(또한 **n**과 비교해본것)
    * 결론 : n보다 작다
    * 따라서 그보다 작은 루트n과 비교해봐야 한다.

<img src="/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/image-20221227221811520.png" alt="image-20221227221811520" style="zoom:80%;" />

* 루트n보다도 작다는 결론을 얻을 수 있다.
  * 치환해서 풀어보면 루트n은 지수가 나오므로 당연히 k^2보다 클 수 밖에 없기 때문
  * 따라서 **(lgn)^2**은 맨 앞으로 순서가 가게 된다.

<img src="/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/image-20221227221959831.png" alt="image-20221227221959831" style="zoom:80%;" />

* 다음은 **로그와 지수의 수학적 성질을 이용**해서 바로 비교가 가능

<img src="/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/image-20221227222135531.png" alt="image-20221227222135531" style="zoom:80%;" />

* e^n과 비교하는 모습이며, 둘다 로그 씌우고 n=2^k하던 방식을 적용해줌으로써 비교!
  * e^n이 2^k로 치환 됨으로써 klgk보다 크기 때문에 **(lgn)!**은 e^n 앞으로 가야한다.
  * 앞의 n^2.5와 비교도 해야하는데 이는 생략

<img src="/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/image-20221227222528449.png" alt="image-20221227222528449" style="zoom:80%;" />

* 2^n!은 n^n과 비교해보면 되고, 양변 로그해보면 바로 비교가 된다.
  * 과정 : n^n, 2^n!을 양변 로그하면 nlgn, n!lg2 이다. lg2는 상수니까 n!으로 봄
  * 따라서 로그보다 팩토리얼이 더 크기 때문에 2^n!이 더 크다.

* 이것이 최종 답



**추가로 2문제 더 소개**

* **for i~k, i=i*i 의 복잡도?**

  * 1x1=1, 2x2=4, 4x4=16, 16x16=.. => 2, 4, 16, ,, => 2, ![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW00002c8c6585.gif)  ...

  * ![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW00002c8c6587.gif)   (k가 1,2,3,4... 로 총 k번 돌거기 때문에 k를 구하기 위한 식. n은 총 k번 이후 값을 그냥 의미하는것)

  * ![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW00002c8c6589.gif)   (k를 구하기위해 양변 log) 
  * ![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW00002c8c658b.gif)   (한번더 log)

  * 최종 답 : O(loglogn)

* **for j~n, for k~i => 2중 for문 의 복잡도?**
  * 참고로 i= ![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW00002c8c658d.gif) 으로 문제에서 주어짐
  * 첫번째 for문 복잡도 : n
  * 두번째 for문 복잡도 : ![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW00002c8c658d.gif)
  * 최종 답 : O(n![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW00002c8c6591.gif)) = O(![img](/images/2022-12-26-(개념) Computational Complexity(계산복잡도)/DRW00002c8c6593.gif))
