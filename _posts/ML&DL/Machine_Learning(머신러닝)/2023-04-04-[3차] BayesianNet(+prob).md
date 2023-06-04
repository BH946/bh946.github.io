---
layout: single
title:  "[3차] BayesianNet(+prob)"
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

**실생활에서 t/f 인지 쉽게 확신할 수 없는 명제가 많다. 이런 불확실성이 있는 명제들은 어떻게 추론할건가?  
=> 확률!!**

**확률 지식은 안다고 가정하고 바로 "베지언 네트워크" 를 자세하게 설명하고 마치겠다.**

<br><br>

# Bayesian Network

**베이지안 네트워크는 그래프로 표현 및 Generative mode(=생성 모델. 예로 GPT) 이다.**

<br><br>

## 구조 예시

**사실 1. 도둑이 들 수 있다. (Burglary)   
사실 2. 지진이 일어 날 수 있다. (Earthquake)  
사실 3. 도둑이 들거나 지진이 일어나면 알람(Alarm)이 울릴 수 있다.   
사실 4. 알람이 울리면 John (John)이 알려줄 수 있다.  
사실 5. 알람이 울리면 Mary (Mary)가 알려줄 수 있다.  
사실 1 에서 5 까지에 이들이 동시에 발생할 확률을 알고 있다.**   

* Query : John 이 알람이 울렸다고 알려 주었을 때 도둑이 들었을 확률?  
  * = P(Burglary|John)

<img src="/images/2023-04-04 [3장] BayesianNet(+prob)/image-20230418231308893.png" alt="image-20230418231308893"  />

<br>

**Naïve 하게 계산했으면??**   

* 만약 `P(Burglary)` 를 구해야하면 `P(A) = P(A ∧ B ∧ C) + P(A ∧ ¬B ∧ C) + P(A ∧ B ∧ ¬C) + P(A ∧ ¬B ∧ ¬C)` 처럼 이런 형태로 엄청 많은 계산을 해야함.
* 즉, 총 `2^5-1 = 31개`  의 Event 경우의 수를 다뤄야 한다.

**장점1) 그런데, Bayesian Network의 경우 따져야할 Event수가 확 줄어든다 (31개 -> 10개)**

<br>

<img src="/images/2023-04-04 [3장] BayesianNet(+prob)/image-20230418232131707.png" alt="image-20230418232131707"  /> 

**장점2) Generative model : Allows arbitrary queries to be answered 이다.**

<br><br>

## 사용(계산) 과정

**John이 Alarm이 왔다고 알려주었는데 도둑이 들었을 확률인 `P(Bur|J)` 의 값은??**

* **우선 알고있는 정보를 정리**

  * **CPT에 저장된 정보**  
    P(Bur) = 0.001, P(Ear) = 0.002  
    P(Al|Bur, Ear) = 0.95,  
    P(Al|Bur, ¬Ear) = 0.94,  
    P(Al|¬Bur, Ear) = 0.29,  
    P(Al|¬Bur, ¬Ear) = 0.001,  
    P(J|Al) = 0.90	P(M|Al) = 0.70,  
    P(J|¬Al) = 0.05	P(M|¬Al) = 0.01

* **식 전개**

  * **P(Bur|J) = P(J|Bur)*P(Bur) / P(J)**
    * P(J|Bur), P(J) 가 필요
    * 먼저, **P(J)**를 구하고나면 **P(J|Bur)** 도 구하겠다.

* **P(J) = ?**

  * **P(J) = P(J, Al) + P(J, ¬Al) = P(J|Al) P(Al) + P(J|¬Al)P(¬Al)**
    * **P(Al)** 가 필요
    * P(Al) = P(Al, Bur, Ear) + P(Al, Bur, ¬Ear) + P(Al, ¬Bur, Ear) + P(Al, ¬Bur, ¬Ear)
      * P(Al, Bur, Ear) = P(Bur, Ear)  * P(Al | Bur, Ear)  
        = P(Bur) * P(Ear) * P(Al | Bur, Ear)  => 생각해보니 Bur과 Ear 독립이라 바로 곱 가능  
        = 0.001 * 0.002 * 0.95  
        = 0.0000019
      * P(Al, Bur, ¬Ear) = P(Bur, ¬Ear)  * P(Al | Bur, ¬Ear)   
        = P(Bur) * P(¬Ear) * P(Al | Bur, ¬Ear)   
        = 0.001 * (1.0 - 0.002) * 0.94  
        = 0.00093812
      * P(Al, ¬Bur, Ear) = P(¬Bur, Ear)  * P(Al | ¬Bur, Ear)  
        = P(¬Bur) * P(Ear) * P(Al | ¬Bur, Ear)   
        = (1.0 - 0.001) * 0.002 * 0.29  
        = 0.00057942
        P(Al, ¬Bur, ¬Ear) = P(¬Bur, ¬Ear)  * P(Al | ¬Bur, ¬Ear)  
        = P(¬Bur) * P(¬Ear) * P(Al | ¬Bur, ¬Ear)   
        = (1.0 - 0.001) * (1 - 0.002) * 0.001   
        = 0.000997002 
      * 0.0000019 + 0.00093812 + 0.00057942 + 0.000997002 = 0.002516442 ≅ **0.0025**
  * **`P(J) = P(J|Al)P(Al) + P(J|¬Al)P(¬Al) = 0.90*0.0025 + 0.05*0.9975 = 0.052`**

* **P(J|Bur) = ?**

  * **`P(J|Bur) = P(J,Bur) / P(Bur) = ... = P(J|Al)*P(Al|Bur)+P(J|¬Al)*P(¬Al|Bur)`**

  * **자세한 풀이과정은 그림을 참고!!**

    <img src="/images/2023-04-04 [3장] BayesianNet(+prob)/image-20230419000356754.png" alt="image-20230419000356754"  /> 

  * `P(Al|Bur)` 와 `P(¬Al|Bur)` 가 필요!
  * **P(Al|Bur) = ?**
    * **`P(Al|Bur) = 𝑃(𝐴𝑙, 𝐵𝑢𝑟)/𝑃(𝐵𝑢𝑟) = {𝑃(𝐴𝑙, 𝐵𝑢𝑟, 𝐸𝑎𝑟)+𝑃(𝐴𝑙, 𝐵𝑢𝑟, ¬𝐸𝑎𝑟)}/𝑃(𝐵𝑢𝑟) `**
    * 이미 다 구했던 값으로 구할 수 있기 때문에 계산과정은 생략
  * `P(J|Bur) = P(J|Al)*P(Al|Bur)+P(J|¬Al)*P(¬Al|Bur) = 0.849017 ≅ 0.849`

<br>

**최종결과 : `P(Bur|J) = 0.849∗0.001 / 0.052 ≅ 0.016`**
