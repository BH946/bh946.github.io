---
title : ,,,
---

## HashKey

* **hashlib 라이브러리 import**

```python
import hashlib

h=hashlib.sha256() # hashlib 객체 생성
h.update(passwd.encode('utf-8')) # h객체에 passwd 값을 넣음
hashing_result=h.hexdigest() # 해시 함수 사용해서 해시키로 변경
```

<br>

## execute_script

* python의 driver의 메소드인데, 해당 메소드를 활용하면 .click()이나 텍스트에 데이터 기입한다던지 등을 매우 간편하게 활용 가능
* 참고로 아래 예시는 셀레니움이다.

```python
driver.execute_script(f"document.getElementById('xxx').value='{id}'") # 기입
driver.execute_script(f"document.getElementById('xxx').value='{pw}'")
driver.execute_script('doLogin()') # 코드내의 로그인 함수 실행
```

<br>

## sessionStorage 활용

* **로그인 기능을 위해 py에서 js문법 활용해서 세션을 사용**

**로그인 기능에서 세션의 데이터를 검사하는 부분**

```python
print('''
<script>
	var login_Chk = sessionStorage.getItem("loginData");
	if (login_Chk != 1) { // login_Chk = 0 : fail, login_Chk = 1 : success
		// move to Login-Page
        location.href="https://login.html";
	}
</script>
''')
```



**로그인 기능에서 세션에 데이터 저장하는 부분**

```python
print('''
	<script>
		var sessionData = 1;
		sessionStorage.setItem("loginData", sessionData ); // save
		location.href="https://home.py";
	</script>
''')
```

