---
title: "[HW2] MatrixFactorization(행렬분해) - Python"
categories: Machine_Learning
tag: [python, machine_learning, concept]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---





## 1. 프로그램 개발 언어

**사용한 언어 : Python 3.9.16**

**import 라이브러리 : random, matplotlib.pyplot, PIL, numpy**

<br><br>

## 2. 프로그램 소스 코드 설명

**(1) A02_image_reconst_main.py**

 문제에서 주어진 값인 학습률 0.1, 최대 반복 100, 원하는 엡실론 값을 설정하기 위해서 **mf.train** **함수의 인자를** **`lr_alpha=0.1, max_iterations=100, epsilon=0.1`** 로 설정했습니다.

<br>

**(2) A02_MF.py - __init__** **함수**

 해당 함수는 생성자 함수이며, 클래스간 공유할 필요가 있는 `R, P, Q 변수를 초기화` 합니다.

<br>

**(3) A02_MF.py - train** **함수**

 해당 함수는 학습하는 함수이며, 입력받은 R 행렬을 P, Q 행렬분해 하는 과정을 아래와 같이 진행합니다.

 우선, P, Q 행렬의 초기값을 설정합니다. `평균 0.0, 표준편차 1/k` 의 **정규분포에서 랜덤으로 샘플링 한 값을 요소로 선정**합니다. 그리고 loss를 저장할 배열을 max_iterations 크기로 초기화합니다.

 다음으로 P, Q 행렬의 값을 **경사하강법처럼 update**를 진행합니다. 이때, R 행렬에서 픽셀값이 0인 것은 무시하기 위해 if(R[i][j] > 0) 조건문을 활용합니다. 그리고 이전 iteration의 loss값과 현재 iteration의 loss 값의 차이를 구해서 **epsilon보다 작으면 바로 탈출**합니다.

 마지막으로 인스턴스 내에 변수로 공유하기 위해 self.R, P, Q를 각각 저장합니다.

<br>

**(2) A02_MF.py** **–** **P_MultipliedBy_Q_Transpose** **함수**

 우리가 구한 P, Q를 이용해서 `reconst_R = P * Q^T` 를 계산합니다.

<br><br>

## 3. 프로그램의 실행 결과

* **참고 : 왼쪽이 오리지널 사진, 중간이 reconsted(재구성)할 Target 이미지, 오른쪽이 학습을 통해 reconsted한 이미지**

<br>

**(1) 평가1-1, 1-2, 1-3**

* k값이 16, 32, 64 로 변화에 따른 결과 출력

