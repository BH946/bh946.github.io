---
title:  "[Python]Tree(트리 관련 알고리즘)"
categories : Algorithm
tag : [스패닝트리의 DFS, BFS, MST(Minimal Spanning Tree) - Prim's, Kruskal's(프림, 크루스칼), Tree Traversal, 전위 순회(preorder), 중위 순회(inorder), 후위 순회(postorder), Tree 종류]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



## Tree(트리구조)

* 순환 X, 무방향 그래프
* 루트노드 유일 = root Node, 터미널 노드 = Leaf node
* 루트노드 내차수 0, 나머지 노드들의 내차수 1 (내차수 : indegree)
* 트리의 루트노드 구하는법  
  1)루트노드는 indegree=0인것(정석 방법)  
  2)그림그려서 확인.  
  3)[(v2,v3),(v2,v1),(v4,v5),(v4,v6),,,]이런식이라면, 요소의 앞부분만 나열 v2,v4... 그리고 요소의 뒷부분만  나열한것에 앞부분 나열한 요소가 나오는건 전부 앞부분 나열한것에서 제거 => 마지막남은 노드가 루트노드(알고리즘 짤때 이런식으로 할 것 같음)

<br>

### 1. Tree의 종류

**n-Tree : 최대 n개 가진 자식 트리**

* 터미널 노드(=리프노드)제외한 모든 정점들의 자식이 n개일 때, 이를 **완전 n-트리**라 하는것이다.

**부분 트리(Subtree) : parent와 child노드 하나씩만 있어도 성립!**

**순서 트리(Ordered Tree) : 트리의 유향 그래프에서 같은 레벨에서 어떤 정점의 자식이 많을 경우, 왼쪽부터 오른쪽으로 순서가 되어 있는 트리**

**Forest : 무향, 비순환 그래프이며, one path로 연결**

**결정 트리(Decision Tree) : 시간,공간의 흐름으로 되돌릴 수 없는 결정인것을 트리로 만들어야지, 그게 아니라면 되돌아 가야하므로 결정 트리구조는 맞지 않다.**

**스패닝 트리 (Spanning Tree) : 그래프 G의 부분 그래프 T가 트리이고, T가 G의 모든 정점들을 포함하는 트리구조**

* 상황에 따라 BFS spanning tree, DFS spanning tree 를 사용

**선택 트리(Selection Tree)**

* **승자 트리(Winner Tree)** : 두 자식 중에서 가장 작은 key값인 record(이긴key)를 나타냄
  * 완전 이진 트리이다.
* **패자 트리(Loser Tree)** : 승자트리처럼 작은 key값이 올라가는데, 그 빈자리는 패자가 기록이됨.
  * 루트위에 0번 노드가 추가된 완전 이원 트리이다.

**완전 이진 트리(Complete Binary Tree) : Leaf노드와 그 부모노드제외하고는 자식이 둘이고, Leaf노드는 Tree의 왼쪽부터 채워진다.**

**이진 검색 트리 (Binary Search Tree) : 이진트리이고, 노드 N을 기준해서 왼쪽 서브트리는 N보다 작고, 오른쪽 서브트리는 N보다 크다.**

**이진 트리 (Binary Tree) : 각 노드는 최대 두개의 자식 노드를 가진다.**

**AVL Tree : BF(Balance Factor)의 절대값인 1보다 작거나 같은경우.**

* BF = |왼쪽 서브트리 높이 - 오른쪽 서브트리 높이|

**m원 탐색 트리 (m-way Search Tree) : 각 노드에서 분기가 최대 m개인 탐색 트리**

**B-Tree(Balanced) : 균형 m원 탐색 트리**

**B+Tree**

**Heaps(힙) : 데이터 완전 이진 트리 - 링크드 리스트보단 리스트로 많이 구현**

* Max-heap : Node는 두 child nodes의 값보다 크다.
* Min-heap : Max-heap과 반대 개념.
* 힙 응용 : 허프만 코드(Huffman Codes) 알고리즘

<br>

### 2. Tree Alogrithm

* 스패닝트리의 DFS, BFS
* 스패닝트리의 MST(Minimal Spanning Tree) - Prim's, Kruskal's(프림, 크루스칼 알고리즘)
  * Prim's - cycle 조심, 순서상관 없음(최소 가중치만 잘 찾기), 인접노드
  * Kruskal's - cycle 조심, 순서상관 있음(최소 가중치 순으로), 인접하게X

* Tree Traversal : 전위 순회(preorder), 중위 순회(inorder), 후위 순회(postorder)

<br>

#### DFS(Spanning Tree) : 그래프 입력 -> spanning tree 반환

