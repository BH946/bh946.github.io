---
title:  "[Python]Searching & Sorting(검색&정렬 관련 알고리즘)"
categories : Algorithm
tag : [LinearSearch(선형 검색), sortedLinearSearch, BinarySearch(바이너리 검색), Bubble Sort(버블 정렬), Selection Sort(선택 정렬), Insertion Sort(삽입 정렬), Merging Sorted Lists(정렬된 리스트 합병 정렬), Merge Sort(합병 정렬), Quick Sort(퀵 정렬), Radix Sort(기수 정렬), Heap Sort(힙 정렬)]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---

## Search(검색)


### 1. LinearSearch(선형 검색) - O(N)

* 가장 기본

```python
def linearSearch(theValues, target):
    n = len(theValues)
    for i in range(n):
        if theValues[i] == target:
            return True
    return False
```

* 정렬된 리스트에서 검색은 조기종료가 가능 (장점) - 성능이 좀 더 좋겠죠

```python
def sortedLinearSearch(theValues, item):
    n = len(theValues)
    for i in range(n):
        if theValues[i] == item:
            return True
        elif theValues[i] > item: # 조기종료
            return False
    return False
```



### 2. BinarySearch(바이너리 검색) - O(N), O(logN)

* 개선 전 : O(N), 개선 후 O(logN)

```python
def binarySearch(theValues, target):
    low = 0
    high = len(theValues) -1  # 맨끝칸은 n-1
    # for x in range(len(theValues)): # 이렇게 하면, 복잡도가 O(N)이 됨.
    while low <= high: # O(logN)
        mid = (high+low) // 2 # 정수 부분만 남겨줌 ('//'의 역할)
        if theValues[mid] == target:
            return True
        elif target < theValues[mid]:
            high = mid -1
        else:
            low = mid +1
    return False
```



## Sort(정렬)



### 1. Bubble Sort(버블 정렬) - O(N<sup>2</sup>)

* for j in range(i+1,n-1):
* for j in range(n-1-i): # for문 둘다 가능
* 현재 위치(j) 다음 위치(j+1) 비교를 반복해서 현재 위치값이 크면 교환을 반복한다.
* 참고 : range(n-1)하는 이유는 list index out of range에러 안뜨게끔.

```python
import random
def bubbleSort(theSeq):
    n = len(theSeq) # 리스트 길이
    for i in range(n-1) : # 크기가 n인 리스트이면 (n-1)번 반복  
        # for j in range(i+1,n-1): # 이렇게 for문 돌려도 가능
        for j in range(n-1-i): # i=0일때 (n-1 - 0)번 버블, i=1일때 (n-1 - 1)번 버블, i=2일때 (n - 1 - 2)번 버블, ..., i=(n-2)일때 1번 버블
            # 현재 위치의 값과 (현재 위치+1)인 위치의 값을 비교하고 현재 위치의 값이 크면 교환
            if theSeq[j] > theSeq[j+1] : 
                tmp = theSeq[j]
                theSeq[j] = theSeq[j+1]
                theSeq[j+1] = tmp
    return theSeq

N = 10 # 10개의 난수 생성
unorderedList = [random.randint(1, 1000) for x in range(N)] # 중복을 포함한 N개의 난수 생성 리스트
print(unorderedList)
orderedList = bubbleSort(unorderedList)
print(orderedList)
```



### 2. Selection Sort(선택 정렬) - O(N<sup>2</sup>)

* 값을 선택해서 제일 작은값을 Linear로 쭉 찾고나서 마지막에 바꿔주고 continue한다.(반복)
* 사람과 유사하게 작동

```python
def selectionSort(theSeq):
    n = len(theSeq)
    for i in range(n-1):
        smallNdx = i
        for j in range(i+1, n):
            if theSeq[j] < theSeq[smallNdx]: # SELECTION
                smallNdx = j                 
        if smallNdx != i:
            tmp = theSeq[i]
            theSeq[i] = theSeq[smallNdx]
            theSeq[smallNdx] = tmp
    return theSeq
```



### 3. Insertion Sort(삽입 정렬) - O(N<sup>2</sup>)

* 자신의 위치를 찾아서 삽입하는 정렬 테크닉

```python
def insertionSort(theSeq):
    n = len(theSeq)
    for i in range(1, n):
        value = theSeq[i]
        pos = i
        while pos > 0 and value < theSeq[pos -1]: # SHIFTING
            theSeq[pos] = theSeq[pos - 1]
            pos -= 1
        theSeq[pos] = value # INSERTION
    return theSeq
```