![그림입니다.  원본 그림의 이름: 평가1_1.png  원본 그림의 크기: 가로 1000pixel, 세로 300pixel  프로그램 이름 : Matplotlib version3.7.1, https://matplotlib.org/](/images/2023-05-26-[HW2] MatrixFactorization(행렬분해) - Python/EMB000046e81378.png)  

  ![그림입니다.  원본 그림의 이름: 평가1_2.png  원본 그림의 크기: 가로 1000pixel, 세로 300pixel  프로그램 이름 : Matplotlib version3.7.1, https://matplotlib.org/](/images/2023-05-26-[HW2] MatrixFactorization(행렬분해) - Python/EMB000046e81379.png)  

  ![그림입니다.  원본 그림의 이름: 평가1_3&2_1.png  원본 그림의 크기: 가로 1000pixel, 세로 300pixel  프로그램 이름 : Matplotlib version3.7.1, https://matplotlib.org/](/images/2023-05-26-[HW2] MatrixFactorization(행렬분해) - Python/EMB000046e8137a.png)  

<br>

**(2) 평가2-1, 2-2, 2-3**

* loss 계산에 사용하는 𝜆(제약)이 0.0, 0.001, 0.01 로 변화에 따른 결과 출력

![그림입니다.  원본 그림의 이름: 평가1_3&2_1.png  원본 그림의 크기: 가로 1000pixel, 세로 300pixel  프로그램 이름 : Matplotlib version3.7.1, https://matplotlib.org/](/images/2023-05-26-[HW2] MatrixFactorization(행렬분해) - Python/EMB000046e8137a.png)  

  ![그림입니다.  원본 그림의 이름: 평가2_2.png  원본 그림의 크기: 가로 1000pixel, 세로 300pixel  프로그램 이름 : Matplotlib version3.7.1, https://matplotlib.org/](/images/2023-05-26-[HW2] MatrixFactorization(행렬분해) - Python/EMB000046e8137b.png)  

  ![그림입니다.  원본 그림의 이름: 평가2_3.png  원본 그림의 크기: 가로 1000pixel, 세로 300pixel  프로그램 이름 : Matplotlib version3.7.1, https://matplotlib.org/](/images/2023-05-26-[HW2] MatrixFactorization(행렬분해) - Python/EMB000046e8137c.png)  

<br>

**(3) 평가3-1, 3-2, 3-3. 3-4, 3-5, 3-6**

* 평가2 와 동일하되 Target 이미지를 바꿔보고 평가해본 결과

![그림입니다.  원본 그림의 이름: 평가3_1.png  원본 그림의 크기: 가로 1000pixel, 세로 300pixel  프로그램 이름 : Matplotlib version3.7.1, https://matplotlib.org/](/images/2023-05-26-[HW2] MatrixFactorization(행렬분해) - Python/EMB000046e8137d.png)  

  ![그림입니다.  원본 그림의 이름: 평가3_2.png  원본 그림의 크기: 가로 1000pixel, 세로 300pixel  프로그램 이름 : Matplotlib version3.7.1, https://matplotlib.org/](/images/2023-05-26-[HW2] MatrixFactorization(행렬분해) - Python/EMB000046e8137e.png)  

  ![그림입니다.  원본 그림의 이름: 평가3_3.png  원본 그림의 크기: 가로 1000pixel, 세로 300pixel  프로그램 이름 : Matplotlib version3.7.1, https://matplotlib.org/](/images/2023-05-26-[HW2] MatrixFactorization(행렬분해) - Python/EMB000046e8137f.png)  

​    

  ![그림입니다.  원본 그림의 이름: 평가3_4.png  원본 그림의 크기: 가로 1000pixel, 세로 300pixel  프로그램 이름 : Matplotlib version3.7.1, https://matplotlib.org/](/images/2023-05-26-[HW2] MatrixFactorization(행렬분해) - Python/EMB000046e81380.png)  

  ![그림입니다.  원본 그림의 이름: 평가3_5.png  원본 그림의 크기: 가로 1000pixel, 세로 300pixel  프로그램 이름 : Matplotlib version3.7.1, https://matplotlib.org/](/images/2023-05-26-[HW2] MatrixFactorization(행렬분해) - Python/EMB000046e81381.png)    ![그림입니다.  원본 그림의 이름: 평가3_6.png  원본 그림의 크기: 가로 1000pixel, 세로 300pixel  프로그램 이름 : Matplotlib version3.7.1, https://matplotlib.org/](/images/2023-05-26-[HW2] MatrixFactorization(행렬분해) - Python/EMB000046e81382.png)  

<br><br>

## 4. 고찰

**(1) Feature vector dimension k가 클 경우와 작을 경우의 각각의 장단점**

* k가 클 때
  * 장점은 평가1~3을 보면 알 수 있듯이 k가 클수록 이미지가 좀 더 품질이 좋다는 점을 알 수 있습니다. 
  * 단점은 연산량이 커질 수 있습니다.
* k가 작을 때
  * 장점은 연산량이 그만큼 작기 때문에 속도가 빠릅니다.
  * 단점은 그만큼 행렬이 작기 때문에 중요한 특징을 잃어버릴 수 있습니다.

<br>

**(2) Regularization weight 𝜆가 클 경우와 작을 경우 어떤 효과**

* 𝜆가 클 때
  * 장점은 그만큼 제한이 강하기 때문에 모델의 과대적합을 줄일 수 있습니다.
  * 단점은 제한이 강하다 보니 오히려 과소적합을 일으킬 수 있습니다.
* 𝜆가 작을 때
  * 장점은 제한이 약하므로 모델의 복잡성을 높이며, 이는 과소적합을 감소 시킬 수 있습니다.
  * 단점은 제한이 약하다 보니 오히려 과대적합을 일으킬 수 있습니다.

<br><br>

**5.** **느낀점**

 초기에 코드를 작성하고 출력을 했을 때 이미지가 그려지지 않았는데, 알고 보니 P, Q 행렬의 update 과정 중에서 계산 실수 때문에 값이 음의 방향으로 무한대로 갔었습니다. 이때, **계산식**을 다시 한번 검토하게 되었고 **행렬곱, 벡터내적, 스칼라곱을 위한 연산자**도 다시 한번 공부하게 되었습니다.

 그리고 Loss(오차)를 줄이기 위해 SGD(확률적 경사하강법)을 왜 사용하는지 더욱 이해하게 되었고, 현재 iteration의 Loss와 다음 iteration의 Loss의 차이값을 epsilon과 비교해서 중간에 탈출한다는 개념을 이해하게 되었습니다.

<br><br>

## 6. 부록

**A02_MF.py 의 train(), P_MultipliedBy_Q_Transpose() 함수가 핵심이며, 해당 코드의 이해를 돕기 위해 부가 설명을 추가하겠습니다.**

<br>

**(1) train() 함수**

![image-20230526114751373](/images/2023-05-26-[HW2] MatrixFactorization(행렬분해) - Python/image-20230526114751373.png)

<br>

**(2) P_MultipliedBy_Q_Transpose() 함수**

![image-20230526114924209](/images/2023-05-26-[HW2] MatrixFactorization(행렬분해) - Python/image-20230526114924209.png)

<br>

**A02_MF.py**

```python
import numpy as np

class MF():
    def __init__(self):
        self.R = None
        self.P = None
        self.Q = None

    def train(self, R, k, reg_lambda, lr_alpha, max_iterations, epsilon):
        # epsilon : 오차
        # loss_u,i : 오차함수(=목적함수)
        # p, q : feature vector
        # 연산자들 -> @ : 행렬곱(=행벡터dot열벡터 나열), dot : 벡터내적(결과:스칼라), * : 스칼라곱
        
        # P, Q의 초기값은 평균0.0, 표준편차1/k 의 정규분포에서 샘플링
        P = np.random.normal(loc=0.0,scale=1/k,size=(R.shape[0],k))
        Q = np.random.normal(loc=0.0,scale=1/k,size=(R.shape[0],k))
        loss = np.zeros(max_iterations)
        for iterate in range(0, max_iterations):
            for i in range(0, P.shape[0]): # R row 접근
                for j in range(0, P.shape[0]): # R col 접근
                    if(R[i][j] > 0): # 0은 삭제한 None 픽셀
                        loss[iterate] += (R[i][j]-P[i].dot(Q[j]))**2 + reg_lambda*np.sum(P[i]**2) + reg_lambda*np.sum(Q[j]**2)
                        # weight update
                        gradP = (R[i][j]-P[i].dot(Q[j]))*Q[j]-reg_lambda*P[i]
                        gradQ = (R[i][j]-P[i].dot(Q[j]))*P[i]-reg_lambda*Q[j]
                        P[i] = P[i] + lr_alpha*gradP
                        Q[j] = Q[j] + lr_alpha*gradQ
            if(iterate-1>=0 and abs(loss[iterate]-loss[iterate-1])<epsilon):
                print(abs(loss[iterate]-loss[iterate-1]))
                print(loss[iterate], loss[iterate-1])
                print(iterate)
                break
        self.R = R # 입력 받은 R 행렬 기록
        self.P = P
        self.Q = Q
        return 1


    def P_MultipliedBy_Q_Transpose(self):
        reconst_R = None
        reconst_R = self.P @ self.Q.T # reconst_R = P * Q^T

        return reconst_R
```

<br>

**A02_image_reconst_main.py**

```python
import numpy as np
from PIL import Image
import matplotlib.pyplot as plt
import random
from A02_MF import MF


def image_load(img_name):
    # Use a breakpoint in the code line below to debug your script.
    pic = Image.open(img_name)
    pix_mat = np.array(pic)
    return pix_mat


def print_img(pixel_mat):
    plt.figure()
    plt.imshow(pixel_mat)
    plt.colorbar()
    plt.grid(False)
    plt.show()


def print_img_list(original, corrupted, reconsted, c_rate):
    plt.figure(figsize=(10, 3))

    plt.subplot(1, 3, 1)
    plt.xticks([])
    plt.yticks([])
    plt.grid(False)
    plt.imshow(original, cmap=plt.cm.binary)
    plt.xlabel("original")

    plt.subplot(1, 3, 2)
    plt.xticks([])
    plt.yticks([])
    plt.grid(False)
    plt.imshow(corrupted, cmap=plt.cm.binary)
    label_format = "corrupted (rate: {c_rate:.2f})"
    label_str = label_format.format(c_rate=c_rate)
    plt.xlabel(label_str)

    plt.subplot(1, 3, 3)
    plt.xticks([])
    plt.yticks([])
    plt.grid(False)
    plt.imshow(reconsted, cmap=plt.cm.binary)
    plt.xlabel("reconsted")

    plt.show()


# Press the green button in the gutter to run the script.
if __name__ == '__main__':
    random.seed(0)
    original_pixels = image_load("./Lena_00c.png") # 그저 원본이미지

    # corruption_rate = 40.0
    # corrupted_pixels = image_load("./Lena_40c.png") # R 이미지
    corruption_rate = 00.0
    corrupted_pixels = image_load("./Lena_00c.png") # R 이미지
    # corruption_rate = 60.0
    # corrupted_pixels = image_load("./Lena_60c.png") # R 이미지
    corrupted_pixels = corrupted_pixels / 255 # Normalize

    zero_pixels = 0
    minus_pixels = 0
    plus_pixels = 0

    pixels_shape = original_pixels.shape
    for i in range(pixels_shape[0]):
        for j in range(pixels_shape[1]):
            value = corrupted_pixels[i][j]
            if value == 0:
                zero_pixels += 1
            elif value < 0:
                minus_pixels += 1
            else:
                plus_pixels += 1

    print("zero: %d, minus:%d, plus: %d" % (zero_pixels, minus_pixels, plus_pixels))

    mf = MF()
    print("train_start")
    mf.train(R=corrupted_pixels, k=64, reg_lambda=0.0, lr_alpha=0.1, max_iterations=100, epsilon=0.1)
    print("train_end")
    reconst_pixels = mf.P_MultipliedBy_Q_Transpose()
    
    if reconst_pixels is None:
        print("MF is not yet implemented.")
    else:
        print_img_list(original_pixels, corrupted_pixels, reconst_pixels, corruption_rate)

```

