---
title:  "[Python]HashMap(해시 관련)"
categories : Algorithm
tag : [Hashing(해싱), ADT, 충돌&오버플로 해결법, Clustering(군집화), Divide and remainder Hashing(mod), Mid-square(중간 제곱) Hashing, Folding(중첩) Hashing, Rehash(재해시), findSlot(자리찾기 함수), Add(추가함수)]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---

## Hashing(해싱)

* 아래 HashMap은 정말 기초부터 만든 class니까 잘 참고.
* key space >> address space
  => 모든 경우의 key 값 공간의 크기에 맞추어 Record 저장공간을 준비하는 것이 아닌, 실제 저장이(예측) 되는 Record수(=주소공간)에 맞추어 저장하는 것이 저장 공간 활용 측면에서 효과적.
  이과정은 key값 공간에 존재하는 값을 주소 공간의 크기에 맞게 변형하는 매핑 함수가 있으면 가능.
  => 이것이 해시 함수(다양하게 있음)
* Hashing(해싱) : 검색(search key)를 제한된 범위의 배열에 매핑(mapping)하는 과정
* Hash Table : 키가 저장된 배열
* 충돌과 오버플로 또한 해결 방법이 다양함.
  * 충돌 해결 기술 분류 체계
    * (Open Hashing) Separate Chaining, Closed Addressing
      키 중복 O (연결리스트로 추가)
    * (Closed Hashing) Open Addressing, Linear, Quadratic, Double,,
      키 중복 X


* Clustering(군집화)
  * Primary clustering(1차 군집화) : 원래 가야하는 hash위치가 근처임으로 인해 발생하는 문제
  * Secondary clustering(2차 군집화) : 수식이 원래 hashslot에 기반해서 발생하는 문제
    => 1차 군집화를 해결하고자 Quadratic Priboing(이차 조사) 즉, slot = (home+i<sup>2</sup>)%M 이런식으로 +i에서 다르게 바꿨다고 해도 떨어진 곳에서 군집화가 또 발생한다는 이야기이다.

### 다양한 해시 함수

* Hash function 1 : Divide and remainder Hashing : mod 이용 => **제일 쉬움**
* Hash function 2 : Mid-square(중간 제곱) Hashing : key 제곱해서 중간부분만 사용
* Hash function 3 : Folding(중첩) Hashing : 접어서 합을 구함
* 기타 해시 함수 : 숫자 추출, 숫자 이동, 진수 변환... 등



### Collision and overflow(충돌과 오버플로) 해결 방법
* 선형조사 : 말그대로 선형조사함. **=> 블로그 게시글에 구현한 코드 올렸으니 참고**
* 선형조사의 단점 개선으로 '2-패스 해시 파일' 있음. **=> 블로그 게시글에 구현한 코드 올렸으니 참고**

* 독립 오버플로 구역 : 독립 오버플로 공간 새로 만들어서 이용.

* 이중해싱 : 2차 해시 함수 사용 **=> 블로그 게시글에 구현한 코드 올렸으니 참고**

* 동거자 체인 : 링크드 리스트로 따로 오버플로 영역 공간 만들어서 각 연결.

* 버킷 체인(이건왜 설명없냐?)



## HashMap

* 다양한 해시 방법들이 있는데 그중 제일 기초가 되는 방식



### 1. MapEntry

* dict처럼 key, value 인스턴스 생성해서 사용하기 위함

```python
class MapEntry: # key, value 인스턴스
    def __init__(self, key, value):
        self.key = key
        self.value = value

UNUSED = None
EMPTY = MapEntry(None, None)
```



### 2. HashMap Class

* add, valueOf, findSlot, rehash, hash1, hash2, printHashAll

