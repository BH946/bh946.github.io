---
title: "[9차] MLP실습 중요!! - AND, IMAGE (feat.tensorflow, keras)"
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

**MLP로 "AND" 연산과 "이미지 분류"를 실습해보려고 한다.** 

* `Tensorflow 2.12.x` 와 `Keras` 를 사용!!
  * Tensorflow : open source platform for machine learning.
  * Keras :  a deep learning API.

* 참고로 이런 실습을 위해서는 따로 환경설정을 하는것을 추천
  * 본인은 `아나콘다` 를 사용해서 파이썬 버전 여러개를 따로 설정해둔 상태
  * 특히 Tensorflow는 버전이 올라가면 완전히 구조가 바뀔 수 있어서 다시 배워야 할 수도 있다는걸 잘 인지하자.

<br>

**기본적으로 이정도 흐름 구상은 기억해두기(예로 이미지 분류의 경우)**

![image-20230614014619130](/images/2023-04-10-[9차] MLP실습 중요!! - AND, IMAGE (feat.tensorflow, keras)/image-20230614014619130.png)  

<br><br>

# Tensor, Keras

* **Tensor**
  * Immutable data type : 변경불가 타입, 바꾸기 위해선 다시 tensor 생성
  * tf.constant(), tf.convert_to_tensor() : array, list 등등을 tensor로 변환
  * tf.cast() : tensor의 데이터 타입을 변경(예로 int32 -> float32)
* **Keras**
  * Model() : 전체 Layer들을 등록
  * compile() : 오차함수와 최적화(예로 SGD)를 등록
  * fit : 모델 훈련
  * predict() : 값 예측
  * 자세한건 아래 그림으로 확인

![image-20230614014130359](/images/2023-04-10-[9차] MLP실습 중요!! - AND, IMAGE (feat.tensorflow, keras)/image-20230614014130359.png) 

![image-20230614014158884](/images/2023-04-10-[9차] MLP실습 중요!! - AND, IMAGE (feat.tensorflow, keras)/image-20230614014158884.png) 

![image-20230614014207785](/images/2023-04-10-[9차] MLP실습 중요!! - AND, IMAGE (feat.tensorflow, keras)/image-20230614014207785.png) 

![image-20230614014215238](/images/2023-04-10-[9차] MLP실습 중요!! - AND, IMAGE (feat.tensorflow, keras)/image-20230614014215238.png) 



<br><br>

# and_classifier.py

**SLP, MLP 둘다 사용해서 비교를 한다.**

* **비교결과 : MLP가 더 결과가 좋게 나왔다.**

<br>

**코드 구조 먼저 간략히 소개하자면, 아래와같이 `Input -> Hidden -> Output`으로 Layer가 나뉘고 만약 Hidden이 None인 경우 `Input -> Output`으로 Layer가 나뉜다.**

**이 두 경우는 순서대로 MLP, SLP의 경우이다.**

![image-20230614013639309](/images/2023-04-10-[9차] MLP실습 중요!! - AND, IMAGE (feat.tensorflow, keras)/image-20230614013639309.png) 

<br>

**and연산 코드**

```python
import tensorflow as tf
from MLP import MLP

def and_classifier_example():
    input_data = tf.constant([[0.0, 0.0], [0.0, 1.0], [1.0, 0.0], [1.0, 1.0]]) # 입력
    input_data = tf.cast(input_data, tf.float32)
    and_labels = tf.constant([0.0, 0.0, 0.0, 1.0]) # 레이블
    and_labels = tf.cast(and_labels, tf.float32)
    batch_size = 1 
    epochs = 1500
    slp_classifier = MLP(hidden_layer_conf=None, num_output_nodes=1)
    slp_classifier.build_model()
    slp_classifier.fit(x=input_data, y=and_labels, batch_size=batch_size, epochs=epochs)
    mlp_classifier = MLP(hidden_layer_conf=[4], num_output_nodes=1)
    mlp_classifier.build_model()
    mlp_classifier.fit(x=input_data, y=and_labels, batch_size=batch_size, epochs=epochs)
    ######## SLP AND prediciton
    prediction = slp_classifier.predict(x=input_data, batch_size=batch_size) 
    input_and_result = zip(input_data, prediction)
    print("====== SLP AND classifier result =====") 
    for x, y in input_and_result:
        if y > 0.5: # 0.5 이상 클래스 1로 분류
            print("%d AND %d => %.2f => 1" % (x[0], x[1], y)) 
        else:
            print("%d AND %d => %.2f => 0" % (x[0], x[1], y)) 
    ######## MLP AND prediciton
    prediction = mlp_classifier.predict(x=input_data, batch_size=batch_size) 
    input_and_result = zip(input_data, prediction)
    print("====== MLP AND classifier result =====") 
    for x, y in input_and_result:
        if y > 0.5:
            print("%d AND %d => %.2f => 1" % (x[0], x[1], y)) 
        else:
            print("%d AND %d => %.2f => 0" % (x[0], x[1], y))

# Entry point
if __name__ == '__main__': 
    # binary and 연산을 SLP, MLP로 계산하는..함수
    and_classifier_example() 

'''
@@@ 예시
====== SLP AND classifier result =====
0 AND 0 => 0.00 => 0
0 AND 1 => 0.11 => 0
1 AND 0 => 0.11 => 0
1 AND 1 => 0.87 => 1
4/4 [==============================] - 0s 1ms/step
====== MLP AND classifier result =====
0 AND 0 => 0.00 => 0
0 AND 1 => 0.06 => 0
1 AND 0 => 0.06 => 0
1 AND 1 => 0.90 => 1
'''
```

