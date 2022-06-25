# 서버 스터디 정리(디스이즈 동아리)



## 0. 디스이즈 서버 방식

**html파싱 -> JSON변환 -> 어플리케이션 출력**

**정보의 지속적인 최신화 및 정확성 확인 : cron을 이용**



## 1. 서버 프로그램 정리



**1. FileZilla**

* 파일 공용으로 관리. (실제 사용 소스들 보관 및 사용)

**2. Bitnami WAMP Stack**

* ? 용도를 잘 모르겠음.

**3. PuTTY**

* Mysql 사용 함.
  * mysql -uroot -p 이건 기억해두기

**4. Sublime Text3 or VS-code**

* VS-code 사용하겠음.



## 2. 로컬과 서버 차이



#### 1. 로컬환경

**내 컴퓨터 안에서만 실행하는 것 == localhost**

* 웹에서 실행할 때 : 인터넷 주소창에 localhost/파일명+확장자



#### 2. 서버환경

**FileZilla or Putty 사용해서 서버 컴퓨터에 원격으로 접속**

* 리모트 사이트 '/var/www/html/...'
* 웹에서 실행할 때 : 인터넷 주소창에 '디스이즈 서버 접속주소/자기폴더이름/파일명+확장자'
* FileZilla에 새 파일 넣었을 때
  1. 마우스 우클릭
  2. 파일권한 탭 선택
  3. 755로 설정
  4. VS-code의 경우 파일소스 들어가서 반드시 'LF'로 설정(동아리에서 사용하는 환경때문)



## 3. Python

**반드시 들여쓰기 확인!! 주의**

#### 1. Python 사용 이유

- 간단한 문법
- 최근 가장 각광받는 언어
- 많은 라이브러리
- 많은 사람이 사용하는 만큼 자료가 많음
- 등

#### 2. 다른 언어와 Python이 다른 점

- ; 안씀
- 들여쓰기로 블럭 구분
- for문에서 range함수로 범위 지정
- for, if에서 tap으로 단계를 구분함. 즉, {}로 안함
- 특정한 자료형 선언 X. 즉, 동적인 자료타입
- 한줄 주석: #, 여러 줄 주석: ''' ''' or """ """
  * Ctrl + '/' 로 간단히 주석처리
- 특정 변수 자료형 체크 시 type() 함수 사용
- 배열은 보통 list 형식으로 주로 사용. 즉, [] or list() 사용도 편함.

#### 3. 모든 .py 파일 맨 위에 들어가야 하는 코드

```python
#!/usr/bin/python3 : 파이썬 설치 경로
# -*- coding: utf-8 -*- : 파일의 인코딩 타입
print("Content-type:text/html;charset=utf-8\\r\\n") 
# http 프로토콜(통신규약)에서 header과 body의 구분을 위해. 구분은 개행으로 하기 때문에 위의 줄이 반드시!! 필요함.

# text/html -> 일반 html
# application/json -> json 출력 시

#######################################################

import sys
# sys이용한 경로 사용
import codecs

import cgi
# cgi프로그램에서 사용되는 모듈
import cgitb
# cgi프로그램 디버깅에 관한 모듈. 에러를 웹 화면에 출력 해 준다.
import requests
# http 요청을 보내는 모듈. URL post, get 요청을 받아올 수 있게 함
import json
# json으로 인코딩 및 디코딩 해줌
import re
# 띄어쓰기(공백)제거, 정규표현식 사용
from bs4 import BeautifulSoup
# HTML 파싱
from datetime import datetime
# 서버시간 변환

import pymysql
# mysql
sys.path.insert(0,'/var/www/html/thisis_py/db')
from db_proc import db
# 우리 서버의 mysql 연결용 파일 경로를 표시 -> 연결 코드는 안 써도 됨.

sys.stdout = codecs.getwriter("utf-8") (sys.stdout.detach())
cgitb.enable()
# 에러 웹 화면에 출력
# cgitb.enable(display=0, logdir='파일경로')는 파일에 출력함
```

#### 4. 기초문법

```
# dict말고는 많이 보던것이여서 dict만 추가
# dict변수.keys() 와 .items()만 챙기면 됨.
temp_Dict = dict()

temp_Dict_Already = {
	"a":"A",
	"b":"bbbbb",
	"c": 123,
	"d": temp_Array
}

print(temp_Dict_Already["d"]) # 하나만 출력

for key in temp_Dict_Already.keys(): # Dict에서 key만 뽑아내기 -> key로 value 조회
	print("<br>", key, ":", temp_Dict_Already[key])

for key, value in temp_Dict_Already.items(): # Dict key, value 모두 조회
	print("<br>", key, ":", value)
```



