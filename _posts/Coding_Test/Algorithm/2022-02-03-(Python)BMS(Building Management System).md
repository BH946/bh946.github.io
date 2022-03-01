---
title:  "[Python]BMS(Building_Management_System)"
categories : Algorithm
tag : [BMS, ADT, search(검색), sort(정렬), delete(삭제)]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---


## BMS : 건물 관리 시스템

* 즉, 우리가 만드는건 데이터를 관리하기 위한 데이터 관리 시스템 느낌이라고 보면 된다.



### 1. Computer Class

* **아래에서 인스턴스 생성을 위함(데이터 생성을 이 클래스 형식으로 생성할거임.)**

```python
class Computer:
    def __init__(self, title, price, rating, date, link):
        self.title = title # 문자열 (제목)
        self.price = price # 정수 (가격)
        self.rating = rating # 부동소수형 float (별점)
        self.date = date # 문자열 (도착 날짜)
        self.link = link # 문자열 (링크)
        self.next = None # 과제 2를 위한 연결리스트

    def __repr__(self):
        return f"COMPUTER [title: {self.title}, price:{self.price}, rating: {self.rating}, date: {self.date}, link: {self.link}]"
        
C1 = Computer("삼성전자 플러스2 퓨어 화이트 노트북 NT550XDZ-AD2AW (펜티엄골드-7505), WIN미포함,  RAM 8GB + SDD 256GB"
    , 593000, 4.5, "2021-03-19", "https://www.coupang.com/np/search?component=&q=%EB%85%B8%ED%8A%B8%EB%B6%81&channel=user")
```



### 2. BMS Class

* BMS로 객체 생성시 generate_db() 함수를 통해 ItemDB, UserDB클래스의 객체도 생성하고, self로 가지는 흐름.
* search(검색), sort(정렬), delete(삭제), print_...(출력)로 구성

```python
# BMS ADT
class BMS:
    def __init__(self, db_size): # 초기 값 사이즈 받아서
        self.db_size = db_size # self로 사용할 수 있게 저장 하고
        self.generate_db() # 데이터 베이스 함수 실행!!
        self.cursor_user_db = 0 # 사용자 DB에 대한 커서
        self.cursor_item_db = None # 아이템 DB에 대한 커서

    def generate_db(self): # 데이터 생성!!
        self.items = ItemDB(self.db_size[0]) # 링크드 리스트로 구성
        self.users = UserDB(self.db_size[1]) # 2D 배열(2차원)로 구성

    def search(self, keyword): # keyword값 검색해주는 함수
        results = []
        # 연결리스트 부분
        currNode = self.items.polyHead
        while currNode is not None:
            if currNode.title == keyword: 
                results.append(currNode)
            currNode = currNode.next
        # 2D 리스트 부분
        for i in range(len(self.users.data)): # 리스트 끝까지 반복
          if self.users.data[i][0] == keyword:
              results.append(self.users.data[i]) # results에 추가!
        return results # 연결리스트와 2d리스트에서 찾은 search값 리스트로 반환

    def sort(self, order_by): # 정렬해주는 함수 => n행3열중 2열에 age있음
        n=len(self.users.data)
        if (order_by == 'asc'): # 오름차순 일시
            for i in range( n - 1 ) : # 크기가 n인 리스트이면 (n-1)번 반복  
                for j in range( n - 1 - i ) :
                    if self.users.data[j][1] > self.users.data[j + 1][1] : # 현재 위치의 값과 (현재 위치+1)인 값 비교
                        tmp = self.users.data[j] # swap!!!
                        self.users.data[j] = self.users.data[j + 1]
                        self.users.data[j + 1] = tmp  
        elif (order_by == 'desc'): # 내림차순 일시
            for i in range( n - 1 ) : # 크기가 n인 리스트이면 (n-1)번 반복  
                for j in range( n - 1 - i ) :
                    if self.users.data[j][1] < self.users.data[j + 1][1] : # 현재 위치의 값과 (현재 위치+1)인 값 비교
                        tmp = self.users.data[j] # swap!!!
                        self.users.data[j] = self.users.data[j + 1]
                        self.users.data[j + 1] = tmp  
        else: # 오름차순, 내림차순 둘다 아니기 때문에 false 리턴
            return False

    def delete(self, keyword): # 삭제하는 함수 => 하나의 특정 컬럼 입력 받고 해당 레코드 전부 삭제
        results = [] # 삭제부분 배열로 보여주기
        # 연결리스트 부분
        prevNode = None
        currNode = self.items.polyHead
        while currNode is not None:
            if(currNode.title == keyword):
                if currNode is self.items.polyHead: # 처음에 값 있을 때
                    results.append(currNode) # 삭제부분 return하기위해 추가!
                    self.items.polyHead = currNode.next
                    self.items.size -= 1
                else: # 다른경우에 값 있을 때
                    results.append(currNode)
                    prevNode.next = currNode.next # 값 없애기
                    self.items.size -= 1
            prevNode = currNode
            currNode = currNode.next
        # 2D 리스트 부분
        for i in range(len(self.users.data)): # 리스트 끝까지 반복
          if self.users.data[i][0] == keyword:
              results.append(self.users.data.pop(i)) # 리스트 해당 행 삭제
              self.users.data += [[0,0,0]] # 배열 크기 유지하기 위해(중요!!)
              self.users.size -= 1 # 배열 사이즈 1 감소
              continue
        return results

    def print_item_db(self, limit):  
        i = 0 
        p = limit
        curNode = self.items.polyHead
        while i < p and curNode is not None:
            print(curNode)
            i += 1
            curNode = curNode.next

    def print_user_db(self, limit):
        for i in range(limit):
            print(self.users.data[i])
    
    def get_users(self, nums): # 과제3 위해 추가!!
        start = self.cursor_user_db
        end = start + nums
        if start > len(self.users.data):
            start = 0
        results = self.users.data[start:end]
        self.cursor_user_db = end
        return results

    def get_items(self, nums): # 과제3 위해 추가!!
        results, i = [], 0
        # 아이템 DB의 커서 세팅
        if self.cursor_item_db is None:  # None이라면
            CurNode = self.items.polyHead # head노드로 초기화!
        else:
            CurNode = self.cursor_item_db 

        while i < nums and CurNode is not None:
            results.append(CurNode) # 리스트에 append!
            i+=1
            CurNode = CurNode.next

        self.cursor_item_db = CurNode

        return results      
```



