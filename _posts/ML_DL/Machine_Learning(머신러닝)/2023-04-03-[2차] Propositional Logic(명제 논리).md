---
layout: single
title:  "[2차] Propositional Logic(명제 논리)"
categories: Machine_Learning
tag: [python, machine_learning, concept]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**인공지능 질의응답 시스템의 기반이 되는 명제 논리(Propositional Logic)의 개념, 문법(Syntax), 의미론(Semantics), 그리고 추론 시스템(Proof Systems)에 대해 설명합니다. 지식 베이스(KB)를 활용한 논리적 추론 방식, CNF와 Horn Clauses, 그리고 Resolution과 SLD Resolution 같은 추론 규칙을 포함하고 있습니다.**

<br><br>

# Question Answering System

**Question Answering (QA) System : 질문자의 질문에 대해  
(1) Knowledge base(이하 KB)의 지식을 바탕으로 (2) 추론하여 대답을 출력하는 시스템.**

* **Chatgpt**를 예시로 들 수 있다.

<img src="/images/2023-04-03 [2장] Propositional Logic(명제 논리)/image-20230418211208983.png" alt="image-20230418211208983"  />

* 해결해야할 문제 : KB에 저장된 “(1)지식은 어떻게 표현”되며, 지식을 사용하여 “(2)어떻게 추론”하는가?
* 따라서 이 시스템에 관해서 자세히 다뤄보려고 한다.

<br><br>

# Propositional Logic
**`Propositional Logic` 란 질의 명제이다.**

* 지식을 (1)표현하고, 지식을 사용하여 (2)추론하는 방법에 대한 연구 중 하나.
* 명제의  내용이  무엇이던, 명제와  명제  간의  논리적  관계가  충분히  정의되어  있다면, 공통적인  추론  방식으로  질의  명제가  참인지  아닌지  추론할  수  있다.

<br>

**Syntax & Semantic -> Proof Systems 순서로 질의 명제 설명하겠다.**

<br><br>

## Syntax & Semantic
**Propositional logic 을  사용하는데  있어서 논리의  `표현 규칙(문법, Syntax)`와 `의미(Semantic) 해석  방식`에  대해  살펴본다.**

<br>

### Syntax(문법)
**1. 명제는 변수로 표현 및 true, false로 판단**

**2. 변수 사이 논리적 관계는 Op로 표시된다.**

* `Op = {￢,∧,∨,⇒, ⇔, (, )}` : Logical operator set. Not, And, Or, implication, equivalence, 괄호

**3. 단일 변수로 표현된 명제 및 명제 사이에 정의된 논리 관계는 formula라 불린다.**

* 예시로 `￢A, (A), A∧ B, A∨ B, A⇒B, A ⇔ B` 는 **전부 formula**
* 반대로 `(￢A ) ∧ ￢` 는 **formula 아님**
  * 이유 : 마지막에 NOT기호만 있고 변수가 없기 때문

<br>

**기호 읽는 법**

<img src="/images/2023-04-03 [2장] Propositional Logic(명제 논리)/image-20230418211603344.png" alt="image-20230418211603344"  />

<br>

#### 연산자 우선순위
**연산자 우선순위: 괄호가 없을 경우 `¬, ∧, ∨, ⇒, ⇔` 순서로 연산자가 적용!!**

<br>

### Semantic(의미,해석)
* **Interpretation(=해석)** :  논리식(formula)을 구성하는 모든 propositional variable에 진리 값 (t or f)를  
할당함으로써, formula가 참인지 거짓인지 결정할 수 있도록 하는 것.
* **Semantically Equivalent (의미적으로 동등)** : formula F, G가 항상 같은 논리값!!
  * 예 : `𝐴⇒𝐵 ≡ ¬𝐴 ∨ 𝐵`

<br>

#### Equivalence rules
<img src="/images/2023-04-03 [2장] Propositional Logic(명제 논리)/image-20230418211834571.png" alt="image-20230418211834571"  />

<br>

**Formula의 해석 결과에 대한 분류**

* **Satisfiable** : 최소한 하나의 interpretation이 true (해석에 따라서는 참일 수 있다.)
* **Unsatisfiable (혹은 Contradiction) **: 모든 interpretation이 false인 formula를 지칭 (어떻게 해석해도 거짓이다.)
* **“Logically valid” or “valid” or “tautology”** : 모든 interpretation이 true 인 formula를 지칭 (어떻게 해석해도 참이다.)

**Formula에 대한 interpretation instance에 대한 분류**

* **Model** : 어떤 formula를 `true` 로 만드는 interpretation을 해당 formula의 model이라 지칭.
* **Satisfiable** : 최소한 하나의 model이 존재
* **Unsatisfiable** : model이 존재하지 않음.
* **“Logically valid” or “valid” or “tautology”** : 모든 interpretation이 model

<br>

#### Model 개념이 중요한 이유
**해당 formula의 Model에 대해서만 논의한다는 것은, 해당 formula를 true로 만드는 interpretation (case)에 대해서만 논의하겠다는 말과 같다.**

**일반적으로 QA 시스템이나 모든 인공지능 시스템에서 시스템이 가지고 있는 지식(KB)은 참 이라고 가정하고 정보를 처리한다.**

**즉, 시스템은 KB의 모델에 대해서만 정보를 처리한다고 볼 수 있다.**
<br><br>

## Proof Systems
**KB(knowledge)에  Propositional logic을  사용하여  지식을  저장하고, KB의  지식을  사용하여  질의  명제  (Query, 이하  Q)가  참인지  거짓인지  판별하는  시스템**

