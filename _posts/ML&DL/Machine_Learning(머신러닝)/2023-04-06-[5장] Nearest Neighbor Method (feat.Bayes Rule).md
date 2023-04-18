---
layout: single
title:  "[5장] Nearest Neighbor Method (feat.Bayes Rule)"
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

**앞에서 Perceptron으로 분류를 했다면, 이번엔 예외적인 분류 방법을 소개한다.**

**또한 Memory, Model 방식의 차이점을 명확히 알고 넘어갔으면 해서 이것도 소개한다.**

<br><br>

# Nearest Neighbor Method

**주어진 모든 Training data를 기억하고 있다가 새로운 data(test data)가 주어지면 새로운 data와 가장 가까운 Training data의 class를 이용하여 새로운 data의 class를 분류하는 방법**

**K-NN 로 줄여 부르기도 함**

* 일반적으로 1개 이웃보단 K개 활용
* 거리는 무엇으로 측정하나?? 여러가지 방법들이 존재
  * 방법1) Euclidean distance : 거리이용
    * 일반적
  * 방법2) similarity : 각도이용
    * 문서유사도는 bag of words 형태인데 이럴땐 코사인(각도) 이용 방법이 효율적
    * EX)   
      d1<1,0,1,1>  
      d2<0,1,1,1>  
      d3<100,0,100,100>  
      d1과 d3의 벡터 거리가 너무너무 크다. 이럴땐 코사인이 효율적.

<br><br>

## Binary Class Vs. Multi Class

* Binary Class Classification
  * 데이터가 속할 수 있는 Class가 단 두개인 Classification.
* Multi Class Classification
  * 데이터가 속할 수 있는 Class가 세 개 이상인 Classification

<br>

**`Nearest Neighbor-based classification` 는 본질적으로 다른 아이디어 이므로 애초에 Multi-class로 볼 수 있다.**

**물론!! `Perceptron` 도 Binary지만 Multi-class로 표현할 수 있다.**

* A,B,C,D 가 있으면 각각 Binary 분류후 그 결과를 이용
* **결과 : Ao,Bx,Cx,Dx** 의 경우 A로 분류

<br><br>

## Regression Approximation(회귀)

**회귀는 클래스 분류보단, 어떤 값을 예측하는 것**

**Nearest Neighbor-based classification 은 작은 변형으로 regression문제도 근사 가능하다.**

* 제일 간단한 회귀 방법은 이웃 노드들의 평균을 구하는 방법이다.
  * 단순한 평균을 사용할 경우, 두 집의 가격이 query 집의 가격 계산에 주는 영향이 같다.
  * 그러나 실제는 더 비슷한 집과 가격이 비슷할 확률이 높다.
* 더 좋은 방법은 `wx+wx+..../w`합 으로 평균 구해서 개선한 방법이다.

<img src="/images/2023-04-06 [5장] Nearest Neighbor Method (feat.Bayes Rule)/image-20230419013902637.png" alt="image-20230419013902637" style="zoom:80%;" /> <img src="/images/2023-04-06 [5장] Nearest Neighbor Method (feat.Bayes Rule)/image-20230419013917048.png" alt="image-20230419013917048" style="zoom:80%;" /> <img src="/images/2023-04-06 [5장] Nearest Neighbor Method (feat.Bayes Rule)/image-20230419013927649.png" alt="image-20230419013927649" style="zoom:80%;" />  

<br>

<img src="/images/2023-04-06 [5장] Nearest Neighbor Method (feat.Bayes Rule)/image-20230419014031216.png" alt="image-20230419014031216"  />

* **왼쪽 빨간박스들이 개선방법으로 구한거고, 오른쪽 빨간박스들이 개선방법 전에 구한 방식이다.**
* 실제로, X값은 `4,4,4,5,8` 로 비교한걸 양쪽 확인해보면 왼쪽은 `6.xxx` 이고 오른쪽은 `5` 로 왼쪽 방식이 훨씬 집 가격을 잘 예측했다는걸 알 수 있다.

<br><br>

# Memory based Vs. Model based

**Memory based method : `K-nearest neighbor`와 같이 모든 training data를 memory에 저장하고 있어야하는 방식.**

**Model based method : `Perceptron`과 같이 분류 경험을 하나의 모델(perceptron) 의 경우 (hyperplain)로 압축하고 model parameter만 저장하고 있는 방식.(Training data는 학습 후에는 저장하고 있을 필요가 없다.)**

<br>

**Model based method와 비교했을 경우 Memory based method의 단점**

* High space complexity 
* High computational complexity (inference 시, training 시간은 거의 0)

**Model based method와 비교했을 경우 Memory based method의 장점**

* (일반적으로) 직관적으로 이해하기 쉬워 구현이 쉽다.
* Almost no training time

<br>

**실제론, 둘다 메모리에 학습데이터가 올라간다.**  

**다만, Model base는 처음에만 올라가서 학습시키고, 이후엔 그냥 사용한다.**
**그리고 Memory base는 학습은 없고, 사용할때마다 매번 메모리에 학습 데이터가 올라간다.**

* Model based 방식
  * 예를들어 Yolo로 학습된 모델의 경우 해당 모델로 바로 객체탐지가 가능하며 성능이 낮은 컴퓨터에서도 당연히 잘 동작한다. 모델에는 파라미터들만 잔뜩 저장된 상태이다.  
  * 다만, 맨 처음 Yolo를 학습시킬 때는 엄청나게 성능이 좋은 컴퓨터로 진행이 되는 편이다.
* Memory based 방식
  * 그런데 K-nearest neighbor 같은 알고리즘을 사용한 경우에는 메모리가 항상 사용되야 하므로 컴퓨터 성능도 괜찮아야 한다.  
  * 다만, 이 방식의 경우 학습을 할 필요가 없는 장점이 있다.
