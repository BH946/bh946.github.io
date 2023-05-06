---
title:  "[Python]논리식 & 부분집합(Logic expression & isSubset)"
categories : Algorithm
tag : [Logic expression, isSubset, 논리식, 논리적추론, 부분집합]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



## 1.논리식(Logic expression)

* \|은 합집합, &는 곱집합, >>는 ->, ~는 not기호 그리고 given은 조건, conclu는 결론

<br>

### pyprover 준비물

```python
pip install pyprover
```

<br>

**기본 import**

```python
from pyprover import *
```

<br>

### pyprover 사용법

**사용 방식**

```python
# (P ^ Q) → P
given = (P&Q)
conclu = P
print("true") if proves( given, conclu ) else print("false")
```

<br>

**다양한 논법**

* 후건 부정(부정 논법) modus tollen

  ```python
  given = (~Q&(P>>Q))
  conclu = ~P
  print("true") if proves( given, conclu ) else print("false")
  ```

* 구성적 양도 논법 constructive dilemma

  ```python
  given = ((P>>Q)&(R>>S)&(P|R))
  conclu = Q|S
  print("true") if proves( given, conclu ) else print("false")
  ```

* 파괴적 양도 논법 destructive dilemma

  ```python
  given = (((P>>Q) & (R>>S)) & (~Q|~S))
  conclu = ~P|~R
  print("true") if proves( given, conclu ) else print("false")
  ```

* 전건 긍정 modus ponens

  ```python
  given = (P & (P>>Q))
  conclu = Q
  print("true") if proves( given, conclu ) else print("false")
  ```

* 가언 삼단 논법 hypothetical syllogism

  ```python
  given = ((P>>Q) & (Q>>R))
  conclu = P>>R
  print("true") if proves( given, conclu ) else print("false")
  ```

* 술어 논리 predicate logic

  * TE는 Exist(**∃x**)를 의미하고 FA는 ForAll(**∀x**)를 의미하는 한정자이다.

  ```python
  # Statement: Not all students like homework 
  # Negation: There is at leat one student who does not like homework 
  # x: students, P: isHomeworkLikable(x)
  
  given = ~(FA(x, P(x))) # Not all students like homework 모든학생들은 숙제를 하지 않았다
  conclu = TE(x, ~P(x)) # 숙제를 하지않은 학생은 최소 한명 이상이 있다
  print("true") if proves( given, conclu ) else print("false")
  ```

<br><br>

## 2.부분집합(isSubset)

* 데이터프레임의 경우 예시..(즉, pandas 기본적으로 import)

<br>

### 예시로 만들어보는 함수

* 데이터 한개한개 전부 비교.(정확 대신 매우느림)

```python
"""## ■ isSubset 함수 만들기
만약 데이터프레임 dfA, dfB가 주어질 때,
dfA가 dfB의 서브셋이고, dfA가 dfB의 서브셋이라면(동치=equivalence) return True
만약 dfA가 dfB의 서브셋이고 그 반대는 성립하지 않는 경우에도 return True
그 외(서로의 서브셋이 아닌 경우, dfB가 dfA의 서브셋이지만 그 반대는 성립하지 않는 경우)는 return False
"""
```

```python
첫번째 방법 알고리즘 시작
1. dfA와 dfB를 리스트 형태로 바꿔 줍니다.
2. dfA를 기준으로 dfB와 리스트 하나하나 비교해서 서로 다른것의 횟수(diffrenceCount)를 카운트 합니다.
3. dfB를 기준으로 dfA와 리스트 하나하나 비교해서 서로 다른것의 횟수(diffrenceCount)를 카운트 합니다.
4. 마지막으로 서로 다른것의 횟수(diffrenceCount)를 이용해서 서브셋인지 아닌지 판단하는 조건문을 작성합니다.
def isSubset(dfA, dfB):
    diffrenceCountA = 0 # 다른 개수
    diffrenceCountB = 0
    listA = dfA.values.tolist() # 리스트로 변환
    listB = dfB.values.tolist()
    for i in range(0, len(listA)): # dfA기준으로 dfB와 하나하나 비교하기
        for j in range(0, len(listB)):
            if(listA[i] == listB[j]): # 같다면, 바로 넘어가기.
                break
            if(j == (len(listB)-1)): # 마지막 행인지 확인 및 여기까지 조건문이 온다면? 같은거 없다는 말.
                diffrenceCountA += 1
    for i in range(0, len(listB)): # dfB기준으로 dfA와 하나하나 비교하기
        for j in range(0, len(listA)):
            if(listB[i] == listA[j]):
                break
            if(j == (len(listA)-1)):
                diffrenceCountB += 1
    if(diffrenceCountA == 0 and diffrenceCountB == 0): # dfA>dfB && dfA<dfB 동치. true 반환
        return True
    elif(diffrenceCountA == 0 and diffrenceCountB != 0): # dfA>dfB && dfA!<dfB 이것도 true 반환
        return True
    elif(diffrenceCountB == 0 and diffrenceCountA != 0): # dfA!<dfB && dfA!>dfB 다름. false 반환
        return False
    elif(diffrenceCountB != 0 and diffrenceCountA != 0): # dfA<dfB && dfA!>dfB 이것은 false 반환
        return False
    else:
        return False
        
# 출력문 생략
```

<br>

아래는 기본 데이터가 같다고 가정하고 크기만 비교한 방법임.(부정확 대신 빠름)

```python
"""## ■ isSubset 함수 만들기
만약 데이터프레임 dfA, dfB가 주어질 때,
dfA가 dfB의 서브셋이고, dfA가 dfB의 서브셋이라면(동치=equivalence) return True
만약 dfA가 dfB의 서브셋이고 그 반대는 성립하지 않는 경우에도 return True
그 외(서로의 서브셋이 아닌 경우, dfB가 dfA의 서브셋이지만 그 반대는 성립하지 않는 경우)는 return False
"""
```

```python
# 두번째 방법 알고리즘 시작
def isSubset(dfA, dfB):
    dfC = pd.merge(dfA, dfB) # 교집합
    listA = dfA.values.tolist() # 리스트로 변환
    listC = dfC.values.tolist()
    lenA = len(listA)
    lenC = len(listC)
    if(lenA <= lenC): # 교집합의 크기와 A집합의 크기가 같거나 A가 작으면 부분집합
        return True
    else:
        return False

print(isSubset(P, intersection_commute))
print(isSubset(P, P))
print(isSubset(P, S))
print(isSubset(P, P_only))
```

