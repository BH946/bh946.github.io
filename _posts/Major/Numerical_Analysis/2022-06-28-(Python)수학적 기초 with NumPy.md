---
title:  "[Python]수학적 기초 with NumPy"
categories : [Numerical_Analysis]
tag : [수치해석, numpy, 자연로그, 테일러 전개식, 축소 곱셈법, 호너 알고리즘, 조립제법]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---

<head>
  <style>
    table.dataframe {
      white-space: normal;
      width: 100%;
      height: 240px;
      display: block;
      overflow: auto;
      font-family: Arial, sans-serif;
      font-size: 0.9rem;
      line-height: 20px;
      text-align: center;
      border: 0px !important;
    }

    table.dataframe th {
      text-align: center;
      font-weight: bold;
      padding: 8px;
    }
    
    table.dataframe td {
      text-align: center;
      padding: 8px;
    }
    
    table.dataframe tr:hover {
      background: #b8d1f3; 
    }
    
    .output_prompt {
      overflow: auto;
      font-size: 0.9rem;
      line-height: 1.45;
      border-radius: 0.3rem;
      -webkit-overflow-scrolling: touch;
      padding: 0.8rem;
      margin-top: 0;
      margin-bottom: 15px;
      font: 1rem Consolas, "Liberation Mono", Menlo, Courier, monospace;
      color: $code-text-color;
      border: solid 1px $border-color;
      border-radius: 0.3rem;
      word-break: normal;
      white-space: pre;
    }

  .dataframe tbody tr th:only-of-type {
      vertical-align: middle;
  }

  .dataframe tbody tr th {
      vertical-align: top;
  }

  .dataframe thead th {
      text-align: center !important;
      padding: 8px;
  }

  .page__content p {
      margin: 0 0 0px !important;
  }

  .page__content p > strong {
    font-size: 0.8rem !important;
  }

  </style>
</head>


## NumPy 기초


### NumPy의 배열



```python
import numpy as np
arr0 = [1.0, 2.0, 3.0]
print(arr0)
print(type(arr0))

arr1 = np.array([1.0, 2.0, 3.0]) # list -> ndarray
print(arr1)
print(type(arr1))

arr2 = list(arr1) # ndarray -> list
print(arr2)
print(type(arr2))
```

<pre>
[1.0, 2.0, 3.0]
<class 'list'>
[1. 2. 3.]
<class 'numpy.ndarray'>
[1.0, 2.0, 3.0]
<class 'list'>
</pre>

```python
import numpy as np
arr0 = [1.0, 2.0, 3.0]
arr1 = np.array(arr0) # list->ndarray
arr2 = np.arange(0, 10, 2) # 2씩 띄어서 배열 생성
arr3 = np.zeros(3)
arr4 = np.ones(3)
arr5 = np.empty(3)
print(arr0, arr1, arr2, arr3, arr4, arr5)
```

<pre>
[1.0, 2.0, 3.0] [1. 2. 3.] [0 2 4 6 8] [0. 0. 0.] [1. 1. 1.] [1. 1. 1.]
</pre>




### NumPy의 행렬



```python
import numpy as np
arr0 = np.zeros((2,2)) # (N,N)
print(arr0)
arr1 = np.array([[0,1],[2,3]]) # [ [], [].. ]
print(arr1)
```

<pre>
[[0. 0.]
 [0. 0.]]
[[0 1]
 [2 3]]
</pre>




### 간단한 선형 시스템

* 4a + 3b = 23  

  3a + 2b = 16



```python
import numpy as np
a = np.array([[4,3], [3,2]])
b = np.array([23, 16])
x = np.linalg.solve(a, b)
print(x) # a, b 값
```

<pre>
[2. 5.]
</pre>
<br>

## 수치적 오차 확인 : 자연로그



### 자연로그와 테일러급수의 오차확인

**자연로그**는 기호 e로 표기되는 특정 상수를 밑으로 하는 로그

**급수**는 부분합의 극한을 의미 -> 무한급수, 무한합이라고도 한다.

**테일러**가 만들었기에 테일러급수라 한다.

**테일러 전개식**



