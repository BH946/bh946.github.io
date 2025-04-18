---
title:  "[고급개념] 고급 트리(BST, RBT, Splay, OST, Range, Interval, Sgement)"
categories : Algorithm
tag : [알고리즘]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**고급 자료구조로 볼 수 있는 트리들을 대부분 소개하려고 한다.**

**여기서 언급한 트리구조들을 잘 이해한다면 트리 부분은 충분히 심화까지 익혔다고 생각한다.**

**참고로 트리의 구조, 순회(검색) 방식, 삽입, 삭제를 중점으로 이해하고 넘어가는것을 권장한다.**

* 해당 트리들은 직접 구현해볼 생각이고, 코드들은 "고급 트리 구현" 게시물에서 확인
* **[고급 트리 구현](https://bh946.github.io/algorithmtest/(%EA%B5%AC%ED%98%84)-%EA%B3%A0%EA%B8%89-%ED%8A%B8%EB%A6%AC-%EA%B5%AC%ED%98%84(BST,-RBT,-Splay,-OST,-Range,-Interval,-Sgement)/)**
* **예시 문제**
  * **[이진 검색 트리1(순회)](https://www.acmicpc.net/problem/5639)**
  * **[이진 검색 트리2(순열)](https://www.acmicpc.net/problem/8916)**
  * **[이진 검색 트리3(RBT)](https://www.acmicpc.net/problem/1539)**
  * **[이진 탐색 트리(RBT)](https://www.acmicpc.net/problem/2957)**
  * **[구간 합 구하기(Segment)](https://www.acmicpc.net/problem/2042)**


<br><br>

# BST(Binary Search Tree)

**지금 소개하는 트리중에서 제일 간단한 구조 및 제일 기초가 되는 구조이다!!**

**편향 이진 트리일 경우만 최악 복잡도가 O(N) 이 되고, 나머지 대부분은 O(h) 복잡도이다.**

* h : 트리 높이

<br><br>

## 1. 트리 구조(성질)

**노드 정보**

* key : 원소의 순서를 정하기 위한 자료
* left : pointer to left child (혹은 NULL)
* right : pointer to right child (혹은 NULL)
* p : pointer to a parent node (root는 NULL 을 가짐)

<br>

**트리 성질**

* key[leftSubtree(x)] ≤ key[x] ≤ key[rightSubtree(x)]

<br><br>

## 2. 연산 과정

* **Successor(=후임)**

  * Case1 : x의 right subtree가 있는 경우

    * x의 right subtree에서 가장 작은 노드가 후임노드

       ![image-20230420235949234](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230420235949234.png) 

  <br>

  * Case2 : x의 right subtree가 없는 경우

    * x의 가장 가까운 조상노드(ancessor) 중에서 그 조상 노드의 left child 가 또한 x의 조상 노드인 경우이다.

      ![image-20230421000006495](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230421000006495.png) 

* **검색**
  
  * 루트부터 키값 비교를 통해 원하는 노드를 찾아낸다.
  
* **삽입**
  
  * 삽입 노드를 z라고 가정한다면 검색 로직을 이용해서 z가 들어갈 자리를 찾아내고,
  * z를 해당자리의 부모와 연결하고, 그 부모는 z를 자식으로 연결한다.
  
* **삭제**
  * 임의의 노드 x를 제거하는 과정은 Case3개로 분류된다.
    * Case1 : x가 child가 없는 경우(=단말노드)
      * 바로 제거해도 문제없다.
      
        ![image-20230421000041802](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230421000041802.png)  
    
    <br>
    
    * Case2 : x가 1개의 child를 가지는 경우
    
      * x의 자식 노드를 x의 부모노드의 자식노드로 만들면(연결) 자동으로 x가 제거
    
        ![image-20230421000107028](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230421000107028.png) 
    
    <br>
    
    * Case3 : x가 2개의 child를 가지는 경우
    
      * x를 x의 successor와 위치를 맞바꾸고, case1 or case2 로 이동
    
        ![image-20230421000155393](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230421000155393.png) 

<br><br>

# RBT(Red Black Tree)

**RBT는 어떤 특정한 규칙을 이해하려고 한다기 보다는, 그냥 재구성하는 규칙을 있는 그대로 받아들이는게 정신건강에 이롭다. . .**

**RBT는 `자가 균형 이진 탐색 트리` 라고도 한다.**  
**연산자들의 시간복잡도가 O(log n), 높이도 편향 이진 트리가 생성될 수 없기 때문에 대략 log n 이다.**  

**따라서 실무에서도 많이 사용되는 자료구조이다. (앞에 BST에선 편향 이진 트리 때문에 최악이 N복잡도)**

<br><br>

## 1. 트리 구조 (성질)

**5개의 성질이 존재**

* 노드는 Red or Black 색
* 모든 단말노드(NULL)는 Black 색
* Red 노드의 자식들은 모두 Black 색
* 어떤  노드로부터  이  노드의  모든  단말노드  까지의 경로는  모두  같은  개수의  Black 노드를  가진다.
* Root 노드는 Black 색

![image-20230421000248503](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230421000248503.png) 

<br><br>

## 2. 연산 과정

* **회전(Rotation)**

  * 자가 균형 때문에 회전을 해야하는 경우가 있는데, LeftRotation과 RightRotation이 있다.

  * 그림을 보면 충분히 이해가 되며 필자는 도르래를 생각하니까 이해하기 쉬웠다.

    <img src="/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230420201922580.png" alt="image-20230420201922580"  /> 

* **검색**
  * BST의 방식과 동일하며, 루트부터 키값 비교를 통해 원하는 노드를 찾아낸다.
  
* **삽입**
  * 삽입 노드 z의 색을 Red로, 두 자식은 리프노드로 연결하고 삽입한다.
  
  * 삽입 과정 또한 일반적인 BST와 유사한데 Red-Red 문제만 따로 해결해준다.
    
    * Red-Red 문제는 연속으로 노드가 Red, Red로 구성되는 경우를 의미한다.
    
    * `Red-Red 문제` 라고해도 z가 루트로 삽입된 경우는 바로 BLACK으로 색을 바꿔서 간단히 해결
    
    * Red-Red 문제는 `총 3가지 Case`가 존재하며 반대의 경우도 고려하면 ` 6가지`이다.
      * z의 부모가 할아버지 왼쪽 자식인 경우 - case1,2,3
      * z의 부모가 할아버지 오른쪽 자식인 경우 - case4,5,6
        * 대칭적으로 다를 뿐 구조는 동일(4,5,6 설명은 생략)
    
    * `Case의 흐름`
      
      * case 1 -> case 2 or case 3
        * case 1 : 삼촌이 RED인 경우
        
        * while 루프를 통하여 이중의 red 색을 가지는 노드를 계속 루트노드까지 올림
        
          * **현재상태 : 할아버지 BLACK, z부모 RED, z삼촌 RED**
          * **따라서 할아버지를 RED, Z부모 BLACK, z삼촌 BLACK으로 변환**
          * **이후 할아버지를 새로운 z로 선언해서 다시 while문 반복**
          
          ![image-20230421000342537](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230421000342537.png) 
      
      <br>
      
      * case 2 -> case 3	
      
        * case 2 : 삼촌이 BLACK, 부모의 오른쪽 자식인 경우
      
        * **leftrotation 활용**
      
          ![image-20230421000406101](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230421000406101.png) 
      
      <br>
      
      * case 3 -> 끝
      
        * case 3 : 삼촌이 BLACK, 부모의 왼쪽 자식인 경우
      
        * **rightrotation 활용**
      
        * **부모 RED->BLACK, 할아버지 BLACK->RED로 변경**
        
          ![image-20230421000418173](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230421000418173.png) 
    
    <br>
    
    * `Red-Red 문제 탈출 시점`
      * 루트인 경우 or 삽입 노드 z의 부모가 Red가 아닐때 인 경우에 탈출한다.
      * 부모가 Red가 아니면 Red-Red 문제가 아니니까!
  
* **삭제**
  
  * BST와 유사하게 노드x를 제거하는데 BST동작과의 차이점은 `successor 노드가 BLACK` 일때 복잡한 과정을 추가로 거쳐야 한다는 점이다. 이후 부터는 추가 과정을 설명하겠다.
    * 참고로 `successor 노드가 RED` 일때는 바로 BLACK으로 바꾸면 되기 때문에 문제없다.
    
    * `successor 노드가 BLACK` 일때는 이중 BLACK 때문에 복잡한 과정을 추가로 거치는 것이다.
    
    * Case는 `총 4가지`로 분류되고, 대칭적인 구조도 생각하면 `8가지`로 분류된다.
    
      * 대칭 기준 
        * x가 부모의 왼쪽 자식일때 -> case 1,2,3,4
        * x가 부모의 오른쪽 자식일때 -> case 5,6,7,8
      * 참고 : w는 x의 형제노드를 의미한다.
      * case 1 : w는 RED
      * case 2 : w는 BLACK && W의 자식들도 BLACK
      * case 3 : w는 BLACK && w의 왼쪽자식이 RED
      * case 4 : w는 BLACK && w의 오른쪽자식이 RED
      * case 5 : w는 RED
      * case 6 : w는 BLACK && W의 자식들도 BLACK
      * case 7 : w는 BLACK && w의 오른쪽자식이 RED
      * case 8 : w는 BLACK && w의 왼쪽자식이 RED
    
    * `Case의 흐름`
      * case 1
        * case 1 -> 2,3,4
        
        * 동작 : w를 BLACK, x의 부모를 red, LeftRotate(x의 부모), w를 x의 형제 노드로
        
          ![image-20230421000523374](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230421000523374.png) 
      
      <br>
      
      * case 2
      
        * case 2 -> 1,2,3,4
      
        * case 2 -> 2 -> 2 -> 2... 가 가능(계속 루트로 상승해가는것)
      
        * 동작 : w를 RED, x를 x의 부모로 업데이트
      
          ![image-20230421000546109](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230421000546109.png) 
      
      <br>
      
      * case 3 -> 4
      
        * 동작 : w를 RED, w의 왼쪽 자식을 BLACK, RightRotate(w), w를 x의 부모의 오른쪽 자식으로 업데이트
      
          ![image-20230421000740376](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230421000740376.png) 
      
      <br>
      
      * case 4 -> 종료
      
        * 동작 : w를 x의 부모의 색으로, x의 부모의 색은 BLACK, w의 오른쪽 자식의 색은 BLACK, LeftRotate(x의 부모), 반복문 탈출(종료)
      
          ![image-20230421000809162](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230421000809162.png) 

<br><br>

# Splay Tree

**Splay Tree는 기본적으로 균형트리 + BST 이다.**  

**차이점은 RBT와 같은 노드의 색같이 추가 정보가 필요로 하지 않다는 점.**  
**다만, 매 연산마다 트리 구조를 재구성 하는 방식으로 진행**

**이런 특징 때문에 Splay Tree를 Self-Reconstructing, Self-Adjusting, 혹은 Self-Organizing (자체 조정, 자체 재구성) Tree라 부른다.**

<br><br>

## 1. 트리 구조 (성질)

**BST와 유사하다.**

<br><br>

## 2. 연산 과정

**기본 동작 : 특정 노드를 회전을 통해서 루트로 올린다.**  

**Splay 작업은 연산 수행후 매번 노드 적용**

* **Search : 검색 조차 x찾고, key x 노드를 splay**
* **Insert : key x를 insert하고 해당 노드에 splay**
* **delete : key x 노드를 delete하고, 해당 노드의 부모를 splay**

<br>

**아래 case1~3개를 지켜서 올리면 더욱 안정적인 트리구조를 형성 가능.**

* **case1 - Zig step**

  * **Root로 보낼 노드를 x라고 가정하겠다(다른 case도 마찬가지)**

  * x의 부모 노드가 root라면 한번의 회전으로 x를 루트노드로 만들 수 있다.

    ![image-20230421001933936](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230421001933936.png) 

<br>

* **case2 - Zig-Zag step**

  * x의 부모노드가 루트가 아니고 x와 x의 부모노드가 서로 다른 방향의 자식인 경우

  * 두 번의 서로 다른 방향의 회전을 통하여 x 를 트리의 위쪽으로 옮길수 있다.

    ![image-20230421002019690](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230421002019690.png) 


<br>

* **case3 - Zig-Zig step**

  * x와 x의 부모노드 모두가 같은 방향의 자식인 경우 

  * 세 노드를 같은 방향으로 동시에 회전(부모부터 자식순으로 회전시키면 됨)

    ![image-20230421002041988](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, OST, Interval, Range, Sgement, Splay)/image-20230421002041988.png) 

<br><br>

# OST(Order Statisitc Tree)

**k-번째로 작은 데이터 같이 순위 같은 것들을 O(n)이 아닌 O(logn) 시간에 가능하게 하기 위한 목적을 가진 트리이다.**

**해당 트리는 균형트리 중에서 RBT를 기반으로 만든 트리이다.  
RBT기반의 노드에 size 가 추가된 개념일 뿐이다.**

**즉, x->size = x->left->size + x->right->size + 1**

<br><br>

## 1. 트리 구조 (성질)

**트리의 구조로 노드는 RBT 노드에 size를 추가한 구조를 형성한다.**

![image-20230421003146386](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, Splay, OST, Range, Interval, Sgement)/image-20230421003146386.png) 

<br><br>

## 2. 연산 과정

* **OSselect(x,k)** -> 순위 k의 데이터를 검색 (데이터)
  * 이 함수는 OSselect(T.root, k)로 처음 호출된다.
  * 즉, 루트부터 검색한다는 의미!!
* **OSrank(T,x)** -> 데이터의 순위를 계산 (순위)

* **insert, delete**는 RBT처럼 하되 size를 추가로 계산해줘야 한다.
  * insert는 지나간 모든 노드의 size값 1씩 증가
  * delete는 삭제노드의 부모부터 루트까지 size값 1감소
  * 그리고 각 연산의 마지막에 트리구조를 바꾸는 회전을 진행할때는 회전 연산에따라 size값 변경

<br><br>

# Range Tree

**`Range Tree` 도 AVL이나 RBT 같은 균형트리를 근간으로 만들어진 트리이다.**

**`Range query` 란 어떤 구간에 있는 원소에 대해 계산하는 문제(종류 2가지)**

- range reporting : 계산 또는 reporting
- range counting : 개수 계산 => 이거 때문에 node에 단말노드 개수 기록
- 이 Range query를 다루는 자료구조가?? => `range tree`
- **전부검색할필요 없이 내부노드를 보고 바로 구간에 속하는지 판단이 가능하니까 빠르다.**

<br><br>

## 1. 트리 구조 (성질)

**1차원 Range Tree는 RBT와 같은 균형트리를 근간으로 다음과 같이 만듬**

* Range Tree의 단말노드는 n개의 실수 {a1, a2, …, an} 를 원소로 한다.
* Range Tree의 내부노드는 (n-1)개의 실수 {a1, a2, …, an-1} 를 원소로 한다.
  * 각 내부 노드는 왼쪽 서브트리에 있는 단말노드 중 가장 값이 큰 것을 저장함.

![image-20230421005512264](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, Splay, OST, Range, Interval, Sgement)/image-20230421005512264.png) 

<br><br>

## 2. 연산 과정

* **range reporting query**

  * 구간 range[l, r] 의 l, r에 대해 각각 search해서 최종 도달한 단말노드를 t<sub>l</sub>, t<sub>r</sub>로 둔다.

  * 루트부터 t<sub>l</sub>, t<sub>r</sub>로 갈라지는 노드를 V<sub>split</sub> 라고하며, 이를 구한다.

  * 이후 부터는 그림을 참고

    ![image-20230421011151169](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, Splay, OST, Range, Interval, Sgement)/image-20230421011151169.png) 

    ![image-20230421011344883](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, Splay, OST, Range, Interval, Sgement)/image-20230421011344883.png) 

<br>

* **range counting query**

  * Range Tree의 각 내부노드에 그 노드를 루트로 하는 subtree의 단말노드 개수를 저장하면 됨

  * 실제 동작 과정 : reporting을 먼저해서 노드들을 구하고, size들을 전부 합하면 원하는 값을 출력

    ![image-20230421010150998](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, Splay, OST, Range, Interval, Sgement)/image-20230421010150998.png) 

