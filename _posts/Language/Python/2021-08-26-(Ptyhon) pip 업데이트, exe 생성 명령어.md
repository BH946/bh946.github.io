---
title:  "[Python] pip 업데이트, exe 생성 명령어?"
categories : PY
tag : [파이썬, pip, Jupyter, Colab, version, exe]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**python의 pip 업데이트 명령어와 exe 생성 법을 알아보자. 이런 내장, 외장 함수들을 검색하는 TIP도 소개한다.**

<br>

<br>

## 본문

**pip 업데이트**

```bash
python -m pip install --upgrade pip
```

<br>

**exe 파일 생성**

- python 버전 호환성 체크
- pip 버전 호환성 체크는 필수

```bash
pyinstaller --onefile sample.py # 해당 py파일 이름
```

<br>

**함수 검색 TIP: 내장, 외장함수 구글링법**

* 구글에 list of python builtins 검색 : 내장함수
* 구글에 list of python modules 검색 : 외장함수