### 4. Merging Sorted Lists(정렬된 리스트 합병 정렬)

* **정렬된 2개의 리스트를 매개변수로 받아, 새로운 정렬된 리스트를 생성**

#### 방법1) 일단 다 추가하고 Sorting 실행 - O(NlogN)

```python
def MergeSortedLists(listA, listB) -> [int]:
    newList = list()
    # 합병
	for a in listA:
        newList.append(listA[a])
	for b in listB:
        newList.append(listB[b])
	# 정렬 메소드 이용
    newList.sort() # O(NlogN) Python의 sort라이브러리는 병합정렬(NlogN)으로 구성된다.
    return newList # 반환
```



#### 방법2) 맨 위의 값을 비교해서 차례로 넣고 남는 값들 채워 넣기 - O(N)

* **조건 : listA, listB는 정렬된 리스트여야 함.**

```python
def MergeSortedLists(listA, listB):
    newList = list()
    a = 0
    b = 0
    while a < len(listA) and b < len(listB):
        if listA[a] < listB[b]:
            newList.append(listA[a])
            a += 1
        else :
            newList.append(listB[b])
            b += 1
    while a < len(listA):
        newList.append(listA[a])
        a += 1
    while b < len(listB):
        newList.append(listB[b])
        b +=1
    return newList
```



### 5. Merge Sort(합병 정렬)

* 위의 4번은 정렬된 리스트를 받았을때의 합병정렬을 의미한거지 혼동X

#### Basic Merge sort in Python

* 재귀적인 호출(매우 비효율), slice사용으로 새로운 list가 각각 생성(매우 비효율), merge는 동일.
  애초에 array(즉, 고정된 배열)은 slice를 제공하지도 않는다.
* **아마도?? O(N<sup>2</sup>)일거라고 추측. 아니여도 O(nlogn)보단 훨씬 안좋을것이다.**

```python
def pythonMergeSort(theList):
    if len(theList) <= 1: # Base Case
        return theList
    else: # Recursive Case
        mid = len(theList) //2
        leftHalf = pythonMergeSort(theList[:mid])
        rightHalf = pythonMergeSort(theList[mid:])
        newList = MergeSortedLists(leftHalf, rightHalf) 
        return newList
fire = [2,4,1,53,5,4,63,63,45,346,35,342,3]
print(fire) # [2,4,1,53,5,4,63,63,45,346,35,342,3]
print(pythonMergeSort(fire)) # [1, 2, 3, 4, 4, 5, 35, 45, 53, 63, 63, 342, 346]
```

#### 향상된 Merge sort - O(NlogN)

* Split virtually : 리스트를 물리적으로 나누지 말고 가상적으로 나누자(인덱스 마커들 사용)
* 전체 데이터를 1/2씩 접근할 때 logN에 데이터 N개 => O(NlogN)

```python
# 향상 Merge sort
def mergeVirtualSeq(theSeq, left, right, end, tmpArray):
    a = left
    b = right
    m = 0
    while a < right and b < end:
        if theSeq[a] < theSeq[b]:
            tmpArray[m] = theSeq[a]
            a+=1
        else:
            tmpArray[m] = theSeq[b]
            b+=1
        m+=1
    while a < right:
        tmpArray[m] = theSeq[a]
        a+=1
        m+=1
    while b < end:
        tmpArray[m] = theSeq[b]
        b+=1
        m+=1
    for i in range(end - left): # 마지막 원래 시퀸스에 넣어주기
        theSeq[i+left] = tmpArray[i]

def recMergeSort(theSeq, first, last, tmpArray):
    if first == last:
        return
    else:
        mid = (first + last) // 2
        recMergeSort(theSeq, first, mid, tmpArray) 
        recMergeSort(theSeq, mid+1, last, tmpArray)
        mergeVirtualSeq(theSeq, first, mid+1, last+1, tmpArray)

def mergeSort(theSeq):
    n = len(theSeq)
    # tmpArray = Array(n)
    tmpArray = [0 for i in range(n)]
    recMergeSort(theSeq, 0, n-1, tmpArray)

fire = [2,4,1,53,5,4,63,63,45,346,35,342,3]
print(fire) # [2,4,1,53,5,4,63,63,45,346,35,342,3]
mergeSort(fire)
print(fire) # [1, 2, 3, 4, 4, 5, 35, 45, 53, 63, 63, 342, 346]
```



### 6. Quick Sort(퀵 정렬) - O(N<sup>2</sup>)

