---
title:  "[Python]Graph_matplotlib(networkx+numpy+pandas)"
categories : PY
tag : [파이썬, 그래프]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



## matplotilib, networkx, numpy, Pandas 라이브러리

* **matplotilib라이브러리를 통해 그래프를 시각화해서 보여줄 수 있다.**
  * 좀더 진보된 라이브러리를 원한다면, seaborn 등을 참고하면 좋다.
* **networkx라이브러리를 통해 그래프를 시각화해서 보여줄 수 있다.**
  * **노드** : 값, **에지** : 경로(연결선), **차수** : input&output하는 경로수
  * 방향 그래프, 무방향 그래프, 가중치 그래프..등등 정말 다양함.
  * pandas -> networkx ? : nx.from_pandas_edgelist(~~) 이용
  
* **numpy라이브러리를 통해 복잡한 수치연산을 수행할 수 있다.(선형대수 라이브러리)**
  * 연산에 numpy를 이용, C언어로 구현됨
  * 이미 경험한 pandas와 matplotlib의 기반으로 사용될 수 도 있다. 예로, 라이브러리를 사용하면 numpy의 행렬을 pandas데이터로 변환할 수 있다.
  * 기본적으로 Numpy는 배열/행렬(array) 단위로 데이터를 기본으로합니다. 따라서 1d, 2d, 3d(d는 차원으로써 dimention의 약어)에 기반한 배열 연산을 수행합니다.
  * 매우 방대하기 때문에, 자세히 알고 싶다면 책을 따로 사서 볼 것.
* **pandas라이브러리는 우리가아는 데이터프레임을 의미(df)**

<br>

**준비물**

```python
pip install -U matplotlib
pip install networkx
pip install numpy
pip install pandas
```

<br>

**기본 import**

```python
import matplotlib.pyplot as plt
import networkx as nx
import numpy as np
import pandas as pd # pandas의 경우 보통 pd로 이름변경해서 사용하는것이 관례임
```
<br><br>

## 1. 사용법(matplotlib)

**데이터프레임의 시각화는 plot()**

```python
df.plot(figsize=(12,12)) # 그림 size가 12x12 크기이다.
plt.show() # 그래프 보여줌(코랩에선 fig까지만)

df.plot(ax=ax1) # 아래 ax1그래프 적용
df_ride_dist.plot(ax=ax2, linestyle='dotted') # linestyle도 적용가능
fig
plt.show()
```

<br>

**여러 개의 그래프**

```python
fig = plt.figure(figsize=(14,10))
ax1 = fig.add_subplot(2,2,1)
ax2 = fig.add_subplot(2,2,2)
ax3 = fig.add_subplot(2,2,3)
plt.show() # 그래프에 데이터는 없는 상태
```

<br>

**3번째 그래프 차트에 x,y 값을 추가**

```python
x = np.arange(50)
y = np.random.randn(50)
ax3.plot(x, y, 'r--')
fig # 코랩에선 여기서 그래프 보여줌
plt.show()
```

<br>

**그래프 스타일 속성**

```python
# 1. df_hadan_transpose 를 막대(bar) 그래프 차트를 사용하여 그리기
# 2. 그래프에 대한 x축 레이블(label)은 '시간대'로 y축 레이블은 승하차 인원, 제목(title)은 '시간대별 승하차 인원'으로 작성

ax = df_hadan_transpose.plot.bar()
ax.set_xlabel("시간대") # 그래프의 x축 이름
ax.set_ylabel("승하차 인원") # 그래프의 y축 이름
ax.set_title("시간대별 승하차 인원") # 그래프의 제목
# plt.show() # => 한글이 깨지는데 파이썬에서는 어째 해결하는지 모르겠다.(VS-CODE에서는 양호)
```

<br>

**이미지 파일로 내보내기**

```python
plt.savefig("bar_chart.png")
```
<br><br>

## 2. 사용법(networkx)

**그래프 선언**

* 기본 그래프 선언(Graph())

  ```python
  g=nx.Graph()
  ```

* 방향 그래프 선언(DiGraph())

  ```python
  G = nx.DiGraph()
  ```

* 가중치 그래프 선언(DiGraph()+weight)

  ```python
  G = nx.DiGraph()
  G.add_nodes_from(['A','B','C','D','E'])
  G.add_edge('A','C',weight=8)
  ...
  ```

* 그래프 선언 함수들

  ```python
  # 랜덤으로 생성되는 방향을 가진 그래프 (directed graph)
  random_digraph = nx.fast_gnp_random_graph(10, 0.3, seed=None, directed=True)
  
  # k-clique(클리크)
  g = nx.connected_caveman_graph(5, 5)
  
  # watts_strogatz_graph는 Small World 그래프 생성을 위해 다음과 같은 arguments을 input으로 받아 그래프를 생성합니다.
  # n은 노드의 수, k는 각 노드마다 연결될 이웃의 수(edge의 수), p= 연결될 확률
  g = nx.watts_strogatz_graph(n = 100, k= 5, p = 0.5, seed=None)
  
  # a chain 0-1-2-3-4
  g = nx.path_graph(5)
  
  # trips는 df이며, df->그래프로 바꾸는 방식
  g = nx.from_pandas_edgelist(trips, source="src", target="dst", edge_attr=['distance'])
  ```
  