### 3. ItemDB 및 UserDB Class

* **실제로 랜덤으로 데이터들을 생성할 부분**

```python
# ItemDB 클래스 : 연결리스트로 구현 -> Computer Class 사용함
# UserDB 클래스 : 2차원 배열로 구현 -> 그냥 임의의 형식으로 데이터 구성

import numpy as np # 수치계산 라이브러리
!pip install names  
import names # 다운받은 names 라이브러리 임포트
import random
import string

class ItemDB: # BMS 조작 통해서 데이터 개수 입력 받음.
    def __init__(self, nums): 
        self.polyHead = None
        self.polyTail = None
        self.size = 0
        self.nums = nums
        self.generate_db()

    def generate_db(self): # ItemDB 생성하는 함수
        monthly_days = np.arange(0,30) # 0~29 배열
        base_date = np.datetime64('2021-03-01') # datetime64라는 numpy라이브러리에 잇는 클래스 사용
        for i in range(self.nums):
            # 컴퓨터 이름
            random_title = names.get_first_name(gender='female')
            # 컴퓨터 가격
            random_price = random.randint(490000,2000000)
            # 컴퓨터 평점
            random_grade = round(random.uniform(1,10),2)
            # 컴퓨터 도착 예정 날짜
            random_date = base_date + np.random.choice(monthly_days) # 랜덤으로 날짜 반환
            # 해당 링크
            random_link = "http://www." + ''.join(random.choice(string.ascii_lowercase) for i in range(10)) + ".com" # 아스키코드 소문자 랜덤 10자 작성
            
            # 인스턴스 생성
            computer = Computer(random_title,random_price, random_grade, random_date, random_link)

            # 링크드 리스트 데이터 추가
            if self.polyHead is None:
                self.polyHead = computer
            else: # 한개 이상이 있는 경우
                self.polyTail.next = computer
            self.polyTail = computer
            self.size += 1

class UserDB:
    def __init__(self, nums):
        self.data = []
        self.nums = nums
        self.size = 0
        self.generate_db()

    def generate_db(self):
        for i in range(self.nums): # 3열에 nums행
            random_name = names.get_first_name(gender='female')
            random_age = random.randint(1,1000)
            random_favorite = ''.join(random.choice(string.ascii_lowercase) for i in range(5))
            self.data += [[random_name, random_age, random_favorite]]
            self.size += 1
```