* 대부분은 O(NlogN)이긴 하지만, 최악은 O(N<sup>2</sup>)이다.
  * C++의 경우 sort라이브러리가 퀵 정렬로 구성되어있는데, 복잡도가 O(NlogN)으로 나타난다. 즉, 대부분은 O(NlogN)이며, O(N<sup>2</sup>)이 안되게끔 C++에서는 수정해놨다.
* pivot으로 구성되어있다, mergeSort와 다르게 추가적인 임시 저장공간이 필요없다는 장점.

```python
# 퀵 정렬
def quickSort(theSeq):
    n = len(theSeq)
    recQuickSort(theSeq, 0, n-1)

def recQuickSort(theSeq, first, last):
    if first >= last:
        return
    else:
        pos = partitionSeq(theSeq, first, last)
        recQuickSort(theSeq, first, pos-1)
        recQuickSort(theSeq, pos+1, last)

def partitionSeq(theSeq, first, last):
    pivot = theSeq[first]
    left = first +1
    right = last
    while left < right: # 둘이 만날때 까지 수행
        while left < right and theSeq[left] < pivot:
            left += 1
        while right >= left and theSeq[right] >= pivot:
            right -= 1
        if left < right : # 만약 파티션 종료 안되었다면 Swap
            tmp = theSeq[left]
            theSeq[left] = theSeq[right]
            theSeq[right] = tmp
    if right != first:
        theSeq[first] = theSeq[right]
        theSeq[right] = pivot
    return right

fire = [2,4,1,53,5,4,63,63,45,346,35,342,3]
quickSort(fire)
print(fire) # [1, 2, 3, 4, 4, 5, 35, 45, 53, 63, 342, 63, 346]
```



### 7. Radix Sort(기수 정렬) - O(nw) = O(N)

* 빠른 분배 정렬 알고리즘. Bin sort 혹은 Bucket sort라고도 함.
* 자리수 마다 추출 공식 : digit = (key // column) % 10
* 큐 생성 : O(k), D번만큼 분배와 모음을 진행 : O(nd) => O(k+nd) = O(N)
  k : 큐의 개수, n : 키의 개수, d : 키의 길이 (위에서 w는 키의 길이를 의미)

```python
def radixSort(intList, numDigits):
    binArray = [None]*10
    for k in range(10): # bin을 10개 미리 만들어둠 (숫자 : 0~9니까)
        binArray[k] = Queue()

    column = 1 # 일의자리 먼저 key!!

    for d in range(numDigits):
        for key in intList:
            digit = (key// column) % 10 
            binArray[digit].put(key)
        i = 0
        for bin in binArray: # binArray전부 intList로 넣기
            # (1의자리 bin들, 10의자리 bin들, 100의자리 bin들이 순서대로 intList를 계속 바꾸고 채워나가 줌. 마지막 자리의 bin들이 적용됬을땐 intList 정렬은 끝)
            while bin.qsize() != 0:
                intList[i] = bin.get()
                i += 1
        column *= 10 # 숫자 자리수 변경 (1이면 10의 자리로)

intList = [212, 413, 125, 531, 522, 411, 634, 632, 457, 346, 358, 342, 390]
radixSort(intList, 3) # 3은 자리수를 의미
print(intList) # [125, 212, 342, 346, 358, 390, 411, 413, 457, 522, 531, 632, 634]
```



### 8. Heap Sort(힙 정렬) - O(NlogN)

* Heaps : 완전 이진 트리, 보통 리스트로 구현, 

#### Simple HeapSort

```python
def simpleHeapSort(theSeq):
    # Create an array-based max-heap.
    n = len(theSeq)
    heap = MaxHeap(n) # MaxHeap 자료구조를 활용 - 크기 n으로 초기화
    
    # Build a max-heap from the list of values.
    for item in theSeq: # 정렬되지 않은 데이터를 힙에 넣음
        heap.add(item)
    # Extract each value from the heap and store them back into the list.
    for i in range(n, 0, -1): # 오름차순
        theSeq[i] = heap.extract() # 힙으로부터 데이터를 추출하면서 정렬
```

#### 향상된 힙 정렬

* 사실 뭐가 향상된건진 잘 모르겠음.

```python
def heapsort(theSeq):
    n = len(theSeq)
    # Build a max-heap within the same array.
    for i in range(n):
        siftUp(theSeq, i)
    # Extract each value and rebuild the heap.
    for j in range(n-1, 0, -1):
        tmp = theSeq[j]
        theSeq[j] = theSeq[0]
        theSeq[0] = tmp
        siftDown(theSeq, j-1, 0)
```







