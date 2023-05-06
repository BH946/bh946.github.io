---
title:  "[Python]Relation(관계 관련 알고리즘)"
categories : Algorithm
tag : [부울곱(Boolean Product), 추이 관계(Transitive Relation), 대칭 관계(Symmetric Relation), 반사 관계(Reflexive Relation), 동치 관계(Equivalence Relation), 플로이드 워셜 알고리즘(Floyd Washall Algorithm)]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



## 관계(Relation)

* 행렬을 사용하기 좋게끔 **numpy**라이브러리를 기본적으로 사용할것이다.
* 관계 R에 대한 P닫힘이란? R1이 R을 포함하고, 성질 P를(반사,대칭,추이 등) 갖는것.
  * R<sup>*</sup> 는 관계 R의 추이폐포(닫힘=폐포)

* 부울곱, 추이, 대칭, 반사, 동치, 플로이드 워셜 알고리즘

<br>

#### 부울곱(Boolean Product)

* 절대!! 행렬의 곱하기와 햇갈리지 말것!! 이것은 Boolean 곱이다!!

```python
def my_boolean_product(R, S):
  '''
  주어진 관계 매트릭스로부터 부울곱 (Boolean Product)한 결과를 계산하시오
  :param R: NxN Boolean 기반 관계행렬
  :param S: NxN Boolean 기반 관계행렬
  :return: NxN Boolean 기반 관계행렬
  '''
  # NxN 정방행렬에서 얘기하는 중이므로 N 구하기
  N = len(R)
  # NxN 부울곱한 관계행렬인 M 초기화
  M = np.full((N,N), False)
  # 부울곱 알고리즘 시작
  for i in range(N): 
    for j in range(N):
      for q in range(N):
        if(R[i,q] and S[q,j]): # 둘다 1, 1이면 True조건문 (한번이라도 1이나오면 됨. 결국 합집합으로 합하기 때문.(예:0or1=1))
          M[i,j] = True
  return M # M은 관계행렬
```

<br>

#### 추이 관계(Transitive Relation)

* 추이관계와 거듭제곱의 관계 -> R=R<sup>2</sup>=R<sup>3</sup>=...이면 추이관계이다.

* (a,b) 와 (b,c)가 True 라면 (a,c)가 True여야한다.

```python
def is_transitive_relation(M):
  '''
  추이 관계인지를 확인하는 함수
  :param M: NxN Boolean 기반 관계행렬
  :return: True / False
  '''
  N = len(M)
  check = 0
  for i in range(N):
    for j in range(N):
      for q in range(N):
        if(M[i,j] and M[j,q]): # (i,j) 와 (j,q)가 True 라면 (i,q)가 True여야한다.
          if(M[i,q] == True):
            check = 1 # 추이관계가 하나라도 존재하긴 하는지 유무를 판단(i,j와j,q가 True인 경우가 아예 없을수도 있어서.)
          else:
            return False # (i,q)가 True가 아니면 추이관계 성립X
  if(check == 1):
    return True
  else:
    return False
```

<br>

#### 대칭 관계(Symmetric Relation)

* (a,b) 와 (b,a)가 같아야 대칭이다.

```python
def is_symmetric_relation(M):
  '''
  대칭 관계인지를 확인하는 함수
  :param M: NxN Boolean 기반 관계행렬
  :return: True / False
  '''
  N = len(M)
  for i in range(N):
    for j in range(N):
      if(M[i,j] != M[j,i]):
        return False
  return True
```

<br>

#### 반사 관계(Reflexive Relation)

* (a,a), (b,b), (c,c) ... 주대각 원소가 1(True)여야 반사이다.

```python
def is_reflexive_relation(M):
  '''
  반사 관계인지를 확인하는 함수
  :param M: NxN Boolean 기반 관계행렬
  :return: True / False
  '''
  # 주대각 원소가 1인지 확인 하면 됨.
  N = len(M)
  for i in range(N):
    if(M[i,i] == False):
      return False
  return True
```

<br>

#### 동치 관계(Equivalence Relation)

* 위의 3가지(추이, 대칭, 반사) 관계가 성립한다면 동치이다.

```python
def is_equivalence_relation(M):
  '''
  동치 관계인지를 확인하는 함수
  :param M: NxN Boolean 기반 관계행렬
  :return: True / False
  '''
  # 반사, 대칭, 추이 이기만 하면 동치관계인지 증명할 수 있다.
  if(is_transitive_relation(M) and is_symmetric_relation(M) and is_reflexive_relation(M)):
    return True
  return False
```

<br>

#### 플로이드 워셜 알고리즘(Floyd Washall Algorithm)

* 워셜 알고리즘은 연결관계를 구하는 방법이다.
  * R<sup>*</sup>

* 주어진 관계행렬을 만족하는 가장 작은 동치 관계(Equivalence relation)를 구할 수 있다.
  * (RUS)<sup>*</sup>

* 간단한 설명(Equivalence relation 구하기)
  * 제일 처음 관계행렬이 W0로 초기화 되며, 최종 Wn을 구하면 그것이 답이 된다.
  * 각각 해당 열의 True, 해당 행의 True값을 보관후 그것으로 나타낼 수 있는 모든 경우의수를 다시 newList로 보관한다.
  * newList로 나타낸 모든 경우를 W행렬에 반영해주고, 이를 n번 반복하면 끝이다.

```python
def floyd_washall(R, S):
  '''
  관계행렬 R과 S를 입력으로 받아서 플로이드 워셜 알고리즘을 통해 가장 작은 동치 관계행렬을 반환한다
  :param R: NxN의 관계행렬
  :param S: NxN의 관계행렬
  :return: NxN의 관계행렬
  '''
  # NxN 관계행렬을 2개 받기 때문에 N을 구하기 간단.
  n = len(R)
  W0 = np.logical_or(R,S) # W0 초기화
  Wn = W0
  for k in range(n):
    colList = [] # 열 리스트
    ranList = [] # 행 리스트
    newList = []
    Wn = Wn
    for i in range(n):
      if(Wn[i][k] == True): # 1열의 논리값 1찾기
        colList.append(i)
      if(Wn[k][i] == True): # 1행의 논리값 1찾기
        ranList.append(i)
    for i in range(len(colList)): # newList에 앞서 저장한 논리값들 정리.
      for j in range(len(ranList)):
        newList += [[colList[i],ranList[j]]]
    for i in range(len(newList)):
      Wn[newList[i][0]][newList[i][1]] = True
  E = Wn
  return E # 가장작은 동치 관계행렬을 반환
```