##  4. GET과 POST



#### 1. GET?

- 웹으로 요청을 전송할 때 url 끝에 ? 나 & 문자를 사용해 데이터를 전송.
- 데이터가 눈에 보이기 때문에 조회를 하는 행위에서 보통 사용.

#### 2. POST?

- GET과 달리 url로 데이터를 전송하지 않음.
- 데이터가 눈에 보이지 않아 '비교적' 안전.
- 무조건적으로 안전한 것은 아니기 때문에 민감한 데이터는 따로 암호화 할 필요가 있다.



## 5. HTML → .py로 GET, POST하기   [form.py]



```python
form = cgi.FieldStorage()
# form이라는 변수에 앞의 페이지에서 전송한(form태그로 전송한) form데이터들을 저장함.

name = form.getvalue('name')
student_Num = form.getvalue('studentNum')
user_Id = form.getvalue('id')
user_Pw = form.getvalue('pw')
fruit = form.getvalue('fruit')
subject = form.getvalue('subject')
elec = form.getvalue('elec')
# 각각의 변수에 앞의 name들로 value를 받음

print("이름: ", name, "<br>")
print("학번: ", student_Num, "<br>")
print("ID: ", user_Id, "<br>")
print("PW: ",user_Pw, "<br>")
print("선호하는 과일: ", fruit, "<br>")
print("선호하는 과목: ", subject, "<br>")
print("선호하는 전자기기: ", elec, "<br>")
```



## 6. py에서 mysql 사용하기



#### 1. py에서 mysql 사용하기 - 연동

```python
import pymysql
sys.path.insert(0,'/var/www/html/thisis_py/student_info')
from get_state_proc import GET_STATE
# 필수!!
```



#### 2. py에서 mysql 사용하기 - INSERT, UPDATE, DELETE

```python
cur = db.cursor()
sql = "INSERT INTO test (name) VALUES ('apple')"
# UPDATE, DELETE 모두 이런 식으로 사용할 수 있음.
cur.execute(sql) # sql 적용.
db.commit() # 현재 상태를 db에 저장한다.

db.close() # db와 연결을 끊는다.
```



#### 3. py에서 mysql 사용하기 - SELECT

```python
cur = db.cursor(pymysql.cursors.DictCursor)
# INSERT, UPDATE, DELETE와 다름.
# Dict 형태로 결과값을 가져오겠다는 뜻.
sql = "SELECT * FROM test"
cur.execute(sql)

results = cur.fetchall()
# 모든 결과값을 받아옴

# cur.fetchone()은 받아온 결과중 한 행만 가져옴.
# fetchone을 실행한 후 다시 실행하면 받아온 행의 바로 다음 행을
# 자동으로 받아옴.

for result in results:
	print("name: ", result['name'], "<br>")
	print("age: ", result['age'], "<br>")
	print("major: ", result['major'], "<br>")

# 받아온 한 행에 대한 열이름 'name', 'age', 'major'에 해당하는 값을
# 가져와 출력함.

db.commit()
db.close()
```



## 7. 파싱하기(requests + beautifulsoup 모듈 사용!)



#### 파싱 코드

```
try:
	response = requests.get('~url~', timeout = 15)
	# get 방식으로 웹서비스를 호출한다. 지정한 시간(15)를 초과하면
    # 잠깐 sleep 후 몇 회 반복을 시도함.
	response.encoding = None
	# encoding 방식 None으로 설정
except requests.exceptions.Timeout:
	print('Timeout')
else:
	# 성공적인 웹서비스 호출 후 실행할 코드
	html = response.text
    # html 코드 받아오기
    soup = BeautifulSoup(html, 'html.parser', from_encoding = 'utf-8')
    # html을 html.parser(구문 해석기)로 utf-8 encoding방식으로 파싱
	# 아래 find메소드 이용한 원하는 부분 파싱은 생략.
```



## 8. datetime?

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



## 9. data들을 DB에 넣기 위해 가공하기 (중요!)

* 다양한 방법들을 알고있지만, re모듈(정규식)이용과 datetime모듈 이용하는 부분 정리하겠다.

#### 공백 없애기

