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

![image-20230613221219861](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230613221219861.png) 

<br><br>

## 뉴런 개념에 GD를 사용하기 위해선?

**Before 가 아닌 After를 사용해야한다.**

![image-20230604165338724](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230604165338724.png) 

<br>

**즉, Sigmoid와 같은 함수로 Activation함수를 변경해야한다.**

* GD(Gradient Descent)를 사용하여 Objective Function 최적화 가능
  * **미분 가능하기 때문에**
  * **단, 그냥 간단한 Perceptron Learning 알고리즘은 당연히 수행가능하고 "미분"이 필요한 GD같은 경우 불가능 하다는 의미이다.**
    * **[Perceptron 알고리즘](https://bh946.github.io/machine_learning/4%EC%B0%A8-Perceptron-(feat.Bayes-Rule)/#perceptronn---learning-algorithm)**
* Non-linearly separable problem도 풀 수 있는 가능성이 생김.
  * Sigmoid, Tanh, ReLU 등등 모두 **Non-linear function**

<br>

**GD의 언어 혼동하는 경우들이 있는데 `SGD(stochastic gradient descent), BGD(Batch gradient descent), MGD(Mini-batch gradient descent)` 가 혼동이 되는경우들이 종종 있다.**

* SGD는 데이터 하나하나에 파라미터 업데이트를 하고, BGD는 데이터 전체를 계산후에 평균값으로 파라미터를 업데이트 한다.
* 각각 장단점이 있는데, 이 둘의 장점들을 가져온게 MGD 방식이다.
* 단, 이 MGD 방식을 SGD로 부르기도 하는곳이 있어서 언어의 혼동에 주의하자.
  * [참고링크](https://light-tree.tistory.com/133)

<br>

**참고로 오차(목적)함수 계산때 예측값을 나타내는게 "활성화함수(f)" 값 일텐데 이를 분류 결과에 알맞는 값으로 클래스를 분류한 값을 `C^` 로 나타내겠다.**

![image-20230613222925700](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230613222925700.png)  

<br><br>

# SLP, MLP, Backpropagation

* **hidden Layer의 유무에 따라 SLP, MLP 로 분류**
  * **참고로 Hidden Layer, Hidden Node를 혼동하지 말것**

* **Backpropagation(역전파) 개념이 사용**
  * **예전에 정리한 게시글 참고 : [MLP](https://bh946.github.io/machine_learning/02.-MLP(%EB%8B%A4%EC%B8%B5-%ED%8D%BC%EC%85%89%ED%8A%B8%EB%A1%A0)/), [Backpropagation 역전파](https://bh946.github.io/machine_learning/01.-Backpropagation(DL-%EC%97%AD%EC%A0%84%ED%8C%8C)/)**

<br><br>

## SLP

**Activation Function이 non-linear 임에도 불구하고, Single layer perceptron은 Non-linear 한 문제를 풀기 어렵다.**

* 왜? Perceptron이 1층 이라서!! - hidden layer가 없다.
* Input Layer는 Input을 다음 Layer로 전달 목적일 뿐 **학습(w)에는 기여하지 않는다.**
* **자세한건 아래그림**

<br>

**아래그림은 MLP의 모습을 나타낸 그림인데, MLP의 경우 h 직선이 굉장히 많이 그려지면서 Non-linearly 분류를 더 잘할 수 있지만, SLP의 경우 아래 그림의 i2 직선처럼 그저 직선1개로 분류하기 때문에 훨씬 부정확하게 분류된다고 생각하면 된다.**

![image-20230613223258003](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230613223258003.png) 

<br><br>

## MLP

**Multi Layer는 hidden layer가 존재한다는게 SLP와의 차이점**

* hidden layer가 1개라도 존재한다면 그것은 MLP

<br>

**미분값 구하는 공식 유도과정 1개만 보고 넘어가자!**

![image-20230613223734332](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230613223734332.png) 

<br><br>

## Backpropagation

* 추론 방향 : Feed Forward
* Train (오차 교정)방향 : Back Propagate
* **자세한건 링크 확인..**

**Node가 존재하는 층에 관계 없이 (=Hidden Layer나 Output Layer나 관계 없이)   
`Gradient Descent` 를 사용하여 model parameter(w, b)를 학습하는데, Output Layer과 Hidden Layer을 구하는 모습을 소개한다.**

**우선 용어와 구조들을 먼저 확인하자!**

![image-20230605002925563](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230605002925563.png)

<br>

![image-20230605002937928](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230605002937928.png)

<br>

**용어 설명은 했고, 해당 식으로 Output node, Hidden node의 Gradient 구하는 과정을 소개한다.**

**아래 내용들은 Hidden Layer가 1개인 MLP 구조의 예시(바로 위 그림의 구조)이다.**

* 정말 중요한점은 "hidden node"도 그냥 "output node"처럼 미분하면 되지만, **식을 조금 변형해서 이전에(output) 계산한 미분값을 활용**하는 모습을 볼 수 있다.
* 이것이 가능한 이유는?? 바로 **hidden node의 출력은 output node의 입력으로 사용**하기 때문이다.
* **바로 아래그림은 hidden node를 구하는 과정이다.**

![image-20230613224244223](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230613224244223.png) 

![image-20230613224253923](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230613224253923.png) 

![image-20230613224306439](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230613224306439.png) 

<br>

**아래는 바로 위 MLP 구조의 Output node 한개와 hidden node 의 Gradient를 구하는 과정의 핵심을 보여주는 식이다.** 

![image-20230605002859911](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230605002859911.png) 

<br>

**다음은 Hidden Layer가 여러개일때의 MLP에서 식이다.**

* 참고로 앞에서 한건 output node가 1개였는데, 여기선 k개 이고, hidden node도 k개이다.
* 즉, 1개의 hidden node 의 식을 구해보면 output node가 k개 연관있기 때문에 아래처럼 식이 도출되는 것이다.

![image-20230605003455909](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230605003455909.png) 

![image-20230614005812942](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230614005812942.png) 

<br>

**이를 일반화 해보면 아래와 같다.**

![image-20230614005857257](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230614005857257.png) 

<br>

**마무리로 장점을 보자!**

![image-20230605003538057](/images/2023-04-09-[8차] Neural Network(slp, mlp, backpropagation)/image-20230605003538057.png)