<br>

**class MLP코드**

```python
import tensorflow as tf

class MLP:
    # 생성자
    def __init__(self, hidden_layer_conf, num_output_nodes):
        self.hidden_layer_conf = hidden_layer_conf
        self.num_output_nodes = num_output_nodes
        self.logic_op_model = None
    
    def build_model(self):
        # 오차함수 : mse, 활성화함수 : sigmoid, SGD(확률적 경사하강법) 사용
        input_layer = tf.keras.Input(shape=[2,])
        hidden_layers = input_layer
        if self.hidden_layer_conf is not None:
            for num_hidden_nodes in self.hidden_layer_conf:
                hidden_layers = tf.keras.layers.Dense(units=num_hidden_nodes, activation=tf.keras.activations.sigmoid, use_bias=True)(hidden_layers)
        output = tf.keras.layers.Dense(units=self.num_output_nodes, activation=tf.keras.activations.sigmoid, use_bias=True)(hidden_layers)
        self.logic_op_model = tf.keras.Model(inputs=input_layer, outputs=output) 
        sgd = tf.keras.optimizers.SGD(learning_rate=0.1)
        self.logic_op_model.compile(optimizer=sgd, loss="mse")

    def fit(self, x, y, batch_size, epochs): 
        self.logic_op_model.fit(x=x, y=y, batch_size=batch_size, epochs=epochs) 

    def predict(self, x, batch_size): 
        prediction = self.logic_op_model.predict(x=x, batch_size=batch_size) 
        return prediction
```

<br><br>

# Image Classification

**이미지 분류이고, 전체적인 흐름을 먼저 알려주겠다. (기본적으로 이 흐름을 이해하고 잘 알고있어야 함)**

* **1~4번 과정은 필수, 5~6번 과정은 부가적인 요소다.**
* 참고로 내용중에 **Objective** 용어가 너무 많은데, 이는 **오차=비용=목적** 등등 함수로 불린다.

![image-20230614014612917](/images/2023-04-10-[9차] MLP실습 중요!! - AND, IMAGE (feat.tensorflow, keras)/image-20230614014612917.png)  

<br>

* 1번
  * CNN, MLP, Yolo 등등 여러가지 중에 선택 단계
* 2번
  * 입력 데이터의 차원을 결정 => 예로 이미지는 픽셀값(28x28)이 입력 데이터로 2차원
  * Keras의 `Flatten()` 을 통해서 **1차원으로 변경** => 내부 노드들은 1차원이기 때문
* 3번
  * 출력 데이터 차원을 결정 => 노드 개수를 결정한다고 봐도 무관(즉, 분류할 클래스 개수!!)
  * 출력 단계이므로 꼭 **활성화 함수**는 `softmax` 같은 확률로 나오는 함수를 권장(GD를 사용할것이라!)
* 4번 
  * 오차 함수는 Cross Entropy Loss의 variation 중 하나인 `SparseCategoricalCrossentropy`를 추천
  * 해당 함수는 출력때 분류되는 클래스 개수(=출력 데이터 차원=노드 개수)로 변환도 추가로 해줌
* 5번
  * Hidden Layer를 몇 층으로 할지와 Node 개수와 활성화 함수까지 설정한다.
  * **활성화 함수**는 `ReLU` 로 설정해 본다. => 내부 계층은 사실 굳이 확률로 값을 도출할 필요가 없기 때문이다.
* 6번
  * Optimizer(최적화)를 SGD가 아닌 Adam을 사용한다(성능 괜찮음)

<br>

**모델 정의를 전체적으로 코드 예를 들어보겠다.**

![image-20230614015635011](/images/2023-04-10-[9차] MLP실습 중요!! - AND, IMAGE (feat.tensorflow, keras)/image-20230614015635011.png) 

<br>

**참고**

* 시그모이드 - 이진 분류 모델의 마지막에 사용하는 활성화 함수
* 소프트맥스 - 다중 분류 모델의 마지막에 사용하는 활성화 함수
* ReLU - 기본적으로 은닉층에 사용하는 활성화 함수
