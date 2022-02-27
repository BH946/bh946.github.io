---
title:  "[Python]Linked Strucutures(연결리스트 구조)"
categories : Algorithm
tag : [링크드 리스트 구조(Linked List), Traversing(순회), Searching(검색), Inserting(삽입), Removing(삭제), Polynomials(다항식 계산), Doubly Linked List(이중), Circular Linked List(순환 or 원형), Multi Linked List(다중 or 멀티)]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---

## Linked Strucutures

* 링크드 리스트 구조(Linked List)
* Traversing(순회), Searching(검색), Inserting(삽입), Removing(삭제), Polynomials(다항식 계산)
* Doubly Linked List(이중), Circular Linked List(순환 or 원형), Multi Linked List(다중 or 멀티)



### 1. The Liked List

* 객체 생성

```python
class ListNode:
    def __init__(self,data):
        self.data = data
        self.next = None

head = ListNode(2)
head.next = ListNode(13)
head.next.next = ListNode(18)
head.next.next.next = ListNode(36)
head.next.next.next.next = ListNode(52)
```



#### Traversing(순회)

```python
def traversal(head):
    curNode = head
    while curNode is not None:
        print (curNode.data)
        curNode = curNode.next
traversal(head)
'''
2
13
18
36
52
'''
```



#### Searching(찾기)

* 정렬안된 링크드 리스트 구조

```python
def unorderedSearch(head, target):
    curNode = head
    while curNode is not None and curNode.data != target:
        curNode = curNode.next
    return curNode is not None # None이 아니면 True반환, 반대면 False반환

answer = unorderedSearch(head,36)
print(answer) # True 출력
```



* 정렬된 링크드 리스트 구조

```python
# 정렬안한 리스트에서 탐색이랑 비슷하나 loop조기 종료 가능(장점)
def sortedSearch( head, target):
    curNode = head
    while curNode is not None and curNode.data < target: # 차이점
        if curNode.data == target:
            return True
        else:
            curNode = curNode.next
    return False
```



#### Inserting(삽입)

* head로 넣게 되는것

```python
# Prepending a node to the linked list(값 넣기)
item = 1
newNode = ListNode(item)
newNode.next = head # 서로 값 바꾸기
head = newNode # 서로 값 바꾸기
print(head.data) # 방금 넣은 데이터 1 출력
```



* tail로 넣게 되는것

```python
# Using a tail Node(Appending)
tail = head.next.next.next.next # 마지막 데이터 임의로 접근해서 tail로 만든것이다.
newNode = ListNode(21) # 추가할게 21이다.
tail.next = newNode  # 참조를 먼저해서 21을 추가해주고
tail = newNode # tail이 21로 바뀐다.
print(tail.data) # 방금 넣은 21 출력
```



* head, tail 둘다 None일 경우(데이터가 없는 경우겠죠)

```python
# Using a tail Node(Appending) head, tail이 None일 경우
head = None 
tail = None
newNode = ListNode(21)
if head is None:
    head = newNode
else:
    tail.next = newNode # head가 None이 아니라면 바로 마지막을 newNode로 바꿔준다.
tail = newNode
```



* 정렬된 링크드 리스트구조에 INSERT하는법

```python
# inserting nodes(정렬된) + 마지막에 넣는 경우와 아무 것도 없는 경우
target = 24
newNode = ListNode(target)
prevNode = None
curNode = head
while curNode is not None and curNode.data < target: # target보다 크면 탈출(정렬 특징)
    prevNode = curNode
    curNode = curNode.next
newNode.next = curNode
if curNode is head:
    head = newNode
else:
    prevNode.next = newNode 
    # curNode = newNode라고 생각하고 해버리면 바로 앞
    # newNode를 curNode에 참조(next)하였기 때문에 전에 입력받은 
    # curNode(target보다 큰 값)의 값만 남는다. 따라서 연결이 되어 있지 않다.
```



#### Removing(삭제)

* head만 생각했을때

```python
# Removing Nodes(제거하기)
preNode = None # 처음에는 이전 노드가 없기 때문
curNode = head
target = 13
while curNode is not None and curNode.data != target:
    preNode = curNode
    curNode = curNode.next
if curNode is not None:
    if curNode is head: # 처음에 값 있을 때
        head = curNode.next
    else: # 다른경우에 값 있을 때
        preNode.next = curNode.next # 값 없애기
```



* head, tail 둘다 고려할때