$$ ln(1+x) = x^1 - \frac{x^2}{2} + \frac{x^3}{3} - \frac{x^4}{4} + ... $$



$$ ln(2) = 1^1 - \frac{1}{2} + \frac{1}{3} - \frac{1}{4} + ... $$



```python
import numpy as np
def NatLog(x, loop): # 이 함수는 테일러 전개식과 같다.
    ans=0
    for i in range(1,loop+1):
        if i%2==0:
            ans -= pow(x,i)/i
        else:
            ans += pow(x,i)/i
    return ans
```


```python
# ln(2)로 오차 테스트 -> 무한으로 갈수록 정확한 극한값이 나온다.
print("ln(2) = "+str(NatLog(1,8)))
print("ln(2) = "+str(NatLog(1,80)))
print("ln(2) = "+str(NatLog(1,800)))
print("ln(2) = "+str(np.log(2))) # 정확한 값(비교를 위해 출력)
```

<pre>
ln(2) = 0.6345238095238095
ln(2) = 0.6869362400091404
ln(2) = 0.6925225711846422
ln(2) = 0.6931471805599453
</pre>




### 자연로그와 테일러급수의 오차확인(개선)


**테일러 전개식(개선)**



$$ ln(\frac{1+x}{1-x}) = 2(\frac{x^1}{1} + \frac{x^3}{3} - \frac{x^5}{5} + ...) $$



$$ ln(2) = 2(\frac{3^{-1}}{1} + \frac{3^{-3}}{3} - \frac{3^{-5}}{5} + ...) $$

* 참고 : x=3<sup>-1</sup>



```python
def NatLogImproved(x,loop): # 테일러 식과 동일
    ans = 0
    for idx, i in enumerate(range(1,loop+1,2)):
        ans+=pow(x,i)/i
    return ans*2.0
```


```python
print("ln(2) = "+str(NatLogImproved(1/3,8)))
print("ln(2) = "+str(NatLogImproved(1/3,80)))
print("ln(2) = "+str(np.log(2))) # 정확한 값
```

<pre>
ln(2) = 0.6931347573322881
ln(2) = 0.6931471805599451
ln(2) = 0.6931471805599453
</pre>
<br>

## 축소 곱셈법과 호너 알고리즘

* 축소 곱셈법은 계속 x를 밖으로 꺼내가는게 축소 곱셈법이다.

* 호너 알고리즘은 이 축소 곱셈법으로 구한 식을 계산하는 알고리즘이다.

* 호너 알고리즘을 조립제법 이라고도 한다.

* 호너 알고리즘을 통해 다항식을 축소 할 수 있다.





### 축소 곱셈법



```python
import numpy as np
import sympy as sp

# f(x) = 1 + 2*x + 3*x**2 + 4*x**3 + 5*x**4
f = [1,2,3,4,5] # x빼고 나열(상수만)
f2 = [5,3,-7,2] # 교재 다항식
def nested(a):
    x=sp.symbols("x")
    f = a[-1] # end index : len(a)-1
    for i in range(len(a)-2, -1, -1): # pseudo : i=n-1 to 0
        f = a[i] + (f*x)
    return f

print(nested(f))
print(nested(f2))
```

<pre>
x*(x*(x*(5*x + 4) + 3) + 2) + 1
x*(x*(2*x - 7) + 3) + 5
</pre>




### 호너 알고리즘



```python
def horner(a,r):
    # a가 조립제법의 x앞 숫자들 배열이고, b가 조립제법의 연산된 값들이다
    b = a[-1] # end index : len(a)-1
    for i in range(len(a)-2, -1, -1): # pseudo : i=n-1 to 0
        b = a[i] + (r*b)
    return b

# f(x) = 1*x**4 - 4*x**3 + 7*x**2 - 5*x - 2
f = [1,-4,7,-5,-2] # x빼고 나열
f.reverse() # 함수를 x차수가 오름차순인 형태의 f(x)식 전용으로 만들어서 reverse() 했음.
print(f)
r = 3
print(horner(f, r)) # f(3) 적용
print(horner(f, 2))# f(2) 적용 => x=2는 근이라서 f(2)=0 출력
```

<pre>
[-2, -5, 7, -4, 1]
19
0
</pre>