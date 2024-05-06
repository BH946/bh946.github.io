---
title:  "[Python]Graph(그래프 관련 알고리즘)"
categories : Algorithm
tag : [DFS, BFS, 소셜 네트워크 분석, 그래프 판별법, 최단거리(다익스트라=Dijkstra's), 플로이드 워셜(Floyd Washall), 웰치-포웰(Welch Powell), Isomorphism graph(동형), Dense graph(밀집), Euler graph(오일러)]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



## Graph(그래프)

* 그래프 : G=(V,E)=(N,E) => V정점, N노드, E간선

<br>

### 1. Basic concept of graphs(여러 그래프들의 기본 개념)

**다중 그래프(Multi Graph) : 두 정점에 2개 이상의 간선이 허용.(=다중 타입)**  
**수도 그래프(Pseudoudo Graph) : 두개 이상의 간선과 self-loop를 가진다.**   
**유향 그래프(Directed Graph) : 정점의 순서를 생각하는 그래프(E는 V에 이항관계)**  
**무향 그래프(Undirected Graph) : 정점의 순서를 무시하는 그래프(k-클리크)**  

**정규 그래프(Regular Graph) : 모든 정점의 차수가 같다. 즉, 모든 꼭짓점이 동일한 수의 이웃을 가지는 그래프. (K와 C는 항상 정규 그래프이다.)**  
=> Kn, Cn, W<sub>3</sub>은 정규 그래프이다. (W는 n이 아닌이유가 W<sub>4</sub>는 정규 그래프X 라서)

* **완전 그래프(Complete)** : 무향 그래프가 모든 정점의 쌍 사이에 간선이 존재.**(Kn)**  
  => n(n-1)/2개의 간선

* **사이클 그래프(Cycle Graph)** : {v<sub>1</sub>,v<sub>2</sub>,..v<sub>n</sub>} and {e<sub>1</sub>,e<sub>2</sub>,..e<sub>n</sub>} 가지고 mod로 표현가능.**(Cn)**  
  => {V<sub>k</sub>,V<sub>k+1</sub>}의 조건은 V<sub>k+1</sub> mod n. 			EX) n=6일시 0~5 존재.

* **휠 그래프(Wheel Graph)** : 허브가 존재하는(모두 연결해주는 노드) 그래프. n+1 노드가짐.**(Wn)**  
  => EX) W<sub>6</sub>은 노드가 7개라는 뜻. Cycle그래프에 허브노드(모든노드 연결지점)를 추가한 그래프이다.

**동형 그래프(Isomorphic Graph) : "두 그래프의" 1.노드수, 2.에지수가 동일하다.(=전단사함수) 3.인접노드와 정확히 일치 한다면 "동형"이라 한다.**  
=> 목적의 대부분은 Planer그래프 만들려고(평면). Why? 노드간 거리 등 간단히 볼수 있다.  

* **준동형 그래프(Homomorphism Graph)** : 그래프는 노드, 에지수가 달라도 그래프 뿌리 즉, 모양이 같다.  
  => zoom in,out 에 실제로 활용

**부분 그래프(Sub Graph) : G=(V,E)있을 때 V'와 E'가 부분집합이다.**

* **진부분 그래프(Proper Subgraph)** : 부분그래프에서 E'!=E이다. (즉, 노드같고 에지다름)
* **스패닝 그래프(Spanning Graph)** : 부분그래프에서 V'=V이고, E'가 부분집합이다. (노드같고 경로가 다름)

**이분 그래프(Bipartite Graph) : M과 N집합 사이에 간선으로 정점들을 연결.**  
=> V=MUN, M∩N=∅(정점집합 V가 M과 N으로 분할)

* **완전 이분그래프(Complete Bipartite Graph)** : M, N내의 모든 정점사이에 모두 간선이 존재.(K<sub>n,m</sub>)  
  => K<sub>m,n</sub>은 mxn간선개수 가짐.
* 이분 그래프와 완전 이분그래프 둘다 각 집합M과N의 내부 노드끼린 에지가 없어야함.