<br>

* **insert, delete는 pass 하겠음**

<br><br>

# Interval Tree

**Interval Tree는 기본적으로 RBT와 같은 균형트리를 기반으로 만들어 진다.**

**IntervalSearch(T,i) : 폐구간 i 와 서로 겹치는 모든 구간을 계산한다.(logn 복잡도)  
필자는 이것이 Interval Tree를 사용하는 목적이라고 생각한다.**

<br><br>

## 1. 트리 구조 (성질)

**아래 그림은 10개의 초기 구간으로 구성된 Interval Tree이다.**  
**노드의 구조를 보면 RBT와 같은 구조에 추가로 interval, max 정보를 가지고 있다.**

**트리를 구성할때 insert방식은 기존 RBT처럼 key값으로 하므로 구간의 low값을 key값으로 설정!!**

![image-20230421012703873](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, Splay, OST, Range, Interval, Sgement)/image-20230421012703873.png) 

<br><br>

## 2. 연산 과정

* **IntervalInsert(), IntervalDelete() 연산은 패스하고, Search를 설명하겠다.**

  * 기존 RBT와 유사하기 때문에 insert, delete는 생략
  * **max값만 Rotate연산 때랑 insert연산 때 추가로 구현해주면 됨**

* **IntervalSearch : 구간 i 와 겹치는 어떤 구간을 찾는 연산**

  * 겹치는 구간을 찾아보려는 "입력 구간"과 "노드의 구간" 을 `a,b,c,d` 관계로 왼쪽 or 오른쪽 서브트리로 내려갈지 결정한다.

  * 이후 max값을 이용해서 해당 서브 트리로 내려갈지 말지를 최종 결정한다.

  * 출력할때는 겹치는 구간 하나라도 있으면 그걸로 바로 반환하고 끝마치자(이건 자유롭게)

    ![image-20230421014006981](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, Splay, OST, Range, Interval, Sgement)/image-20230421014006981.png) 

