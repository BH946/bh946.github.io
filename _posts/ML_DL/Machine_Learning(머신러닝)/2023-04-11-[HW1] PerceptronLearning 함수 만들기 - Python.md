---
title: "[HW1] PerceptronLearning 함수 만들기 - Python"
categories: Machine_Learning
tag: [python, machine_learning, concept]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**퍼셉트론 학습 알고리즘 구현 과정과 결과를 설명합니다. 인공 데이터셋(Synthetic)과 실제 데이터셋(Real)을 사용하여 퍼셉트론 모델을 훈련시키고, 바이어스를 고려한 가중치 학습, 혼동 행렬(Confusion Matrix) 분석을 통해 각각 약 0.99와 0.93의 정확도를 달성한 과정을 코드와 함께 상세히 기록하고 있습니다.**

<br><br>

## 1. 프로그램 개발 언어

**사용한 언어 : Python 3.9.7**

**import 라이브러리 : random, matplotlib.pyplot**

<br><br>

## 2. 프로그램의 실행 결과

**Synthetic, Real.txt 에 있는 데이터는 `0.117,-7.821,1.979,0` 이런 형태로 수 천개 있는 데이터셋이다.**

<br>

### 2-1. Synthetic 데이터 학습 결과

![1](/images/2023-05-05-[HW1] PerceptronLearning 함수 만들기 - Python/EMB00004c145d0e.bmp)  

  ![2](/images/2023-05-05-[HW1] PerceptronLearning 함수 만들기 - Python/EMB00004c145d0f.bmp)  

<br>

### 2-2. Real 데이터 학습 결과

  ![3](/images/2023-05-05-[HW1] PerceptronLearning 함수 만들기 - Python/EMB00004c145d10.bmp)  

![4](/images/2023-05-05-[HW1] PerceptronLearning 함수 만들기 - Python/EMB00004c145d11.bmp)  

<br><br>

## 3. 프로그램 소스 코드 설명

**(1) 전체적인 소스의 흐름을 먼저 소개**

 첫 번째로 Synthetic 데이터 파일 입출력을 받습니다. 

 두 번째로 Weight의 값을 Random으로 지정하며, 이때 Bias 고려를 위해서 4차원으로 초기화 합니다.

 세 번째로 `perceptronLearning` 함수를 만들어서 Perceptron을 훈련 및 분류되지 않은 개수를 `failCount` 배열에 기록합니다.

 네 번째로 `confusionMatrix` 함수를 만들어서 훈련된 Perceptron과 훈련되지않은 Perceptron의 confusionMatrix 결과를 비교합니다.

 마지막으로 이 과정을 Real 데이터 파일도 적용합니다.

<br>

**(2) 학습하는 `perceptronLearning` 함수를 소개**

 `perceptronLearning` 함수는 W를 훈련하는 함수입니다. 해당 W는 Bias를 함께 학습하기 위해서 Bias를 따로두지 않고, W행렬에 1차원을 추가해서 Bias를 -Bias 값으로 세팅해서 총 W를 4차원으로 구성하였습니다.

입력 데이터인 X도 Bias를 위해 4차원이라고 생각하고 Bias를 위해 추가한 차원의 값은 1로 지정하였습니다.

따라서 WX의 식을 `wx = x0*w[0] + x1*w[1] + x2*w[2] + w[3]*1` 로 구현하였습니다.

 추가로 고려한 것은 Normalization 이였고, 이는 입력 데이터인 X의 요소들에 절대값을 씌워서 합한 값을 X값에 나누는 형태로 구현하였습니다.

따라서 `xx=abs(x0)+abs(x1)+abs(x2) => w[0]+=x0/xx` 이런 형태로 구현하였습니다.

 마지막으로 Bias의 update는 앞전에 X데이터에 추가한 값 1을 고정으로 업데이트 했습니다. 즉, `w[3]+=1` 형태로 구현하였습니다.

<br>

**(3) 분류실패 개수를 세는 `failCountFun` 함수를 소개**

 이 함수의 경우 학습하는 **`perceptronLearning`** 함수와 로직은 유사하며, 단지 Update를 시키지 않고 분류 실패한 횟수만 개수를 기록합니다.

<br>

**(4) 마지막으로 `confusionMatrix` 함수를 소개**

 트레이닝 된 W를 활용하는 것을 `confusionMatrix1` 함수로 선언하였고,

트레이닝이 되지 않은 W를 활용하는 것을 `confusionMatrix2` 함수로 선언하였습니다.

 confusionMatrix의 요소인 TP, TN, FP, FN 들을 조건문에 따라서 구하였고, 이를 이용해서 결과를 출력하였습니다. 참고로 Class가 1인 것을 Positive, Class가 0인 것을 Negative로 가정해서 분류를 진행하였습니다.

<br><br>

## 4. Test 데이터를 사용한 평가 결과

처음에 삽입한 스크린 샷을 보면 결과가 나와있습니다.

Synthetic 데이터는 대략 0.99 의 결과를 보여주었고, Real 데이터는 대략 0.93 의 결과를 보여주었습니다.
<br><br>