**Graph density : 에지 수에 의해 밀집, 희소가 결정된다고 볼 수 있다.**  
=> 1) Undirected simple graphs : D=2|E|/|V|(|V|-1)  
=> 2) Directed simple graphs : D=|E|/|V|(|V|-1)

* **희소 그래프(Sparse Graph)** : 간선의 개수가 |V|<sup>2</sup>보다 훨씬 작은 그래프.   
  => 주로 인접리스트 사용
* **밀집 그래프(Dense Graph)** : 간선의 개수가 |V|<sup>2</sup>과 비례하는 그래프.  
  => 주로 인접행렬을 사용
* **가중치 그래프(Weighted Graph)** : 간선에 음수가 아닌 가중치를 할당.  
  => 최소 가중치로 가는 방법(알고리즘) 여러가지 존재함.

**오일러 루프(Euler Loop) : 모든 "간선(에지)"를 딱 한번만 거쳐서 시작 정점으로 다시 돌아오는 루프.**

* **오일러 그래프(Euler Graph)** : 오일러 루프를 가진 그래프.  
  => 필요충분조건 : G의 모든 정점이 짝수의 차수를 가짐.
* **추가설명** : 오일러 경로란? 모든 변을 꼭 한 번씩 지나는 경로. 그런데, 얘는 오일러 그래프의 필요충분조건이 아니라는점 기억!! 오일러 그래프는 오일러 루프이기만 하면 됨!!

**해밀턴 순환(Hamilton Cycle) : 모든 "정점"들을 단 한번만 거쳐서 다시 시작 정점으로 돌아오는 순환.**

* **해밀턴 그래프(Hamilton Graph)** : 해밀턴순환을 가진 그래프.

**평면 그래프(Planar Graph) : 어떤 교선도 갖지 않고 평면 위에 그릴 수 있는 그래프.**  
=> 이분, 완전 그래프는 Planar가 되기 힘들다.

* **오일러의 정리** : v-e+f=2 암기. (Planar라면, 오일러식 성립) => e<=3(v-2) 로 계산  
  교점 : 교차되는 점  
  교선 : 교차되는 간선
* **다각형 그래프(Polygon Graph)** : 하나의 순환으로 된 그래프를 다각형이라 한다.  
  => 내용보단 의미만 알기. 이 또한 평면 그래프이다.
  * 면(face) : 다각형 그래프 G에 의해서 정의되는 영역들  
    G의 최대순환(maximal cycle) : G의 모든 면을 포함하고 있는 다각형.  
    면 r의 차수 : r의 경계를 이루는 경로의 길이. deg(r) = 에지수.
* **비평면 그래프(Nonplanar Graph)** : 평면 그래프가 아닌 그래프.  
  => EX) 이분, 완전 그래프, 오일러식 만족X

<br>

