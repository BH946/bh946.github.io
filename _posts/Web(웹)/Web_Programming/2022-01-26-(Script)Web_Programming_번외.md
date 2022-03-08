---
title:  "Web_Programming(html, jsp, sql)"
categories : test
tag : [android, github]
author_profile: false
sidebar:
   nav: "docs"
---

**=> 자세한건 slid파일(워드파일) 정리본 보기.**

* 클라이언트 : HTML, CSS, JavaScript 등..
* 서버 : JSP,PHP,ASP,Node.js,Go웹 등..
* DB : SQL 등..
  * DB = 데이터베이스 => S/W, H/W, Language와 다른, 체계/구조적으로 잘 정리된 data들의 집합체
    JDBC = DB를 다루기 위한 자바 API => DBMS 상관없이 SQL문을 던져서 실행하게 해줌.
    즉, Driver 로드(DBMS) -> DB 연결(JDBC) -> SQL문 실행 순서
    SQL은 Language(C언어 같이 언어를 의미.)
    DBMS는 MySQL같은 시스템 => S/W 에 포함.
    참고 : MongoDB는 NoSQL(SQL언어가 아닌 새로운 언어 사용)



## 5. 번외(script)

<script>
alert("미등록 ~"); => 팝업창 띄움
location.href="login.html"; => 다른 페이지로 이동.
history.back() 의 장점은 예전 화면 그대로 돌아간다.(답변 내용같은거 초기화 안되는 장점)
</script> => 자바 스크립트.
=> script에서 쓰는 메소드들

```python
print('''
<script>
	var sessionData = 1;
	sessionStorage.setItem("loginData", sessionData ); // save
	location.href="http://dsistest2021.cafe24.com/thisis/counting_view.py";
</script>
''')
```

=> session 예시(save)

```python
print('''
<script>
	var loginchk = sessionStorage.getItem("loginData");
	if (loginchk != 1) { // loginchk = 0 : fail, loginchk = 1 : success
		// move to Login-Page
        location.href="http://dsistest2021.cafe24.com/thisis/admin/Thisis_ManagementTest.html";
	}
</script>
''')
```

=> session 예시(load)

