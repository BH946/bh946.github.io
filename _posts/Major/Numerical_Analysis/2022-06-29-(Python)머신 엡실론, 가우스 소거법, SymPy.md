---
title:  "[Python]머신 엡실론, 가우스 소거법, SymPy"
categories : [Numerical_Analysis]
tag : [수치해석, 머신 엡실론, 가우스 소거법, SymPy]
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


## 머신 엡실론(Machine Epsilon)

* 컴퓨터는 숫자 실수를 bit수로 하여 유한하게 가지기 때문에 생기는 한계를 머신 엡실론이라 한다.

* 기호 : ε

  * 1 + ε != 1



```python
# 컴퓨터가 가지는 한계(머신 엡실론) 때문에 False인 것이다.
print(0.1 + 0.2 == 0.3)
```

<pre>
False
</pre>

```python
print(0.1 + 0.2 == 0.3)
print(0.3)
print((0.1+0.2)-0.3) # 오차
```

<pre>
False
0.3
5.551115123125783e-17
</pre>




### 오차가 엡실론 만큼인지 확인법



```python
import sys
import numpy as np
print(np.abs((0.1+0.2)-0.3) <= sys.float_info.epsilon) # float의 머신엡실론
print(np.isclose(0.1+0.2, 0.3)) # 두수가 머신엡실론 안으로 들어오는지 확인하는 함수(가까운수인지)
```

<pre>
True
True
</pre>
<br>

## 가우스 소거법

* 선형 시스템을 컴퓨터로 해결하는 방법이다.



```python
A = np.array(
[[15,-2,-6,0],
[-2,12,-4,-1],
[-6,-4,19,-9],
[0,-1,-9,21]])
B = [300,0,0,0]
X = np.linalg.solve(A,B)
print(X) # 해를 구한것
```

<pre>
[26.54915785  9.35370153 13.25499412  6.12612613]
</pre>
<br>

## 근의 위치 찾기 - SymPy



```python
import numpy as np
import sympy as sp

a = sp.symbols("a")
type(a)
```

<pre>
sympy.core.symbol.Symbol
</pre>

```python
# 해당 식을 풀어서 a를 구할것임
f = a*sp.cosh(50.0/a)-(a+10.0)
f
```

<pre>
a*cosh(50.0/a) - a - 10.0
</pre>

```python
equation = sp.Eq(a*sp.cosh(50.0/a), a+10)
sp.nsolve(equation, [100,200]) # 범위를 넣어주면 훨씬 빨리 찾는다.
# 이 범위는? 우리가 해결해야 할 알고리즘(이분법을 통해 범위축소해서 넣어주면 더 좋지 않을까 싶다)
```

<pre>
126.632436039989
</pre>