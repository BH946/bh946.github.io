---
title:  "VS_Code 정리(+Debug)"  
categories : Code_Editor
tag : [VS_Code, Debug, 디버그]  
toc: true  
toc_sticky: true  
author_profile: false  
sidebar:  
 nav: "docs"    
typora-root-url: ../../..
published: false
---



## VScode(=Visual Studio Code)? 텍스트 에디터

**무거운 IDE와는 다르게 텍스트 파일을 편집하는 용도로 가벼운 애플리케이션**  
IDE는 개발의 모든 작업(코드작성, 컴파일, 디버그, 소프트웨어 패키징, 배포)등의 모두 포함되어 무거우나, 텍스트 에디터는 코드 작성만을 위한 도구이기 때문에 가벼운것

**환경설정에 따라 IDE와 같은 동작 가능**

**유용한 확장파일**

* Auto Close Tag, Auto Rename Tag : HTML 태그 자동완성, 이름 변경시 양 옆의 태그 동시 변경
* Korean Language Pack : 한글 패치
* Material Icon theme : 미관적 요소, 아이콘과 폴더를 쉽게 구분할 수 있게 해줌
* Prettier - Code formatter : 코드를 특정한 양식에 자동으로 맞춰주는 기능

<br><br>

## VScode 유용한 기능(단축키)

### 터미널

**Ctrl+'~'** 누를시 기존 터미널창 오픈

**Ctrl+Shift+'~'** 누를시 새로운 터미널창 오픈 :blush:

<br>

### 기본 설정

**F1 or Ctrl+shift+P**키 누를시 검색가능. -> interpreter검색해서 Select Interpreter접근 -> 깔려있는 python 버전 확인 및 선택가능. 

**설정(톱니바퀴) + Keyboard Shortcuts** 에서 키보드를 통한 단축키들 모두 알 수 있고, 커스텀도 가능하다.

**설정(톱니바퀴) + settings** 에서 font size, tab size 변경가능 -> 각각 16, 2 추천

* **editor bracket pair** 입력을 한다.(대괄호 쌍을 찾는 기능)
  * **Bracket Pair Colorization: Enabled** 를 체크하면,   
    열린 대괄호에 맞는 닫힌 대괄호를 자동으로 인식해서 색깔별로 구분해주고 있다
  * **Bracket Pairs** 를 true로 사용하면, tab의 공간을 세로줄로 표시해서 구분하기 쉽게 해준다.
* **word wrap** 입력을 한다.(줄 바꿈 기능)
  * **Word Wrap** 을 on으로 변경시 긴 글이 화면의 창을 벗어나지 않고, 다음줄로 자동으로 넘어간다.

**설정(톱니바퀴) + Color Theme** 에서 테마 변경 가능

* 추천 테마 5가지 : `night owl, cobalt2, shades of purple, coder coder dark, codestackr`

<br>

### 빠른 파일 이동

**Ctrl+Tab** 누를시 현재 오픈한 파일들에서 키보드로 간편히 파일 전환 가능

**Ctrl+W** 누를시 현재 오픈한 해당 파일 종료

**Ctrl+K+W** 누를시 현재 오픈한 모든 파일들 종료

**Ctrl+P** 누를시 현재 프로젝트의 모든 파일들을 접근할 수 있으며, 간편히 검색해서 접근가능. 매우 유용

<br>

### 빠른 코드 조작

**Alt+Arrow** 누를시 해당 커서에 있는 코드가 이동할 수 있다.

**Alt+Shift+Arrow** 누를시 해당 커서에 있는 코드를 복제해서 추가 할 수 있다.

**Ctrl+D** 누를시 해당 커서에 있는 단어와 같은 단어가 선택되고, 계속 누를시 계속 선택이 추가된다.

**Alt+마우스** 누를시 커서를 계속 계속 추가할 수 있다.

html, jsx 에서 **.abc 또는 #abc**로 입력시 class와 id가 abc인 div태그를 자동완성 해준다.

* 물론 다른 태그들 또한 바로 태그이름 입력시 자동완성 해준다.  
  예로 input#user 입력시 input태그이며 id=user로 태그 자동완성이 된다.
* 단지, div가 .으로 입력시 자동완성 해준다고 생각하면 된다.

<br><br>

## 나의 VSCODE 환경,,

`python`이 3.10.1이 기본적으로 깔려있고, 아나콘다(가상환경)으로 python 3.9.7이 깔려있음

interpreter에 python 3.9.7버전을 선택해놨고, cmd창에서 run(실행)시 자동으로 3.9.7실행

혹시 버전적용 잘 안되는경우 터미널 새로 생성 or git bash 터미널 사용추천!

<br><br>

## 중요! 디버그(Debug)

* 디버그는 정말 중요한 기술!

<br>

### 1. 실행방법(환경설정)

**환경설정 : 왼쪽 메뉴중에서 실행 및 디버그(Run) -> create a launch.json -> python클릭**

```python
# python으로 설정 됨으로써 다음부터 자동으로 python으로 디버깅
"name": "Python: Current File",
"type": "python",
"request": "launch",
"program": "${file}",
"console": "integratedTerminal"
```

**=> 다음부터 실행할 때 디버그 실행 버튼만 누르면 python으로 자동으로 디버깅 작업 실시한다.**

<br>

### 2. 조작법

**버튼이 상단에 뜨기 때문에 직접 클릭해도 되고, 단축기를 활용해도 된다.**

* **F5** : 처음 디버그 실행 및 중단점으로 이동하는 역할(**따라서 중단점을 적용후 실행할것!!**)
* **F10** : 한줄 단위로 디버그(함수도 바로 넘어감)
* **마우스 우클릭 -> Jump to Cursor** : 원하는 위치(위로)로 커서를 이동 할 수 있다(**주의 : 문장을 무시**)
* **마우스 우클릭 -> Run to Cursor** : 원하는 위치로 커서를 이동(**Jump와 다르게 이동하면서 문장을 실행**)
* **단계정보(F10), 단계출력(shift+F10)** : 함수안으로 접근하고, 함수를 탈출하는 역할을 한다
* Run -> 모든 중단점 제거 : 한번에 모든 중단점을 제거
* Run -> 모든 중단점 사용안함 : 회색빛으로 중단점 바뀌며, 중단점 역할은 안함
* 나머지는 굳이 단축기 안써도 될 것 같다

<br>

### 3. 구성환경의 이해

#### 마우스 접촉

* **마우스를 알고싶은 정보에 두는것으로 데이터 값, 타입 등의 정보를 알 수 있다.**

<br>

#### 변수(VARIAVLES)

* **지역, 전역 변수들의 정보를 볼 수 있다**
* **데이터 값을 수정해서 바로 보고싶다면? 여기서 바로 값을 수정할 수 있다**

<br>

#### 조사식(WATCH)

* **+버튼을 눌러서 함수 입력 및 변수를 입력하여 정보를 얻을 수 있다.**

<br>

#### 호출스택(CALL STACK)

* **함수들이 어디서 불려서 호출되는지 등의 정보를 알 수 있다**



