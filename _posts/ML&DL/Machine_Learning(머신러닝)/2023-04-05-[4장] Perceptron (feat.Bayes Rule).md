---
layout: single
title:  "[4장] Perceptron (feat.Bayes Rule)"
categories: Machine_Learning
tag: [python, machine_learning, concept]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



# 다양한 용어

**Feature Vector : 입력 instance를 우리가 관심 있어 하는 (혹은 정량화 가능한) 특징(Feature)으로만 나타낸 형태.**

* 예) 사과의 Feature : Size, Color
* 사과 1 : < 10 cm, Red>,  <= feature vector for “사과1”
* 사과 2 : < 12 cm, Green>,  <= feature vector for “사과2”

<br>

**Hyperplane : 현재 차원을 둘로 나누는 한차원 작은 평면(or 선) 이다.**

**Hyperspace : vector space**

<br>

**Training Data :  ML 알고리즘을 학습하기 위해 사용되는 학습 데이터**

**Test Data : 학습이 제대로 되었는지 시험하기 위한 데이터 (Training에 사용하지 않은 데이터)**

**Validation Data : 방향이 잘 나아가고 있는지 검증을 위한 데이터**

<br>

**Data Mining : 데이터로부터 지식을 획득하는 과정과 그 표현 및 응용을 "데이터 마이닝"**   

**기계 학습과 데이터 마이닝의 두 가지 기술된 작업은 공식적으로 매우 유사하기 때문에 두 영역에서 사용되는 기본 방법은 대부분 동일합니다.   
따라서 학습 알고리즘 설명에서는 기계 학습과 데이터 마이닝을 구분하지 않습니다.**

**데이터마이닝, 또는 자료채굴은 대규모로 저장된 데이터안에서 체계적이고 자동적으로 통계적규칙이나 짜임을 분석하여 가치있는 정보를 빼내는 과정이라고도 할 수 있다.**

* Perceptron 알고리즘을 이해하고 나면 데이터마이닝이 좀 더 이해가 될 것이다.

<br>

**Data Analysis - Feature dependency : 데이터 마이닝 관점에서는 Feature의 통계적인 연관성은 중요**

* 예로 1000개의 feature 중 중요한 feature 10개만 골라줘라 하면?? 연관성을 모르면 어려움

* 또한, 요즘 뉴럴 네트워크는 알아서 중요한 feature 들을 골라주지만, 그래도 이것 자체도 컴퓨터 리소스를 많이 사용

* **따라서 feature를 미리 줄이면 좋은점이 있는거지, 안좋을건 없다.**

* **그래서 그 방법은?? 비슷한 feature들을 날려버리는 방법 (상관관계 분석)**

  * Covariance : Feature i, j의 분포 경향이 비슷하면 양의 값, 반대이면 음의 값을 가진다.  
    두 Feature i, j 의 Co-relation coefficient 𝑲𝒊𝒋 (=상관계수), **시그마**는 공분산, **s**는 표준편차를 의미

    <img src="/images/2023-04-05 [4장] Perceptron (feat.Bayes Rule)/image-20230419004143853.png" alt="image-20230419004143853" style="zoom:80%;" /> 

    * 상관계수 1: 경향 완전 일치
    * 상관계수 -1: 경향 완전 불일치

<br>

**Linear Classifier : 학습된 학습 기준선(Hyperplane) 들이 곡선이 아닌 직선인 것을 의미한다.  **
**곡선은 Non-Linear Classifier 로 봐야한다.**

**Linearly Separable : 위 개념과 비슷한데, 2개의 class를 Hyperplane 으로 나누어서 직선의 위, 직선의 아래로 분류를 의미한다.**

<br><br>

# Perceptron

**Perceptron 알고리즘은 "인공 신경망"의 구성요소로써 간단히 말하자면 ''다중입력 -> 한개출력"**

**벡터로 식을 나타낸 경우 `WX=0` 의 형태이며, W는 초기에 <1,1,1> 같이 랜덤 값으로 설정하고, 학습하고 나면 이것이 최종적으로 학습한 파라미터값이 되어서 학습된 값으로 생성된다.**

<br><br>

## 기본식

**원점에서 비교하는 경우 : `Perceptron: 𝑤1 𝑥1  + 𝑤2 𝑥2   =  0`**

* Weight vector : 𝑤벡터  = <  𝑤1 , 𝑤2   > 
* Feature vector : 𝑥벡터  = <  𝑥1 , 𝑥2   >

<br>

**원점이 아닌곳에서 비교하는 경우 : `Perceptron: 𝑤1 𝑥1  + 𝑤2 𝑥2  + 𝜃  =  0 `**

* bias가 추가되며 이를 벡터에 한 차원을 추가해서 `WX=0` 으로 또 표현이 가능하다.
* **따라서 학습하게 되면 W 뿐만 아니라 내부에 bias도 함께 학습된다.**

<br><br>

## Perceptronn - Learning algorithm

**보통 `경사하강법` 을 많이 사용하지만, 여기선 더욱 원초적인 기초 방법을 소개한다.**

<img src="/images/2023-04-05 [4장] Perceptron (feat.Bayes Rule)/image-20230419010407021.png" alt="image-20230419010407021"  />  **M+, M-가 각각 클래스를 의미하고, 분류될때까지 w를 업데이트**

<br>

**찾는과정**

<img src="/images/2023-04-05 [4장] Perceptron (feat.Bayes Rule)/image-20230419010836881.png" alt="image-20230419010836881"  />

<br><br>

## Learning algorithm 개선

**원래 초기값 W를 아무거나 잡고, W업데이트를 그냥 벡터로 진행했었다.**

**이를 초기값 W개선, W업데이트 개선을 통해서 알고리즘을 개선한 방식을 제안한다.**

* 초기값 W는 `"클래스1을 향하는 벡터X - 클래스2를 향하는 벡터"` 식을 사용한다.
  * 이 값을 사용하면, 초기 법선벡터가 굉장히 분류율이 높게 생성된다.
* W업데이트 개선은 `유닛벡터` 를 이용한 방법이다.
  * 기존 벡터는 굉장히 크기가 크기 때문에, 크기는 1로 고정하고 방향만을 다뤄서 효율을 높인다.

<br>

<img src="/images/2023-04-05 [4장] Perceptron (feat.Bayes Rule)/image-20230419011403773.png" alt="image-20230419011403773"  />