<br>

**노드, 에지**

* **노드, 에지 추가**

  * 노드, 에지 추가(**add_node**, **add_edge**, **add_path**)

    ```python
    g.add_node("A")
    g.add_edge("A","C")
    nx.add_path(g1, [1,2,3,4,5,6,7,8,9,10]) # 노드와 경로 한번에 지정
    nx.add_path(g2, [10,20,30,40,50,60,70,80,90,100])
    ```

  * 리스트 등으로부터 노드, 에지 추가(**add_nodes_from**, **add_edges_from**)

    ```python
    g.add_nodes_from(["D","E","F"]) # 리스트 등으로부터 여러 노드를 추가합니다
    g.add_edges_from([("B","B"),("B","C"),("B","D"),("B","F")])
    ```

* **노드, 에지 삭제**

  ```python
  g.remove_node("G")
  g.remove_edge("C","D")
  ```

* **노드, 에지의 수를 확인**

  ```python
  g.number_of_nodes()
  g.remove_edge("C","D")
  ```

* **노드, 에지를 확인**

  ```python
  g.nodes
  g.edges
  ```

<br>

**그래프 화면 출력 여러가지 방법들**

```python
nx.draw(g, with_labels = True) # networkx 라이브러리
plt.savefig("G.png") # matplotlib 라이브러리
plt.show()
```

<br>

**차수(Degree)**

```python
# in/out degree 출력
G.degree

# in_degree 출력
G.in_degree()

# out_degree 출력
G.out_degree()
```

<br>

 **전임노드(Predecessor node)와 후임노드(Successor node)**

* 예를 들어 A를 기준으로 치면 다음으로 가리키는 C, E가 Successor node가 된다.

  그리고 A를 기준으로 화살표를 받는 B,D가 Predecessor node가 된다.

```python
# A의 successor nodes
list(G.successors('A'))

# A의 predecessors nodes
list(G.predecessors('A'))
```
<br><br>

## 3. 사용법(numpy)

**numpy 데이터 선언하기**

```python
data1 = [1,2,3,4,5] # 리스트로 봄
arr1 = np.array(data1) # 배열로 보여줌.
arr4 = np.array([[1,2,3],[4,5,6],[7,8,9],[10,11,12]]) # 2차원 배열로 보여줌

np.zeros((3,3)) # 3x3배열 0으로 초기화
np.ones((3,3)) # 3x3배열 1로 초기화
np.arange(0,30) # 0~29 배열
```

<br>

**numpy 데이터간 연산하기**

```python
arr1 = np.array([[True,True],[False, False]])
arr2 = np.array([[True,True],[True, False]])

# 논리합 계산하기
# astype(int)를 통해서 True/False 값을 0/1로 변환하기
np.logical_and(arr1, arr2).astype(int) # 2개 배열 비교해서 and연산 결과.
```

<br>

**numpy와 random을 이용한 조합**

```python
monthly_days = np.arange(0,30) # 0~29 배열
base_date = np.datetime64('2021-03-01') # datetime64라는 numpy라이브러리에 잇는 클래스 사용
random_date = base_date + np.random.choice(monthly_days) # 랜덤으로 날짜 반환
# 출력 : 2021-03-05, 2021-03-08 ... 일자는 랜덤으로
```

<br>

**관계에 대한 이해**

```python
def gen_rand_boolean_matrix(N): 
  arr = np.full((N,N), False) # 배열로 NxN 생성해줌, 전부 False로.
  idx = np.random.randint(N, size=N)
  # replace "False" by "True" at given indices
  arr[range(N), idx] = True # range(N)을 통해 N번
  return arr

M = 8
R = gen_rand_boolean_matrix(M)
S = gen_rand_boolean_matrix(M)
R_and_S = np.logical_and(R,S)
print(R_and_S) # 배열의 관계 즉, 논리합을 알 수 있다.
print(R) # 배열 초기화 한값으로 나타냄(T/F)
print(R.astype(int)) # 배열 int형으로 나타냄(1/0)
```
<br><br>

## 4. 사용법(pandas)

**함수들**

* 상위, 하위 호출(**head, tail**)

  ```python
  print(df.head(10)) # 명령어를 호출하여 상위 10개의 데이터를 불러옵니다.
  print(df.tail(10)) # 하위 출력
  ```

