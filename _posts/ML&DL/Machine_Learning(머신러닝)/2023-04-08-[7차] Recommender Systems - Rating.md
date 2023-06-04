---
title: "[7차] Recommender Systems - Rating"
categories: Machine_Learning
tag: [python, machine_learning, concept, Rating_prediction, Similarity_Based_Collaborative_Filtering, Matrix_Factorization]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



# Intro

**Recommender System : 사용자 (user) 개인의 취향에 맞춘 온라인 상의 아이템(Item, 상품 등)을 추천하는 시스템**

* **추천 시스템의 방법중에서 Rating Prediction(=평점 예측) 방식을 공부해보려고 한다.**

* **대표적 알고리즘 2개인 `Similarity Based Collaborative Filtering` 과 `Matrix Factorization` 에 대해서 알아보겠다.**

<br><br>

# Similarity Based Collaborative Filtering

**한국어로 `유사도 기반 협업 필터` 라고 한다.**

**즉, 아래 행렬의 `?` 를 예측하는 것이다.**

* 행 : 사용자(user)
* 열 : 아이템(item)
* 요소 : 점수(rating)

<img src="/images/2023-04-08-[7차] Recommender Systems - Rating/image-20230604145721184.png" alt="image-20230604145721184"  /> 

<br><br>

## 공식

**`아이템 기반 협업 필터 (Item-based CF)` 식을 소개한다.**

* i : **예측**하려는 item
* i' : **실제** 추천 후보 item
* 아래 식의 핵심은 `유사도 * 실제 평점` 과 `Normalization` 이다.

![image-20230604145934080](/images/2023-04-08-[7차] Recommender Systems - Rating/image-20230604145934080.png)

<br>

**유사도(sim)는 정확히 말하자면 `코사인 유사도` 이다. 따라서 `코사인 유사도 공식` 을 사용**

**아래부터는 user1의 Titanic 의 평점을 예측하는 과정을 나타내보겠다.**

**1. 예측에 필요한 sim 들을 구한다.**

![image-20230604150855980](/images/2023-04-08-[7차] Recommender Systems - Rating/image-20230604150855980.png)

<br>

**2. 구한 값들을 이용해서 s<sub>user1,Titanic</sub> 을 구한다.**

* **user1의 Titanic 의 예측 점수가 4 점이라는 결론을 내릴 수 있다.**

![image-20230604151114601](/images/2023-04-08-[7차] Recommender Systems - Rating/image-20230604151114601.png)

<br><br>

# Matrix Factorization

**한국어로 `행렬 분해` 라고 한다.**

**`Objective Function(=목적, 오차, 손실, 비용함수)` 을 최소화 하는 P, Q 값을 계산.**  
**물론 P, Q 값의 초기값은 임의로 설정후 P, Q 값을 update 해나가는 것이다.**

**앞서 소개한 `유사도 기반 협업 필터` 의 단점 때문에 `행렬 분해` 를 사용하게 되었는데, 비교해보자.**

* **유사도 기반 단점**
  * 계산을 위해 Rating Matrix를 모두 메모리에 올려야 한다. (높은 메모리 요구량)
  * 하나의 Item 예측에 여러 Item들 유사도를 계산해야 한다. (높은 계산량)
  * 두 아이템을 공통적으로 소비한 사용자가 적어도 한 명 필요하다. (한계)
* **행렬 분해 장점**
  * 분해한 P, Q 행렬의 크기는, Rating Matrix보다 매우 작다. (낮은 메모리 요구량)
  * 두 벡터의 내적만 계산한다. (낮은 계산량)
  * 두 아이템을 공통적으로 소비한 사용자가 없어도 계산 가능하다. 

## 공식

**이 행렬 분해의 핵심은 아래 수도 코드가 전부이다.**

* **참고 링크 : [경사하강법](https://bh946.github.io/machine_learning/02.-MLP(%EB%8B%A4%EC%B8%B5-%ED%8D%BC%EC%85%89%ED%8A%B8%EB%A1%A0)/#4-optimizationexgd-method)**
  * **우린 W가 아닌 P, Q를 Update 하게 된다.**

![image-20230604154827038](/images/2023-04-08-[7차] Recommender Systems - Rating/image-20230604154827038.png)

<br>

**Objective Function 의 공식**

![image-20230604154400310](/images/2023-04-08-[7차] Recommender Systems - Rating/image-20230604154400310.png)

<br>

**위의 Objectiv Function을 최소화 시켜야 하므로 이때 `Stochastic Gradient Descent` 사용**  

**아래는 공식을 풀어낸 모습을 나타낸다.**

![image-20230604154555539](/images/2023-04-08-[7차] Recommender Systems - Rating/image-20230604154555539.png)   

**=>** ![image-20230604154616412](/images/2023-04-08-[7차] Recommender Systems - Rating/image-20230604154616412.png) 

<br>

**아래 식의 의미는 수많은 iterate(순회)에도 `이전 오차와 현재 오차의 크기` 가 큰 변화가 없을 시 설정한 iterate 반복 횟수를 채우기 전에 유도리 있게 break를 한다는 것이다.**

![image-20230604155157694](/images/2023-04-08-[7차] Recommender Systems - Rating/image-20230604155157694.png) 

<br><br>

## 구현 예시

**실제 구현했을때 코드의 예시**

* **자세한것은 아래 링크 참고**
  * [실습](https://bh946.github.io/machine_learning/HW2-MatrixFactorization(%ED%96%89%EB%A0%AC%EB%B6%84%ED%95%B4)-Python/)

![image-20230604155224192](/images/2023-04-08-[7차] Recommender Systems - Rating/image-20230604155224192.png)

