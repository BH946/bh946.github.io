---
title: "[9차] MLP실습 - AND, Image (feat.tensorflow, keras)"
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

**MLP로 AND 연산과 이미지 분류를 실습해보려고 한다.** 

* `Tensorflow 2.12.x` 와 `Keras` 를 사용!!
* 참고로 이런 실습을 위해서는 따로 환경설정을 하는것을 추천
  * 본인은 `아나콘다` 를 사용해서 파이썬 버전 여러개를 따로 설정해둔 상태

<br><br>

# and_classifier.py

**SLP, MLP 둘다 사용해서 비교를 한다.**

* **비교결과 : MLP가 더 결과가 좋게 나왔다.**

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

**아래는 MLP 클래스**

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

![image-20230605005119704](/images/2023-04-10-[9차] MLP실습 - AND, Image (feat.tensorflow, keras)/image-20230605005119704.png) 

<br>