* 차원(486 rows × 29 column), 통계(**shape, describe**)

  ```python
  # 2차원 행렬로 구성된 데이터 프레임의 크기 출력 ( number_of_rows, number_of_columns )
  print(f"차원 수 : {df.shape}") # 행 x 열
  print(f"행의 수 : {df.shape[0]}") # 행
  print(f"열의 수 : {df.shape[1]}") # 열
  print(df.describe()) # 데이터프레임의 표현적인 통계(descriptive statistics) 보기
  ```

* df에 원하는 필드와 원하는 레코드만 df_sub로 저장

  ```python
  df_sub = df[['역번호','역명','구분','08시-09시']]
  df_sub = df_sub[30000:40000]
  ```

* '구분'에 따른 평균,최대,최소 구하기(= '구분'에 그룹화 후 평균,최대,최소)

  ```python
  print(df_sub.groupby(['구분'],as_index=True).mean()) # mean() 평균
  # print(df_sub.groupby(['구분'],as_index=True).min()) # min() 최소
  # print(df_sub.groupby(['구분'],as_index=True).max()) # max() 최대
  ```

* 특정 컬럼값을 만족하는 데이터프레임을 출력하기(**loc, iloc**)

  ```python
  df.loc[df['column_name'] == some_value]
  # ex) 하단역(102)에 대한 승하차정보만 갖게 필터링
  df_hadan = df.loc[df['역번호'] == 102]
  # ex) 두 가지 이상의 필터를 혼합
  df_hadan_sub = df_hadan.loc[(df_hadan['08시-09시'] >= 1000) & (df_hadan['18시-19시'] >= 2000)]
  
  # df.iloc[start_row:end_row, start_col:end_col]
  mr.iloc[:,1:2] # mr은 df형식
  mr.iloc[:,0:2] # 이 열만 보여줌
  mr[1:2] # 이 행만 보여줌
  ```

* 행, 열 교환(**Transpose** 메소드)

  ```python
   df.T
  ```

* 열 이름 바꾸기(**rename**)

  ```python
  usa_airports = ~~.loc[:,[1,4,6,7]] # df로 구성된 usa_airports
  usa_airports.rename(columns={1: 'name', 4: 'id', 6: 'latitude', 7: 'longitude'}, inplace=True)
  ```
  
* **리스트로 변환**

  ```python
  listA = dfA.values.tolist() # 데이터프레임의 값들을 tolist()한다.
  ```

* **해당 데이터에 원하는 값이 있는지 찾기**

  ```python
  src = usa_airports[usa_airports.name.str.contains('San Francisco International Airport')]
  # 더 나아가서 공항 이름 찾은 행 데이터 src를 공항코드로 접근하기
  src = src.iloc[:,1].values[0] # 공항코드 값으로 변경
  ```


<br>

**csv->xlsx (read_csv(), to_excel()) : csv읽기, 엑셀로 변환** 

* **read_csv()**

  ```python
  md = pd.read_csv('부산교통공사_시간대별 승하차인원_20210801.csv', encoding='cp949')
  ```

  * 속성들 : header, encoding, low_memory... 등

    ```python
    header=None으로 속성을 줄 수 있다.
    encoding='cp949'
    low_memory=False
    ```

* 우선 ExcelWriter메소드 사용을 위해 xlsx파트에서 import 따라하기.
* Workbook + Pandas인 응용이라 생각하면 됨.

```python
# encoding은 항상 고려해줘야함. (한글이 깨질 수 있기 때문)
md = pd.read_csv('부산교통공사_시간대별 승하차인원_20210801.csv', encoding='cp949')
writer = pd.ExcelWriter('product_jinnyhands.xlsx') # 이때 엑셀 만들어짐.

md.to_excel(writer, index = False) # 엑셀에 데이터 삽입
writer.save() # 엑셀 데이터 저장
```
<br>

### 집합 연산

**UNION(합집합)**

* 두가지 방법 이상이 기대될수 있다. 1) pd.concat을 사용하여 데이터프레임을 유지하거나, 2) 각 데이터에 대해 list 형태로 변환한 뒤에 계산하는 방법

```python
# pd.concat 사용

union_commute = pd.concat([P, S], ignore_index = True) # pd.concat을 이용해 P, S를 합할수 있다. ignore_index를 통해 행 인덱스 번호도 재배열!
union_commute = union_commute.drop_duplicates() # drop_duplicates메소드를 이용해 중복값 제거를 할 수 있다.
```

<br>

**INTERSECTION(교집합)**

* pd.concat은 outer로 join이 기본값으로 되어있으므로 합집합이 나온다.(outer:합집합, inner:교집합) 따라서 join을 inner로 바꿔주거나, merge함수를 사용해주면 된다.(merge의 기본값은 inner)

```python
# pd.merge 사용

intersection_commute = pd.merge(P, S)
```

<br>

**DIFFERENCE(차집합)**

* isin메소드 이용

```python
P_only = P[P.index.isin(S.index) == False]
S_only = S[S.index.isin(P.index) == False]
```