```python
# Using a tail Node(Removing)
# tail과 head가 독립적으로 이용된다. 마지막(tail)이 현재의 값으로 제거 될 때 tail도 한개 줄어든다.
tail = head.next.next.next.next # head는 위에 이미 초기화 되어있고, tail도 초기화 되었다 가정.
preNode = None # 처음에는 이전 노드가 없기 때문
curNode = head
target = 13 # 삭제할 데이터
while curNode is not None and curNode.data != target:
    preNode = curNode
    curNode = curNode.next
if curNode is not None:
    if curNode is head: # 처음일 때
        head = curNode.next
    else: # 다른 경우에 값 있을 때
        preNode.next = curNode.next # 값 없애기
    if curNode is tail:
        tail = preNode # tail 바꿔주기
```





### 2. Polynomials(다항식 계산)

```python
# Polynomials(다항식) with Linked 구조
# Term(항), Coefficient(계수), Degree(차수)
# head:차수, tail:계수
class PolyTermNode(object): # 항
    def __init__(self, degree, coefficient):
        self.degree = degree
        self.coefficient = coefficient
        self.next = None
```

```python

class Polynomial: # 다항식
    def __init__(self, degree = None, coefficient=None): # None으로 초기화 해둠
        if degree is None: # coef가 None이여야 None 아닌가?
            self.polyHead = None
        else:
            self.polyHead = PolyTermNode(degree,coefficient) # 항만들기
        self.polyTail = self.polyHead

    def appendTerm(self, degree, coefficient): # 맨 뒤에 추가ok
        if coefficient != 0.0: # 계수 0 아닐때!!
            newTerm = PolyTermNode(degree, coefficient)
            if self.polyHead is None:
                self.polyHead = newTerm
            else:
                self.polyTail.next = newTerm # head.next도 가능?
            self.polyTail = newTerm

    def degree(self): # 최고 높은 차수의 항만 선택해서 차수값을 반환하면 됨
        if self.polyHead is None:
            return -1 # -1은 무엇??
        else:
            return self.polyHead.degree # ok

    def getitem(self, degree): # 원하는 차수 구하기
        #assert
        curNode = self.polyHead
        while curNode is not None and curNode.degree >= degree:
            prevNode = curNode
            curNode = curNode.next
        if prevNode is None or prevNode.degree != degree:
            return 0.0
        else:
            return prevNode.degree

    def evaluate(self, scalar): # 계산하기 ok
        #assert
        result = 0.0 # 합은 0으로 초기화, 곱은 1로 초기화를 많이 함.
        curNode = self.polyHead
        while curNode is not None:
            result += curNode.coefficient * (scalar ** curNode.degree)
            curNode = curNode.next
        return result
    
    def multiply(self, rhsPoly): # 곱하기
        totalPoly = Polynomial() # 전체 합산 할 다항식 만들기.
        nodeA = self.polyHead # poly1다항식의 head노드
        while nodeA is not None: # outer loop
            newPoly = Polynomial() # 새로운 다항식 만들기
            nodeB = rhsPoly.polyHead # poly2다항식의 head노드
            while nodeB is not None: # inner loop
                newDegree = nodeA.degree + nodeB.degree
                newCoeff = nodeA.coefficient * nodeB.coefficient
                newPoly.appendTerm(newDegree, newCoeff)
                nodeB.next
            # 전체 식에 합산
            # totalPoly == None if totalPoly.add(newPoly) else totalPoly = newPoly
            if(totalPoly != None):
                totalPoly.add(newPoly)
            else:
                totalPoly = newPoly
            nodeA.next
        return totalPoly

    def add(self, rhsPoly): # 더하기
        newPoly = Polynomial()
        nodeA = self.polyHead # 수정(하나의 노드를!! 초기화)
        nodeB = rhsPoly.polyHead # 수정
        
        while nodeA is not None and nodeB is not None:
            if nodeA.degree > nodeB.degree :
                degree = nodeA.degree
                value = nodeA.coefficient
                nodeA = nodeA.next
            elif nodeA.degree < nodeB.degree :
                degree = nodeB.degree
                value = nodeB.coefficient
                nodeB = nodeB.next
            else :
                degree = nodeA.degree
                value = nodeA.coefficient + nodeB.coefficient
                nodeA = nodeA.next
                nodeB = nodeB.next
            newPoly.appendTerm(degree, value)

        while nodeA is not None:
            self.appendTerm(nodeA.degree, nodeA.coefficient)
            newPoly = nodeA.next

        while nodeB is not None:
            self.appendTerm(nodeB.degree, nodeB.coefficient)
            newPoly = nodeB.next
        return newPoly

    def substraction(self, rhsPoly): # 빼기
        newPoly = Polynomial()
        nodeA = self.polyHead
        nodeB = rhsPoly.polyHead

        while nodeA is not None and nodeB is not None:
            if nodeA.degree > nodeB.degree: # nodeA 차수 더크면 그대로 내려옴(0빼서)
                degree = nodeA.degree
                value = nodeA.coefficient
                nodeA = nodeA.next
            elif nodeA.degree < nodeB.degree : # nodeA 차수 작으면 계수에 (-)붙어서 내려옴
                degree = nodeB.degree
                value = nodeB.coefficient * (-1)
                nodeB = nodeB.next
            else: # 차수 같을시
                if(nodeA.coefficient != nodeB.coefficient): # A-B가 0이 아닌경우
                    degree = nodeA.degree
                    value = nodeA.coefficient - nodeB.coefficient
                elif(nodeA.coefficient == 0): # A항 없을시 
                    degree = nodeB.degree
                    value = nodeB.coefficient * (-1)
                nodeB = nodeB.next
                nodeA = nodeA.next
            newPoly.appendTerm(degree,value)

        while nodeA is not None:
            self.appendTerm(nodeA.degree, nodeA.coefficient)
            newPoly = nodeA.next

        while nodeB is not None:
            self.appendTerm(nodeB.degree, nodeB.coefficient*(-1))
            newPoly = nodeB.next
        return newPoly

    # def multiply(self, rhsPoly): # 곱하기
    #     nodeA = self.polyHead
    #     totalPoly = Polynomial()
    #     while nodeA is not None:
    #         tempPoly = rhsPoly.termMultiply(nodeA) # 함수곱
    #         totalPoly = totalPoly.add(tempPoly)
    #         nodeA = nodeA.next
    #     return totalPoly

    # def termMultiply(self, termNode):
    #     newPoly = Polynomial() # 맞나?
    #     curr = termNode.next
    #     while curr is not None:
    #         newDegree = curr.degree + termNode.degree
    #         newCoeff = curr.coefficient*termNode.coefficient
    #         newPoly.appendTerm(newDegree, newCoeff)
    #         curr = curr.next # 맞나?
    #     return newPoly  
    
```



