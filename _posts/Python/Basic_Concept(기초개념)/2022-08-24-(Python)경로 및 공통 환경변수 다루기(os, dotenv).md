---
title:  "[Python]경로 및 공통 환경변수 다루기(os, dotenv)"
categories : [Basic_Concept_PY, Knowledge]
tag : [파이썬, env]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



## os 라이브러리

**경로를 자세히 모르는 경우에 `os 라이브러리` 를 활용해서 경로를 다룬다.**

- `os.getcwd(), os.chdir('경로지정')` 대표적으로 이 2개를 활용할 수 있다.
- `os.getcwd()` 는 현재 경로를 나타내고
- `os.chdir('경로지정')` 은 지정한 경로로 os 경로를 이동한다.

<br>

## python-dotenv 라이브러리

**파일 구조를 세분화 하며, 그 파일들에 공통으로 사용할 변수들의 경우 `.env` 에 저장해서 다룬다.**

보통 `TEST_NAME = "test"` 이런식으로 대문자로 `.env` 파일을 작성하는 편이다.

- `load_dotenv()` 를 `main.py` 에 작성해서 `.env` 파일을 로드하여 어디서든 사용하게 만든다.
- 파일을 코드 수행중에 **getter(), setter()** 을 할 수 있다.

  - 실제 로컬의 `.env`파일의 내용이 setter()를 해도 변하진 않지만 코드 사용중에선 변화한다.
  - **setter()** 방식 - `os.environ["pathDir"] = "12345"` 
  - **getter()** 방식 - `os.environ.get("pathDir")`
- 참고 : git에 안올릴려면 `.gitignore` 파일에 `.env` 코드 한줄 추가!

<br>

## os+dotenv 활용

`dotenv`를 활용해서 `.env` 파일을 **getter(), setter()** 를 활용해서 사용하는데,

`os` 의 **os.getcwd(), os.chdir('경로지정')** 를 이용해서 경로를 자유롭게 같이 다루어주면 더 효율적이다.

```python
# src/main.py
import os
from dotenv import load_dotenv
load_dotenv() # .env 로드

os.chdir('../log') # src와 같은 계층인 log 폴더로 이동
LOG_FILES_PATHDIR = os.getcwd() 
os.environ["LOG_FILES_PATHDIR"] = LOG_FILES_PATHDIR # .env 파일에 해당 경로 작성
```



이런식으로 코드 실행중에 원하는 경로에 접근해서 그 경로를 .env 파일에 담아둘 수 있다.

이 경로가 필요하게 될경우 다른 py 파일에서도 전부 접근할 수 있어서 유용하다.
