---
title:  "[Python]TripleStack, Blocking_Circular_Queue(3개의 스택과 원형큐)"
categories : Algorithm
tag : [TripleStack, Blocking_Circular_Queue, 3개스택, 닫힌 원형큐]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---

## TripleStack과 Blocking_Circular_Queue 활용

* **3개의 스택과 원형큐를 이용한 데이터 관리 시스템**



### 1. TripleStack class

* **1개의 스택이아닌 3개의 스택으로 데이터 관리**
* isEmpty, push_and_assort, pop_multi, get_highest_stack

```python
class TripleStack:
    def __init__(self, limit):
        self.stacks = [[],[],[]]  # stack[0], stack[1], stack[2]
        self.limit = limit
    
    def isEmpty(self) -> bool: # 하나라도 있으면 true반환!
        for i in range(3):
            if(len(self.stacks[i]) == 0):
                return True
        return False

    def push_and_assort(self, _users) -> bool: # 에러없이 성공하면 True반환!
        n_discarded = 0
        for user in _users: # _users리스트 하나하나 접근
            username = user[0] # user 3개의 열중에 첫번째 열을 username으로 저장
            if 'A' <= username[0] <= 'L':  # username에 첫번째 글자를 비교
                self.stacks[0].append(user)
            elif 'L' <= username[0] <= 'S' and user[1] > 50: # 첫번째 글자와 숫자까지 비교
                self.stacks[1].append(user)
            elif 'M' <= username[0] <= 'Z': # 첫번째 글자 비교
                self.stacks[2].append(user)
            else:  # discard other users
                n_discarded += 1
        return True

    def pop_multi(self, nums, no): # pop하는 함수
        stack_size = len(self.stacks[no])
        if nums > stack_size:
            return [self.stacks[no].pop() for i in range(stack_size)]
        else: # nums < stack_size (빼낼데이터 보다 더 많이 가지고 있는 경우)
            return [self.stacks[no].pop() for i in range(nums)]

    def get_highest_stack(self) -> int: # 가득찬 스택의 번호를 반환
        no = 0
        for i in range(3):
            if(len(self.stacks[i]) >= self.limit):
                no = i
        return no
```



### 2. Blocking Circular Queue class

* 닫힌 원형큐 형태 사용
* isEmpty, isFull, enqueue, dequeue

```python
class CustomBCQ:
    def __init__(self, maxSize):
        self.count = 0
        self.front = 0
        self.limit = maxSize
        self.back = maxSize - 1
        self.isBlocked = False
        self.queue = [None] * maxSize

    def isEmpty(self) -> bool: # 큐가 비었으면 True반환!
        return self.count == 0

    def isFull(self) -> bool: # 큐가 가득찼으면 True반환!
        return self.limit == self.count

    def enqueue(self, item): # 아이템 큐에 추가! (isBlocked에 따른 분류)
        if not self.isBlocked: # Blocked 가 아닌 경우
            self.back = (self.back + 1) % self.limit
            self.queue[self.back] = item
            self.count += 1
            if self.count == self.limit: # 가득 찼는지 확인
              print("Enqueue operation is blocked from now on.")
              self.isBlocked = True
        else: # Blocked 인 경우
            print("Enqueue operation is not allowed. Because CustomBCQ is blocked. ")

    def dequeue(self): # 큐에서 아이템 빼기 (25%이하면 isBlocked = False)
        # 아이템을 큐에서 제거
        item = self.queue[self.front]
        self.front = (self.front + 1) % self.limit
        self.count -= 1
        # percentage를 구하는 방법
        quotient = self.count // self.limit
        percentage = quotient * 100 ###  25%
        # 25% 이하이면 블록을 해제
        if percentage <= 25 and self.isBlocked:
            print("Enqueue operation is allowed now.")
            self.isBlocked = False
        return item
```



### 3. 프로그램 테스트

#### 3-1. TripleStack

```python
# TripleStack 데이터 로딩!!
ts = TripleStack(100) # limit=100, 스택 3개만들기
users = bms.get_users(100) # 0~99 총 100개 users 추출

n = ts.push_and_assort(users) # 각 조건에 맞게 3개의 스택에 분배

# 스택 현재 상태 출력
print("TripleStack: ", [len(s) for s in ts.stacks])

# 현재 제일 많은 데이터를 가진 스택을 출력
no_stack = ts.get_highest_stack() # 가득찬 스택 번호 구하기
print(f"Get the highest stack # : {no_stack}")

# pop_multi 를 수행함
some_users = ts.pop_multi(300, no_stack) # 가득찬 스택에 요소 300개 pop하기

# 스택 현재 상태 출력
print("TripleStack: ", [len(s) for s in ts.stacks])

# 비어 있는 스택이 있는지 확인
print("The state of TripleStack:", ts.isEmpty())
'''
TripleStack:  [50, 0, 50]
Get the highest stack # : 0
TripleStack:  [0, 0, 50]
The state of TripleStack: True
'''
```



#### 3-2. Blocking Circular Queue

```python
# Blocking Circular Queue
MAX_SIZE = 100 # 상수 지정
SIZE_TO_READ = 150 # 상수 지정

bcq = CustomBCQ(MAX_SIZE) # 크기 100지정
dequeued_list = []

items = bms.get_items(SIZE_TO_READ) # 150개 데이터 가져옴

i = 0
stage = 1
while i < len(items):
  # 가득찬 상태가 아니고 (넣을려는 데이터 크기-1)보다 작은 상태이면 계속 enqueue를 수행함
    while ( not bcq.isFull() ) and ( i < SIZE_TO_READ ):
        bcq.enqueue(items[i])  
        i += 1
  
    # 블록킹 상태에도 enqueue가 수행되는지 확인.
    if i < SIZE_TO_READ:
        bcq.enqueue(items[i])  

    print(f"In stage ({stage}), {len(bcq.queue)} items are enqueued." )

    # 큐가 비어있지 않을때 까지 dequeue
    while not bcq.isEmpty():
        item = bcq.dequeue()
        dequeued_list.append(item)

    print(f"+++ Total {len(dequeued_list)} items are dequeued until ({stage}), ." )
    stage += 1 
'''
Enqueue operation is blocked from now on.
Enqueue operation is not allowed. Because CustomBCQ is blocked. 
In stage (1), 100 items are enqueued.
Enqueue operation is allowed now.
+++ Total 100 items are dequeued until (1), .
In stage (2), 100 items are enqueued.
+++ Total 150 items are dequeued until (2), .
'''
```



### 4. 복잡도

* 주관적인 생각이기 때문에 틀릴 수 있음.

```python
# TripleStack 클래스 내 push_and_assort, pop_multi 메소드, Blocking Circular Queue 클래스 내 enqueue, dequeue 메소드
"""
  T: O(N) - Linear time
  push_and_assort : push하는 메소드
"""  
"""
  T: O(N) - Linear time
  pop_multi : pop하는 메소드
"""  
"""
  T: O(1) - Constant time
  enqueue : push하는 메소드
"""  
"""
  T: O(1) - Constant time
  dequeue : pop하는 메소드
"""  
```

