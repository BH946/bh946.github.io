---
title:  "[Python] Basic(기초문법) 재정리 - 최신ver(1)"
categories : PY
tag : [python, 문법, 자료구조, 함수]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



**Python의 핵심 자료구조와 기본 문법을 체계적으로 정리한다. - List, Tuple, Dict, Set, Func, 패키지와 모듈, lambda, list Comprehension(=리스트 함축), iterable 과 iterator**

<br>

<br>

## 리스트(List) 다루기 📝

- 생성: `list()` 또는 `[]`

- 원본 변경되지 않는 메소드

  - `append([val])`: [val] 통째로 삽입 (대체: `a[1] = [val]`)

  - `extend([val])`: val로 삽입 (대체: `a[1:2] = [val]`)

    - ```python
      a = [1, 2, 3]
      
      # append 대체
      a[1] = [4, 5]  # 결과: [1, [4, 5], 3]
      
      # extend 대체
      a[1:2] = [4, 5]  # 결과: [1, 4, 5, 3]
      ```

  - `sorted()`: 정렬된 새 리스트 반환

  - `index(val)`, `count(val)` 등

- 원본 변경되는 메소드

  - `insert(idx,val)`: idx 위치에 삽입
  - `pop(idx)`: idx 위치 제거 (미지정시 마지막 요소)
  - `remove(val)`: 값 제거 (여러개면 앞에서부터)
  - `del(var)`: 변수를 메모리에서 제거
    - 없는 변수이면 예외터지기 때문에 보통 if in 문으로 확인 먼저하는것을 권장
  - `sort()`, `reverse()`: 원본에 바로 오름차순, 내림차순 정렬

* 합치는 메소드: `zip()`
  * zip(list1, list2)
    * list1=[1,2,3,4], list2=[11,22,33] 이라면,
    * zip(list1, list2) -> [(1,11),(2,22),(3,33)]

- 슬라이싱 메소드: `a[start:end:step]`

  - start와 end지점을 먼저 찾고, 해당 지점에서 부터 step 에 맞게 이동한다.

    * 단, [::] 는 전체 범위를 의미하므로 step이 음수면 맨뒤부터 출력!

  - ```python
    a = [1,2,3,4,5]
    print(a[::-2])    # [5,3,1]
    print(a[::2])     # [1,3,5]
    print(a[-2::-2])  # [4,2]
    print(a[-2::-1])  # [4,3,2,1]
    print(a[0::-2])   # [1]
    ```

<br>

<br>

## 튜플(Tuple) 다루기 📝

**튜플은 요소 변경이나 삭제가 불가능한 읽기 전용 자료구조**

- 생성: `tuple()` 또는 `()`

  * `b=1,2,3,4` : 자동으로 `(1,2,3,4)` 튜플로 반환

  * 요소 1개는 구분에 주의!
    * b=(1) -> int
    * b=(1,) -> tuple (즉, 단일 요소는 콤마 필수)

- 변환: `tuple(list)` ↔️ `list(tuple)`

  - 요소 변경 및 삭제를 리스트로 바꿔서 하면 됨!

- 튜플 패킹, 언패킹

  - 함수의 반환이 return a,b,c 라 가정
  - 튜플 패킹 : `X = 함수()` -> `X = (a,b,c)`
  - 튜플 언패킹 : `a,b,c = X`

- 다양한 초기화 방법 (튜플 효과)

  - ```python
    a,b,c=[None]*3
    a,b=100,200
    a=100; b=200; c=300
    ```

<br>

<br>

## Dictionary와 Set 같이 보기

**Dict, Set 둘다 {}를 사용하며 순서가 없다!**

* 표현식을 key:value 형태로 적고 {} 로 감싸주면 dict
* 표현식을 value 형태로 적고 {} 로 감싸주면 set

<br><br>

### Dictionary

- 생성: `dict()` 또는 `{key:val}`

- 제거: `del(dict[key])`, `pop(key)`

- 추가: `dict = 'a'` → `{10: 'a'}`

- 조회: `dict[key]` 또는 `get(key)`

  * dict[key] 는 KeyError 발생시키지만

  * get(key) 는 None 을 반환!
  * 특히, in 키워드 사용시 key를 검색함!