**그림** -> 출처 : [wolfram](https://mathworld.wolfram.com/), [gs](https://www.geeksforgeeks.org/)

- **다중, 수도 그래프**
  - ![GraphsSimple](https://mathworld.wolfram.com/images/eps-svg/GraphsSimple_800.svg)  

- **방향, 무방향 그래프**
  - ![GraphsDirected](https://mathworld.wolfram.com/images/eps-svg/GraphsDirected_750.svg) 

- **정규 그래프** -> 이웃 3개 예시
  - ![CubicGraphs](https://mathworld.wolfram.com/images/eps-svg/CubicGraphs_800.svg)  

- **완전 그래프 (Complete Graph):**
  - ![CompleteGraphs](https://mathworld.wolfram.com/images/eps-svg/CompleteGraphs_801.svg) 

- **사이클 그래프 (Cycle Graph):**
  - ![CycleGraphs](https://mathworld.wolfram.com/images/eps-svg/CycleGraphs_850.svg) 

- **휠 그래프 (Wheel Graph):**
  - ![WheelGraphs](https://mathworld.wolfram.com/images/eps-svg/WheelGraphs_1001.svg) 

- **동형 그래프 (Isomorphic Graph):**
  - <img src="https://github.com/BH946/bh946.github.io/assets/80165014/a586b401-524c-4855-82dd-0ffde971a5b0" alt="Isomorphic" style="zoom:67%;" />  

- **준동형 그래프 (Homomorphism Graph):**
  - <img src="https://github.com/BH946/bh946.github.io/assets/80165014/623bd1e2-5a7f-4e12-b547-9b5084415294" alt="Homomorphism" style="zoom: 67%;" /> 

- **부분 그래프 (Sub Graph):**
  - <img src="https://github.com/BH946/bh946.github.io/assets/80165014/00046f47-e3d2-4a75-8dd3-0cfdbf9cc8d2" alt="sub" style="zoom: 25%;" />  

- **진부분 그래프 (Proper Subgraph):**
  - <img src="https://media.geeksforgeeks.org/wp-content/uploads/isomorphism-question.jpg" alt="Proper" style="zoom:50%;" />  

- **스패닝 그래프 (Spanning Graph):**
  - ![SpanningTrees](https://mathworld.wolfram.com/images/eps-svg/SpanningTrees_1000.svg) 

- **이분 그래프 (Bipartite Graph):**
  - ![BipartiteGraph](https://mathworld.wolfram.com/images/eps-svg/BipartiteGraph_1000.svg) 

- **완전 이분 그래프 (Complete Bipartite Graph):**
  - ![CompleteBipartiteGraph](https://mathworld.wolfram.com/images/eps-svg/CompleteBipartiteGraph_1000.svg) 

- **오일러 그래프 (Euler Graph):**
  - ![EulerianGraphsConnected](https://mathworld.wolfram.com/images/eps-svg/EulerianGraphsConnected_800.svg)  

- **해밀턴 그래프 (Hamilton Graph):**
  - ![HamiltonianGraphs](https://mathworld.wolfram.com/images/eps-svg/HamiltonianGraphs_800.svg) 

- **평면 그래프 (Planar Graph):**
  - ![PlanarGraphs](https://mathworld.wolfram.com/images/eps-svg/PlanarGraphs_800.svg) 

- **비평면 그래프 (Nonplanar Graph):**
  - <img src="https://mathworld.wolfram.com/images/eps-svg/PentatopeGraph_800.svg" alt="PentatopeGraph" style="zoom:67%;" /> 

<br>

### 2. Graph Coloring

**정점의 착색(=그래프 착색)**  
=> 인접한 노드만 다른컬러이면 되는게 여기서 중요한 포인트.

**n-색 가능(n-colorable) : n가지의 색으로 G의 착색이 가능**  
**착색수(chromatic number) : 착색하는데 필요한 최소의 색의 가지 수(x(G)로 표기)**  
**고유착색 : 인접 정점이 같은 색 갖지 않는 착색**  
=> 최대 colorable 의 경우 노드수 라는것  
=> chromatic은 최소 색 가지 수

그래프 G를 착색할 때 **웰치-포웰의 알고리즘** 사용가능 => chromatic 찾는 알고리즘

1. degree 구해서, vertice in descinding order을 구하는게 첫 단계!!(내림차순)
2. 그리고 처음에 Red정하면 그다음 녀석이 Red정한노드와 인접한지 확인후 인접안하면 Red색 주는 형식으로 반복반복..

```python
# Pseudo
Welch-Powell alogrithm consists of following steps:
1) Find the degree of each vertex
2) List the vertices in order of descending degrees
3) Color the first vertex with color 1
4) Move down the list and color all the vertices not connected to the colored vertex, with the same color
5) Repeat step 4 on all uncolored vertices with a new color, in descending order of degrees until all the vertices are colored
```

<br>

### 3. Graph Alogrithm

* 주로 networkx라이브러리 이용
* DFS, BFS, 소셜 네트워크 분석, 그래프 판별법, 최단거리(다익스트라=Dijkstra's), 플로이드 워셜(Floyd Washall), 웰치-포웰(Welch Powell), Isomorphism graph, Dense graph, Euler graph
* 그래프 판별법 종류 : Isomorphism graph, Sparse and Dense graphs, Euler and Hamiltonian graph

<br>

#### 깊이 우선 탐색(DFS)

* 목표 찾는데 좋음, 스택 구조(재귀)
* 일반적으로 BFS 중점으로 시작 후 DFS 까지 이용해서 탐색을 하는 편이다.

```python
# Pseudocode
void DFS(v)
int v;
{
    int w;
    extern int VISITED[];
    VISITED[v]=1; # src노드는 True로.
    while(v에 인접한 모든 노드 w) # v에 인접노드 없을때까지 반복
    	if( !VISITED(w))
    		DFS(w) # 재귀
}
```

<br>

#### 넓이 우선 검색(BFS)

* 가까운 근처 이웃부터 검색하는 방식
* DFS와 다름(깊이 우선 검색) : 보통 재귀 이용 <-> BFS : 보통 queue 이용(FIFO구조)

```python
def bfs(graph, source):
  '''
  # 넓이우선 검색 함수를 구현하시오
  :param graph: graph는 이전 g 파라미터
  :param source: source는 시작노드
  :return: 마지막 도착 노드
  '''
  visit = [] # 방문한 노드들 모아둘것임.
  queue = []
  queue.append(source)
  while queue: # 리스트 빌 때까지 반복 (len = 0)
    node = queue.pop(0) # queue리스트를 처음부터 pop해주면서 방문한 노드인지 확인작업 반복.
    if node not in visit: # 방문 안한 노드일경우 조건문.
      visit.append(node) # visit에 방문한 node추가.
      queue.extend(graph[node]) # queue리스트에 graph[node]부분 추가. ex) graph[0]=[2,3,4,24]
  last_visited = visit[len(visit)-1]
  return last_visited
print(bfs(g, 0)) # g는 k-클리크 그래프라고 가정(완전 그래프)
```

<br>

#### 소셜네트워크 분석

* HOP : 노드간 직접적인 연결이다. 예를 들면, (A)-(B)-(C) 가 3개로 노드로 이루어진 그래프에서, A와 C는 2 hops 관계 있다고 본다. 따라서, 큰 hop은 해당 노드로 부터 멀리 떨어져 있는 노드로 볼 수 있다. 소셜네트워크에서는, 친구와 친구 사이의 관계가 먼 것으로 이야기한다.

* Watts-Strogatz 그래프 모델 : Small-World 그래프로 표현. Small-World 그래프는 많은 노드가 하나 이상의 이웃을 가지지 않는, 일부 노드에 많은 이웃을 가지게되는 그래프입니다. 예로, 우리가 알고있는 인플루언서 기반 소셜네트워크, 온라인 상점 내 인기상품 기반 상품 네트워크 등은 이러한 Small World 그래프 입니다. 또한, 실제 사람들의 커뮤니티에서도 많은 사람들은 적은 수의 사람들을 알고 있으며, 소수의 사람들이 많은 인맥을 가지는 구조로 보여주게 됩니다.

  참조링크: (Small-World 그래프)[https://en.wikipedia.org/wiki/Small-world_network]

<br>

**1. source 로부터 n-HOPS가 떨어진 노드들을 찾는 방법**

```python
# shortest_path_length의 의미는 최단경로 길이를 의미!!
HOPS = 2
[n for n, d in nx.shortest_path_length(g ,source=0).items() if d==HOPS] # 1번 방법
for d in nx.shortest_path_length(g, source=0).items(): # 2번 방법
  if d[1] == 2:
    print(d)
```

<br>

**2. 주어진 노드가 50% 이상의 친구 노드를 가지는 최소 홉수찾는 방법**

* 알고리즘 : 인플루언서 p가 주어졌을때, p가 전체 노드의 50%를 알게 되는 최소 HOPS을 찾는 함수를 만들어야한다.
  즉, 0노드로 출발해서 도착하는 노드들과의 path즉, 홉수를 전체 구한다.
  그리고 첫 노드부터 하나씩 접근해서 'real_want_nodes' 이상 접근하면 그만두고, 그때의 홉수를 반환한다.

```python
def find_min_hops_with_above_50_friends(g, influencer=0, min_percent=50):
  '''
  주어진 노드가 50% 이상의 친구 노드를 가지는 최소 홉수를 찾으시오
  :param g: 그래프
  :param influencer: 타겟이 될 인플루언서 노드
  :param min_percent: 최소 요구해야할 노드의 비율 실제 필요한 노드 수는 전체 노드 수(g.nodes) * 0.5 이상이 되어야한다.
  :return: 최소 hops 로 반환해야한다.
  '''
  real_want_nodes = int(g.number_of_nodes() * (50/100)) # 노드 몇개 이상이여야 하는지 구함.
  arr = [] # 전체 d 담은 배열 => [도착노드, 홉수],[도착노드, 홉수] ~~ 이런식의 배열로 구성.
  arr2 = [] # 홉수만 담은 배열 => [홉수, 홉수, 홉수~~] 로 구성.
  for d in nx.shortest_path_length(g, influencer).items(): # 0노드부터 시작해서 도착하는 노드들과의 홉수를 찾아준다.
    arr.append(list(d))
  for i in range(len(arr)):
    arr2.append(arr[i][1])

  # 노드 몇개 이상이어야 min_percent 이상인지는 위에서 'real_want_nodes'로 구했다.
  # 따라서 'real_want_nodes'가 만약 50이라면 50번째 index로 접근해서 홉수를 가져오면 그것이 최소 홉수가된다.
  # 홉수가 0부터 오름차순으로 정렬되어 있기 때문에 최소 홉수라 할 수 있다.
  HOPS = arr2[real_want_nodes] 
  return HOPS

print(find_min_hops_with_above_50_friends(g, 0, 50)) # g는 Watts-Strogatz그래프 모델
```

<br>

#### 동형 그래프(Isomorphism Graph)

* **물론 nx.is_isomorphic이 존재한다.**
* 알고리즘 :   
  두 그래프가 노드수, 에지수 같은지 확인.   
  G1의 이웃 노드의 수와 G2의 이웃 노드의 수가 동일한 흐름을 보이는지?(역순무시)

```python
def is_isomorphic(G1, G2):
  '''
  G1과 G2가 isomorphic 관계인지를 체크
  :param G1: Directed 그래프
  :param G2: Directed 그래프
  :return: Boolean
  '''
  # 1. 두 그래프가 노드수, 에지수 같은지 확인.
  # 2. G1의 이웃 노드의 수와 G2의 이웃 노드의 수가 동일한 흐름을 보이는지?(역순무시)

  # 1. 먼저 노드수, 에지수 같은지 확인
  if(G1.number_of_nodes() == G2.number_of_nodes() and G1.number_of_edges() == G2.number_of_edges()):
    # 2. G1의 이웃 노드의 수랑 G2의 이웃 노드의 수가 동일한 흐름인지 보기.
    G1NodeList = list(G1.nodes)
    G2NodeList = list(G2.nodes)
    for i in range(G1.number_of_nodes()):
      G1SuccessorsList = list(G1.successors(G1NodeList[i])) # 후임노드 리스트
      G2SuccessorsList = list(G2.successors(G2NodeList[i]))
      G1PredecessorsList = list(G1.predecessors(G1NodeList[i]))
      G2PredecessorsList = list(G2.predecessors(G2NodeList[i])) # 전임노드 리스트
      if(len(G1SuccessorsList)!=len(G2SuccessorsList) or len(G1PredecessorsList)!=len(G2PredecessorsList)):
        # 여기가 G1과 G2의 이웃 노드의 수가 동일한지 확인하는 코드. 동일하지 않다면,
        return False
  else: # 노드수, 에지수가 다르다면
    return False
  return True # 다 통과했으니 True
```

<br>

#### 그래프 밀집도(Graph Density)

* **물론 nx.density이 존재한다.**
* 그래프의 밀집도를 계산한다.

```python
def calculate_density(G):
  '''
  :param G: directed 그래프
  :return: graph density의 값 [0,1]
  '''
  # 무방향이 아닌 방향그래프 이기때문에 올바른 식은 E/V(V-1) 로 볼 수 있다.
  # 물론 무방향그래프의 식은 E/(V(V-1)/2) 마지막에 2까지 나눈 식이다. 그러나 여기선 방향그래프의 식으로 사용해준다.
  E = int(G.number_of_edges())
  V = int(G.number_of_nodes())
  D = E/(V*(V-1))
  return D
```

<br>

#### 오일러 그래프(Euler Graph)

* **물론 nx.is_Eulerian이 존재한다.**

```python
# 오일러 루프를 포함하면, 오일러 그래프이다. (모든 정점의 차수가 짝수)
def is_Eulerian(g):
  '''
  :param g: undirected graph
  :return: Boolean
  '''
  # 모든 정점이 짝수의 차수
  count = 0 # 홀수 차수 카운트
  for i in range(len(g.degree)):
    if(g.degree[i]%2 != 0): # 짝수 차수인지 아닌지 확인
      count += 1
  if(count > 0): # 홀수 차수가 0보다 크다면
    return False
  return True # 홀수 차수가 없으면 여기까지 왔으니, True로 반환
```

<br>

#### 두 노드 사이에 경유하는 노드가 있는지 확인 함수

```python
def has_nonstop_flight(graph, src, dst):
  '''
  두 공항 사이에 non-stop flight가 있는지 체크하는 함수
  :param graph: trips 그래프
  :param src: 출발 공항
  :param dst: 목적 공항
  :return: Boolean
  '''
  # 최단거리 경로 상에서 경유 공항이 있는지 확인.
  short_path = nx.shortest_path(G,src,dst,'distance') # 최단거리 경로 구해주는 메소드 이용.
  if(len(short_path)==2): # 리스트 크기가 2이면 직항이고 그 이상이면 경유한것이다. 따라서 2인지 확인한다.
    return True
  else:
    return False
```

<br>

#### 다익스트라 알고리즘(Dijkstra's Alogrithm)

* 단일점에서의 최단경로One to All Shortest Path 알고리즘 중 가장 많이 사용
* 무방향, 방향 그래프 둘다 적용 가능
* G는 그래프, V(G) = 정점의 집합, S=distance[]가 최소인 정점 집합

```c
// Pseudocode
Dijkstra_shortestPath(G,v)
    S <- {v}; // v자신은 가중치 0일테니 바로 S에 초기화
	for (i <- 0; i∈V(G); i <- i+1) do // 가중치 인접행렬 만드는 for문
        distance[i] <- weight[v][i];
	for (i <- 0; S!=V(G); i <- i+1) do { // 방문하지 않은 노드 있는동안
        u <- S에 속하지 않은 정점 중에서 distance[]가 최소인 정점;
        S <- S U {u};
        for (w <- 0; w∈V(G); w <- w+1) do
            if(w ∉ S) then
                distance[w] <- min(distance[w], distance[u] + wieht[u][w])
    } // 두번째 for문을 통해 최소인 distance로 가중치 인접행렬 만드는 중
end Dijkstra_shortestPath()
```

<br>

#### 원하는 경로를 지나는 노드의 최단경로 거리는?

```python
def shortest_path_length_from_airports(graph, airport_seq):
  '''
  주어진 공항의 시퀀스(sequence)로부터 최단경로의 거리를 계산하시오
  :param graph: trips 그래프
  :param airport_seq: 공항정보 (list형)
  :return: 총 거리(distance) 값(float형)
  '''
  total = float(0)
  for i in range(len(airport_seq)): # 0,1,2,3
    id_1 = usa_airports[usa_airports.name.str.contains(airport_seq[i])]
    id_2 = usa_airports[usa_airports.name.str.contains(airport_seq[i+1])]
    id_1 = id_1.iloc[:,1].values[0] # 공항 id
    id_2 = id_2.iloc[:,1].values[0] # 공항 id
    total += float(graph[id_1][id_2]['distance'])
    if(i==len(airport_seq)-2): # indexOut에러 안뜨게끔 처리 # 길이:4이므로 i==2
      break
  return total

seq = ['Los Angeles International Airport','Hartsfield Jackson Atlanta International Airport','Miami International Airport','Washington Dulles International Airport']
```

<br>

#### 단일 출발지(직항) 최단거리 찾기

* 알고리즘 : 직항 공항을 먼저 전부 찾습니다. 그리고 distance가 제일 크면 가장멀고, 작으면 가장 가까운 곳이기 때문에 이를 이용해서 구합니다.

```python
def get_farthest_or_shortest_airports_from(graph, src):
  '''
  가장 멀거나 가장 가까운 노드 찾는 함수
  :param graph: trips 그래프
  :param src: 출발지
  :return: ( farthest airport code, shortest airport code ) (tuple 형으로 반환)
  '''
  # 1. nonStopList 구하기
  nonStopList = []
  srcList = list(graph[src])
  datas = nx.single_source_shortest_path(graph,src) # src에서 출발해서 도착하는곳 최단경로를 모두 나타냄.
  for data in srcList:
    if(len(datas[data])==2): # 직항인지 판별 EX)['SFO', 'ATL']나오면 SFO->ATL로 직항이 맞음.
      nonStopList += [datas[data]]
  # 2. 가장 가까운, 먼 공항 반환.
  distanceList = []
  farthest = ''
  shortest = ''
  for i in range(len(nonStopList)):
    distanceList.append(graph[nonStopList[i][0]][nonStopList[i][1]]['distance'])

  maxDistance = max(distanceList)
  minDistance = min(distanceList)

  for i in range(len(nonStopList)):
    nonStopDistance = graph[nonStopList[i][0]][nonStopList[i][1]]['distance']
    if(nonStopDistance == maxDistance):
      farthest = nonStopList[i][1]
    elif(nonStopDistance == minDistance):
      shortest = nonStopList[i][1]

  return (farthest, shortest)
```

<br>

#### 플로이드 워셜 알고리즘 활용

* **1. Floyd Washall 알고리즘 활용**

  Floyd Washall 알고리즘은 모든 쌍의 경로의 가중치를 계산하는 데 사용된다. 다음 예제를 사용하여 우리는 LAX 코드를 가진 공항에서 출발하는 모든 공항에 대한 최단경로의 거리값을 계산할 수 있다.

  https://networkx.org/documentation/stable/reference/algorithms/generated/networkx.algorithms.shortest_paths.dense.floyd_warshall.html#networkx.algorithms.shortest_paths.dense.floyd_warshall

  ```python
  all_pairs_shortest_path = nx.floyd_warshall(G, weight='distance')
  
  # 예시 floyd_warshall
  for k, dists in all_pairs_shortest_path.items():
   if k == 'LAX':
    print(dists)
  ```

* **2. 비행기의 속도가 500 km/h 라고 가정할 때 2시간내로 도착하는 공항 코드를 반환하는 함수를 작성하시오**

  ```python
  def get_airports_within_2hours(G, speed):
    '''
    2시간내
    :param G: trips 그래프
    :param speed: 비행기의 속도  km/h
    :return: 공항 코드 (list 형)
    '''
    # 500km/h는 1시간에 500km이기때문에 2시간이내는 1000km이내로 도착하는 공항 코드 찾으면 된다.(이내=이하)
    airportList = [] # return값
    street = speed*2 # 거리
    all_pairs_shortest_path = nx.floyd_warshall(G, weight='distance') # 알고리즘 이용해서 전체노드들 최단거리 가져옴
    nodes = list(G.nodes) # 전체노드
    # 예시 floyd_warshall
    for k, dists in all_pairs_shortest_path.items(): # k가 출발공항, dists가 도착공항(distance포함)
      for node in nodes:
        if k == node: # 출발공항이 도착공항이 되는경우는 빼려고 넣음.
          continue
        if(dists[node] <= street): # 앞서 정의한 거리이하인것 찾기.
          airportList.append(node)
    # 중복노드 제거(출발노드->도착노드가 분명 경유해서 가던지 하면, 도착노드가 겹치는게 존재할 수 있기 때문)
    airportSet = set(airportList)
    airportList = list(airportSet)
    return airportList
  
  flight_speed = 500 # km/h
  get_airports_within_2hours(G, flight_speed)
  ```

  