```python
# Pseudocode
DFS(G : connected graph with vertices v1, v2, ... vn)
T : tree consisting only of the vertex v1 # v1 가진 T 미리 만듬
visit(v1) # 함수 시작

visit(v: vertex of G):
    for each vertex w adjacent to v and not yet in T: # 각 v인접노드 w가 아직방문x인것만
        add vertex w and edge (v,w) to T # w노드와 (v,w)에지를 T에 추가
        visit(w) # 재귀
```

<br>

#### BFS(Spanning Tree) : 그래프 입력 -> spanning tree 반환

```python
# Pseudocode
BFS(G : connected graph with vertices v1, v2, ..., vn)
T : tree consisting only of vertex v1 # v1 가진 T 미리 만듬
L : empty list # 자료구조 큐를 이용해 리스트 유지하게 됨.
put v1 in the list L of unprocessed vertices # 리스트 L에 v1노드 put
while L is not empyt:
    remove the first vertex, v, from L # L에서 v1제거
    for each neighbor w of v: # 제거한 노드의 이웃노드들 접근(이웃노드 : w)
        if w is not in L and not in T then # w가 L에 없고, T에 없다면
        	add w to the end of the list L # L 끝자리에 w 추가
            add w and edge (v,w) to T # T에 w노드, 에지 추가
```

<br>

#### MST(Minimal Spanning Tree)_Prim's Algorithm : 가중치 그래프 -> spanning tree

```python
# Pseudocode
Prim(G: weighted connected undirected graph with n vertices)
T : a minimum_weight edge # T 초기화(최소 가중치 에지하나 선정해서)
for i = 1 to n-2 :
    e = (an edge of minimum weight incident to a vertex in T) and not forming a simple circuit in T if added to T # 인접하게, 순환O로
    T = T with e added
return T (T is a minimum spanning tree of G)
```

<br>

#### MST(Minimal Spanning Tree)_Kruskal's Algorithm : 가중치 그래프 -> spanning tree

```python
# Pseudocode
Kruskal(G: weighted connected undirected graph with n vertices)
T : empty graph # T 초기화(empty로)
for i = 1 to n-1 : # prim's에선 에지한개 이미 초기화 해서 n-2 한것이다.
	e = any edge in G with smallest weight that does not form a simple circuit when added to T # 인접상관X에 순환X로
    T = T with e added
return T (T is a minimum spanning tree of G)
```

<br>

#### Preorder Traversal(전위 순회)

```python
# D -> L -> R
preorder(T):
    if (T != None):
        visit T.data # 현재 데이터 처리
        preorder(T.left) # 왼쪽 서브트리로 이동
        preorder(T.right)
```

<br>

#### Inorder Traversal(중위 순회)

```python
# L -> D -> R
preorder(T):
    if (T != None):
        preorder(T.left) # 왼쪽 서브트리로 이동
        visit T.data # 현재 데이터 처리
        preorder(T.right)
```

<br>

#### Postorder Traversal(후위 순회)

```python
# L -> R -> D
preorder(T):
    if (T != None):
        preorder(T.left) # 왼쪽 서브트리로 이동
        preorder(T.right)
        visit T.data # 현재 데이터 처리
```

<br>

#### 전위, 중위, 후위 순회 예시

```python
class Node:
    def __init__(self, name, left=None, right=None):
        self.name = name
        self.left = left
        self.right = right

def map(): # 트리구조 직접 만들어줬음.
    n1 = Node('H')
    n2 = Node('F')
    n3 = Node('S')
    n4 = Node('U')
    n5 = Node('E')
    n6 = Node('Z')
    n7 = Node('K')
    n8 = Node('N')
    n9 = Node('A')
    n10 = Node('Y')
    n11 = Node('T')
    n1.left = n2
    n2.left = n4
    n4.left = n8
    n2.right = n5
    n5.right = n9
    n9.right = n11
    n1.right = n3
    n3.left = n6
    n3.right = n7
    n7.right = n10
    return n1


def A_course(n): # 전위
    if n != None:
        print(n.name, '-> ', end='')
        A_course(n.left)
        A_course(n.right)

def B_course(n): # 중위
    if n != None:
        B_course(n.left)
        print(n.name, '-> ', end='')
        B_course(n.right)

def C_course(n): # 후위
    if n != None:
        C_course(n.left)
        C_course(n.right)
        print(n.name, '-> ', end='')

start = map()
print('A-코스:\t', end='')
A_course(start)
print('B-코스:\t', end='')
B_course(start)
print('C-코스:\t', end='')
C_course(start)
'''
A-코스:      H -> F -> U -> N -> E -> A -> T -> S -> Z -> K -> Y -> 
B-코스:      N -> U -> F -> E -> A -> T -> H -> Z -> S -> K -> Y -> 
C-코스:      N -> U -> T -> A -> E -> F -> Z -> Y -> K -> S -> H ->
'''
```

