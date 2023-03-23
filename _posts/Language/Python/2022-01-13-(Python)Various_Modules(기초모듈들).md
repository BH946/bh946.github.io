---
title:  "[Python]Various_Modules(기초모듈들)"
categories : PY
tag : [파이썬, 문법]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



# 다양한 모듈들

## 시간

```python
import time
time.sleep(1) # 1초 정지
```

<br><br>

## 날짜

```python
from datetime import datetime
now = datetime.now() # 현재시간 기준.
type(now) # => datetime.datetime 형식인 클래스로 타입나옴. str로 바꿔줘야 이쁘게나옴.
str(now) # 까지 하길 추천.
```

<br><br>

## 클립보드

```python
import pyperclip
pyperclip.copy("저장할 내용") # '저장할 내용' copy.
# 붙혀넣는 방법은 매우 다양.(pyautogui이용 or selenium에서 send_keys()이용)
```

<br><br>

## 단축기, 마우스 매크로 입력

**단축기**

```python
import pyautogui

pyautogui.keyDown('ctrl') # 키 다운상태
time.sleep(0.1) # 시간조절!
pyautogui.press('v') # 한번 press
pyautogui.keyUp('ctrl') # 키 업!

pyautogui.press('left', 4, 0.2) # 0.2초 간격으로 4번 'left'키 press
```

<br>

**마우스**

```python
import pyautogui

pyautogui.moveTo(1240, 945) # 마우스 이동
time.sleep(0.3)
pyautogui.click() # 마우스 클릭
```

<br>

**핫키 입력**

```python
import keyboard

keyboard.add_hotkey("F9", screenshot) # F9를 핫키로 screenshot함수 실행
keyboard.wait("esc") # 사용자가 esc를 누를때까지 프로그램 실행
```

<br><br>

## 이미지

#### auto_screenshot

```python
from PIL import ImageGrab  # 파이썬 이미지 라이브러리

for i in range(1, 11): # 2초 간격으로 10개 이미지 저장
    img = ImageGrab.grab() # 현재 스크린 이미지를 가져옴
    img.save("image{}.png".format(i)) # 파일로 저장
    time.sleep(2)
```

<br>

#### advanced_screenshot

```python
import keyboard # 추가!

def screenshot():
    # ex) _20200601_102030
    curr_time = time.strftime("_%Y%m%d_%H%M%S")
    img = ImageGrab.grab()
    img.save("image{}.png".format(curr_time))


keyboard.add_hotkey("F9", screenshot)
keyboard.wait("esc") # 사용자가 esc를 누를때까지 프로그램 실행
```

<br><br>

## random, string

```python
random_link = "http://www." + ''.join(random.choice(string.ascii_lowercase) for i in range(10)) + ".com" # 아스키코드 소문자 랜덤 10자 작성
```