#### 계산

```python
# 자유롭게 숫자 기입할것.
# evaluate 계산 @@!#@#@#(항 뿐만아니라 다항식도 계산!!)
print(Polynomial(2,5).evaluate(5)) # 계산@@@@@!!!!!

# append!@!@!@!!!
Poly1 = Polynomial(2,5)
Poly1.appendTerm(1,3)
print(Poly1.polyTail.coefficient) 

# degree() 최고 차항구하는 것.(head추출 하면 됨)
Poly1 = Polynomial(5,5)
Poly1.appendTerm(3,3)
print(Poly1.degree())

# getitem !!@!@ prevNode로 수정 했다~!
Poly1 = Polynomial(2,5)
Poly1.appendTerm(1,3)
print(Poly1.getitem(1))


# Poly1에 5x^2 + 3x^1 을 만들고 Poly2에도 똑같이 만들어서 더합니다.
Poly1 = Polynomial(2,5)
Poly1.appendTerm(1,3)
Poly2 = Polynomial(2,4)
Poly2.appendTerm(1,2)
# 다항식을 더해서 newPoly에 저장합니다.
newpoly = Poly1.add(Poly2)
psum = newpoly.evaluate(1) # 더한값에 x=1을 해서 계산해줍니다.
print(psum) # 예상값 : 10x^2+6x^1 (x=1이면) = 10+6 = 16
```



### 3. Doubly Linked List(이중 연결리스트)

* next, prev 둘다 사용하는것이 이중 연결 리스트의 특징이다.

```python
# 이중 연결 리스트
class DListNode:
    def __init__(self, data):
        self.data = data
        self.next = None
        self.prev = None

class DoubleLinkedList:
    def __init__(self):
        self.head = None
        self.tail = None
        self.probe = None
    # tail 이용한 순회
    def revTraversal(self):
        curNode = self.tail
        while curNode is not None:
            print(curNode.data)
            curNode = curNode.prev
    # Searching 간단 -생략
    def revSearching(self, target):
        if self.head is None:
            return False
        elif self.probe is None: # probe 초기화 시켜주기
            probe = self.head
        if target < probe.data:
            while probe is not None and target <= probe.data:
                if target == probe.data:
                    return True
                else:
                    probe = probe.prev
        else:
            while probe is not None and target >= probe.data:
                if target == probe.data:
                    return True
                else:
                    probe = probe.next
        return False
    # add
    def revAdding(self, value):
        newnode = DListNode(value)
        if self.head is None:
            self.head = newnode
            self.tail = self.head
        elif value < self.head.data:
            newnode.next = self.head
            self.head.prev = newnode
            self.head = newnode
        elif value > self.tail.data:
            newnode.prev = self.tail
            self.tail.next = newnode
            self.tail = newnode
        else:
            node = self.head
            while node is not None and node.data < value:
                node = node.next
            newnode.next = node
            newnode.prev = node.prev
            node.prev.next = newnode
            node.prev = newnode
        return newnode.data # 리턴은 자유~!
    # remove -> 생략

DList = DoubleLinkedList()
DList.revAdding(5)
DList.revAdding(3)
DList.revAdding(2)
DList.revTraversal() # 5,3,2
print(DList.revSearching(2)) # True
```



