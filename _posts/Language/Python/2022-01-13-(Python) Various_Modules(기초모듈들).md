---
title:  "[Python]Various_Modules(다양한 모듈)"
categories : PY
tag : [python, modules, time, datetime, automation, 파이썬, 모듈, 자동화, 시간]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



**Python의 다양한 기본 모듈과 외부 모듈들을 활용하여 시간, 자동화, 이미지 처리 등 다양한 기능을 구현할 수 있다 - time, datetime, pyperclip, pyautogui, keyboard, PIL(Python Imaging Library), random, string**

<br>

<br>

## 시간 관련 모듈 ⏰

**time 모듈**

```python
import time

time.sleep(1)  # 1초 대기
time.strftime("%Y%m%d_%H%M%S")  # 날짜/시간 포맷팅
```

<br>

**datetime 모듈**

```python
from datetime import datetime

now = datetime.now()  # 현재 시간 객체
formatted_date = str(now)  # 문자열로 변환
```

<br>

<br>

## 자동화 모듈

**클립보드 제어**

```python
import pyperclip

pyperclip.copy("복사할 텍스트")  # 클립보드에 복사
```

<br>

**키보드/마우스 제어**

```python
import pyautogui
import keyboard

# 키보드 매크로
pyautogui.keyDown('ctrl')  # 키 누르기
pyautogui.press('v')  # 키 한번 누르기
pyautogui.keyUp('ctrl')  # 키 떼기

# 마우스 매크로
pyautogui.moveTo(100, 200)  # 마우스 이동
pyautogui.click()  # 클릭

# 핫키 설정
keyboard.add_hotkey('F9', screenshot)  # F9 누르면 스크린샷
keyboard.wait('esc')  # 사용자가 esc를 누를때까지 프로그램 실행
```

<br>

<br>

## 이미지 처리 

**스크린샷**

```python
from PIL import ImageGrab  # 파이썬 이미지 라이브러리

for i in range(1, 11): # 2초 간격으로 10개 이미지 저장
    img = ImageGrab.grab() # 현재 스크린 이미지를 가져옴
    img.save("image{}.png".format(i)) # 파일로 저장
    time.sleep(2)	
```

<br>

<br>

## 랜덤 문자열 생성 🎲

```python
import random
import string

random_link = "http://www." + ''.join(random.choice(string.ascii_lowercase) for i in range(10)) + ".com" # 아스키코드 소문자 랜덤 10자 작성
```



