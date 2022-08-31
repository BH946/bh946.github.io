---
title:  "[Python]Integration(적분) - 사다리꼴, 심프슨, 가우스 구적법"
categories : [Numerical_Analysis]
tag : [수치해석]
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


## 적분

* 고등학교때 이미 적분은 많이 배웠으므로 자세한 설명은 생략하고, 공식만 정리
* 그리고 실습해본 코드만 정리하겠다.

<br>


## 공식

사다리꼴 공식 : $(b-a)\frac{f(a) + f(b)}{2}$

* $h = \frac{b - a}{n}$



뉴턴-코츠 적분

* 1차

  * 복합 사다리꼴 공식 : $T(f;P) = \frac{h}{2}\sum^{n-1}_{i=0}[f(x_i)+f(x_{i+1})]$

  * $h = \frac{b - a}{n}$

  * 재귀 사다리꼴 공식 : $R(n,0) = h\sum^{2^{n-1}}_{i=1}[f(a+ih)+\frac{h}{2}[f(a)+f(b)]$

  * $h = \frac{b - a}{2^n}$

  * 롬베르크 알고리즘  

    $R(n,0) = \frac{1}{2}R(n-1,0) + h\sum^{2^{n-1}}_{k=1}f[a+(2k-1)h]$  

    $R(n,m) = R(n,m-1) + \frac{1}{4^m-1}[R(n,m-1) - R(n-1,m-1)]$

  * $h = \frac{b - a}{2^n}$

* 2차

  * 심프슨 1/3 공식 : $\frac{h}{3}[f(a)+4f(a+h)+f(a+2h)]$

* 3차

  * 심프슨 3/8 공식 : $\frac{3}{8}h[f(x_0) + 3f(x_1) + 3f(x_2) + f(x_3)]$



가우스 구적법 : 생략

* 분할점 잘 정하면 정확도 상승 발견

<br>

## 실습




### 사다리꼴 공식 Lab


```python
import numpy as np
import sympy as sp
x = sp.symbols("x")
```


```python
def Trapezoid(a,b,h): # 사다리꼴 공식
    return (a+b)*h/2.0

def IntegralTrapezoid(num, f, a, b): # 복합 사다리꼴 공식
    h = (b-a)/num
    sum = 0.0
    for i in range(0, num): # num : 1, 10, 100, 1000
        localA = a + i*h
        localB = a + (i+1)*h
        fa = f.subs(x, localA)
        fb = f.subs(x, localB)
        localArea = Trapezoid(fa, fb, h)
        sum += localArea
    return sum
```


```python
f = x**2
for loop in range(0,4):
    area = IntegralTrapezoid(10**loop, f, 0, 5)
    print(10**loop, " ", area)
print ("=======")
f = x**3
for loop in range(0,4):
    area = IntegralTrapezoid(10**loop, f, 0, 5)
    print(10**loop, " ", area)
```

<pre>
1   62.5000000000000
10   41.8750000000000
100   41.6687500000000
1000   41.6666875000000
=======
1   312.500000000000
10   157.812500000000
100   156.265625000000
1000   156.250156250000
</pre>
<br>

## 수치적분 함수(라이브러리)

```python
# SymPy의 integrate()함수
import sympy as sp

x = sp.Symbol('x')
# f = sp.integrate(sp.cos(x),x) # 부정적분
f0 = sp.integrate(sp.cos(x),(x,0,sp.pi/2)) # cos(x)함수를 0 ~ pi/2 범위
f1 = sp.integrate(sp.cos(x),(x,0,sp.pi)) # cos(x)함수를 0 ~ pi 범위
f2 = sp.integrate(sp.cos(x),(x,0,3*sp.pi/2)) # cos(x)함수를 0 ~ 3*pi/2 범위
f3 = sp.integrate(sp.cos(x),(x,0,2*sp.pi)) # cos(x)함수를 0 ~ 2*pi 범위
print(f0, f1, f2, f3)
```

<pre>
1 0 -1 0
</pre>

```python
# Scipy라이브러리의 quad()함수
import sympy as sp
import scipy.integrate as sc

x = lambda x: x**2
f = sc.quad(x, 0, 1)
print(f)
```

<pre>
(0.33333333333333337, 3.700743415417189e-15)
</pre>