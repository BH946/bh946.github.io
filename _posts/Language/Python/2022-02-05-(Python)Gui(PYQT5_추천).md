---
title:  "[Python]GUI(PYQT5_추천)"
categories : PY
tag : [파이썬, pyqt, gui]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---

## GUI(구이)

* PYQT5 라이브러리 사용
  * QT 디자이너 프로그램을 활용해 GUI를 제작하기 편하다는 큰 장점!!
* QT 디자이너 사용 제작 (디자인)
  * .UI -> .PY 파일로 변환
  * PY 파일로 변환된 파일로 코딩(PYQT5 코드 변경)

참고 사이트 : https://www.youtube.com/watch?v=rHDWnfsJP4A

<br>

## 1. QT 디자이너

* 구글에서 검색후 간단히 설치

### 드래그 앤 드롭으로 GUI 제작

* 말 그대로 UI를 꾸미면 되고, 간단해서 설명이 필요없다.

### 함수연결

**함수를 연결 해놔야 버튼같은것을 클릭했을때 동작을 py에서 작성할 코드를 수행할테니 꼭 연결!**

* Edit Signals/Slots 아이콘을 사용
* 버튼같은 곳에 클릭후 드래그앤 드롭으로 공란에 끌어다 준다.
* 함수를 설정해준다(예 : click()에 start()함수 새로 edit해서 적용)

### 저장(Save as)

* Ui로 저장을 하게된다.

### UI -> PY 변환

* cmd -> cd를 통한 경로 접근 -> pyuic5 -x 파일이름.ui -o 파일이름.py
* PYQT5로 변경된 코드를 볼 수 있다!!

<br>

## 2. 파일이름_starter.py

* **starter파일을 만들어서 이 파일로 우리가 만든 GUI를 실행한다**

```python
import sys
from automatic import Ui_MainWindow # 수정부분(from뒤에 QT로 만든 파일이름을 적어주면 됨)
from PyQt5 import *
from PyQt5.QtCore import *
from PyQt5.QtWidgets import *

class kinwriter(QMainWindow, Ui_MainWindow): 
    
    def __init__(self):

        super().__init__()

        self.setupUi(self)
        # self.timer = QTimer(self)
        # self.timer.setSingleShot(False)
        # self.timer.setInterval(5000) # in milliseconds, so 5000 = 5 seconds
        # # self.timer.timeout.connect(self.start_Macro)
        # self.timer.start()i0nscn2kdlr2k


        #print(self.hasMouseTracking())


        self.show()

app = QApplication([])
sn = kinwriter()
QApplication.processEvents()
sys.exit(app.exec_())
```

<br>

## 코드 수정(QT에서 만든 함수추가)

* 이부분을 하지않고 starter파일 실행시 에러

```python
# QT디자이너로 만든 파일이름.py의 코드를 수정
# 예로 start(), stop() 함수 만들었다고 가정

def start() # 예시일 뿐이니까 코드 작성은 하지 않겠다
	pass

def stop()
	pass
```

**이처럼 적절한 공간에 함수 선언해두면 starter가 문제없이 실행될 것이다.**