* QA 시스템처럼 시스템이 질의 명제 Q가 참이라는 대답을 하기 위해서는, KB에 기술된 명제가 참이라 가정할 때 Q가 참이어야 한다.
  * **즉, KB내용은 모두 true라고 가정을 하고 시작.**
* KB의 내용은 모두 true임을 가정하므로, 하나하나 따로 표현한 formula를 And 로 묶어서 하나의 formula로 표현 가능.
  * 예시
  * `A⇒B` (수업 A 들었으면 B를 들을 수 있다.)
  * `(A ∧ B) ⇒ C` (수업 A, B를 들었으면 C를 들을 수 있다.)
  * `KB = (A ⇒ B) ∧ ((A ∧ B) ⇒ C)` 로 표현 가능!!

<br>

### CNF(논리곱 정규형), Horn Clauses
**CNF(=Conjunctive Normal Form) 는 괄호안 OR, 괄호밖 AND 로 이루어진 `(𝑉11∨𝑉12∨...) ∧ (𝑉21∨𝑉22...) ` 형태를 가진다.**  

**참고로 KB는 Conjunctive Normal Form으로 변환이 쉬움**

* 첫번째 이유 : KB는 보통 (A ⇒ B) 형식 formula 와 단일 변수 formula의 And 연산
* 두번째 이유 : (A ⇒ B) 형식은 (¬ A ∨ B) (by implication) 로 변환 가능하기 때문

<br>

**Horn Clauses 는 CNF의 어느 Clause(괄호) 안에 Positive Variable(긍정) 이 1개 이하의 경우를 의미한다.**

* 예시
* `(¬𝑨𝟏∨...∨¬𝑨𝒎∨𝑩)` 혹은 `(¬𝑨𝟏 ∨...∨¬𝑨𝒎) ` 혹은`(𝑩)` 이면 **Horn clause**

<br>

### 추론하는 형태는?
**그렇다면 KB에서 Query Q가 참인지 어떻게 `inference(추론)` 할 것인가?**  
**Proof by Contradiction (모순에 의한 증명) 을 사용한다!!**

* 정의 : 𝑲𝑩 ∧¬𝑸 를 false로 만드는 interpretation 만이 존재함(unsatisfiable, contradiction)을 보이면 𝑲𝑩 ⊨ 𝑸 임을 보일 수 있다.
* 이것을 보이는 방법(Derivation(=도출))은 Inference Rule을 잘 적용해서 구한다!
* 다만, 이는 추론 알고리즘(예. 사람 등을)을 이용한 추론이므로 알고리즘에 따라서는 항상 의미적으로 정확한 결과를 도출하지 않을 수 있다.
* 이를 해결할만한 방법은 `Sound, Complete` 이다.

<br>

* **Sound** : 시스템이 도출한 Q는 KB가 내포(entail)한 Q를 구성하는 interpretation (일부)임에는 틀림없다. 
  * 즉, KB가 entail하지 않는 Q를 잘못 도출하지는 않는다.
  * 다만,  KB ⊨ Q를 만족하는 모든 Q를 도출하지는 않는다.
* **Complete** : KB가 내포하는 모든 formula Q를 도출 할 수 있다.
* 다만, KB ⊨ Q를 만족하는 모든 Q를 도출할 수 있다면, 추가로 잘못된(Sound하지 않은) Q를 도출해도 상관없다.
* **EX) 어떤 KB가  KB ⊨ A, KB ⊨ B, KB ⊨ C, KB ⊨ D 만을 만족한다고 가정하자. KB로 부터 추론한 결과 C1,C2,C3 중에서 제일 좋은 알고리즘은??**
  * C1은 KB ⊦ A, KB ⊦ B, KB ⊦ C 을  추론하였고,
  * C2는 KB ⊦ A, KB ⊦ B, KB ⊦ C, KB ⊦ D, KB ⊦ E 를  추론하였고, 
  * C3은 KB ⊦ A, KB ⊦ B, KB ⊦ C, KB ⊦ D를  추론하였다면,
  * C1은 Sound, C2는 Complete, C3는 Sound & Complete한 시스템이다.
  * **Sound와 Complete함이 증명된 추론 알고리즘이 좋은 알고리즘이다. (즉, C3)**

<br>

### Proof Systems –  Inference Rule(보충설명)
* **CNF : Resolution Rule 적용 가능**  
  * Step 3개, forward 방식

* **Horn Clauses : SLD Resolution 적용 가능**
  * backward 방식

<br>

#### Resolution rule – 적용 예
**Step 1. Formalization**  
**모든 명제를 변수로 표현 및 `Formula(논리식)`으로 표현**

**Step 2. Transformation into normal form**  
**Step 1의 논리식을 `CNF`로 변환**

**Step 3. Proof**  
**Step 1에서 표현한 논리식과, (필요하다면) 새로 추가한 가정을 바탕으로 `Resolution`을 적용하여 알고 싶은 명제가 true인지 false인지 확인**

​	<img src="/images/2023-04-03 [2장] Propositional Logic(명제 논리)/image-20230418225517162.png" alt="image-20230418225517162" style="zoom:80%;" /> **Resolution 적용시 변수 하나가 줄어드는 형태(분자=전제, 분모=결론 의미)**

<br>

<img src="/images/2023-04-03 [2장] Propositional Logic(명제 논리)/image-20230418225959095.png" alt="image-20230418225959095"  />

<br>

#### SLD resolution - 적용 예

<img src="/images/2023-04-03 [2장] Propositional Logic(명제 논리)/image-20230418225754937.png" alt="image-20230418225754937"  />

<br>

**이것을 계속 반복하다 보면??**

<img src="/images/2023-04-03 [2장] Propositional Logic(명제 논리)/image-20230418225906683.png" alt="image-20230418225906683"  />

