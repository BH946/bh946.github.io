---
title : ,,
---

## .py에서 mysql사용하기위해?

```python
import pymysql
sys.path.insert(0,'/var/www/html/thisis_py/db')
from db_proc import db
# 로 3줄 필요.
```

그리고 **db_proc .py**의 내부는?

```python
import pymysql
db = pymysql.connect(host="127.0.0.1", user="xxx", passwd="xxx", db="xxx", charset='utf8')
```

즉, 여기있는 db변수를 가져와 사용중.  
해당 db변수에는 xxx이름의 database를 가짐.

<br>

## form을 통해 데이터 주고받을 때



**get 방식으로 이렇게 보내면?**

```python
print(f'<a class="list" href="아무거나.py?name={name}&poster={poster}">')
```



**get 방식을 잘 가져옴**

```python
form = cgi.FieldStorage() # form 데이터 가져와 담음
name = form["name"].value
poster = form["poster"].value
```

<br>

## 기본 sql 사용방식

```python
cur = db.cursor() # 커서 반환
# 만약 cur = db.cursor(pymysql.cursors.DictCursor)란? Dict 형태로 결과값 가져오겠다는 뜻
sql = "insert into ~~~"
cur.execute(sql) # 입력(엔터라고 생각)
cur.fetchall() # 모든 결과값을 받아옴
cur.fetchone() # 한 행만 가져옴. 다시 실행시 그 다음 행만 가져옴.
db.commit() # 현재 상태를 db에 저장
db.close() # db와 연결 종료
```

<br>

## 에러를 웹 or 파일에 출력

```python
import sys
import codecs
import cgi # cgi프로그램에서 사용되는 모듈
import cgitb # cgi프로그램 디버깅에 관한 모듈. 에러를 웹 화면에 출력 해 준다.
sys.stdout = codecs.getwriter("utf-8") (sys.stdout.detach())
cgitb.enable() # 1. 에러 웹 화면에 출력
cgitb.enable(display=0, logdir='파일경로') # 2. 에러 파일에 출력함
```

<br>

## timeout

* timeout 사용 안하면 타임아웃이 발생 안하는것(기본값:None)
* timeout=15로 사용하면, 15초 뒤에 잠시 sleep후 몇 회 반복을 시도함.
  * => timeout=(3,5)는? 연결 대기시간: 3초, 읽기 대기시간: 5초. 
    => timeout=15는? 연결,읽기 대기시간 15초.

```python
try: 
    # requests.get("http...", timeout=15) 하는 부분
    response.encoding = None # encoding 방식 None으로 설정
except requests.exceptions.Timeout: # 시간이 많이 지체되어 timeout발생시
else: # 코드 실행부분
```

<br>

## 파싱중 유용한 데이터 가공 함수들

* strip() : 특정 문자 제거
* 등등..

<br>

## 개발과정에서 앱 대신 html을 통해 테스트

* 앱에서 서버에 데이터 요청 가정을 위해 post_test.html같이 테스트 html만들어서 form태그로 넘겨 테스트하자.
* (서버)응답 받는 test.py에서 form = chi.FieldStorage()를 통해 form태그로 전송받은 데이터들 사용하면서 테스트 하면된다.

<br>

## form.action

참고로 버튼 태그에서 onClick내부에 send함수이후 return false를 통해서 이벤트 끝낸것이다.  
return true의 경우 내장되어있는 이벤트 함수 실행할 것이다.(form.action 실행하는 submit이벤트 함수 실행)   
따라서 여기선 따로 함수로 submit 이벤트 함수역할로 대신 실행했기 때문에 마무리로 false를 리턴.

* form 태그의 action속성을 ""으로 주고, submit때 원하는 경로로 action보내기 위해서
* 아래코드처럼 구성하면 된다. (**각 버튼마다 함수로 나뉜 구조**)

```html
<script type="text/javascript">
    function send(theForm){
        theForm.action = './conference_test.py'
        theForm.submit()
    }

    function attendConfirm(theForm){
        theForm.action = './conference_confirm.py'
        theForm.submit()
    }
</script>

... form태그 내부...
<button type="submit" value="제출하기" onClick="send(this.form); return false">제출하기</button>
<button type="submit" value="불참확인" onClick="attendConfirm(this.form); return false">불참확인</button>
```



* 만약 **하나의 함수**로 action을 구분해서 보내고싶다면 함수 및 form태그 내부도 수정이 필요

```html
<script type="text/javascript">
    function send(num){
        if (num === 0) {
        	theForm.action = './conference_test.py'
            theForm.submit()
        }
        else {
            theForm.action = './conference_confirm.py'
       		theForm.submit()
        }
    }
</script>

... form태그 내부...
<button type="submit" value="제출하기" onClick="send(1); return false">제출하기</button>
<button type="submit" value="불참확인" onClick="send(2); return false">불참확인</button>
```
