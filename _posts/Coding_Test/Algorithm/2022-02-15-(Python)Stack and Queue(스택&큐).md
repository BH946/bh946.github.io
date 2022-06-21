---
title:  "[Python]Stack & Queue(스택&큐) => 백트래킹 VS 브루트포스(추후에 정리)"
categories : [Algorithm, Revise]
tag : [Stack(스택, LIFO), List - based Stack, 리스트 스택(ADT), Linkde List - based Stack, 연결리스트 스택(ADT), Balanced delimiters, 백트래킹 VS 브루트포스, Queue(큐, FIFO), List-based Queue, 리스트 큐(ADT), Circular Queue(원형 큐), Priority Queue(우선순위 큐) - Unbounded, Linkded List-based Queue, 연결리스트 큐(ADT)]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---

## Stack(스택)

* LIFO (Last in, first out)



### 1. List - based Stack => 리스트 스택(ADT)

```python
class Stack:
    def __init__(self, item):
        self.theItems = [item] # 리스트 초기화
        
    def isEmpty(self):
        return len(self.theItems) == 0 # return len(self) == 0
    
    def len(self):
        return len(self.theItems)
    
    def peek(self):
        return self.theItems[-1] # 파이썬 -1은 맨뒤 보여줌
    
    def pop(self):
        return self.theItems.pop() # 파이썬 내장함수pop
    
    def push(self, item):
        self.theItems.append(item)

a = Stack(5)
a.push(2)
a.push(4)
print(a.peek()) # 4
print(a.pop()) # 4
print(a.isEmpty()) # False
```



### 2. Linkde List - based Stack => 연결리스트 스택(ADT)

```python
class StackNode:
    def __init__(self, item, link):
        self.item = item
        self.next = link

class Stack:
    def __init__(self):
        self.top = None
        self.size = 0
    
    def isEmpty(self):
        return self.top is None

    def len(self):
        return self.size
    
    def peek(self):
        return self.top.item
    
    def pop(self):
        node = self.top
        self.top = self.top.next
        self.size -= 1
        return node.item
    
    def push(self, item):
        self.top = StackNode(item,self.top)
        self.size += 1

stack = Stack()
stack.push(2)
stack.push(4)
stack.peek()
print(stack.pop()) # 4
print(stack.pop()) # 2
```



### 3. 다양한 알고리즘

#### Balanced delimiters

* {}, [], () 괄호들이 균형인지(잘 못 표기한건 없는지) 확인하는 함수

```python
# Balanced delimiters
def isValidSource(srcfile):
    s = Stack() # 스택 객체 생성시 처음 0 데이터가 들어가 있으므로,
    s.pop() # pop을 한번 해주고 사용한다.
    for line in srcfile:
        for token in line: # 참고 : 괄호 아닌건 다 무시(괄호만 체크하는 함수임)
            if token in "{[(": # 열린 괄호면 push
                s.push(token)
            elif token in "}])": # 닫힌 괄호면 균형 여부 확인
                if s.isEmpty():
                    return False
                else:
                    left = s.pop()              # 균형 여부 확인
                    if(token == "}" and left != "{") or \
                        (token == "]" and left != "[") or \
                        (token == ")" and left != "("):
                        return False
    return s.isEmpty() # 스택에 괄호 다 삭제 되었다면 True일거고, 균형이 맞다는 의미가 된다.

a = '{A + (B * C) - (D / [E + F])}'
print(isValidSource(a)) # True
```



#### 백트래킹 VS 브루트포스(추후에 정리)





## Queue(큐)

* FIFO (First in, first out)



### 1. List-based Queue => 리스트 큐(ADT)

* 생략(아래 다양한 큐나 확인(어차피 List로 구성))



#### Code of Circular Queue(원형 큐)

* 고정된 크기의 배열에서 임의의 숫자가 자리를 찾는 방법(공식)
  * (self.back +1) % maxSize 란 => (현재위치) % 전체크기

