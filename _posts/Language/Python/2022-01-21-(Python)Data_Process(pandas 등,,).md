---
title:  "[Python]Data_Process(정규식,json,datetime 등)"
categories : PY
tag : [파이썬, pandas, re, json, datetime]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---


## 다양한 데이터 가공 방식



### 1. re(정규식) : 원하는 형태



#### re 사용법

**기본 import**

```python
import re
```

**다양한 사용법**

* p = re.compile("원하는 형태")

  * . (ca.e) : 하나의 문자를 의미 > care, cafe, case.. | caffe X

  * ^ (^de) : 문자열의 시작 > desk, destiniation | fade X
  * $ (se$) : 문자열의 끝 > case, base | face X

  ```python
  # ca?e
  # care, cafe, case, cave ...
  # caae, cabe, cace, cade, ...
  p = re.compile("ca.e")
  => ca.e 사용시 여기 주석들 전부 매칭 되겠죠.
  ```

* m = p.match("비교할 문자열") \# match : 주어진 문자열의 처음부터 일치하는지 확인

  ```python
  m = p.match("careless")
  print(m)
  #### 출력화면
  <re.Match object; span=(0, 4), match='care'>
  ```

* m = p.search("비교할 문자열")  \# search : 주어진 문자열 중에 일치하는게 있는지 확인

  ```python
  m = p.search("careless")
  # 출력 동일
  ```

* lst = p.findall("비교할 문자열") \# findall : 일치하는 모든 것을 리스트 형태로 반환

  ```python
  lst = p.findall("good care cafe")
  # 리스트로 출력 => ['care', 'cafe']
  ```

**사용 예시**

* 간단한 출력 함수 선언

  ```python
  def print_match(m):
      if m:
          print("m.group():", m.group()) # 일치하는 문자열 반환
          print("m.string:", m.string) # 입력받은 문자열 (string은 함수가아니라 변수라서 괄호X)
          print("m.start():", m.start()) # 일치하는 문자열의 시작 index
          print("m.end():", m.end()) # 일치하는 문자열의 끝 index
          print("m.span():", m.span()) # 일치하는 문자열의 시작 / 끝 index
      else:
          print("매칭되지 않음")
  ```

* 출력

  ```python
  p = re.compile("ca.e") 
  m = p.match("careless")
  print_match(m)
  m = p.search("careless")
  print_match(m)
  lst = p.findall("good care cafe")
  print(lst) # 위에서 본 출력과 동일하게 리스트로.
  
  ### 출력화면(1개만 적어둠.)
  m.group(): care
  m.string: careless
  m.start(): 0
  m.end(): 4
  m.span(): (0, 4)
  ```



### 2. datetime?

- datetime이란 날짜 및 시간을 구하고 싶거나 날짜 및 시간 형식의 어떤 것을 만들고 싶을 때 사용하는 객체. 아래처럼 따로 모듈을 추가하여 사용함.

```python
from datetime import datetime
# 'datetime 모듈을 datetime이라는 이름으로 사용하겠다.'

# 지금 현재 날짜와 시간 가져오기
now = datetime.now()
print(now)

# 만들어진 datetime객체를 년-월-일 형식의 string으로 변환하기
now = strftime('%Y-%m-%d')
print(now)

# string으로 변환된 날짜를 다시 datetime객체로 만들기
now = strptime(now, '%Y-%m-%d')
print(now)
```

- 형식은 %Y-%m-%d 말고도 많음! 대문자인지 소문자인지에 따라 또 다른 점이 있음. 궁금한 사람은 따로 찾아보기.
- datetime → string 의 경우 **str 'f' time**, string → datetime의 경우 **str 'p' time**임에 유의하기.



### 3. JSON



#### 기본 준비

```python
import json
# application/json 로 꼭 변경해야 합니다.
print("Content-type:application/json;charset=utf-8\r\n")
```



#### 사용법

**예시**

* 준비

  ```python
  ...
  notice = cur.fetchall() # 로 가져온 데이터를 (dict 형식으로 가져옴.)
  
  json_Notice = [] # 최종 출력할 변수
  json_Notice_Temp = []
  
  for page in range(1,4): # 1~3page 이런식으로 사용
      if page == 1:
      	for i in notice[0:29]: # 1page
  ```

* 내부내용

  * 바로 위의 for문 내부

    ```python
    #### 바로 위의 for문 안에..
    # notice는 dict형식임.
    # for문으로 1개씩 접근해서 'key'값 입력해서 'value'값 가져올거임. (1개씩 데이터이름이 'i')
    # 가져온 'value'값을 새로운 dict형식으로 새로만든 'key'값에 넣어서 json_Notice_Temp에 append
    
    json_Notice_Temp.append({'id':i['notice_No'],'title':i['notice_Title'],'게시자':i['notice_Writer'],'작성일':i['notice_Day'],'조회':i['notice_Lookup'],'address':i['notice_link']})
    ```

  * 전체 for문 내부 (elif page == 2: 와 elif page == 3: 는 생략)

    ```python
    json_Notice.append({page:json_Notice_Temp}) # page를 키값으로 가지고, 뒤에 데이터들 dict으로 저장
    json_Notice_Temp = [] # 다시 초기화 (다음 페이지를 위해)
    ```

  * for문 벗어나서 (json.dumps는 코드 정렬해주는 함수)

    ```
    json_Notice = json.dumps(json_Notice, indent=4, ensure_ascii=False) 
    # True로하면 아스키코드로 출력, indent는 들여쓰기
    print(json_Notice)
    ```



## 응용



### 공백 없애기

```python
cal_Date = re.sub(r'\\s+', '', cal_Date)
# 정규식사용. 모르는 사람은 검색해서 공부해보기.
# replace 사용해도 됨! 차이점 궁금하면 직접 검색해보기.
print(cal_Date)
```



### 날짜의 형식을 변경해 가공하기

- 굳이 해당 날짜를 string → datetime → string 하는 이유:

  * table의 날짜를 보면 month 및 day가 한 자리인 경우 앞에 0 없이 적혀 있는데, 우리는 앞자리를 채워서 모두 같은 자리가 되도록 해 줄 것이기 때문. (즉, 2 => 02 로!!)

    ```python
    # string -> datetime 변환
    cal_First_Datetime = datetime.strptime(cal_First_Text, '%Y-%m-%d')
    print(cal_First_Datetime)
    
    # datetime -> string 변환
    cal_First_Text = cal_First_Datetime.strftime('%Y-%m-%d')
    ```

* 요일 가공 방식

  * weekday: 해당 datetime 객체의 날짜에 맞는 요일에 따라 숫자를 반환함.

  * 월: 0 화: 1 수: 2 ... 일: 6 순서.

  ```python
  # 요일 반환
  cal_Week1 = cal_First_Datetime.weekday()
  
  # 요일을 쉽게 꺼내 붙이기 위해 코드 상단에 요일만 들어있는 list를 선언한다.
  week = ['(월)', '(화)', '(수)', '(목)', '(금)', '(토)', '(일)']
  cal_First_Text += week[cal_Week1]
  ```

  