<br><br>

# Sgement Tree

**여러개의 데이터가 연속으로 있을때 배열에서는 그 구간의 합을 구할때 복잡도가 N이 걸리는데,**   
**이걸 세그먼트 트리를 이용해서 logN으로 개선시키려는 목적**

<br><br>

## 1. 트리 구조 (성질)

**세그먼트 트리에서 단말노드와 리프노드의 의미**

* 리프 노드: 배열의 연속된 값 그 자체
* 내부 노드: 왼쪽 자식과 오른쪽 자식의 합을 저장함

<br>

![image-20230421014532168](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, Splay, OST, Range, Interval, Sgement)/image-20230421014532168.png) 

* **참고 : 그림의 루트노드 값 0~9의 의미는 0부터 9까지의 합을 의미한다.**

<br><br>

## 2. 연산 과정

**node가 담당하고 있는 구간이 [start,end]  
합을 구해야 하는 구간이 [left,right]**

**다음과 같이 4가지 경우로 나누어질 수 있다**

1. [left,right]와 [start,end]가 겹치지 않는 경우
  * 더 이상 탐색할 필요 없음. 0을 리턴
2. [left,right]가 [start,end]를 완전히 포함하는 경우
  * 더 이상 탐색할 필요 없음. 그 노드의 값을 리턴
3. [start,end]가 [left,right]를 완전히 포함하는 경우
  * 자식 트리에서 탐색을 계속해 함
4. [left,right]와 [start,end]가 겹쳐져 있는 경우 (1,2,3 제외한 나머지 경우)
  * 자식 트리에서 탐색을 계속해 함

<br>

![image-20230421014843721](/images/2023-04-01-(고급개념) 고급 트리(BST, RBT, Splay, OST, Range, Interval, Sgement)/image-20230421014843721.png) 

<br><br>
