---
title: "[8차] Neural Network(slp, mlp, backpropagation)"
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

* Neural Network 를 컴퓨터와 뇌의 관점에서 비교
* 이 개념을 활용한 응용
  * SLP, MLP
  * Backpropagation

<br><br>

# Neural Network in Computer Science

**Nerual Network의 특징**

* 병렬
* 분산 저장
* 연결 강도의 변화로 학습 발생

<br>

**Nerual Network와 일반 컴퓨터의 비교**

![image-20230604162916732](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230604162916732.png)

<br><br>

## Node(Artificial Neuron) VS. Biological Neuron

**Biological Neuron 관점에서 먼저 설명하자면,**

**실제 뇌에서의 뉴런은 시냅스들의 다른 뉴런(일반적으로 수천 개)들로부터 입력을 받는다.  
입력은 대략적으로 합산되고, 입력이 임계값(=threshold)을 초과하면 뉴런은 신체에서 축삭 아래로 이동하는 전기 스파이크를 다음 뉴런(들)으로 보낸다.**

**Learning in Brain 을 보면, 뉴런 사이의 `연결 강도` 가 변화한다.**  
**Long Term Potentiation(LTP)이다.**

* 자극의  결과, 2개의  신경  세포  사이의  연결  강도  강화/약화된  상태가  장기간  유지되 
  는  것.
* 학습  및  기억을  위한  세포  기반.

<br>

**앞서 설명한 뉴런을 Node(Artificial Neuron) 관점으로 보는것이 아래 그림들의 핵심이다.**

* **Node 하나를 하나의 Perceptron(다중 입력-> 하나 출력 알고리즘)으로 볼 수 있다.**

![image-20230604163500243](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230604163500243.png)

![image-20230604163520764](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230604163520764.png)

<br><br>

## 뉴런 개념에 GD를 사용하기 위해선?

**Before 가 아닌 After를 사용해야한다.**

![image-20230604165338724](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230604165338724.png)

<br>

**즉, Sigmoid와 같은 함수로 Activation함수를 변경해야한다.**

* GD(Gradient Descent)를 사용하여 Objective Function 최적화 가능
  * **미분 가능하기 때문에**
* Non-linearly separable problem도 풀 수 있는 가능성이 생김.
  * Sigmoid, Tanh, ReLU 등등 모두 Non-linear function

<br><br>

# SLP, MLP, Backpropagation

* **hidden Layer의 유무에 따라 SLP, MLP 로 분류**
* **Backpropagation(역전파) 개념이 사용**
  * **예전에 정리한 게시글 참고 : [MLP](https://bh946.github.io/machine_learning/02.-MLP(%EB%8B%A4%EC%B8%B5-%ED%8D%BC%EC%85%89%ED%8A%B8%EB%A1%A0)/), [Backpropagation 역전파](https://bh946.github.io/machine_learning/01.-Backpropagation(DL-%EC%97%AD%EC%A0%84%ED%8C%8C)/)**

<br><br>

## SLP

**Activation Function이 non-linear 임에도 불구하고, Single layer perceptron은 Non-linear 한 문제를 풀기 어렵다.**

* 왜? Perceptron이 1층 이라서!! - hidden layer가 없다.
* Input Layer는 Input을 다음 Layer로 전달 목적일 뿐 학습(w)에는 기여하지 않는다.
* **자세한건 링크 확인**

<br><br>

## MLP

**Multi Layer는 hidden layer가 존재한다는게 SLP와의 차이점**

**자세한건 링크 확인**

<br><br>

## Backpropagation

* 추론 방향 : Feed Forward
* Train (오차 교정)방향 : Back Propagate
* **자세한건 링크 확인..**

**Node가 존재하는 층에 관계 없이 (=Hidden Layer나 Output Layer나 관계 없이)   
`Gradient Descent` 를 사용하여 model parameter(w, b)를 학습하는데, Output Layer과 Hidden Layer을 구하는 모습을 소개한다.**

![image-20230605002925563](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230605002925563.png)

<br>

![image-20230605002937928](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230605002937928.png)

<br>

**용어 설명은 했고, 해당 식으로 Output, Hidden의 Gradient 구하는 과정을 소개한다.**

* **단, Hidden Layer가 1개일때의 식이다.**

![image-20230605002859911](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230605002859911.png)

<br>

**다음은 Hidden Layer가 여러개일때의 식이다.**

![image-20230605003455909](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230605003455909.png)

![image-20230605003525848](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230605003525848.png)

<br>

**마무리**

![image-20230605003538057](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230605003538057.png)