```python
class HashMap: 
    def __init__(self):
        self.table = list(EMPTY for i in range(7)) # list로 수정! 0~6이니까 크기 7
        self.count = 0
        self.maxCount = len(self.table) - len(self.table) // 3
        self.rehashNumber = 0 # 총 리해시 넘버
        self.crashNumber = 0 # 총 충돌 넘버
        self.crashAfterPrint = [] # 마지막 리해시 후 충돌 출력(충돌 잘 되었는지 확인을 위해서!)
    
    def __len__(self): # 크기 반환
        return self.count
    
    def __contains__(self, key): # key를 포함하는지 체크
        slot = self.findSlot(key,False)
        return self.table[slot].key is not None # key가 None이 아니라면 True 반환

    def add(self, key, value): # 동일 키 False, 다른 키 True 반환
        # 동일한 키 유무 판단을 위해 for문 접근
        for slots in self.table: # 크기 7인 list를 각각 접근
            if slots.key == key: 
                slot = self.findSlot(key, False)
                self.table[slot].value = value
                return False
        # return이 아니라는 것은 동일한 키가 없다는 뜻
        slot = self.findSlot(key,True)
        self.table[slot] = MapEntry(key, value)
        self.count += 1
        if self.count == self.maxCount: # 2/3 수준정도에서 리해시 하는 부분
            self.rehash()
        return True

    def valueOf(self, key): # key에 연관된 값을 반환
        slot = self.findSlot(key,False)
        return self.table[slot].value

    def findSlot(self, key, forInsert): # forInsert가 True인 경우 추가, 아닌 경우 해당 값 update
        slot = self.hash1(key)
        step = self.hash2(key)
        M = len(self.table)
        while self.table is not UNUSED: # list가 아예 None이 아니라면 반복! False일시 반복문 탈출
            if forInsert and \
                (self.table[slot] is UNUSED or self.table[slot] is EMPTY): # True 이면서 slot이 None이라면 slot 반환
                return slot
            elif not forInsert and \
                (self.table[slot] is not EMPTY and self.table[slot].key == key): # False 이면서 slot이 None이 아니면서 slot의 key가 같은경우 slot 반환
                return slot
            elif not forInsert and \
                (self.table[slot] is UNUSED or self.table[slot] is EMPTY): # False 이면서 slot이 None이라면 slot 반환 => contains, valueOf를 위해 생성
                return slot
            else: # 충돌
                self.crashAfterPrint.append("충돌전 slot : " + str(slot))
                slot = (slot + step) % M
                self.crashAfterPrint.append("충돌후 slot : " + str(slot))
                self.crashNumber += 1

    def rehash(self):
        origTable = self.table
        newSize = len(self.table) * 2 + 1 # 크기 15
        self.table = list(EMPTY for i in range(newSize)) # list로 수정

        self.count = 0
        self.maxCount = newSize - newSize // 3
        
        for entry in origTable:
            if entry is not UNUSED and entry is not EMPTY:
                slot = self.findSlot(entry.key, True) # entry.key 넘겨서 slot 다시 반환
                self.table[slot] = entry
                self.count += 1
        self.crashAfterPrint = [] # 마지막 리해시 후 충돌 구하기위해 또다시 초기화
        self.rehashNumber += 1

    def hash1(self, key):
        return abs(hash(key)) % len(self.table) # hash()는 파이썬에서 제공하는 함수입니다!

    def hash2(self, key):
        return 1 + abs(hash(key)) % (len(self.table) - 2)

    def printHashAll(self): # 임의로 만든 총 리스트 출력 (충돌 잘 되었는지 확인을 위해서!)
        for i in range (len(self.table)):
            print("리스트 번호 : {0}, key : {1}, value : {2}".format(i, self.table[i].key, self.table[i].value))
```



### 3. 프로그램 테스트

```python
# HashMap에 자료를 넣기
items = bms.get_items(100)

map = HashMap()
for item in items:
    key = item.title + item.link # 고유의 key
    value = item.title # 값
    map.add(key, value) # 추가하기
```

```python
# 1. 총 리해시 횟수
print("총 리해시 횟수 : ", map.rehashNumber)
print("마지막 리해시 후 크기 : ", len(map.table))

# 2. 총 충돌 횟수
print("총 충돌 횟수 : ", map.crashNumber)
'''
총 리해시 횟수 :  5
마지막 리해시 후 크기 :  255
총 충돌 횟수 :  115
'''
```

```python
# 리해시 할 때마다 slot의 위치또한 같이 변할 테니까 마지막 리해시 후 충돌된 slot들 확인

# 3. 마지막 리해시 후 충돌 출력!
print(map.crashAfterPrint)

# 4. 총 List 보여주는 함수
map.printHashAll()
'''
['충돌전 slot : 143', '충돌후 slot : 181', '충돌전 slot : 89', '충돌후 slot : 235', '충돌전 slot : 235', '충돌후 slot : 126', '충돌전 slot : 239', '충돌후 slot : 244', '충돌전 slot : 244', '충돌후 slot : 249', '충돌전 slot : 183', '충돌후 slot : 222', '충돌전 slot : 95', '충돌후 slot : 15', '충돌전 slot : 73', '충돌후 slot : 101', '충돌전 slot : 2', '충돌후 slot : 115', '충돌전 slot : 24', '충돌후 slot : 215']
리스트 번호 : 0, key : None, value : None
리스트 번호 : 1, key : Janicehttp://www.pkrucihdmk.com, value : Janice
리스트 번호 : 2, key : Charlenehttp://www.anzmvvjvrh.com, value : Charlene
리스트 번호 : 3, key : None, value : None
... 생략
'''
```



### 4. 복잡도

* 주관적인 생각임으로 틀릴 수 있음

```python
# HashMap 클래스 내 rehash, findSlot 메소드
"""
  T: O(N^2) - Quadratic time
  rehash : 재해시 하는 메소드
  => while문 안에 findSlot 써줌으로 써 N^2이 된다고 생각
"""  
"""
  T: O(N) - Linear time
  findSlot : findSlot 메소드
"""  
```