### 4. 프로그램 테스트

```python
# 요구사항 1, 2
bms = BMS([50000,10000]) # 5만개,1만개 생성, 테스트 경우, 사이즈를 줄인뒤 실행 
bms.print_item_db(10) # 아이템 DB 출력 10개
bms.print_user_db(10) # 사용자 DB 출력 10개

''' 출력
COMPUTER [title: Dorothy, price:1209935, rating: 6.08, date: 2021-03-17, link: http://www.kbzeadnkvu.com]
COMPUTER [title: Stella, price:1489798, rating: 7.83, date: 2021-03-27, link: http://www.oeheamcuhm.com]
... 생략
['Billie', 54, 'lmife']
['Nicole', 76, 'ssbis']
... 생략
'''
```

```python
# 검색 및 삭제할 키워드
keyword = bms.users.data[0][0]
print(keyword)
'''
Billie
'''
```

```python
# 요구사항 3
results = bms.search(keyword)

tmp_items = []
tmp_users = []
for result in results:
    if isinstance(result, Computer): # items인지 users인지 검사
        tmp_items.append(result)
    else:
        tmp_users.append(result)

print(results)
print("검색된 아이템의 수(item): {n_item} 검색된 아이템의 수(user): {n_user}".format(n_item=len(tmp_items), n_user=len(tmp_users)))
'''
[COMPUTER [title: Billie, price:1284472, rating: 5.58, date: 2021-03-12, link: http://www.jgnvgillzj.com], COMPUTER [title: Billie, price:1774479, rating: 3.38, date: 2021-03-14, link: http://www.jeemxrcgqf.com], 
['Billie', 54, 'lmife'], ['Billie', 721, 'eoohl'], ['Billie', 760, 'qcves'], ['Billie', 435, 'jjdum'], ['Billie', 794, 'cyojf'], ['Billie', 593, 'ysgje'], ['Billie', 168, 'nuclq'], ['Billie', 7, 'wedsg'], ['Billie', 869, 'mnkci']]
... 생략
검색된 아이템의 수(item): 30 검색된 아이템의 수(user): 9
'''
```

```python
# 요구사항 4
# 사용자DB를 정렬하고 ASC 순으로 정렬되었는지를 확인
bms.sort('asc') 
bms.print_user_db(10) # 사용자 DB 출력 10개
'''
['Olga', 1, 'ernie']
['Sandra', 1, 'rwpjo']
['Gladys', 1, 'gxzzr']
['Otilia', 1, 'ekrfu']
['Bernadine', 1, 'wshre']
['Shannon', 1, 'vbhux']
['Erma', 1, 'zqjlq']
['Jeanette', 1, 'ycelv']
['Eva', 1, 'kctix']
['Tiffany', 2, 'ehcjk']
'''
```

```python
# 요구사항 5 
# 다시 검색하면 관련데이터는 삭제되었기 때문에 자료가 없어야함
bms.delete(keyword) 

print("수정된 ItemDB의 크기: {n_item_db} 수정된 UserDB의 크기: {n_user_db}".format(n_item_db=bms.items.size, n_user_db=bms.users.size))
results = bms.search(keyword)
print(f"{keyword}를 사용자, 아이템DB에서 다시 검색한 결과: {len(results)}")
'''
수정된 ItemDB의 크기: 49970 수정된 UserDB의 크기: 9991
Billie를 사용자, 아이템DB에서 다시 검색한 결과: 0
'''
```



### 5. 복잡도

* 주관적이므로 틀릴 수 있음

```python
# BMS 내 search, sort, delete 메소드
"""
  T: O(N+M) = O(N) - Linear time
  search : 검색 메소드
"""  
"""
  T: O(N^2) - Quadratic time
  sort : 정렬 메소드
"""  
"""
  T: O(N^2) - Quadratic time
  delete : 삭제 메소드
  => 연결리스트는 O(N)이지만 2차원 리스트는 pop(i)때문에 O(N^2)이 나온다고 생각
"""  
```







