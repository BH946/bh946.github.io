---
title:  "[Python] 데이터 가공 따라하기(정규식,json,datetime)"
categories : PY
tag : [파이썬, pandas, re, json, datetime, python, data-processing, regex, 데이터가공, 정규식, 날짜처리]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



**Python의 re, datetime, json 모듈을 활용하여 데이터를 효과적으로 가공하고 처리할 수 있다**

<br>

<br>

## 정규식(re) 활용 📝

**기본 패턴**

```python
import re

# 패턴 정의
p = re.compile("ca.e")  # 패턴 객체 생성

# 주요 메서드 사용
m = p.match("careless")
print(m)
    
# 출력 예시: 
<re.Match object; span=(0, 4), match='care'>
```

<br>

**주요 메서드**

- `match()`: 문자열 처음부터 패턴 매칭
- `search()`: 문자열 전체에서 패턴 매칭
- `findall()`: 모든 매칭을 리스트로 반환 -> ex: ['care', 'cafe']

<br>

**패턴 문자**

- `.`: 하나의 문자 매칭 (예: ca.e → care, cafe)
- `^`: 문자열 시작 (예: ^de → desk)
- `$`: 문자열 끝 (예: se$ → case)

<br>

**매치 객체 메서드**

```python
def print_match(m):
    if m:
        print("m.group():", m.group())  # 매칭된 문자열
        print("m.string:", m.string)    # 입력 문자열
        print("m.start():", m.start())  # 매칭 시작 위치
        print("m.end():", m.end())      # 매칭 끝 위치
        print("m.span():", m.span())    # (시작, 끝) 튜플
```

<br>

**응용: 공백 없애기**

```python
cal_Date = re.sub(r'\\s+', '', cal_Date) # 중간의 '' 는 공백을 의미
print(cal_Date)
```

<br>

<br>

## datetime?

**기본 사용법**

- 형식은 %Y-%m-%d 말고도 많음! 대문자인지 소문자인지에 따라 또 다른 점이 있음. 궁금한 사람은 따로 찾아보기.
- datetime → string 의 경우 **str 'f' time**, string → datetime의 경우 **str 'p' time**임에 유의하기.

```python
from datetime import datetime
# 'datetime 모듈을 datetime이라는 이름으로 사용하겠다.'

# 지금 현재 날짜와 시간 가져오기
now = datetime.now()
print(now)

# 문자열 변환
str_date = now.strftime('%Y-%m-%d')  # datetime → 문자열
date_obj = datetime.strptime(str_date, '%Y-%m-%d')  # 문자열 → datetime
```

<br>

**응용: 날짜 형식 변환**

```python
# 한자리 월/일을 두자리로 변환 (2 → 02)
# string → datetime → string 로 두자리 변환 하는 원리
cal_First_Datetime = datetime.strptime(cal_First_Text, '%Y-%m-%d')
cal_First_Text = cal_First_Datetime.strftime('%Y-%m-%d')

# 요일 처리
week = ['(월)', '(화)', '(수)', '(목)', '(금)', '(토)', '(일)']
weekday = cal_First_Datetime.weekday()  # 0(월) ~ 6(일)
cal_First_Text += week[weekday]
```

<br>

<br>

##  JSON

**기본 설정**

```python
import json
print("Content-type:application/json;charset=utf-8\r\n")
```

<br>

**데이터 처리**

```python
# JSON 데이터 준비
json_Notice = []  # 최종 출력용
json_Notice_Temp = []  # 임시 저장용

# 페이지별 데이터 처리
for page in range(1,4): # 1~3 페이지 처리
    if page == 1:
        for i in notice[0:29]: # 첫 페이지 29개 항목 처리
            json_Notice_Temp.append({
                'id': i['notice_No'],
                'title': i['notice_Title'],
                '게시자': i['notice_Writer'],
                '작성일': i['notice_Day'],
                '조회': i['notice_Lookup'],
                'address': i['notice_link']
            })
    
    # 페이지 데이터 저장
    json_Notice.append({page: json_Notice_Temp})
    json_Notice_Temp = []  # 다음 페이지를 위한 초기화

# JSON 변환 및 출력
json_Notice = json.dumps(json_Notice, indent=4, ensure_ascii=False) # indent:들여쓰기 4칸
print(json_Notice)
```

<br>

**결과 구조 json**

```json
[
    {
        "1": [
            {
                "id": "notice_1",
                "title": "첫번째 공지",
                "게시자": "관리자",
                "작성일": "2024-01-03",
                "조회": "100",
                "address": "http://..."
            },
            // ... 추가 항목들
        ]
    },
    // 페이지 2, 3도 동일한 구조
]
```