- **자주 사용**

  * `dict.keys(), dict.values(), dict.items()`
    * 각각 key, value, key:value 반환하며 리스트 형태로 반환

  * 정렬할 때 `import operator` 사용!

    **sorted(리스트 데이터, 정렬방법 키 설정)**

    ```python
    s = {'학번':123, '순번':456, '번호':789} # dict
    result = sorted(s.items(), key=operator.itemgetter(0)) # 정렬!
    ```

    * ('학번',123) 의 0번째 index인 '학번' 키를 기준으로 정렬!

    ```python
    s=[ {"name": "jane", "age": 22, "grade": 'A'}, {"name": "dave", "age": 32, "grade": 'B'} , {...} ]
    result = sorted(s, key=operator.itemgetter('age'))
    ```

    * index가 아닌 직접 key 설정 가능 -> 'age' 키를 기준으로 정렬!

    ```python
    `result = sorted(s, key = lambda x : x[1])`
    ```

    * 람다 함수로도 간편히 설정 가능!
    * 이것은 1번째 index 자리의 키를 기준으로 정렬!


<br><br>

### Set

**집합처럼 중복값이 없음!**

- 생성: `set()` 또는 `{val}`
- 추가: `add()`
- 연산: `set1 & set2` (교집합), `set1 | set2` (합집합)
- **set(list)로 값의 종류를 구할 때 유용 (중복 제거하니까)**

<br>

<br>

## 고급 기능 ⚡

**람다, 리스트 함축, 함수, 패키지와 모듈, iterable 과 iterator 를 소개**

<br><br>

### Lambda, List Comprehension

**람다 함수 사용**

* `X = (lambda x,y : x+y)(100,200)` -> 300 출력

* `t = (100, 200, 300); X = (lambda x : x[0])(t)` -> 100 출력

<br>

**리스트 함축 사용 -> [...], {...}**

* 3가지: `[표현식 for문], [표현식 for문 if], [표현식 if else for문]`
* `[x**2 for x in [1,2,3,4,5]]` + if 를 바로 뒤에 붙이기 가능
  * 단, else도 붙이고 싶으면 앞에 붙여야 함
  * `[x if x%3==0 else 0 for x in range(1,21)]`

<br><br>

### Function

**함수의 주소..값..전달.. 살펴보자**

- `id(var)`: 주소같은 것을 제공! 

* 얕은복사, 깊은복사
  * alist = [...] 선언후 blist = alist 하면? 같은 "주소"를 가짐 (얕은복사)
  * 만약 blist = list(alist) 나 blist = alist[:] 는 다른 "주소"를 가짐 (깊은복사)
  
* 리스트, 튜플 등으로 함수에 전달하면 "주소"를 전달
* **"값" 을 함수에 전달하면 "값" 전달**
* **함수가 "return 변수"를 반환하면 해당 변수의 "주소"를 반환**

<br>

**동적으로 매개변수 받을 수도 있다. + 기본값 설정도 가능**

- `def 함수(*args, **kwargs):`

* 가변 인자: `*args` (tuple), `**kwargs` (dict)

- 기본값 지정: `def 함수(A=True):`

<br>

**다른 파일에서 import를 하면 함수들이 자동 실행이 되는데 이를 방지하는 방법이 있다.**

- `if __name__ = '__main__':` 로 메인 함수를 지정하면 다른 파일에서 import 할 때 실행이 되지 않는다는 장점이 있다.
- 즉, 직접 해당 파일을 실행해야만 이 메인 함수가 구동이 되는 원리이다.

<br><br>

### 패키지와 모듈

**패키지는 모듈의 묶음이다. 그리고 모듈은 함수의 집합이다.****

* Module.py 라는 다양한 함수로 구성 된 모듈이 있다고 가정하자
* `import Module`: 해당 파일의 함수 전부 import !!
  * 사용형태: Module.함수()
* f`rom Module import 사용할 함수`: 해당 파일의 원하는 함수를 import
  * 사용형태: 함수()
* import 패키지.모듈 -> 이렇게 하나씩 체인형태로 접근 한다.

<br><br>

### Iterable과 Iterator + Generator

**iterable은 iterator로 변환 가능한 형태로 생각하면 됨**

**iterator는 next() 같은 것을 제공하는 장점**

**Generator 타입도 iterable 형태인데, 값이 필요할 때 마다 메모리에서 생성 후 반환하는 장점**

- `list`, `tuple`은 iterable 형태 -> iterator로 변환 가능