```python
# Code of Circular Queue => 원형 큐
class QueueNode(object):
    def __init__(self, item):
        self.item = item
        self.next = None # 안쓰긴함

class Queue:
    def __init__(self, maxSize):
        self.count = 0 # size
        self.front = 0 # 맨 앞
        self.back = maxSize -1 # 맨 뒤
        self.qArray = [0 for i in range(maxSize)] # 고정 크기 Array(maxSize), 0초기화
    
    def isEmpty(self):
        return self.count == 0
    
    def isFull(self):
        return self.count == len(self.qArray)
    
    def len(self):
        return self.count
    
    def enqueue(self, item):
        maxSize = len(self.qArray)
        self.back = (self.back +1) % maxSize # index공식 (현위치)%전체크기
        self.qArray[self.back] = item
        self.count += 1
    
    def dequeue(self):
        item = self.qArray[self.front]
        maxSize = len(self.qArray)
        self.front = (self.front +1) % maxSize
        self.count -= 1
        return item
a = Queue(5) # size 5
a.enqueue(32)
a.enqueue(31)
a.enqueue(30)
a.enqueue(29)
a.enqueue(28)
a.enqueue(20) 
a.enqueue(21) 
print(a.dequeue()) # 20(값)
print(a.dequeue()) # 21
print(a.qArray) # [20, 21, 30, 29, 28]
print(a.count) # 5(크기)
print(a.front) # 2(index)
```



#### Priority Queue(우선순위 큐) - Unbounded

* Unbounded는 무한 즉, 한정되어 있지 않다라는 의미. dequeue하면서 우선순위로 추출
* Bounded는 우선순위 고정되어 있다는 의미. (보통 리스트 - 연결리스트 이렇게 2중 구조로 형성)
  * 이부분에 관한 우선순위 큐는 따로 정리는 안하겠고, 필요하다면 책에서 확인.

```python
# Priority Queue - 우선순위 큐
# Unbounded!! => Queue ADT와 비슷함

import numpy as np # 수치계산 라이브러리
import names # names 라이브러리 임포트(pip필요)
import random

class PriorityQEntry: # item과 우선순위를 포함한 노드 생성
    def __init__(self, item, priority):
        self.item = item
        self.priority = priority

class PriorityQueue:
    def __init__(self):
        self.qList = list()
    
    def isEmpty(self):
        return len(self) == 0
    
    def len(self):
        return len(self.qList)
    
    def enqueue(self, item, priority): # enqueue => rear로 넣음
        entry = PriorityQEntry(item, priority) # entry라고 새로 큐 만듬
        self.qList.append(entry) # qList에 append(entry)

N =10
qList = []
for i in range(N): # 생성
    random_char = names.get_first_name(gender='female') # 이름 반환
    random_number = random.randint(1,11)
    instance = PriorityQEntry(random_char, random_number)
    qList.append(instance)

priorityQ = PriorityQueue() # 우선순위 큐 객체 생성
for data in qList:
    priorityQ.enqueue(data.item, data.priority) # enqueue

for i in range(priorityQ.len()):
    print(priorityQ.dequeue())
'''
('Connie', 3)
('Mary', 3)
('Patricia', 4)
('Kimberly', 7)
('Sheri', 7)
('Janie', 7)
('Kayla', 7)
('Alison', 8)
('Maria', 8)
('Beatrice', 9)
'''
```





### 2. Linkded List-based Queue => 연결리스트 큐(ADT)

```python
# Linkded List-based Queue => 연결리스트 큐
class QueueNode(object):
    def __init__(self, item):
        self.item = item
        self.next = None

class Queue:
    def __init__(self, item=None):
        #self.qhead = None
        #self.qtail = None
        self.qhead = QueueNode(item)
        self.qtail = self.qhead
        self.count = 0

    def isEmpty(self):
        return self.qhead is None

    def len(self):
        return self.count

    def enqueue(self, item): # 맨뒤에 넣기(append)
        self.count += 1
        node = QueueNode(item)
        if self.isEmpty():
            self.qhead = node
        else:
            self.qtail.next = node
        self.qtail = node # 추가

    def dequeue(self): # 반대편에서 빼기!?(맨앞)
        # assert not self.isEmpty()
        node = self.qhead
        if self.qhead is self.qtail:
            self.qtail = None
        self.qhead = self.qhead.next
        self.count -= 1
        return node.item

a = Queue(10)
a.enqueue(5)
a.enqueue(12)
print(a.dequeue()) # 10
print(a.qhead.item) # 5
print(a.qtail.item) # 12
```