## 5. 결론

초기에 구현을 할때는 Bias를 0으로 가정하고 단순히 구현을 시작을 했지만, 결과가 좋지 않았음.

그래서 Bias를 추가해서 학습을 진행했더니 결과가 확연히 좋아졌고, Bias의 있고 없고 차이가 이해 됨   

그리고 Perceptron학습 알고리즘을 직접 구현해보니 W를 학습한다는 의미가 더욱 직접적으로 이해가 된다.

<br><br>

## 6. 부록

```python
# 구현 과정...
# perceptron 구현 과제 - 입력은 3차원을 기준
# bias를 0으로 가정하면?. 즉, 0을 기준으로 두개의 클래스 분류. 때문에 식도 WX=0 방정식
# 하지만, 여기서는 bias를 있다고 가정한다. 따라서 식도 wx+b=0 방정식
# bias를 위해서 X행렬에 1차원을 추가해서 bias에 추가할 값으로 1을 세팅
# bias를 따로두지 않고, W행렬에 1차원을 추가해서 bias를 -bias 값으로 세팅

################### Setting 시작 ###################
# import
import random
import matplotlib.pyplot as plt

# 전역변수
failCount = []

################### Setting 끝 ###################
################### 함수 정의 시작 ###################

# 분류실패 개수 구하는 함수
def failCountFun(w, trainData,n):
  counts = 0
  for data in trainData:
    x0=float(data[0])
    x1=float(data[1])
    x2=float(data[2])
    nc=int(data[3])
    # w[3]은 bias이며 미리 음수로 세팅한 상태
    wx = x0*w[0] + x1*w[1] + x2*w[2] + w[3]*1
    if(nc==1):
      if(wx<=0): # 분류 실패
        counts+=1
    elif(nc==0):
      if(wx>0): # 분류 실패
        counts+=1
    else:
      print("분류 작업을 실패했습니다.")
  failCount.append([n,counts])

# Perceptron학습하는 함수
def perceptronLearning(w, trainData, limit):
  n = 0
  # 학습 전의 failCount 구하기
  failCountFun(w, trainData,n)
  # 학습 시작
  while(n<limit): # 1000번 수행
    n+=1
    for data in trainData:
      x0=float(data[0])
      x1=float(data[1])
      x2=float(data[2])
      nc=int(data[3])
      # w[3]은 bias이며 미리 음수로 세팅한 상태
      wx = x0*w[0] + x1*w[1] + x2*w[2] + w[3]*1 
      if(nc==1):
        if(wx<=0):
          # Normalization => X/xx
          xx=abs(x0)+abs(x1)+abs(x2) 
          w[0]+=x0/xx
          w[1]+=x1/xx
          w[2]+=x2/xx
          w[3]+=1 # bias update
      elif(nc==0):
        if(wx>0):
          # Normalization => X/xx
          xx=abs(x0)+abs(x1)+abs(x2)
          w[0]-=x0/xx
          w[1]-=x1/xx
          w[2]-=x2/xx
          w[3]-=1 # bias update
      else:
        print("잘못된 데이터가 있습니다.")
    if(n<=30): # 0~30 iteration 까지
      # 학습 이후에도 failCount 구하기
      failCountFun(w, trainData,n)

# 트레이닝 된 W로 confusionMatrix 구하는 함수
def confusionMatrix1(w, testData):
  print("Confusion Matrix")
  # 순서대로 문제 속 테이블의 A,B,C,D 를 의미
  TP=0; TN=0; FP=0; FN = 0; 
  # 1. W를 매번 랜덤으로 총 10번 confusionMatrix 구해보기
  for data in testData:
    x0=float(data[0])
    x1=float(data[1])
    x2=float(data[2])
    nc=int(data[3])
    # w[3]은 bias이며 미리 음수로 세팅한 상태
    wx = x0*w[0] + x1*w[1] + x2*w[2] + w[3]*1 
    if(nc==1): # positive
      if(wx<=0):
        FP+=1
      else:
        # 의미 : Class1데이터(Positive)를 Class1로 잘 분류(True)
        TP+=1 # True Positive
    elif(nc==0): # negative
      if(wx>0):
        FN+=1
      else:
        # 의미 : Class0데이터(Negative)를 Class0로 잘 분류(True)
        TN+=1 # True Negative
    else:
      print("잘못된 데이터가 있습니다.")
  # 2. 구한 confusionMatrix을 출력
  print("True Positive : "+str(TP))
  print("False Positive : "+str(FP))
  print("False Negative : "+str(FN))
  print("True Negative : "+str(TN))
  # ACC = (TP + TN) / (전체 데이타 수 = P + N)
  print("ACC : "+str((TP+TN)/len(testData)))
  
# 트레이닝 안된 W로 confusionMatrix 구하는 함수
def confusionMatrix2(w, testData):
  print("Confusion Matrix")
  # 순서대로 문제 속 테이블의 A,B,C,D 를 의미
  TP=0; TN=0; FP=0; FN = 0; 
  # 1. W를 매번 랜덤으로 총 10번 confusionMatrix 구해보기
  for i in range(0, 10):
    for data in testData:
      x0=float(data[0])
      x1=float(data[1])
      x2=float(data[2])
      nc=int(data[3])
      # w[3]은 bias이며 미리 음수로 세팅한 상태
      wx = x0*w[0] + x1*w[1] + x2*w[2] + w[3]*1 
      if(nc==1): # positive
        if(wx<=0):
          FP+=1
        else:
          # 의미 : Class1데이터(Positive)를 Class1로 잘 분류(True)
          TP+=1 # True Positive
      elif(nc==0): # negative
        if(wx>0):
          FN+=1
        else:
          # 의미 : Class0데이터(Negative)를 Class0로 잘 분류(True)
          TN+=1 # True Negative
      else:
        print("잘못된 데이터가 있습니다.")
    # 마지막 차원인 bias는 음수로 초기값 가질거라서 -1000~-1 랜덤!
    w = [random.randrange(-1000,1000),random.randrange(-1000,1000),random.randrange(-1000,1000),random.randrange(-1000,-1)]
  # 2. 출력은 confusionMatrix를 평균값으로 나타내기
  print("True Positive : "+str(TP/10))
  print("True Negative : "+str(TN/10))
  print("False Positive : "+str(FP/10))
  print("False Negative : "+str(FN/10))
  # ACC = (TP + TN) / (전체 데이타 수 = P + N)
  print("ACC : "+str(((TP/10)+(TN/10))/len(testData)))

################### 함수 정의 끝 ###################
################### DataSet(Synthetic) 시작 ###################

trainData = []
testData = []
f = open("synthetic_data_train.txt","r")
while(True):
  line = f.readline()
  if not line: break
  lineList = line.split(",")
  lineList[3] = lineList[3].replace("\n","")
  trainData.append(lineList)
f.close()

f = open("synthetic_data_test.txt","r")
while(True):
  line = f.readline()
  if not line: break
  lineList = line.split(",")
  lineList[3] = lineList[3].replace("\n","")
  testData.append(lineList)
f.close()

################### DataSet(Synthetic) 끝 ###################
################### 구현 시작(Synthetic) ###################

# init weight Random (-1000~1000)
# 참고 : 마지막 차원은 bias
w1 = [random.randrange(-1000,1000),random.randrange(-1000,1000),random.randrange(-1000,1000),random.randrange(-1000,-1)] 
w2 = w1.copy()
# EX) w1 = [-1,-1,-1,-1]

# 3-1. Training 진행과정
# 0~30 Iteration 과정 중 제대로 분류되지 않은 데이터 개수를,
# 그래프로 표시
x=[]; y=[]
perceptronLearning(w1, trainData, 1000)
for counts in failCount:
  x.append(counts[0])
  y.append(counts[1])
plt.plot(x,y) # graph
plt.show()


# 3-2. Training Perceptron VS Init Perceptron
# Test set으로 비교

print("---------트레이닝 OK---------")
confusionMatrix1(w1,testData)
print()
print("---------트레이닝 NO---------")
confusionMatrix2(w2,testData)

################### 구현 끝(Synthetic) ###################





################### DataSet(Real) 시작 ###################

trainData = []
testData = []
f = open("datatraining.txt","r")
line = f.readline() # 첫번째 줄은 컬럼명이므로 pass
while(True):
  line = f.readline()
  if not line: break
  lineList = line.split(",")
  lineList[7] = lineList[7].replace("\n","")
  trainData.append([lineList[2],lineList[4],lineList[5],lineList[7]])
f.close()

f = open("datatset2.txt","r")
line = f.readline() # 첫번째 줄은 컬럼명이므로 pass
while(True):
  line = f.readline()
  if not line: break
  lineList = line.split(",")
  lineList[7] = lineList[7].replace("\n","")
  testData.append([lineList[2],lineList[4],lineList[5],lineList[7]])
f.close()

################### DataSet(Real) 끝 ###################
################### 구현 시작(Real) ###################

# init weight Random (-1000~1000)
# 참고 : 마지막 차원은 bias
w1 = [random.randrange(-1000,1000),random.randrange(-1000,1000),random.randrange(-1000,1000),random.randrange(-1000,-1)] 
w2 = w1.copy()
# EX) w1 = [-1,-1,-1,-1]

# 3-1. Training 진행과정
# 0~30 Iteration 과정 중 제대로 분류되지 않은 데이터 개수를,
# 그래프로 표시
x=[]; y=[]; failCount = [] # failCount 다시 초기화
perceptronLearning(w1, trainData, 1000)
for counts in failCount:
  x.append(counts[0])
  y.append(counts[1])
plt.plot(x,y) # graph
plt.show()


# 3-2. Training Perceptron VS Init Perceptron
# Test set으로 비교

print("---------트레이닝 OK---------")
confusionMatrix1(w1,testData)
print()
print("---------트레이닝 NO---------")
confusionMatrix2(w2,testData)
################### 구현 끝(Real) ###################
```

