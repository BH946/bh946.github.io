---
title:  "[Python] 경로와 환경변수 관리하기(os, dotenv)"
categories : [PY, Knowledge]
tag : [파이썬, env, python, os, dotenv, 환경변수, 경로관리]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---



**Python에서 경로와 환경변수를 효과적으로 관리하는 방법으로 os와 python-dotenv 라이브러리를 활용할 수 있다.**

## OS 라이브러리

**os 라이브러리는 경로 관리를 위한 핵심 기능을 제공한다.**

**주요 기능**

- `os.getcwd()`: 현재 작업 디렉토리 경로 반환
- `os.chdir(path)`: 작업 디렉토리를 지정한 path 경로로 변경

<br>

<br>

## Python-dotenv 라이브러리

**환경변수를** `.env` **파일에서 관리할 수 있게 해주는 라이브러리이다.**

<br>

**사용 방법**

```python
from dotenv import load_dotenv
load_dotenv()  # .env 파일 로드
```

<br>

**환경변수 설정과 조회법**

```python
# 환경변수 설정
os.environ["KEY"] = "VALUE"

# 환경변수 조회
value = os.environ.get("KEY")
```

<br>

<br>

## 실전 활용 예시

```python
import os
from dotenv import load_dotenv

# .env 파일 로드
load_dotenv()

# src와 같은 계층인 log 폴더로 이동
os.chdir('../log')

# 현재 경로를 환경변수로 저장
LOG_FILES_PATHDIR = os.getcwd()
os.environ["LOG_FILES_PATHDIR"] = LOG_FILES_PATHDIR # .env 파일에 해당 경로 작성
```

<br><br>

### 추가 팁

- `.env` 파일의 변수명은 **대문자로** 작성하는 것이 관례이다
- `.gitignore`에 `.env`를 추가하여 민감한 **정보 보호**
- 실행 중 환경변수를 변경해도 실제 `.env` **파일은 변경되지 않음**