### 4. Circular Linked List(원형 연결리스트)

* 단순, 이중 연결리스트에서 모두 구현 가능하다.
* 노드간의 연결의 끝부분들이 원형처럼 각 반대쪽의 끝부분과 연결이 되어있는 형태이다.

```python
# 원형 연결 리스트
class CListNode:
    def __init__(self, data):
        self.data = data
        self.next = None

class CircleLinkedList:
    def __init__(self):
        self.head = None
        self.listRef = None
    # 순회
    def traverse(self):
        curNode = self.listRef
        done = self.listRef is None
        while not done:
            curNode = curNode.next
            print(curNode.data)
            done = curNode is self.listRef    
    # 검색
    def search(self, target):
        curNode = self.listRef
        done = self.listRef is None
        while not done:
            curNode = curNode.next
            if curNode.data == target:
                return True
            else:
                done = curNode is self.listRef or curNode.data > target
        return False            
    # 추가
    def Adding(self, value):
        newNode = CListNode(value)
        if self.listRef is None: # empty list
            self.listRef = newNode
            newNode.next = newNode
        elif value < self.listRef.next.data: # insert in front
            newNode.next = self.listRef.next
            self.listRef.next = newNode
        elif value > self.listRef.next.data: # insert in back
            newNode.next = self.listRef.next
            self.listRef.next = newNode
            self.listRef = newNode
        else: # insert in middle
            predNode = None
            curNode = self.listRef
            done = self.listRef is None
            while not done:
                predNode = curNode
                curNode = curNode.next
                done = curNode is self.listRef or curNode.data > value
            newNode.next = curNode
            predNode.next = newNode
    # remove -> 생략

CList = CircleLinkedList()
CList.Adding(5)
CList.Adding(4)
CList.Adding(30)
CList.traverse() # 4,,5,30
print(CList.search(1)) # False
```



### 5. Multi Linked List(멀티 연결리스트)

* 예를들어 next로 노드끼리 연결하는데, 다음 노드 뿐만아니라 다른 노드에도 연결하는 형태이다.
  * EX) SparseMatrix(희소 매트릭스) : 행, 열이 필요하기 때문에 이를 다중 연결 리스트 만으로도 구현이 가능한 것이다.(MLL)
    * 그러나 만약 단일 연결 리스트로만 구현하고 싶다면(SLL) 리스트 - 연결리스트 형태로 행과 열을 구분해서 구현이 가능하다는점을 참고하자.
* Chains(복수의 키를 생성하기 위해), Chain은 목적에 따라 다양하게 생성할 수 있음
* 우리가 기본으로 알던건 SLL(Singly Linked List)인것이고, 이것은 MLL(Multi Linked List)이다.
  * SLL : 좌, 우 연결만 생각
  * MLL : 상, 하, 좌, 우 연결만 생각

```python
# 희소 매트릭스에는 연결리스트 구조로 해결하는것이 좋다.
# SLL이나 MLL 방식 둘다 구현 가능하다.
# 만들다가 말았던 상태라, 아래 코드는 절대 구동이 되는건 아니고 궁금하다면 인터넷 서핑을 이용해 수정해보자.

# 희소 매트릭스 MLL(멀티 연결 리스트)
class MatrixMListNode:
    def __init__(self, row, col, value):
        self.row = row
        self.col = col
        self.value = value
        # self.curRow = 0
        # self.curCol = 0
        self.nextCol = None
        self.nextRow = None

    def findNextElement(self):
        i = self.curRow
        while i < len(self.rowArray) and self.rowArray[i] is None:
            i += 1
        self.curRow = i
        if i < len(self.rowArray):
            self.curNode = self.rowArray[i]
        else:
            self.curNode = None

        
# 희소 매트릭스 SLL(단일) -> 멀티 연결리스트가 아니라 단일 연결리스트임
class SparseMatrixIterator:
    def __init__(self, rowArray):
        self.rowArray = rowArray
        self.curRow = 0
        self.curNode = None
        self.findNextElement()
    def __iter__(self):
        return self
    def next(self):
        if self.curNode is None:
            raise StopIteration
        else:
            value = self.curNode.value
            self.curNode = self.curNode.next
            if self.curNode is None:
                self.findNextElement()
            return value
    def findNextElement(self):
        i = self.curRow
        while i < len(self.rowArray) and self.rowArray[i] is None:
            i += 1
        self.curRow = i
        if i < len(self.rowArray):
            self.curNode = self.rowArray[i]
        else:
            self.curNode = None

```





