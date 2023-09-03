---
title: "[Error] Spring-JDK 버전문제 해결방안"
categories : [Knowledge]
tag : [spring, 스프링, 스프링부트, jdk, java, version, 버전문제]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..

---



# Error

**`No active profile set, falling back to 1 default profile: "default"` 등등...**

**자바의 버전 문제로 인한 에러를 자주 접할 수 있는데, 이를 해결하려면 정확한 동작 과정의 이해가 필요**

**사실 이 메시지는 오류 메시지는 아니고 "프로필" 설정이 안되어있어서 기본값을 쓰겠다는 내용일 뿐이다.**

<br>

**`No active profile set, falling back to 1 default profile: "default"` 가 발생한 경우**

* war로 프로젝트를 생성하고 인텔리제이에서 실행하는 경우 만날 수도 있다.

* war로 Packaging 설정한 프로젝트는 Build and run using 옵션을 Gradle로 바꾸면 정상적으로 에러 없이 동작한다.
  * IntelliJ 로 실행을 설정했을경우 Gradle 로 바꿔야하는 것

<br>

**Spring의 web 라이브러리 유무**

* 실행해서 `localhost:8080` 으로 웹을 접근하려면 아래 과정을 이해해야 한다. 
  * 참고) 웹 접근이 아니면 상관없고, 자바 코드자체는 정상 실행함
* **web 라이브러리**를 설치해야 내장 톰캣(WAS)을 포함하기 때문에 이를 사용해서 실행
* 해당 라이브러리가 없으면 "**서블릿 라이브러리**" 를 설치해서 "서블릿 코드" 작성해서 실행해야 정상 실행

<br>

**"시스템 환경 변수" 유형들**

* 기존 IntelliJ 내부에서 자바 설정을 맞추고 IntelliJ 로 실행시 **"설정한 자바 사용"**

* cmd 로 실행시 cmd 의 "현재 자바 사용"

  * 예로 gradlew build 의 경우 코드를 보면 JAVA_HOME 을 사용하듯이 **"시스템 환경 변수" 를 사용**
  * **IntelliJ 로 실행시 이를 IntelliJ 에 설정한 자바를 사용하는데, cmd 처럼 외부에서 실행시 다르다는 차이점을 꼭 기억**

* 1)만약 수동으로 직접 "시스템 환경 변수" 설정에 들어가서 수정시 적용하려면 "재부팅" 필요

  * 아래 명령어로 간단히 "재부팅"
  * taskkill /f /im explorer.exe  
    explorer.exe

* 2)만약 cmd에서 자바 버전 간단히 변경했다면, 굳이 "재부팅"은 필요없고 해당 cmd 내에서 명령어들은 해당 변경된 자바 버전으로 잘 실행

  * 버전관리 쉽게하는법은 아래 블로거 분의 내용 참고!

    * **[jdk 버전관리 자동](https://computer-science-student.tistory.com/467)**

  * 실제 설정한 모습

    ![image](https://github.com/BH946/spring-second-roadmap/assets/80165014/fb05b24e-1bb2-4444-830c-0dce5b282f48) 

    ![image](https://github.com/BH946/spring-second-roadmap/assets/80165014/9aca1993-b6a2-44ab-b1fd-4cbdb889e5e3) 
