---
title: "Naive Bayes (feat.Bayes Rule)"
categories: Machine_Learning
tag: [python, machine_learning, concept]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



# Intro

**이때까지 `Perceptron, k-nn Classifier` 을 공부했고, 이번엔 `Naive Bayes Classifier`을 소개한다.**

**Bayes Rule을 이용한 Classifier**

* **Perceptron** : Feature Space의 공간을 나누는 Hyperplane을 찾는 기하학적 접근.
* **k-nn Classifier** : Feature Space의 공간에서 가까운 데이터를 찾아 판단하는 기하학적 접근.
* **Naïve Bayes Classifier** : 확률이론을 사용한 확률적 접근

<br><br>

# Naive Bayes Classifier
<img src="/images/2023-04-07-[6장] Naive Bayes (feat.Bayes Rule)/image-20230419162152390.png" alt="image-20230419162152390"  />

* **확률을 구하는 방법이므로 제일 높은 확률이 추측한 정답이 된다(argmax)**

<br><br>

## 식 유도 원리

**우리가 구하는것은 `P(Y|X1,X2,...,Xn)` 라고 볼 수 있다.  **

**`P(Y|X1,X2,...,Xn)`란? X1,X2,...,Xn 인 특징이 동시에 관측되었을 때 Y라는 Class에 속할 확률을 의미한다.** 

**Bayes' rule 공식을 통해서 조건부 식을 유도한다.**

<img src="/images/2023-04-07-[6장] Naive Bayes (feat.Bayes Rule)/image-20230419162350148.png" alt="image-20230419162350148"  /> 

<br>

**유도한 식에서 분모를 제외하면 처음에 언급했던 공식이 유도됨을 알 수 있다.**

<img src="/images/2023-04-07-[6장] Naive Bayes (feat.Bayes Rule)/image-20230419162733619.png" alt="image-20230419162733619" style="zoom:80%;" /> 

* 분모를 제외해도 되는 이유는 전부 같은 분모들이 생성되기 때문이다.
* 따라서 확률값을 비교만하는 입장으로써 분모값은 생략하고 비교해도 되는것이다.

<br>

**마지막으로 Conditionally Independent(조건부 독립) 를 통해서 위 공식을 쉽게 계산도 할 수 있다.**

**일반적으로 "독립"이라고 가정해도 문제 없는경우들이 많기 때문에 Conditionally Independent 를 가정하면 위 식을 다음과 같이 변경 할 수 있다.**

* 물론, 질병 판별하는 이런것에는 함부로 "독립" 을 가정하면 분류의 정확성이 떨어져서 치명적이므로 상황에 맞게 판단해서 "독립"을 가정해야한다.

<img src="/images/2023-04-07-[6장] Naive Bayes (feat.Bayes Rule)/image-20230419164326969.png" alt="image-20230419164326969"  />

<br><br>

## 예시

**스키관련 데이터 셋을 이용해서 스키를 타느냐 안타느냐에 관해 추측해보겠다.**

<img src="/images/2023-04-07-[6장] Naive Bayes (feat.Bayes Rule)/image-20230419163031886.png" alt="image-20230419163031886"  />

* Y=y1, Y=y2 일 확률 식을 공식을 이용해서 구한다.

<br>

**이후 값을 대입만 하면 되므로 데이터셋을 통해서 각각 값을 얻어낸다.**

<img src="/images/2023-04-07-[6장] Naive Bayes (feat.Bayes Rule)/image-20230419163226371.png" alt="image-20230419163226371"  />

* 데이터셋을 통해서 왼쪽 값들을 전부 구할 수 있었고, 이 값들을 오른쪽 공식에 적용했다.
* **결과로는 스키를 타는 Y=y1 확률이 더 높게 나왔으므로 Skiing은 Yes라는 결론을 내린다.**

<br>

**다만, 값을 구하는 과정에서 Y=y2일 확률이 0이 나오는건 문제가 있다.**  

**이는 데이터가 적어서 발생한 문제이며, 이런 경우는 "사전확률"을 구해서 해결할 수 있다.**

<br><br>

## Priori probability(사전확률)

**확률이 0이 나올때 처럼 결과를 믿을수 없는 경우 "사전확률"을 사용하겠다는 목적을 가진다.**  
**다르게 말하자면, 데이터가 작을땐 사전지식을 믿고 데티어가 많을땐 데이터 확률을 믿는다.**

* 사전확률은 정말 간단히 1/N 으로 설정하기도 한다.
* 책에서 제시하는 공식은 아래와 같다.

<br>

<img src="/images/2023-04-07-[6장] Naive Bayes (feat.Bayes Rule)/image-20230419163819582.png" alt="image-20230419163819582"  />