```python
cal_Date = re.sub(r'\\s+', '', cal_Date)
# 정규식사용. 모르는 사람은 검색해서 공부해보기.
# replace 사용해도 됨! 차이점 궁금하면 직접 검색해보기.
print(cal_Date)
```



#### 날짜의 형식을 변경해 가공하기

- 굳이 해당 날짜를 string → datetime → string 하는 이유:

  * table의 날짜를 보면 month 및 day가 한 자리인 경우 앞에 0 없이 적혀 있는데, 우리는 앞자리를 채워서 모두 같은 자리가 되도록 해 줄 것이기 때문. (즉, 2 => 02 로!!)

    ```
    # string -> datetime 변환
    cal_First_Datetime = datetime.strptime(cal_First_Text, '%Y-%m-%d')
    print(cal_First_Datetime)
    
    # datetime -> string 변환
    cal_First_Text = cal_First_Datetime.strftime('%Y-%m-%d')
    ```

* 요일 가공 방식

  * weekday: 해당 datetime 객체의 날짜에 맞는 요일에 따라 숫자를 반환함.

  * 월: 0 화: 1 수: 2 ... 일: 6 순서.

  ```
  # 요일 반환
  cal_Week1 = cal_First_Datetime.weekday()
  
  # 요일을 쉽게 꺼내 붙이기 위해 코드 상단에 요일만 들어있는 list를 선언한다.
  week = ['(월)', '(화)', '(수)', '(목)', '(금)', '(토)', '(일)']
  cal_First_Text += week[cal_Week1]
  ```

  

## 10. JSON



#### 기본 준비

```
import json
# application/json 로 꼭 변경해야 합니다.
print("Content-type:application/json;charset=utf-8\r\n")
```



#### 사용법

**예시**

* 준비

  ```
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

    ```
    #### 바로 위의 for문 안에..
    # notice는 dict형식임.
    # for문으로 1개씩 접근해서 'key'값 입력해서 'value'값 가져올거임. (1개씩 데이터이름이 'i')
    # 가져온 'value'값을 새로운 dict형식으로 새로만든 'key'값에 넣어서 json_Notice_Temp에 append
    
    json_Notice_Temp.append({'id':i['notice_No'],'title':i['notice_Title'],'게시자':i['notice_Writer'],'작성일':i['notice_Day'],'조회':i['notice_Lookup'],'address':i['notice_link']})
    ```

  * 전체 for문 내부 (elif page == 2: 와 elif page == 3: 는 생략)

    ```
    json_Notice.append({page:json_Notice_Temp}) # page를 키값으로 가지고, 뒤에 데이터들 dict으로 저장
    json_Notice_Temp = [] # 다시 초기화 (다음 페이지를 위해)
    ```

  * for문 벗어나서 (json.dumps는 코드 정렬해주는 함수)

    ```
    json_Notice = json.dumps(json_Notice, indent=4, ensure_ascii=False) 
    # True로하면 아스키코드로 출력, indent는 들여쓰기
    print(json_Notice)
    ```



## 11. 마지막 로그인 필요한 사이트 requests로 접속

* F12에서 네트워크 부분에서 확인할 수 있으며, 로그인시 필요한 정보가 복잡한 경우도 있는데 우리 사이트의 경우 GET_STATE()함수로 따로 만들어 놈.
  * 네트워크 부분에 header, body(login) 부분 다 있음. 여기 내용을 requests세션에 추가.

```
# 로그인 정보
url='https://student.donga.ac.kr/Login.aspx'
viewstate,viewstategenerator,eventvalidation=GET_STATE(url)

try:
    userid = '실제 아이디 입력'
    passwd = '실제 비밀번호 입력'
except:
    print(1)
else:
    header={
        'Referer':'https://student.donga.ac.kr/Login.aspx',
        'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.190 Safari/537.36'
        }
    login={
        viewstate['name'] : viewstate['value'],
        viewstategenerator['name'] : viewstategenerator['value'],
        eventvalidation['name'] : eventvalidation['value'],
        'txtStudentCd' : userid,
        'txtPasswd' : passwd,
        'ibtnLogin.x' : '0',
        'ibtnLogin.y' : '0'
        }

    session = requests.session()
    session.post(url, headers=header, data=login)

    # 전체성적
    url = 'https://student.donga.ac.kr/Univ/SUH/SSUH0011.aspx?m=6&rbtn=1'
    response = session.get(url)
```





