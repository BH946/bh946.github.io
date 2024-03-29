---
title:  "Visual_Studio 정리(+Debug)"
categories : Code_Editor
tag : [Visual_Studio, Debug, 디버그, ERROR, 에러]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: false
---



## Visual Studio..

 **대부분의 IDE가 제공하는 표준 편집기 및 디버거로서 뿐만 아니라, 컴파일러와 코드 완성 도구, 그래픽 디자이너를 비롯해 소프트웨어 개발 프로세스를 향상시키는 많은 기능을 포함**

**참고) Visual Studio 와 Visual Studio Code 는 다른겁니다.**

<br>


### 1. 일반적인 exe로 컴파일 방법

사용한 컴파일러명 : Visual C++

컴파일 방법 : 

1. Visual Studio에 들어가서 빌드 > 솔루션 정리를 먼저 해줍니다.
2. 메뉴 프로젝트 > 자신의 프로젝트네임 속성 메뉴를 선택합니다.
3. 출력 형식이 애플리케이션이 아니면 애플리케이션으로 바꿔줍니다.
4. 빌드에서 구성을 Release형식으로 바꾸고, 플랫폼을 전체가 쓸 수 있게 항목을 바꿔줍니다.
5. 다시 빌드 해야하기 때문에 빌드 > 솔루션 다시 빌드 메뉴를 선택합니다.
6. 마지막으로 빌드 > 일괄 빌드 들어가서 변경한 속성들 반영이 됬는지 확인후 빌드 해줍니다.
7. 끝으로 프로젝트 폴더로 들어가면, exe파일이 생성이 됨을 알 수 있습니다.

<br><br>

## 중요! 디버그(Debug)

![image_visual_studio_debug](../../../images/2022-03-04-Visual_Studio 정리(+Debug)/image_visual_studio_debug.png)

<br>

### 환경설정 : 위의 스크린샷처럼 구성할것(디버그 환경)

* **F10** : 디버그 실행 및 한줄씩 디버그 실행
* **F5** : 중단점(빨간점)으로 바로 넘어감(이 또한 디버그 실행 역할 + 바로 중단점으로 넘어감)
* **F9** : 중단점 체크 및 해제
* **F7** : 디버그 종료

* **Ctrl + F5 : 디버그 하지않고 바로 실행**

<br><br>

## 에러

### 다음번에 또 실수 안하기 위해 기록.

**VS 들어가서 파일을 오픈할때 폴더로 열면 코드가 정상적으로 실행이 안되며 에러가 발생한다.**

* 따라서 확장자가 프로젝트인 파일로 직접 열거나 VS에서 파일 오픈할때 폴더가 아닌 프로젝트로 열기를 통해서 확장자가 프로젝트인 파일을 열어서 반드시 접근해야한다.
