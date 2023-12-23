---
title:  "[Python]Basic(기초문법) - 추가정리(최신1)"
categories : PY
tag : [파이썬, 문법]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



## Intro

**Python 추가로 정리하고 싶은 문법들 작성**

* List, Tuple, Dict, Set, Func 등등..

<br><br>

## List

**생성 : `list(), []`** 

**메소드**

* 원본 변경X : append, extend
  * append([val]) -> [val] 통째로 삽입
    * 또다른 방법 : a[1] = [val]
  * extend([val]) -> val 로 삽입
    * 또다른 방법 : a[1:2] = [val]
  * sorted() -> 정렬
  * index(val), count(val) 등등...
* 원본 변경O : insert(idx,val), pop(idx), remove(val), del(var)
  * remove : val 여러개면 앞에것부터 제거
  * pop : idx 지정안하면 맨 뒤부터 제거
  * del(변수명) : 변수명을 메모리에서 지움
    * 없는 변수이면 예외터지기 때문에 보통 if in 문으로 확인 먼저하는것을 권장
  * sort(), reverse() -> 오름차순, 내림차순 정렬
* zip(list1, list2)
  * list1=[1,2,3,4], list2=[11,22,33] 이라면
  * zip(list1, list2) -> [(1,11),(2,22),(3,33)]

<br>

**`a[start:end:step]`**

* a=[1,2,3,4,5] 가정
* a[::-2] -> [5,3,1]
* a[::2] -> [1,3,5]
* a[-2::-2] -> [4,2]
* a[-2::-1] -> [4,3,2,1]
* a[0::-2] -> [1]
* 원리는 start와 end지점을 먼저 찾고, 해당 지점에서 부터 step 에 맞게 이동한다.
  * 단, [::] 는 전체 범위를 의미하므로 step이 음수면 맨뒤부터 출력!

<br><br>

## Tuple

**튜플은 요소 변경이나 삭제가 불가능한 읽기전용**

**생성 : `tuple(), ()`**

* `b=1,2,3,4` : 자동으로 `(1,2,3,4)` 튜플로 반환
* 1개 일때 주의!
  * b=(1) -> int
  * b=(1,) -> tuple

<br>

**요소 변경, 삭제 하고싶을때**

* tuple(list) <-> list(tuple)

<br>

**튜플 패킹, 언패킹**

* 함수의 반환이 return a,b,c 라 가정
* 튜플 패킹 : `X = 함수()` -> `X = (a,b,c)`
* 튜플 언패킹 : `a,b,c = X`

<br>

**다양한 초기화 방법들(튜플 덕분)**

* a,b,c=[None]*3
* a,b=100,200
* a=100; b=200; c=300

<br><br>

## Dictionary + Set

**Dict, Set 둘다 {}를 사용하며 순서가 없다.**

**생성 : `dict(), {key:val}`**

**보통 제거 메소드 : `del(dict[key]), pop(key)`**

**보통 추가할 때 : `dict[10] = 'a' -> {10 : 'a'}`**

**보통 조회할 때 : `dict[key]` or `get(key)`**

* dict[key] 는 KeyError 발생시키지만
* get(key) 는 None 을 반환!

<br>

**특히, in 사용시 key를 검색!**

**자주 사용**

* dict.keys(), dict.values(), dict.items()
  * 각각 key, value, key:value 반환하며 리스트형태로 반환
* import operator 사용! -> 정렬할때 !
  * s={'학번':123, '순번':456, '번호':789}
  * sorted(리스트데이터, 정렬방법 키설정)
  * **`result = sorted(s.items(), key = operator.itemgetter(0))`**
    * ('학번',123) 의 0번째 index인 '학번' 키를 기준으로 정렬!
    * 즉, index로 설정하면 해당 index 자리의 키를 기준!!
  * s=[ {"name": "jane", "age": 22, "grade": 'A'}, {"name": "dave", "age": 32, "grade": 'B'} , {...} ]
  * **`result = sorted(s, key=operator.itemgetter('age'))`**
    * index가 아닌 직접 key 설정 가능 -> 'age' 키를 기준으로 정렬!
  * `result = sorted(s, key = lambda x : x[1])`
    * 람다 함수로도 간편히 설정 가능!
    * 이것은 1 index 자리의 키를 기준으로 정렬!

<br><br>

## Set

**집합처럼 중복값이 없음**

**생성 : `set(), {val}`**

**값 추가 : add()**

**set1 & set2 -> 교집합**

**set1 \| set2 -> 합집합**

**set(list)로 값들의 종류같은것을 구할때 유용**

<br><br>

## Lambda, List Comprehension

**람다 함수 사용**

* `X = (lambda x,y : x+y)(100,200)` -> 300

* `t = (100, 200, 300); X = (lambda x : x[0])(t)` -> 100

<br>

**리스트 함축 사용 -> [...], {...}**

* **`[표현식 for문], [표현식 for문 if], [표현식 if else for문]`**

* `[x**2 for x in [1,2,3,4,5]]` + if 뒤에 붙이는것 가능
  * 단, else도 붙이고 싶으면 앞에 붙여야함
  * `[x if x%3==0 else 0 for x in range(1,21)]`
* {} 로 dict, set 형태로 반환도 가능함
  * 표현식을 key:value 형태로 적고 {} 로 감싸주면 dict
  * 표현식을 value 형태로 적고 {} 로 감싸주면 set

<br><br>

## Function

**파이썬도 주소같은것을 제공하며 `id(var)` 함수로 확인 가능**

**주소..값..전달..**

* alist = [...] 선언후 blist = alist 하면?
  * 같은 "주소"를 가짐 (얕은복사)
  * 만약 blist = list(alist) 나 blist = alist[:] 는 다른 "주소"를 가짐 (깊은복사)
* 리스트, 튜플 등으로 함수에 전달하면 "주소" 전달
* 그냥 값 을 함수에 전달하면 "값" 전달
* 함수가 return 변수 를 반환하면 해당 변수의 "주소" 반환

<br>

**`def 함수(*args, **kwargs):`**

* 동적으로 매개변수 받을 수 있다.
* `*args : tuple`
* `**kwargs : dict`
* **또한, `def 함수(A=True):` 처럼 기본값을 매개변수에 지정가능**

<br>

**패키지는 모듈의 묶음**

**모듈은 합수의 집합**

* Module.py -> 모듈
* import Module -> 해당 파일의 함수 전부 import
  * 단, Module.함수() 이렇게 사용
* from Module import 함수 -> 해당 파일의 원하는 함수들 import
  * 함수() 바로 사용
* import 패키지.모듈 -> 이렇게 하나씩 접근해나가면 됨

<br>

**`if __name__ = '__main__':`**

* 메인 함수로 보면되고, 다른 파일에서 import 할때 실행이 되지 않는다는 장점이 있다.
* 매개변수

<br>

**list, tuple 등등은 iterable 형태로써 iterator로 변환 가능하다.**

* iterator는 next() 같은것을 제공
* iterable은 iterator로 변환 가능한 형태로 생각하면 된다.

* Generator 타입도 iterable 형태인데, 값이 필요할 때 마다 메모리에서 생성후 반환하는 장점이 있다.
