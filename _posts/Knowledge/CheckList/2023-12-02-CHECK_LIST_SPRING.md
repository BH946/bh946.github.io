---
title: "[체크리스트] Spring"
categories : [CheckList]
tag : [spring, 스프링, 스프링부트]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**Spring + Spring Boot + JPA + Spring Data JPA + MyBatis + Thymeleaf + DB(H2, MySQL, Oracle) 등 활용한 개발 경험**  
+) Eclipse, STS, 전자정부프레임워크

<br>

**헷갈리는 스프링, 스프링부트 프로젝트 생성과 JAR, WAR**

- Spring Framework 는 사이트 제공은 없고, STS3이나 직접 디렉토리 구조를 구성!  
  - 디렉토리 구조가 "**스프링부트+JSP+WAR**" 구조와 비슷 -> `src/main/webapp/WEB-INF/jsp`
  - 템플릿 엔진(View)에서 JSP를 사용
- Spring Boot 는 [start.spring.io](http://start.spring.io/) 이나 IDE(이클립스, STS 등)로 플젝 생성  
  - 디렉토리 구조가 "타임리프" 사용에 맞는 구조 -> **webapp부분 없고 java, resources가 끝!**
  - 템플릿 엔진(View)에서 타임리프를 사용(오피셜)
    - **외부 라이브러리(임베디드-톰캣-JSP 라이브러리) 사용시 JSP 사용가능**
      - WAR인데 JAR 실행처럼 IDE에서 내부톰캣으로 바로 실행도 되는 장점!
    - 스프링부트에서 JSP사용할거면 WAR 형식 사용을 추천.(JAR는 좀 복잡)
- 참고: 원래 WAR는 따로 WAS(톰캣)을 구동후 위에 돌려야 하고, JAR는 JVM위에서 바로 돌릴수 있다.

<br>

`IDE: IntelliJ & Eclipse + Build Tool: Gradle - Groovy & Maven - Pom.xml`


> 주로 IntelliJ + Gradle 사용 중

<details><summary><b>실제 빌드와 실행 둘 다 IntelliJ 로 설정 (실행 속도가 더 빠름. 주의: 부트 3.2부터는 Gradle로 하자) + "코드 컨벤션" + "자바 버전 설정"</b></summary><br>
참고: 스프링부트 3.2부터는 IntelliJ IDEA로 빌드와 실행 설정하면 에러가 발생. 따라서 Gradle을 사용해서 빌드하고 실행하자. (첨 빌드는 느릴지라도 Gradle 캐시를 하므로 2번째 부턴 빠름)
<br>
<b>Settings → gradle 검색 → IntelliJ IDEA로 설정 (빌드툴, 실제 실행 설정)</b>
<img src="https://github.com/user-attachments/assets/9f087f29-4dba-4d9e-9c33-42b15a449a03"/><br><br>
<b>Settings → Editor → Code Style → 구글 컨밴션 적용 (Ctrl+I, Ctrl+L 로 빠르게 정렬)</b><img src="https://github.com/user-attachments/assets/ddd01a9d-5c01-4ab0-9435-dc15a697e63f"/><br><br>
<b>Settings -> gradle 검색 -> 빌드 툴(Gradle) 자바버전 설정(위 사진참고)</b><br>
<b>Project Strucutre -> Project -> 프로젝트의 자바버전 설정</b>
<img src="https://github.com/user-attachments/assets/4ffc05a9-0ac2-40f6-83b4-1bf6929937fc"/>
</details>

<details><summary><b>툴 마다 외부 라이브러리 적용법</b></summary>
<ul>
    <li>Maven(빌드 툴)은 <b>pom.xml</b>에서 라이브러리 설정</li>
    <li>Gradle(빌드 툴)은 <b>build.gradle</b>에서 라이브러리 설정</li>
    <li>이클립스(IDE)에서 빌드 툴 사용안했을 때는 직접 jar파일 집어 넣었었음.(전통)</li>
    <li>인텔리J(IDE)에서도 가능할거임. 물론, 어느 IDE든 "빌드 툴에 의존성 추가"가 간편!</li>
</ul>
</details>
- 특히 boot-starter-web 라이브러리 없으면 바로 종료되기 때문에, Spring을 사용한다면 이때 `ApplicationRunner` 구현체로 자바코드 실행하는게 보통

  - ApplicationRunner는 스프링 부트가 애플리케이션(서버)이 완전히 초기화된 후 실행되는 콜백 인터페이스이기 때문!!

- 아래 JUnit은 Boot기준(@SpringBootTest)이고, 순수 스프링은 @RunWith(SpringJUnit4ClassRunner.class)와 @ContextConfiguration를 사용

  <details><summary><b>build.gradle 설정 예시 (+플러그인)</b></summary>
  <div markdown="1"><br>
  **스프링부트 플러그인 사용 시 "라이브러리 버전관리 자동화" -> 지원 안되는건 "직접 버전 등록 필수!"**<br>
  `spring 3.x` 사용은 `java17` 필수!<br>
  ```groovy
  plugins {
    id 'java'
    id 'org.springframework.boot' version '3.1.2'
    id 'io.spring.dependency-management' version '1.1.0'
  }
  group = 'com'
    version = '0.0.1-SNAPSHOT'
   	sourceCompatibility = '17'
    configurations {
    compileOnly {
      extendsFrom annotationProcessor
    }
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    // data-jpa(jpa, spring data jpa), web(http), lombok, db(h2, oracle)
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
    runtimeOnly 'com.h2database:h2'
    // https://mvnrepository.com/artifact/com.oracle.database.jdbc/ojdbc10
    implementation group: 'com.oracle.database.jdbc', name: 'ojdbc10',     version: '19.21.0.0'
    annotationProcessor 'org.projectlombok:lombok'
    // test(JUnit, AsserJ 등), lombok 을 test 에서 사용
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testCompileOnly 'org.projectlombok:lombok'
    testAnnotationProcessor 'org.projectlombok:lombok'
    //valid(NotEmpty 등) 사용위해 추가
    implementation 'org.springframework.boot:spring-boot-starter-validation'
    implementation 'org.springframework.boot:spring-boot-starter'
    implementation 'org.springframework.boot:spring-boot-devtools'
    // 캐시(caffeine 로 캐시매니저 간단 등록)
    implementation 'org.springframework.boot:spring-boot-starter-cache'
    implementation 'com.github.ben-manes.caffeine:caffeine:3.1.1' 
    // 모니터링(actuator, prometheus)
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    implementation 'io.micrometer:micrometer-registry-prometheus'
  }
  tasks.named('test') {
    useJUnitPlatform()
  }
  ```
  </div>
  </details>
  <details><summary><b>SpringBoot+jUnit 버전별로 라이브러리 추가 주의(Spring2+JUnit4, Srping3+JUnit5)</b></summary>
  <div markdown="1"><br>
  **(1) SpringBoot 2.xx + JUnit4(Test Code)**<br>
  * `build.gradle`
  <div markdown="1">
  ```groovy
  //JUnit4 추가(junit5로 자동실행 되기 때문) - 의존성 추가
  testImplementation("org.junit.vintage:junit-vintage-engine") {
    exclude group: "org.hamcrest", module: "hamcrest-core"
  }
  ```
  </div>
  * `TestCode.java`
  <div markdown="1">
  ```java
  @RunWith(SpringRunner.class) // SpringRunner : Junit4
  @SpringBootTest // 스프링과 통합 테스트 - "빈" 등 스프링 사용시 필수
  public class ItemServiceTest { 
    @Test // 기본 테스트(필수)
    public void 조회() {} 
  }
  ```
  </div>
  **(2) SpringBoot 3.xx + JUni5(Test Code)**
  * H2 사용시 반드시 버전 업그레이드 + 자바 17이상
  * RunWith, JUnit4 등록이 없어졌다고 보면 됨.
  * `build.gradle`
  <div markdown="1">
  ```groovy
  // JUnit5 자동 사용!
  // 테스트 코드에서 lombok 사용하는 꿀 팁! -> 아래 의존성 추가
  testCompileOnly 'org.projectlombok:lombok'
  testAnnotationProcessor 'org.projectlombok:lombok'
  ```
  </div>
  * `TestCode.java`
  <div markdown="1">
  ```java
  @SpringBootTest // 스프링과 통합 테스트 - "빈" 등 스프링 사용시 필수
  public class ItemServiceTest { 
    @Test // 기본 테스트(필수)
    public void 조회() {} 
  }
  ```
  </div>
  </div>
  </details>

<br>

**이클립스에서 스프링 플젝보면 Explorer source폴더가 두개가 있는 이유가 뭘까?**

- **물리적 위치는 동일**하지만, **폴더별 역할이 다름!**
- 패키지모양을 담은 폴더는 **컴파일 대상** 폴더이고, 그냥 일반폴더는 **컴파일하지않는** static 리소스를 보통 담는다.

<details><summary><b>Eclipse+Spring Tools 플러그인 / STS3,4 => 맘 편히 STS4 사용을 권장!</b></summary>
<div markdown="1"><br>
**참고문서: [STS3,4 설치와 환경설정](https://www.snugarchive.com/blog/sts-setup/), [이클립스 설치와 환경설정](https://www.snugarchive.com/blog/java-eclipse-setup/), [아파치 설치와 환경설정](https://www.snugarchive.com/blog/apache-tomcat-setup/)**<br>
**"Eclipse엔 Spring Tools 이란 플러그인을 추가"**하거나 아예 Spring 개발용으로 개발된 이클립스인 **"STS IDE"**를 사용함.<br>
1. **이클립스 기반인 STS IDE 사용의 경우: [STS 설치 문서](https://spring.io/tools)**
   JSP, JS 사용할거면 Help > Eclipse Marketplace > JAVA EE 플러그인 설치는 필요함 (**웹 개발 도구 추가ver!** )<br>
   **STS3도 Boot 지원. 근데 STS4 만큼 최신 Boot 지원은 아님.**  <br>
   단, STS4의 경우 legacy가 지원 안되는것 같아서 얘는 오히려 STS3 플러그인도 추가 설치해야 legacy도 사용 가능. (물론, 부트만 쓸거면 상관 없겠지만!)<br>
   **=> 따라서 spring legacy project 랑 spring boot(starter) project 둘다 쓰려면 STS3을 쓰거나, 최신 Boot 사용을 위해 STS4를 쓰되 STS3 플러그인까지 설치하자.**<br>
   **=> 중요: STS4에서 STS3을 사용하려면 반드시 STS4.14.1 + STS3.9.22 Add-On For STS4(플러그인) 버전을 사용할 것. (스프링은 STS4를 권장하므로 이제 이전버전 지원안하거든)**<br><br>
   spring starter project 로 생성 시 spring init 사이트에서 부트 플젝 생성하던것처럼 라이브러리도 다 추가하고 손 쉽게 가능!! -> [생성모습](https://euni8917.tistory.com/199)<br><br>
   **JS 환경설정 필요: STS4 의 경우 JAVA EE 플러그인 설치 완료 가정**
   - Window > Preferences > General > Editors > File Associations로 이동합니다.<br>
     'File types'에 *.js를 추가합니다.<br>
     'Associated editors'에서 'Generic Text Editor'를 기본값(default)으로 설정합니다.
2. **이클립스에서 STS 플러그인 설치하는 경우: [이클립스 설치 문서](https://www.eclipse.org/downloads/packages/)**
   Eclipse IDE for Java Developers(=JAVA SE) 는 일반 자바 개발에 설치하고, 보통 이 경우 **웹 개발 용인 Eclipse IDE for Enterprise Java and Web Developers(=JAVA EE) 로 설치!**<br>
   => Standard가 JAVA SE이고, JAVA EE나 JAVA ME(Micro.작은)는 JAVA SE 기반으로 만든 플랫폼.<br><br>
   STS 플러그인은 Help > Eclipse Marketplace > 에서 끝이 RELEASE로 끝나는 최상단의 3버전과 4버전을 설치 (STS3, 4 둘다 사용하려고!)<br>
   **=> 중요: STS4에서 STS3을 사용하려면 반드시 STS4.14.1 + STS3.9.22 Add-On For STS4 버전을 사용할 것. (스프링은 STS4를 권장하므로 이제 이전버전 지원안하거든)**<br><br>
   **특히, 이클립스 버전도 STS 프러그인 지원하는지 잘 맞춰줘야함. 이클립스 조차 JDK호환 버전이 다를수 있음 ㅠ (이래서 걍 STS4 IDE 이런거 쓰는데 편해..)**<br>
   => 아래 사진은 STS3 플러그인이 제공하는 STS4 플러그인 버전과 이클립스 버전을 알려줌.<br>
   <img src="https://github.com/user-attachments/assets/2720f436-db1d-4795-b33a-cc177252d4fa" alt="Image" style="zoom:80%;" /> <br>
   **이클립스 필수 고려할 환경 설정~! (STS IDE도 이클립스랑 유사해서 이거처럼ㄱㄱ)**
   - **IDE가 사용할 JVM 설정하기:  IDE에게 어떤 버전의 JVM을 사용할지 알려주기.**<br>
     **IDE(이클립스)가 사용할 jdk이지 프로젝트에서 사용할 jdk가 아님. 서로 독립임**
     1. 'eclipse.ini' 파일을 텍스트 편집기로 엽니다.
     2. 파일속 -vmargs 바로 위에 다음 두 줄을 추가합니다:
        -vm<br>
        C:\Program Files\Java\jdk-17\bin<br><br>
        이미 적혀있던건 이거였다: plugins/org.eclipse.justj.openjdk.hotspot.jre.full.win32.x86_64_21.0.6.v20250130-0529/jre/bin
   - 워크스페이스(workspace=작업폴더) 지정하기
     - File > Switch Workspace > Other
   - 롬복(Lombok) 플러그인 설치하기
     - 맨날 부트에서 의존성으로 받았는데 그 롬복 말하는거.
   - 폰트 설정하기
     1. Window > Preferences > General > Appearance > Colors and Fonts로 이동합니다.
     2. "Basic" 폴더에서 "Text Font"를 선택합니다.
     3. "Edit" 버튼을 클릭하여 원하는 폰트를 선택합니다.
   - 인코딩(encoding) 방식 변경하기
     1. Window > Preferences > General > Workspace로 이동합니다.
     2. Text file encoding 항목을 UTF-8로 변경합니다. (보통 이미 UTF-8임.)
     3. CSS, HTML, JSP 등 웹 파일의 인코딩 방식도 Window > Preferences > Web 에서 UTF-8로
   - 기본 브라우저 변경하기: 이클립스는 기본 내장 브라우저 사용해서 외부로 바꿔주기
     1. Window > Preferences > General > Web Browser로 이동합니다.
     2. "Use external web browser"를 선택합니다.
     3. 원하는 브라우저를 선택합니다.
     4. 목록에 없는 경우 "New"를 클릭하여 추가할 수 있습니다.
   - 퍼스펙티브(perspective) 설정하기: 이클립스를 열면 개발 목적에 맞게 미리 준비된 전체 영역이 보임.
     - 전체 퍼스펙티브 옵션 보기: 주황색 박스 왼쪽의 'Open Perspective'를 선택
     - **본인은 JAVA EE 환경 + GIT + Debug 세팅**
   - **실제 자주 사용하던건?**  
     Project->Build Auto : 저장마다 .class 생성<br>
     Open Perspective → Debug, Git, Java 등 화면 구성<br>
     New Java Project → JDK17 사용<br>
     참고) zip 파일 import로 압축 풀 필요 없이 간단히 프로젝트 추가 가능<br>
     참고) 이클립스가 혹시 JDK버전 문제로 실행 안될 땐 'eclipse.ini' 도 고려<br>
     참고) 기본 내장 브라우저 사용 시 Window > Preferences > General > Web Browser
3. **추가 정보: 톰캣 설치해야겠죵?** -> `C:\Program Files\Apache Software Foundation\Tomcat 10.0` 본인은 예전에 톰캣10 설치해뒀길래 이거 그대로 사용하겠음.
   물론, Spring Boot는 내장 톰캣지원함. STS4는 Spring Boot를 지원하니까 당연히 내장 톰캣도 가능!!<br><br>
   **톰캣 필수 고려할 환경 설정~!**
   - 서버테스트
     - 로컬: 톰캣 > bin > startup.bat
     - IDE: [Window] - [Preferences] - [Server] - [Runtime Environments]<br>
       또는 이클립스 하단에 있는 `Servers` 뷰에서 파란색 링크
     - 참고) Spring Boot 사용 시 내장 톰캣있음! spring-web라이브러리에 포함! **단, JSP는 꼭 따로 임베디드 톰캣 JSP 라이브러리를 추가해줘야 정상 동작!**
   - 포트 번호 변경
     - 로컬: 톰캣 > conf > server.xml 에서 설정
     - IDE: Servers 항목을 더블 클릭하여 Overview 에서 설정
</div>
</details>

<details><summary><b>Eclipse의 실행환경 설정과 우선순위(JDK) - 초기 설정에 참고</b></summary>
<div markdown="1"><br>
Project->Build Auto : 저장마다 .class 생성<br>
Open Perspective → Debug, Git, Java 등 화면 구성<br>
New Java Project → JDK17 사용<br>
참고) zip 파일 import로 압축 풀 필요 없이 간단히 프로젝트 추가 가능<br>
참고) 이클립스가 혹시 JDK버전 문제로 실행 안될 땐 'eclipse.ini' 도 고려<br>
참고) 기본 내장 브라우저 사용 시 Window > Preferences > General > Web Browser<br>
<br>
**이클립스는 다음과 같은 우선순위로 실행 환경을 결정합니다:**<br>
=> 참고1: 이클립스가 구동되기 위한 jvm인 jdk21이랑 플젝에서 사용중인 jdk17이랑 독립.<br>
=> 참고2: 프로젝트 우클릭 → `Properties > Java Compiler`로 이동해서 컴파일러 level부터 꼭 체크!
1. 프로젝트별 설정 - 빌드 경로(`Java Build Path`)에 설정된 JRE/JDK.<br>  
   +) Gradle/Maven 등 빌드 도구에 명시된 JDK. => build.gradle 같은거.<br>
   프로젝트를 우클릭하고 `Properties > Java Build Path > Libraries` 에서 `JRE System Library`를 선택한 후 `Edit` 버튼을 클릭<br>
   build.gradle도 당연히 jdk 버전 맞춰야겠지?
2. 이클립스의 기본 Execution Environment 설정.<br>
   `Window > Preferences > Java > Installed JREs` 에서 확인. 기본값이 무엇인지.  
   본인의 경우 jre(jdk21)이 체크. 이걸 뭐.. jdk17로 바꾸던지 아래 javase-17항목에서 jdk21대신 jdk17로 바꾸던지. **(물론, 건드릴 필요없이 걍 1단계에서 끝남.)**<br>
   `Window > Preferences > Java > Installed JREs > Execution Environments` 에서 17이면 JavaSE-17 항목에 연결된 JRE/JDK 체크
3. 최종적으로 시스템 PATH 변수에 등록된 Java 버전.<br>
   이건 그 시스템 환경변수에서 등록한거 말함.
</div>
</details>
<br>

<details><summary><b>IntelliJ 단축키</b></summary>
<div markdown="1"><br>
* `Alt + Insert` : getter, setter, constructor 등 자동 생성
* **`Ctrl + Alt + O` : 사용안하는 import 자동 정리(제거 등)**
  * **`Ctrl + Alt + L` : 자동 정렬 -> Google Java Convention이나 Default 로 사용중**
* `Ctrl + Alt + V` : 변수 선언부를 자동 작성
* `Ctrl + Alt + M` : 코드 리팩토링하기 쉽게끔 함수 자동 생성
* `Ctrl+T->extra method` : 코드 리팩토링하기 쉽게끔 드래그한 코드를 하나의 함수로 자동 생성
* `Alt + Shift + Down/Up` : 코드 한줄을 위, 아래 자리 이동 가능
* `Ctrl + D` : 코드 한줄 바로 아래에 복제
* `Ctrl + Alt + Shift` : 멀티 커서 가능
* `Shift + F6` : 변수명을 한번에 바꿀 때 사용
* `Alt + 1 ` : 왼쪽 프로젝트 폴더 구조 열기
* `Alt + F12` : 터미널 창 열기
</div>
</details>
<details><summary><b>Eclipse 단축키(키맵)</b></summary>
<div markdown="1"><br>
- **코드 실행 및 프로젝트 관리**
  - 실행하기: Ctrl + F11
  - 프로젝트 실행하기: Ctrl + Alt + R
  - 프로젝트 속성으로 이동하기: Alt + Enter
  - 디버그 모드로 실행: F11
- **코드 편집**
  - 패키지 임포트 및 정리하기: Ctrl + Shift + O
    - 아직 임포트가 부족하다면: 이클립스 상단에 Project > Clean
  - 추천 리스트 받기: Ctrl + Space
  - 주석 처리/제거하기: Ctrl + / (=JAVA) 또는 Ctrl + Shift + / (=XML,JSP)
  - 자동 정렬하기: Ctrl + Shift + F
  - 들여쓰기/내어쓰기: Tab ↔ Shift + Tab
  - 코드 한 줄 위/아래로 이동하기: Alt + ↑ ↔ Alt + ↓
  - 특정 함수 위치로 이동하기: F3
  - 화면 확대/축소하기: Ctrl + + ↔ Ctrl + -
- **리팩토링 및 코드 생성**
  - 세터(Setter) 생성하기: Alt + Shift + S, R
  - 생성자(Constructor) 생성하기: Alt + Shift + S, O
  - 인터페이스 만들기: Alt + Shift + T, I
  - 메서드 추출: Alt + Shift + M
  - 변수 추출: Alt + Shift + L
  - 클래스 이름 변경: Alt + Shift + R
- **네비게이션**
  - 이전/다음 화살표 위치로 가기: Alt + ← ↔ Alt + →
  - 패키지 복사하기: F2
  - 파일 간 전환: Ctrl + E
  - 메서드 간 이동: Ctrl + O
- **검색 및 찾기**
  - 파일 찾기: Ctrl + Shift + R
  - 전체 검색: Ctrl + H
  - 선언부로 이동: F3 또는 Ctrl + 클릭
- **편집기 관리**
  - 편집기 전체화면 전환: Ctrl + M
  - 다음 편집기로 전환: Ctrl + F6
  - 편집기 닫기: Ctrl + W
- **기타**
  - 빠른 수정: Ctrl + 1
  - 오류 수정 제안: Ctrl + .
</div>
</details>

<br>

<br>

## 개발흐름, 네이밍 TIP

<br><br>

### 개발흐름

**참고: [깃, 구글 자바 컨벤션과 기능개발 흐름](https://bh946.github.io/knowledge/CHECK_LIST_JAVA_CONVENTION/)**

**개발과정**

* **요구사항 분석(대략적 기능)**
* **기능 목록(상세한 기능)**
* **도메인 설계**
  * 도메인 모델 분석(간략히)
  * 테이블 설계(DB) + 엔티티 설계(JPA)
  * **`ERDCloud` 툴을 사용한 설계 => 테이블, 엔티티 설계를 여기서 한번에**
* **기능 구현 -> TDD 기법 활용 권장**
  * 도메인 구현 -> 엔티티를 의미하며, 모든 계층에서 사용
  * 레퍼지토리 구현 -> DB와 상호작용
  * 서비스 구현 -> 비지니스 로직 & 트랜잭션
    * `도메인 모델 패턴` : 서비스 계층은 단순히 엔티티에 필요한 요청을 위임하는 방식
    * `트랜잭션 스크립트 패턴` : 엔티티에는 비지니스 로직이 거의 없고 서비스 계층이 담당하는 방식
    * **참고로 본인은 `도메인 모델 패턴` 방식을 주로 사용**

  * 컨트롤러 구현 -> 웹 계층과 상호작용 (API 포함)

<br><br>

### 네이밍 TIP

**Database**

- 테이블명 형식으로 `ORDER 또는 order` 사용 **=> 대문자** or 소문자
- 컬럼명 형식으로 `order_id` 사용 **=> 스네이크 케이스**
  - 보통 **PK**인 컬럼명을 `테이블명_id` 형태로 쓰지만, 나머지 컬럼명들은 `테이블명` **을 안붙이는 편** (ex:member_id, name)
- 스프링에서 테이블 매핑 마지막에 전부 **"대문자"**로 자동
  - 실제로 스프링 부트로 DB 테이블 자동 생성시 **"대문자" 이름 확인**

<br>

**JPA -> ORM(객체 관계 매핑)**

- 엔티티명 형식으로 `OrderItem` 사용 **=> 파스칼 케이스**
  - **스프링 부트는 엔티티명을 `OrderItem -> ORDERITEM` 처럼 "대문자"로 바꿔서 매핑**
- 필드명 형식으로 `orderId` 사용 **=> 카멜 케이스**  
  - **스프링 부트는 필드명을 `orderId -> order_id` 로 컬럼명 찾아서 매핑**

<br>

<br>

## (도메인) 테이블 설계와 엔티티 구현

<br><br>

### 테이블 설계

**N:M** 관계는 **1:N, N:1** 로 풀기

* N:M 관계를 두 테이블로 구성하는건 데이터 중복 야기. 외래키를 2개 써서 해결하려 해도 애초에 외래키 2개 사용 자체가 너무 비효율. 가능은 한지도 모르겠고.

  <details><summary><b>테이블 관계 TIP</b></summary>
  <div markdown="1">
  1. 관계를 생각 할 때 테이블로 생각하지 말고, '한 행'을 기준으로 생각. 테이블 명도 마찬가지.<br>
     - '학생', '수업'<br>
  2. 논리적으로 생각할 땐, 연결(매핑) 테이블은 생각하지 않는다.<br>
     - 철수의 학생 코드는 학생_수업 테이블에 여러개 존재한다. **(X)**<br>
     - 철수는 국어, 영어, 수학 수업을 수강한다.**(O)**<br>
  3. 항상 일대다(1:N) 기준으로 생각하자. 다대일(N:1)보다 직관적<br>
     - 철수가 여러 수업을 수강한다.**(O)**<br>
     - 국어, 영어, 수학은 철수를 수용 한다**.(X)**<br>
  <img src="https://github.com/user-attachments/assets/21355a21-4a11-460e-879d-1685387e1a57" alt="N:M관계" style="zoom:80%;" /><br>
  <img src="https://github.com/user-attachments/assets/5f9f899c-86e8-4f51-ab52-e872fba9863a" alt="1:N:1관계"/>
  </div>
  </details>

<br>

**외래키가 있어야할 위치**

* **1:N, N:1** 의 경우 **N**에 사용 -> **1**은 양방향 필요시 연결(엔티티)

  <details><summary><b>1쪽에 FK 놓으면 2가지 단점</b></summary>
  <div markdown="1"><br>
  플레이어(N) 2명 등록 후 첫 팀(1)에 모두 등록 시?? 플레이어:팀=N:1 관계<br>
  - Team 테이블에 pk 중복 문제<br>
  - Player 테이블 업뎃 시 Team 테이블도 같이 업뎃 문제<br>
  ```sql
  insert into Player (id, name) values (1, '철수');
  insert into Player (id, name) values (2, '훈이');
  insert into Team (id, name, player_id) values (1, '떡잎팀', 1); -- 철수 추가
  insert into Team (id, name, player_id) values (1, '떡잎팀', 2); -- 훈이 추가
  ```
  </div>
  </details>

* **1:1**의 경우 **상황에 따라** 사용 - 보통은 **주 테이블에 외래키** 사용
  
  * **주 테이블 외래키 단방향** - 단점 : 값 없으면 외래 키에 null 허용
  * **대상 테이블에 외래키 양방향** - 단점 : 무조건 즉시로딩
  
  <details><summary><b>객체 관점과 DB 관점의 양방향 차이</b></summary>
  <div markdown="1"><br>
  데이터베이스의 양방향 관계: 외래 키 하나만으로도 양쪽 테이블을 자유롭게 조인할 수 있다. 따라서 단방향이나 양방향의 개념이 특별히 존재하지 않는다.
  객체의 양방향 관계: 참조 필드가 있는 쪽에서만 다른 객체를 참조할 수 있다는 특징이 있다. 따라서 두 가지 관계가 존재한다.(단방향, 양방향)
  </div>
  </details>

<br>

**상속 매핑**은 `일반적인 전략, Mapped Superclass` 을 사용

* **일반적인 전략** : JOINED, SINGLE_TABLE 방식이 유명한데 보통 **JOINED 방식을** 선호
  * **부모 자식간에 join**을 하게 됨
    * 참고로 join 열에 Index 추가하면 빠르게 join이 가능
  * 혹시나 테이블이 너무 단순하다면 SINGLE_TABLE 을 사용 -> 조인이 없어서 속도 빠름!

* **Mapped Superclass 전략**
  * 상속 매핑으로 선언된 클래스를 상속받게 되면 해당 상속 내용을 전부 테이블에 넣을 수 있다.

<br><br>

### 엔티티 구현

**객체 중심** 설계!

* ```java
  @Getter @Entity @Slf4j // @Slf4j 는 log
  @NoArgsConstructor(access = AccessLevel.PROTECTED)
  @Table(name = "MEMBER", indexes = @Index(name = "IDX_MEMBER_ID", columnList = "member_id desc")) // 인덱스 추가 법
  public class Member {...}
      
  @Id // pk
  @GeneratedValue
  @Column(name = "member_id") // db컬럼명 매핑, 참고로 nullable = false 속성은 not null
  private Long id; // 엔티티에선 id 에도 보통 "테이블명 생략"
  ```
  
* 보통 **PK**인 필드명을 `id` 로 쓰고 **직접** 테이블의 컬럼명과 **매핑**을 선언함 - @Column(...)

* 개발과정에선 **@Getter, @Setter**를 열어두고 나중에 **리팩토링으로 @Setter 제거**
  * 엔티티에서의 **비지니스 메서드** 구현은 **Setter 제거 효과**
  * **setter를 최대한 사용하지 않게끔 DTO 방식 권장**
  * **Setter 제거** 위해 **생성자 Protected 패턴 (생성 편의 메서드 static으로 선언)** -> protected는 동일 패키지 까지만 허용
    - `@NoArgsConstructor(access = AccessLevel.PROTECTED)` 활용
    - 코드 의미대로 No Args(매개변수x) 생성자를 생성하는 코드!! (범위는 Protected)

* `GenerationType.AUTO` 옵션이 기본값 (확실한 건 직접 택을 권장)

  * 자동으로 IDENTITY, SEQUENCE, TABLE 중 택1
    * IDENTITY: DB가 제공하는 auto_increment 자동증가 컬럼
    * SEQUENCE: 오라클에서 사용하던 그 시퀀스
    * TABLE: 별도의 테이블로 ID값 관리

<br>

엔티티 설계 때 **연관관계는 단방향 우선 개발(테스트)** 후 양방향 관계 추가

* ```java
  @OneToMany(mappedBy = "member") // 양방향 (member:lists = 1:N)
  private List<Lists> lists = new ArrayList<>(); // 컬렉션은 필드에서 바로 초기화
  
  @OneToOne(fetch = FetchType.LAZY) // 단방향(=원래방향), 지연로딩 설정
  @JoinColumn(name = "profile_id") // FK
  private Profile profile;
  
  // CascadeType.REMOVE 를 해줘야 고아객체가 안생기게 되며, Lists 삭제도 정상
  @OneToMany(mappedBy = "lists", cascade = CascadeType.REMOVE) // 양방향
  private List<Task> tasks = new ArrayList<>();
  ```

* 양방향은 코드만으로 해결 가능해서 **DB 설계에 아무런 영향을 끼치지 않음**

* **양방향TIP: 연관관계 편의 메서드 + mappedBy** 를 세트로 항상 같이 작성 (개인적인 생각)

<br>

즉시 or 지연 로딩 중에서 무조건 **"지연 로딩"** 으로 개발 => **즉시 로딩의 N+1 문제** 때문


  - [정말 잘 정리하신 분! 참고 N+1](https://velog.io/@jinyoungchoi95/JPA-%EB%AA%A8%EB%93%A0-N1-%EB%B0%9C%EC%83%9D-%EC%BC%80%EC%9D%B4%EC%8A%A4%EA%B3%BC-%ED%95%B4%EA%B2%B0%EC%B1%85)
    
    <details><summary><b>즉시 로딩, 지연로딩에서 N+1 왜 발생??</b></summary>
    <div markdown="1"><br>
    참고 -> User:Article = 1:N 가정<br><br>
    **(1)즉시로딩 예시**<br>
    <img src="https://github.com/user-attachments/assets/1dfa20b9-817c-4e6b-8e50-f5fdf9d1a424" alt="image" style="zoom:80%;" /><br>
    <img src="https://github.com/user-attachments/assets/7f266527-92a0-4669-adb6-ae6aecf17b38" alt="image" style="zoom:50%;" /><br>
    <img src="https://github.com/user-attachments/assets/d7b0aab1-7246-4363-b6fc-ce79e97cd194" alt="image" style="zoom:80%;" /><br>
    모든 User 검색(findAll) 요청(1번) 시 User의 컬럼에 Article 때문에(N번) 추가 쿼리<br>
    즉시 로딩임을 보고 바로 추가 쿼리 날렸음<br><br>
    **(2)지연로딩 예시**<br>
    <img src="https://github.com/user-attachments/assets/05bada9d-a33d-4479-a403-e42d1ca41ff8" alt="image" style="zoom:80%;" /><br>
    <img src="https://github.com/user-attachments/assets/10c39d5f-87aa-475e-b895-4bb0e4e53669" alt="image" style="zoom: 80%;" /><br>
    <img src="https://github.com/user-attachments/assets/8b65009f-09f4-4ead-aabc-e30b046c8bb5" alt="image" style="zoom:80%;" /><br>
    모든 User 검색(findAll) 요청(1번) 후 나중에 User의 컬럼에 Article 조회할 때(N번) 추가 쿼리<br>
    지연 로딩임을 보고 처음에 추가 쿼리를 날리지는 않음. 단, 이후에 User.article 접근할 때 이미 User는 select했어서 join을 사용하지 못하고 N번 Article을 추가 select 쿼리 발생
    </div>
    </details>
    
  - 코드상에서 `@XToOne` 은 기본이 **즉시 로딩**이므로 반드시 **지연로딩**으로 전부 변경

  - 단, **"지연 로딩"**도 **N+1 문제** 발생시킬 수 있다는 점을 알고가자 -> **fetch join**으로 해결

  - **"지연 로딩"**이 가능한 이유 : "프록시(가짜객체)"를 사용하기 때문

<br>

옵션 중에서 **cascade** 사용 유무는 관계가 **완전 종속일때만** 사용 (연관된 데이터 연쇄적 변경 효과)

- cascade는 영속성 전이를 하므로, 연관관계 매핑과는 전혀 관계 없음 

- 단지 이를 사용하면 **생명주기를 같이** 하는것

  <details><summary><b>두 가지 예시 코드</b></summary>
  <div markdown="1">
  ```java
  //Task.java
  @OneToOne(fetch = FetchType.LAZY, cascade = CascadeType.ALL) // 1:1관계며 같이 존재함. (생명주기 같아야함)
  @JoinColumn(name = "task_status_id")
  private TaskStatus taskStatus;
  //
  //Lists.java
  // CascadeType.REMOVE 를 해줘야 고아객체가 안생기게 되며, Lists 삭제도 정상적으로 가능
  @OneToMany(mappedBy = "lists", cascade = CascadeType.REMOVE) // 양방향
  private List<Task> tasks = new ArrayList<>();
  ```
  </div>
  </details>

<br>

**중복 코드**를 줄이는 효과적인 방법들

- **`임베디드 타입(값 타입)` 과 `상속-Mapped Superclass`** 이것 두개를 잘 활용 + 간단한 건 `Enum` 도 좋음 -> 중복 코드를 많이 줄임

  - `값 타입` 은 좁은 범위 중복 때, `상속` 부분은 넓은 범위 중복 때 사용하자  
    -> 상속은 필드 뿐만아니라 메소드까지 상속 되니까!

    - `값 타입` 은 엔티티 클래스에 `private Long id;` 와 같은 필드라고 생각!
    - 그리고 식별자가 없으므로 "엔티티와 혼동X"
    - 또한, 값 비교에 equlas 오버라이드는 필수

  - **설계할때 부터 "값 타입"으로 활용될거는 따로 빼서 설계 + 상속은 중복 보고 리팩토링 하던지**

    <details><summary><b>임베디드 타입(값 타입)+컬렉션 엔티티 예시</b></summary>
    <div markdown="1">
    - "임베디드 객체" 는 값 타입 하나. @Embedded 로<br>
    - "값 타입 컬렉션" 은 값 타입 하나 이상. 일대다 고아+cascade 로<br>
    <br>**임베디드 객체**<br>
    <div markdown="1">
    ```java
    @Embeddable //임베디드 타입(값 타입) 생성
    public class Address {
      private String street;
      private String city;
      private String zipCode;
      //
      // 반드시 equals(), hashCode()를 오버라이드 해야 합니다.
      @Override
      public boolean equals(Object o) {
        if (this==o) return true;
        if (o == null || this.getClass() != o.getClass()) return false;
        Address address = (Address) o;
        return Objects.equals(street, address.street) &&
            Objects.equals(city, address.city) &&
            Objects.equals(zipCode, address.zipCode);
      }
      //
      @Override
      public int hashCode() {
        return Objects.hash(street, city, zipCode);
      }
    }
    ```
    </div>
    <div markdown="1">
    ```java
    @Entity
    public class User {
      @Id @GeneratedValue
      private Long id;
      private String name;
      @Embedded //임베디드 타입(값 타입) 사용
      private Address address;
      //...
    }
    //
    //테이블 구조
    +-----------------+
    |      User       |
    +-----------------+
    | id (PK)        |
    | name           |
    | street         |
    | city           |
    | zipCode        |
    +-----------------+
    ```
    </div>
    **값 타입 컬렉션 대안 -> "영속성 전이 + 고아 객체 제거" 1:N 엔티티**<br>
    `@ElementCollection` 로 값 타입 컬렉션이 가능하지만 별로 추천하진 않고 "영속성 전이 + 고아 객체 제거" 1:N 엔티티를 추천!<br>
    <div markdown="1">
    ```java
    //임베디드로 만든 Address가 아닌 엔티티로 만들기
    @Entity
    public class Address {
      @Id @GeneratedValue
      private Long id;
      private String street;
      private String city;
      private String zipCode;
      //...
    }
    ```
    </div>
    <div markdown="1">
    ```java
    //Member.java
    //    @ElementCollection
    //    @CollectionTable(name = "ADDRESS", joinColumns = @JoinColumn(name = "MEMBER_ID"))
    //    private List<Address> addressHistory = new ArrayList<>();
    //
    @OneToMany(cascade = ALL, orphanRemoval = true)
    @JoinColumn(name = "MEMBER_ID")
    private List<Address> addresses = new ArrayList<>();
    ```
    </div>
    </div>
    </details>
    
    
    <details><summary><b>상속-@MappedSuperclass</b></summary>
    <div markdown="1"><br>
    특징: 필드+메소드까지 상속<br>
    <div markdown="1">
    ```java
    @MappedSuperclass //생성
    public abstract class BaseEntity {
      @Id
      @GeneratedValue
      private Long id;
      private LocalDate createdAt;  // 날짜만
      private LocalDate updatedAt;
      //
      @PrePersist
      public void onPrePersist() {
        this.createdAt = LocalDate.now();  // 현재 날짜로 설정
        this.updatedAt = LocalDate.now();
      }
      @PreUpdate
      public void onPreUpdate() {
        this.updatedAt = LocalDate.now();  // 수정 날짜 갱신
      }
    }
    ```
    </div>
    <div markdown="1">
    ```java
    @Entity
    public class Product extends BaseEntity {
      private String name;
      private double price;
      // 다른 필드들...
    }
    @Entity
    public class Order extends BaseEntity {
      private String orderNumber;
      // 다른 필드들...
    }
    ```
    </div>
    </div>
    </details>
    - `@PrePersist`: 엔티티가 **처음 저장되기 전에** 실행되는 메서드를 정의. (`createdAt`, `updatedAt` 자동 설정)
    - `@PreUpdate`: 엔티티가 **업데이트되기 전에** 실행되는 메서드를 정의. (`updatedAt` 자동 갱신)
  
- **ENUM** 데이터 사용 시 `@Enumerated(EnumType.STRING)` 로 꼭 `STRING` 으로 옵션

  - ```java
    public enum OrderStatus {
      NEW, PROCESSING, COMPLETED, CANCELLED
    }
    
    @Entity
    public class Order {
      @Enumerated(EnumType.STRING)
      private OrderStatus status;
      // 다른 필드들...
    }
    ```

  - 데이터베이스에 `NEW`, `PROCESSING` 같은 "문자열이 저장" (인덱스 이런게 아니라)

  - 따라서 Enum 순서가 바뀌거나 값이 바뀌어도 문제가 생기지 않음.

<br>

**초기화 - 생성자 주입, 컬렉션**

- **컬렉션(List같은)은 필드**에서 초기화 하자
  - 코드간결, **null 문제에서 안전**
  - ex: `private List<Task> tasks = new ArrayList<>();`
- 의존성 주입(DI)은 필드 주입이나 setter 주입 대신에 **생성자 주입을 사용**하자. 
  - 즉, DI 중 @Resource, @Autowired를 이용한 Field Injection보다는  
    **@RequiredArgsConstructor와 final**을 이용한 **Constructor Injection**을 사용하자
  - **@RequiredArgsConstructor는 “final 붙은 필드를 인자로 받는 생성자"를 자동 생성**
    - ex: `private final ExpService expService` 선언만 해도 바로 사용 가능!
  - **주의**: 객체에 관한 생성자 1개일때 Spring 4.3이후부턴 자동으로 @Autowired 가 붙어서 위 방식을 사용한거지만 여러 생성자를 사용할 경우는 무슨 생성자에 생성자 주입을 사용할지 선택해서 @Autowired를 꼭 붙여줘야 함.

<br>

**엔티티의 메서드 TIP : 생성\_편의 메서드, 연관관계\_편의 메서드, 비지니스\_편의 메서드(업뎃,조회 등) 권장**

- **생성 메서드** 사용 권장 -> 생성자 대용 굿

  - ex: `public static Member createMember()` 같은 것

  - 목적 : 엔티티에 있는 다양한 속성들을 이 생성메서드 **하나로 간편**히 다 적용 & **무분별한 엔티티 생성을 막기** 위함 -> 생성자 Protected 패턴!!
  
  - 이를 위해 **`@NoArgsConstructor(access = AccessLevel.PROTECTED)` 사용!**
  
    - 기본 생성자의 접근 제어를 PROTECTED로 설정해놓게 되면 **무분별한 객체 생성에 ide 상에서 한번 더 체크할 수 있는 수단**
    - Protected 접근자는 반드시 **같은 패키지만 허용!!** - 상위, 하위 패키지 전부 불가
  
    <details><summary><b>생성 메서드 코드</b></summary>
    <div markdown="1">
    ```java
    // 기본생성자 Public->Protected
    @NoArgsConstructor(access = AccessLevel.PROTECTED)
    public class Order {
      // ...
      //==생성 메서드==// => 수많은 정보 한번에! 그리고 public static 필수
      public static Order createOrder(Member member, Delivery delivery, OrderItem... orderItems) {
        Order order = new Order();
        order.setMember(member);
        order.setDelivery(delivery);
        for(OrderItem orderItem : orderItems) {
          order.addOrderItem(orderItem);
        }
        order.setStatus(OrderStatus.ORDER);
        order.setOrderDate(LocalDateTime.now());
        return order;
      }
    }
    // main 함수 내부 가정
    // 외부 가능
    Order order1 = Order.createOrder(member, delivery, orderItems); 
    order1.setDelivery(delivery2); // 정상
    // 외부 불가능 (Protected:같은 패키지만 가능!!)
    Order order2 = new Order(); 
    order2.setDelivery(delivery2); // ide상에서 에러 발생
    ```
    </div>
    </details>
  
- **연관관계\_편의 메서드** 사용 권장 -> **양방향**에 굿!!

  - ex: `addLists(), sestCharacter()` 같은 것

    - (가정) Order와 OrderItem는 1:N이고 외래키 가진 주인은 OrderItem 인 상태이다.
    - (1) 주인인 OrderItem가 Order를 접근하는건 **단방향**이므로 **바로** 접근 가능
    - (2) Order가 OrderItem를 접근하는건 **양방향**이므로 **조금 돌아가서** 접근 가능
    - **(3) Order->OrderItem 접근을 간단히 사용하기 위해 연관관계 메서드를 사용**

    <details><summary><b>연관관계 메서드 코드</b></summary>
    <div markdown="1">
    ```java
    // (1) OrderItem->Order 접근 예시 => 단반향
    orderItem.getOrder(); // orderItem에서 order정보 접근 모습
    //
    // (2) Order->OrderItem 접근 예시 => 반대방향 (양방향)
    order.getOrderItems().add(orderItem); // order에 orderItem추가 목적
    orderItem.setOrder(order); // 두줄 필요 (중요!!)
    //
    // (3) 연관관계 편의 메서드
    // 즉, Order->OrderItem 접근(양방향)을 편의 메서드 만들어서 활용!
    // Order 클래스 내부
    public void addOrderItem(OrderItem orderItem) {
      this.orderItems.add(orderItem);
      orderItem.setOrder(this);
    }
    // main 함수 내부
    public void main~~(){
      order.addOrderItem(orderItem); // 한줄로 order에 orderItem정보 추가
    }
    ```
    </div>
    </details>


  - **비지니스 메서드** 사용 권장

    - Service 파트가 아닌 Entity파트에서 비지니스 로직 구현이 가능할 것 같은 경우에는 Entity에서 개발을 적극 권장(=**도메인 모델 패턴**) => **장점 : 좀 더 객체 지향적인 코드**

      <details><summary><b>비지니스 메서드 코드</b></summary>
      <div markdown="1">
      ```java
      // 간단히 엔티티에서 구현 가능하면, 서비스가 아닌 엔티티에서 로직 구현(객체지향적)
      // 재고 추가 함수 (비지니스 로직)
      public void addStock(int quantity) {
        this.stockQuantity += quantity;
      }
      // 날짜 비교 함수 (비지니스 로직)
      private static boolean compareDate(Task task, LocalDateTime listsDate) {
        // 년,월,일 만 비교하면 충분 하므로 Time 은 비교X
        LocalDate taskDay = task.getStartTime().toLocalDate();
        LocalDate listsDay = listsDate.toLocalDate();
        if (taskDay.compareTo(listsDay) == 0) { // 동일시 0
          return true;
        }
        return false;
      }
      // update (비지니스 로직)
      public Lists updateTime(Long timerAllUseTime, Long curTime) {
        this.timerAllUseTime = timerAllUseTime;
        this.curTime = curTime;
        return this;
      }
      // 주문상품 전체 가격 조회 (비지니스 로직)
      public int getTotalPrice() {
        return getOrderPrice() * getCount(); // 가격 * 수량 = 주문상품 가격
      }
      ```
      </div>
      </details>

<br>

**추가 TIP: 엔티티에 정규식(Valid)과 타입컨버터 + DTO방식**

- 규칙을 @Pattern 애노테이션 하나로 바로 적용이 가능 -> Vaildation 꺼임.

  - `@NotNull @Pattern(regexp = "^[0-9]+", message = "비밀번호는 숫자로 입력 해주세요.")`

- 타입컨버터 사용으로 LocalDateTime으로 저장된 데이터가 나중에 **사용할 때 정해둔 pattern 방식으로 String 반환되는 것!**

  ```java
  private String password;
  @DateTimeFormat(pattern = "yy.MM.dd.HH:mm")
  private LocalDateTime date1;
  @DateTimeFormat(pattern = "yy년 MM월 dd일 HH시 mm분")
  private LocalDateTime date2;
  ```

- 만약 타입컨버터 애노테이션을 사용안했으면?? -> **더 복잡한 코드**

  ```java
  private String date1; // string으로 변경 및 format 활용
  private String date2;
  
  DateTimeFormatter formatter1 = DateTimeFormatter.ofPattern("yy.MM.dd.HH:mm");
  DateTimeFormatter formatter2 = DateTimeFormatter.ofPattern("yy년 MM월 dd일 HH시 mm분");
  item.date1 = LocalDateTime.now().format(formatter1);
  item.date2 = LocalDateTime.now().format(formatter2);
  ```

- **Item.java -> dto/AddItemDto.java, dto/UpdateItemDto.java**

  <details><summary><b>예시 코드</b></summary>
  <div markdown="1"><br>
  **Item.java**
  ```java
  @Entity
  @Getter
  @NoArgsConstructor(access = AccessLevel.PROTECTED)
  public class Item {
    @Id
    @GeneratedValue
    @Column(name = "item_id")
    private Long id;
    private Long No;
    private String nickName;
    private String password;
    private String title;
    private String content;
    private String imgSrc;
    @DateTimeFormat(pattern = "yy.MM.dd.HH:mm")
    private LocalDateTime date1;
    @DateTimeFormat(pattern = "yy년 MM월 dd일 HH시 mm분")
    private LocalDateTime date2;
    //==생성 편의 메서드==//
    public static Item createItem(AddItemDto addItemDto) {
      Item item = new Item();
      item.nickName = (addItemDto.getNickName().equals("")) ? "익명" : addItemDto.getNickName();
      item.password = (addItemDto.getPassword().equals("")) ? "" : addItemDto.getPassword();
      item.title = (addItemDto.getTitle().equals("")) ? "무제" : addItemDto.getTitle();
      item.content = (addItemDto.getContent().equals("")) ? "" : addItemDto.getContent();
      item.imgSrc = addItemDto.getImgSrc();
      item.date1 = LocalDateTime.now();
      item.date2 = LocalDateTime.now();
      return item;
    }
    //==비지니스 로직 편의 메서드==//
    public Item updateItem(UpdateItemDto dto) {
      this.nickName = (dto.getNickName().equals("")) ? "익명" : dto.getNickName();
      this.password = (dto.getPassword().equals("")) ? "" : dto.getPassword();
      this.title = (dto.getTitle().equals("")) ? "무제" : dto.getTitle();
      this.content = (dto.getContent().equals("")) ? "" : dto.getContent();
      // 최신 업데이트 시간
      this.date1 = LocalDateTime.now();
      this.date2 = LocalDateTime.now();
      return this;
    }
  }
  ```
  **AddItemDto.java -> id와 date가 없는 detail**
  ```java
  @Getter
  public class AddItemDto {
    @NotNull
    private String nickName;
    @NotNull
    @Pattern(regexp = "^[0-9]+", message = "비밀번호는 숫자로 입력 해주세요.")
    private String password;
    @NotNull
    private String title;
    @NotNull
    private String content;
    @NotBlank(message = "이미지가 없습니다. 다시 시도하세요.")
    private String imgSrc;
    //==생성 편의 메서드==//
    public AddItemDto(String nickName, String password, String title, String content, String imgSrc) {
      this.nickName = nickName;
      this.password = password;
      this.title = title;
      this.content = content;
      this.imgSrc = imgSrc;
    }
  }
  ```
  **UpdateItemDto.java -> id가 있는 datil**
  ```java
  @Getter
  public class UpdateItemDto {
    @NotNull
    private Long id;
    @NotNull
    private String nickName;
    @NotNull
    @Pattern(regexp = "^[0-9]+", message = "비밀번호는 숫자로 입력 해주세요.")
    private String password;
    @NotNull
    private String title;
    @NotNull
    private String content;
    @NotBlank(message = "이미지가 없습니다. 다시 시도하세요.")
    private String imgSrc;
    //==생성 편의 메서드==//
    public UpdateItemDto(Long id, String nickName, String password, String title, String content,
        String imgSrc) {
      this.id = id;
      this.nickName = nickName;
      this.password = password;
      this.title = title;
      this.content = content;
      this.imgSrc = imgSrc;
    }
  }
  ```
  </div>
  </details>

<br>

<br>

## (레포지토리=DAO, 서비스) 기능 구현 + 인터페이스

**인터페이스 관련해서 얘기하기 위해 주제에서 언급했다. 본인은 확장성을 위해서라도 항상 "레포지토리,서비스 단에 인터페이스를 활용"할 생각이다.**

**인터페이스를 간략히 알아보자.**

- **ve1)만약 MemberRepository 클래스**를 구현 했다면??  
  **ver2)만약 MemberRepository 인터페이스를 정의**하고 **MemberRepositoryImpl 클래스로 해당 인터페이스를 구현(implements)** 했다면??
- 둘 다 서비스계층에서 바로 `memberRepository.save()` 이런식으로 사용 가능
  - 여기서 알 수 있는 장점 -> 인터페이스를 구현하는 **MemberRepositoryImpl2 클래스**를 또 추가해서 구현체를 바꿔도 "서비스계층의 `memberRepository.save()`" 코드는 수정 할 필요가 없다!! 굉장히 유연해진다!!

- **TIP:** 인터페이스의 메소드가 뭐였는지 헷갈릴 수 있다. **"주석을 활용하자"**

  - ```java
    public interface ItemService {
        /**
    	 * Item의 이전, 이후 Item 구하기
    	 * @param id
    	 * @return
    	 * @throws Exception
    	 */
        public List<Item> findThree(Long id) throws Exception;
    }
    ```

  - 이렇게 하면 **ItemServiceImpl 구현체**에서 오버라이딩때 메소드 설명란(hover)에 **"주석내용"이 보임!**

<br><br>

### 레포지토리(DAO)

**레포지토리는** `Spring Data JPA + JPA` **함께 사용 중! -> 아래 "스프링 DB 관련" 챕터 필독!**

참고로 Spring Data JPA는 `JpaReository 인터페이스` 로 볼 수 있고, 이거만 상속해도 **서비스 단에서 바로 사용**이 가능하다.   
**-> 자동으로 구현체를 만들어 주기 때문**

**따라서 인터페이스 사용을 추천한다!**

**단, 아래 예시 코드는 어댑터추가ver-type2이고, 본인은 보통 단순ver을 사용!**

<details><summary><b>예시 코드 보기 - 레포지토리</b></summary>
<div markdown="1"><br>
**MemberRepository 인터페이스 + MemberRepositoryCustom 인터페이스 + MemberRepositoryCustomImpl 클래스 의 조합**<br>
```java
/**
 * MemberRepository 인터페이스 -> 서비스 계층에서 사용하게 될 본체
 * 여기서 2개의 인터페이스를 상속 받는다.
 * - JpaRepository<Member, Long> 이 바로 Spring Data JPA 이다.
 * - MemberRepositoryCustom 이 바로 일반 JPA 이다. 
 */
public interface MemberRepository extends JpaRepository<Member, Long>, MemberRepositoryCustom {
  //CRUD 자동 제공
}
/**
 * MemberRepositoryCustom 인터페이스 -> 일반 JPA로 커스텀
 */
public interface MemberRepositoryCustom {
  Member findOne(Long id);
  Member findByUid(String uid);
  List<FindMemberResponseDto> findAllWithPage(int pageId);
}
/**
 * MemberRepositoryCustomImpl 클래스 -> 일반 JPA로 커스텀 구현체
 * - save() 주석 -> JpaRepository 꺼 사용하려고
 * - @Override -> 인터페이스 구현 때 당연히 필수 키워드
 */
@Repository
@RequiredArgsConstructor // 생성자 주입 + 엔티티매니저 주입 제공
public class MemberRepositoryCustomImpl implements MemberRepositoryCustom {
  private final EntityManager em;
  /**
   * save, findOne, findByUid, findAllWithPage
   */
//  public void save(Member member) {
//    if (member.getId() == null) {
//      em.persist(member); // db 저장
//    }
//  }
  @Override
  public Member findOne(Long id) {
    return em.find(Member.class, id);
  }
  @Override
  public Member findByUid(String uid) {
    List<Member> findMembers = em.createQuery("select m from Member m where m.uid = :uid",
            Member.class)
        .setParameter("uid", uid)
        .getResultList(); // List로 반환 받아야 null처리가 쉬움
    return findMembers.isEmpty() ? null : findMembers.get(0);
  }
  @Override
  public List<FindMemberResponseDto> findAllWithPage(int pageId) {
    int offset = (pageId - 1) * 10;
    int limit = 10;
    List<Object[]> objects = em.createNativeQuery("select m.member_id, m.nickname, e.level " +
            "from (select * from member order by member_id desc limit " + offset + "," + limit + ") m "
            +
            "inner join character c on m.character_id=c.character_id " +
            "inner join exp e on c.exp_id=e.exp_id;")
        .getResultList();
    return objects.stream()
        .map(o -> new FindMemberResponseDto((Long) o[0], (String) o[1], (Long) o[2]))
        .collect(Collectors.toList());
  }
}
```
</div>
</details>
<br>

**Sring Data JPA 기본 제공 CRUD표**


- 참고: [JpaRepository 메소드 규칙 정리](https://priming.tistory.com/114)

<br>

**레포에서 em.createQuery()**할때 `.getResultList();` 로 반환받는게 null처리 용이!! (보통 하나를 받더라도 이걸로 하는 중!)

**Spring Data JPA를 사용해보면 Optional<>** 로 반환하므로 `orElse(null)` 또는 `orElseThrow(new ... )` 등 이런식으로 사용하기!  
=> 물론, **Member** 이런식으로 반환타입써서 해주면 Optional<>로 반환 안함!  

**RequestDto, ResponseDto**는 주로 컨트롤러 단에서 내부 엔티티 보호 목적으로 사용!!+JsonIgnore문제에도 자유롭다!(DTO Lazy강제초기화 필수)  
따라서 **"미리 요청,응답 양식이 있는게 아니라면"** 컨트롤러 단 개발할 때 Dto 고려ㄱ  
=> 보통 본인은 정해둔 요청 양식없이 바로 해버려서 이런흐름: 요청은 예상해서 Dto로 바로 엔티티 static create매개변수로 사용 기기 + 응답은 컨트롤러때 static class ㄱㄱㄹ  
그리고 **TDD엔 그냥 내부 엔티티로 바로 테스트**하면 되는데 굳이 Dto쓰려다보니 Dto하나 수정에 수정파일이 넘 많아지네. 그래서 이 부분 참고하라고 작성함.

일반 JPA를 사용할 때는 서비스 계층의 @Transactional 범위 내에서 동작하지만, **Spring Data JPA는 각 메서드가 독립적인 트랜잭션**으로 실행될 수 있어 차이가 발생!  
=> EntityManager는 **트랜잭션 범위의 영속성 컨텍스트**를 사용  
=> 서비스 계층에서 @Transactional이 있으면 그 트랜잭션 내에서 동작  
=> EntityManager 사용하려면 당연히 @Transactional이 필요  
=> 참고 TDD 코드 플젝: [entertainment 플젝](https://github.com/BH946/entertainment/tree/backend/entertainment/src/test/java/com/cafe24/entertainment/Repository/CardReadingRepositoryTest.java)

**JPQL 은 아래 정리한 주제에서 참고**

<br><br>

### 서비스

도메인, 레포지토리 처럼 @Service 선언해서 자동 빈 등록하면 됨.  
**그러나, 수동 빈 등록법을 보여주려고 함! 추가로 모니터링 비지니스 로직도 함께 보자!**

**다만, 서비스 단에서는 인터페이스 잘 사용 안하므로 그건 알고 있자.**

인터페이스 설명은 레포지토리에서 했기에 생략하고, 모니터링 비지니스 로직을 위한 **"수동 빈 등록"** 을 잠시 보자.

- @Service 는 자동 빈 등록을 하는데 이걸 대신하려면 수동으로 빈 등록을 해야한다.

- @Configuration 과 @Bean 조합으로 등록할 수 있다.

- ```java
  // 빈 등록
  @Configuration
  public class TaskConfigV2 {
    @Bean
    TaskService taskService(TaskRepository taskRepository, MeterRegistry registry) {
      return new TaskServiceV2(taskRepository, registry);
    }
    // @Timed 사용(->예시 코드 참고) 위해서 반드시 필수 -> AOP 사용
    @Bean
    public TimedAspect timedAspect(MeterRegistry registry) {
      return new TimedAspect(registry); 
    }
  }
  ```

<br>

<details><summary><b>예시 코드 보기 - 서비스</b></summary>
<div markdown="1"><br>
**TaskService 인터페이스 + TaskServiceV2 클래스(구현체) + TaskConfigV2 클래스(설정-빈 등록)**<br>
TaskServiceV2 를 보면 정말 레포지토리 <-> 컨트롤러 이어주는 역할 하는 느낌이다.<br>
```java
/**
 * TaskService 인터페이스
 */
public interface TaskService {
  void join(Task task); // 일정 등록
  Task findOne(Long taskId);
  Task findOneWithMember(Long memberId, Long taskId);
  void remove(Task task);
  void update(Task task, String content, LocalDateTime startTime, LocalDateTime endTime);
  void updateStatus(Task task, Boolean completedStatus, Boolean timerOnOff,
      Long remainTime); // 일정 완료
  void updateAll(List<Task> taskList, String content, LocalDateTime startTime, LocalDateTime endTime);
}
//
/*
@Timed 사용 시 아래와같은 빌더 작성을 생략가능
Counter.builder("my.task")
        .tag("class", this.getClass().getName())
        .tag("method", "addTask")
        .description("task")
        .register(registry).increment();
 */
//TaskServiceV2 클래스 -> 인터페이스 구현체 (@Override 필수)
@Timed("my.task") // 모니터링
@Transactional(readOnly = true) // 읽기모드 기본 사용
@RequiredArgsConstructor // 생성자 주입 + 엔티티 매니저(서비스에서는 안씀)
public class TaskServiceV2 implements TaskService {
  private final TaskRepository taskRepository;
  private final MeterRegistry registry;
  @Override
  @Transactional // 쓰기모드 사용 위해
  public void join(Task task) {
    taskRepository.save(task);
    sleep(500);
  }
  @Override
  @Transactional // 쓰기모드 사용 위해
  public void remove(Task task) {
    taskRepository.remove(task);
  }
  @Override
  @Transactional // 더티체킹 - db 적용
  public void update(Task task, String content, LocalDateTime startTime, LocalDateTime endTime) {
    task.updateTask(content, startTime, endTime);
  }
  //...
}
//TaskConfigV2 클래스 -> 수동 빈 등록 설정
@Configuration
public class TaskConfigV2 {
  @Bean
  TaskService taskService(TaskRepository taskRepository, MeterRegistry registry) {
    return new TaskServiceV2(taskRepository, registry);
  }
  // 위에서 사용한 @Timed 사용 위해 반드시 필수 -> AOP 사용
  @Bean
  public TimedAspect timedAspect(MeterRegistry registry) {
    return new TimedAspect(registry); 
  }
}
```
</div>
</details>
<br>

**서비스 단에서 @Transactional(readOnly=true) 전역, @Cacheable(), @Scheduled()**   
-> 캐시랑 스케줄링을 여기서 사용했다. 

<details><summary><b>예시 코드 보기 - 캐시와 스케줄</b></summary>
<div markdown="1"><br>
**Cacheable, Scheduled, CacheEvict 예시**<br>
```java
// value랑 cacheNames는 동일. 둘다 "캐시이름"의미. 하나 생략 가능.
// key는 말 그대로 구분하는 키값
// 만약 설정한 캐시매니저가 따로 있으면 cacheManager = "cacheManager2" 속성 추가
@Cacheable(value = "members", key = "#pageId", cacheNames = "members") // [캐시 없으면 저장] 조회
public List<FindMemberResponseDto> findAllWithPage(int pageId) {
  return memberRepository.findAllWithPage(pageId);
}
// 캐시에 저장된 값 제거 -> 30분 마다 실행하겠다.
// 초(0-59) 분(0-59) 시간(0-23) 일(1-31) 월(1-12) 요일(0-6) : "00 30 * * * *"
// - 요일? (0: 일, 1: 월, 2:화, 3:수, 4:목, 5:금, 6:토)
@Scheduled(cron = "00 30 * * * *") // 30분 00초 마다 수행
@CacheEvict(value = "members", allEntries = true)
public void initCacheMembers() {
}
```
</div>
</details>

참고 : 왜 @Transactional 이런건 바로 사용 가능한가?
* @Transactional 은 "빈에 반드시 TransactionManager 가 필요" 
* 스프링 부트는 자동으로 TransactionManager 등등 을 "빈에 등록" -> "자동 구성"

- **쓰기모드 필요할때만 @Transactional 추가 선언** -> readOnly=false 해야해서

<br>

<br>

## (컨트롤러) 통신 구현 + 컴포넌트 스캔

**컴포넌트 스캔은 전체와 부분으로 적용 가능**

- 그냥 @SpringBootApplication 만 해도 컴포넌트 스캔을 **전체 범위**로 설정!!
- `@SpringBootApplication(scanBasePackages = "hello.itemservice.web")` 로 하면 **web하위만** 컴포넌트 스캔!!<br>부분 스캔이면... 나머지는 어떻게 스캔?? -> **@Import**
  - 해당 하위에 컨트롤러들이 있는데 @Controller 를 제외한 레포,서비스 등은 스프링빈 수동 등록필요!!
  - 이를 **@Import(레포이름) 스캔으로 추가 등록!**

<br>

**파라미터 요청이나 응답** 둘다 “**DTO(+Valid)”는 거의 필수** 사용 -> Jackson, Gson (json변환 라이브러리) 필수 공부

- **DTO는 경로 상 제일 하위에서 가지는 계층에 두자**

- LePl 플젝(Jackson), Swing 플젝(Gson) 사용했으니 참고~~: [정리한 노션](https://www.notion.so/Java-Spring-7611b33fb665463284dc7a4ffb783c39#19c4a96ae5e341a2b2b628a963bf6d53)

  - JSON 반환시 꼭 마지막에 객체로 감싸서 반환 -> `{ 데이터 }`
  - 배열 JSON이면 꼭 마지막에 배열로 감싸서 반환 -> `[{},{}...]`

  <details><summary><b>Jackson 2.8.0 이전 버전 LocalDateTime 오류?</b></summary>
  <div markdown="1">
  ```java
  Map<String, LocalDateTime> map = new HashMap<>();
  map.put("startTime", st);
  map.put("endTime", en);
  ObjectMapper obj = new ObjectMapper();
  String content = obj.registerModule(new JavaTimeModule())
      .writeValueAsString(map); // Jackson 2.8.0 이전 버전에서는 JavaTimeModule 을 써야 에러 해결(직렬화 에러)
  ```
  </div>
  </details>

- API 응답 스펙에 맞추어 별도의 **DTO를 반환** 권장

  * **setter를 최대한 사용하지않고, 파라미터를 줄여주는 효과**

- 특히, **DTO에서 lazy 강제 초기화 하고 있다.** 안하면 `LazyInitializationException` 에러

  - 강제 초기화를 안하면 fetch join으로 모두 조회 했어도 해당 엔티티를 찾을 수 없게 되어서 null이 응답!! -> "영속성에 등록하지 않아서. 즉, 메모리에 올려두지 않아서."

    <details><summary><b>자세히 (출력사진+코드)</b></summary>
    <div markdown="1"><br>
    **Lazy 강제 초기화 안 할때**<br>
    <img src="https://github.com/user-attachments/assets/093f2678-788f-4ecf-8ade-4790f5cb31e1" alt="image" style="zoom:80%;" /><br>
    <img src="https://github.com/user-attachments/assets/4c512757-f58b-44ba-b89f-4fb9d0706dae" alt="image" style="zoom:80%;" /><br>
    <br>**강제 초기화 할 때(하는법 코드참고)**
    <div markdown="1">
    ```java
    //ListsResDto 생성자 내부
    List<Lists> listsList = listsService...
    List<ListsResDto> result = listsList.stream()
        .map(o -> new ListsResDto(o))
        .collect(Collectors.toList()); //반환 List타입 설정
    this.listsTasks = lists.getTasks().stream()
        .map(o -> new TaskDto(o))
        .collect(Collectors.toList());
    //TaskDto 생성자 내부
    this.completedStatus = task.getTaskStatus().getCompletedStatus();
    this.timerOnOff = task.getTaskStatus().getTimerOnOff();
    ```
    </div>
    <img src="https://github.com/user-attachments/assets/80d56805-64a3-41e9-ba9d-a0cddb027498" alt="image" style="zoom:80%;" /><br>    
    </div>
    </details>

<br>

**반환타입 void일 때: 자동으로 뷰리졸버는 요청URL과 동일한 뷰를 탐색해서 반환! (직접 String반환 안해도!)**

**보통 @RestController + @RequestMapping + @RequiredArgsConstructor 조합 사용**

- @RequiredArgsConstructor 은 이미 언급해서 생략

- @RestController 는 API에 필수(+**@ResponseBody도 포함**해서 예로 Json 반환 자동)

- @RequestMapping("api/v1/members") 는 전역 주소 -> GET, POST 둘다 한번에 지원
  - @PostMapping, @GetMapping -> 주로 사용 (좀 더 유지보수하기 좋을듯?)
  - @RequestBody, @ResponseBody : HttpEntity 처럼 **HTTP 메시지 컨버터**가 **HTTP 메시지 바디**의 내용을 우리가 원하는 문자나 객체(DTO) 등으로 자동 변환

  - 요청(ex:json)엔 @RequestBody, 응답엔 @ResponseBody!!
  - **응답은 항상 아래 흐름**
    - `ResponseEntity.status(HttpStatus.NOT_FOUND).body(FAIL_LOGIN)` 이런 패턴 사용중임! -> ResponseEntity 활용 (상태코드 담기 좋다)
    - `@ResponseStatus(HttpStatus.*BAD_REQUEST*)` 이걸로ResponseEntity 없이 쉽게 반환할 수도 있음 (상태코드를 @ResponseStatus로 해결)
    - **Valid 사용하면 Api응답양식**까지 포함해서 body에 반환
       `ApiResponse res = ApiResponse.error(HttpStatus.BAD_REQUEST.value(), bindingResult);` -> (ApirResponse는 아래 리팩토링 주제 쪽에서 참고-Valid)

- 아래는 참고
  
  <details><summary><b>HttpStatus 클래스에서 제공하는 주요 상태 코드</b></summary>
  <div markdown="1">
  - **1xx: 정보 응답**
      - **100 Continue**: 클라이언트가 요청을 계속 진행해도 좋다는 의미입니다.
      - **101 Switching Protocols**: 서버가 클라이언트의 요청에 따라 프로토콜을 전환하고 있다는 의미입니다.
  - **2xx: 성공**
      - **200 OK**: 요청이 성공적으로 처리되었습니다.
      - **201 Created**: 요청이 성공적으로 처리되어 새로운 자원이 생성되었습니다.
      - **202 Accepted**: 요청이 수락되었으나 아직 처리되지 않았음을 의미합니다.
      - **204 No Content**: 요청이 성공적으로 처리되었으나 반환할 내용이 없음을 의미합니다.
  - **3xx: 리다이렉션**
      - **300 Multiple Choices**: 요청에 대해 여러 개의 선택지가 있음을 의미합니다.
      - **301 Moved Permanently**: 요청한 자원이 영구적으로 새로운 URI로 이동하였음을 나타냅니다.
      - **302 Found**: 요청한 자원이 다른 URI로 임시로 이동되었음을 의미합니다.
      - **304 Not Modified**: 클라이언트가 캐시한 자원이 수정되지 않았음을 나타냅니다.
  - **4xx: 클라이언트 오류**
      - **400 Bad Request**: 요청이 잘못되어 서버가 이해할 수 없음을 의미합니다.
      - **401 Unauthorized**: 요청에 인증이 필요하며, 인증 정보가 없거나 유효하지 않음을 나타냅니다.
      - **403 Forbidden**: 서버가 요청을 이해했지만, 요청을 수행할 권한이 없음을 의미합니다.
      - **404 Not Found**: 요청한 자원을 찾을 수 없음을 나타냅니다.
      - **405 Method Not Allowed**: 요청한 HTTP 메서드가 해당 자원에서 지원되지 않음을 의미합니다.
  - **5xx: 서버 오류**
      - **500 Internal Server Error**: 서버에서 요청을 처리하는 도중 오류가 발생했음을 나타냅니다.
      - **501 Not Implemented**: 요청한 메서드가 서버에서 구현되지 않았음을 의미합니다.
      - **503 Service Unavailable**: 서버가 현재 요청을 처리할 수 없음을 나타내며, 보통 서버가 과부하 상태이거나 유지보수 중일 때 발생합니다.
  </div>
  </details>
  
- **세션 얻으려고 HttpServletRequest** 파라미터를 받기도 함. -> request 가 세션 관리 기억!

  ```java
  // 세션 있으면 세션 반환, 없으면 신규 세션 생성
  HttpSession session = request.getSession(); // UUID 형태로 알아서 생성 (기본값 : true)
  // 세션에 로그인 회원 정보 보관
  session.setAttribute(SESSION_NAME_LOGIN, findMember.getId());
  ```

<br>

<br>

## 테스트 코드 -> 코드 커버리지 필수

<br><br>

### 코드 커버리지

[코드 커버리지](https://amaran-th.github.io/소프트웨어 설계/[IntelliJ] 코드 커버리지 확인하기/) 내용이 궁금하다면 참고 링크

- **인텔리J-edit configurations 세팅** 및 테스트 실행 때 **run test with 커버리지로 실행 ㄱ(run test with 커버리지 이것만해도 바로 됨)**  

  <img src="https://github.com/user-attachments/assets/3804a3c4-e550-466d-bef9-3e6d56da6dcf" alt="image" style="zoom:150%;" /> 

  - 참고로 **edit configurations에서** **Run 멀티 설정, 실행 프로필 설정 등** 여러가지 할 수 있음.

<br><br>

### 테스트 코드 흐름

참고) [졸작](https://github.com/BH946/LePl_Spring/tree/kbh)은 JUnit5, [우테코](https://github.com/BH946/java-lotto-6/tree/main/src/test/java/lotto)는 AssertJ 사용했음, **TDD는 테스트 주도 개발 방법론**

**보통 메모리DB로 테스트**: application.properties 주석 시 boot-data-jpa 의존성이 자동 제공 및 @Entity, @Id, @GeneratedValue로 JPA 어노테이션 사용해야 테이블 생성까지 자동..!

* **JUnit5**

  * `Assertions.assertNull, Assertions.assertInstanceOf, Assertions.assertEquals, Assertions.assertThrows` 등등 자주 사용

  <details><summary><b>예시 참고 코드</b></summary>
  <div markdown="1">
  ```java
  //어떤 객체인지 체크
  Assertions.assertInstanceOf(Character.class, character);
  //결과 비교 - equals
  Assertions.assertEquals(member, memberRepository.findOne(saveId));
  //예외 처리 + 예외 메시지까지 확인법
  Throwable exception = Assertions.assertThrows(IllegalStateException.class, () -> {
      followService.join(findFollow); // 중복검증 예외 발생
  });
  Assertions.assertEquals("이미 팔로우 요청을 하셨습니다.", exception.getMessage());
  log.info("exception.getMessage() : {}", exception.getMessage());
  //일부러 예외 터트린 테스트라면
  Assertions.fail("중복검증 예외가 발생해야 한다.");
  //프록시 체크 -> 서비스에 @Transactinal 은 프록시 사용
  Assertions.assertThat(AopUtils.isAopProxy(memberService)).isTrue();
  Assertions.assertThat(AopUtils.isAopProxy(memberRepository)).isFalse();
  ```
  </div>
  </details>

* **AssertJ -> Junit5에서 파생된것**

  * ```java
    assertThat(connection).isNotNull();
    assertThat(findMember).isEqualTo(member);
    assertThatThrownBy(() -> repository.findById(member.getMemberId()))
        .isInstanceOf(NoSuchElementException.class); // 예외 터지는거 확인
    ```

* **print대신 assert 로 비교하자**
  * `Assertion`을 이용하자. 이걸 사용해서 `assertEquals`함수 사용시 두개 인자가 동일한지 봐준다
    * 안 동일하면 오류, 동일하면 정상 동작
    * `Assertions.assertEquals(member, memberRepository.findOne(saveId));`
    * `Assertions.assertThat(member).isEqualeTo(result);` 이것도 위처럼 사용된다.
    * `Assertions.assertInstanceOf(Character.class, character);` 어떤 객체인지 체크하기 좋음
  
* **예외 테스트**

  * (1)try, catch보다 간편하게 `assertThrows`를 사용해서 일부러 예외를 터트려서 테스트

    * 아래 JUnit5 에 정리한 부분이 이 내용

  * (2)또는 try, catch대신 `@Test(expected = IllegalStateException.class)` 를 선언하면 알아서 해당 예외 터질 때 종료해줌
    * 만약 해당 예외가 안터지면 그다음 코드들이 계속 실행됨. 그 코드는 아래 형태로 작성
    * `Assertions.fail("예외가 발생해야 한다.");` 예외가 안터져서 오히려 에러 라고 로그를 남겨줌


<br>

* **스프링 테스트 선언법**

  * Junit4 : @RunWith, @SpringBootTest 둘다 선언 - 클래스 단에
  * **Junit5** : @SpringBootTest 선언 - 클래스 단에 (@RunWith를 자동 제공)
  * **이후 @Test** 를 메소드마다 선언하여 테스트!! - 메소드 단에
    * @RunWith(SpringRunner.class) : 스프링과 테스트 통합 -> **Junit4 이하만 사용**
    * **@SpringBootTest** :  스프링 컨테이너와 테스트를 통합 실행. 모든 빈 로드. (이게 없으면 @Autowired 다 실패) -> (레포, 서비스 테스트때 당연히 쓰겠지?)   
      **=> 컨트롤러만 테스트?? @WebMvcTest 사용 (서블릿 컨테이너 MOCK)**
      * **@SpringBootApplication을 찾아서 테스트를 위한 Bean을 생성!** 즉, 스프링 빈 사용하고 싶으면 반드시 필수
      * @TestConfiguration : 테스트에서 **스프링 빈 등록을 지원**하는 어노테이션
      * 중요: **수동 빈** 등록(ex:XML)할 때는 **메인의 XML파일을 test/resources 하위로 복제하자!**
        * xml수동 빈 등록은 메인에서 @ImportResource("classpath:..)"로 직접 가져오는데,  
          테스트환경에서는 classpath 가 test/resources 경로가 우선이라 못 가져온다.   
          못 찾으면 main/resources 경로에서 찾는다고는 하던데 본인 경험상 못 찾더라.
* **given, when, then 예시** -> tdd 로 자동완성 사용 중

  * given에 멤버 이름 설정
  * when에 서비스의 join함수 사용(회원가입 되는지 확인하는 것)
  * then에 결과를 보는것. 멤버이름이 잘 생겼는지 등등..(assert보통 씀!)
    - **@BeforEach, @AfterEach**는 각 @Test 수행 전, 후 동작 -> @Test마다 **독립적 실행** 상태라 필요 (@Test마다 실행해 줌)
    - 다만, DB와 연동하는 **레포(DAO)단**에선 **@Transactional의 @Rollback(false)**를 통해 **DB의 데이터 공유는 전역**으로 가능해서 테스트가 정상 동작 하는 것!
* **@Transactional** : 테스트를 실행할 때 마다 트랜잭션을 시작하고 테스트가 끝나면 트랜잭션을 강제로 롤백 (이 어노테이션은 테스트 케이스에서 사용될때만 기본값으로 롤백)

  * **롤백을 하기때문에 내부에서 굳이 영속성 컨텍스트 플러시를 안하는 특징**을 가짐
  * @Rollback(false) : **롤백 취소**
    * 롤백을 안하니까 **자동flush** 진행하므로 insert문 로그 확인가능
  * em.flush() 함수 사용 : **flush 진행**
    * 롤백은 건드리지않고 **수동flush** 진행하므로, 롤백은 그대로 진행하면서 insert문 로그 확인가능
  * **서비스 TDD 때 트랜잭션 선언안하면 서비스가 동작을 안하기 때문에 그냥 전역으로 써주고! 대신에 서비스 코드에 트랜잭션 있는건 꼭 확인**
    * 왜냐하면 **TDD에 선언한 트랜잭션때문에 서비스에 트랜잭션이 없어도 동작하기 때문!**
    * 참고) 트랜잭션이 겹칠텐데 전파로 인해 기존 사용중인 트랜잭션을 그대로 사용하므로 문제가 없다.
* **@TestMethodOrder(MethodOrderer.OrderAnnotation.class)**

  * **@Order()+@Rollback(value=false)** 활용 -> 주로 db저장 먼저하려고!
  * `@Order(1), @Order(2)` ... 로 테스트 코드 실행 순서 지정 가능
    * 주의 : Order 라는 클래스가 이미 import 중이라면 `@org.junit.jupiter.api.Order(1)`
    * 또한, `org.junit.jupiter.api` 패키지의 Order 를 사용한다는 점을 기억

  * `@DisplayName` 는 간단히 테스트 출력때 항목 이름을 설정해서 출력 가능

- **@Slf4j** -> `private static final Logger log = LoggerFactory.getLogger(YourClassName.class);`와 같은 로깅 필드가 자동 생성
  - `log.info("{}", member.getId());` 이런식 사용

- **컨트롤러"만" 테스트: @WebMvcTest** 사용 (서블릿 컨테이너 MOCK) -> WAS 역할을 톰캣 대신 해주는것!

  - @MockBean 으로 **가짜** 객체 등록. -> **중요: 실제 동작X**

    - 따라서 `when(member.getId()).thenReturn(1L);` 이런식으로 **임의로 리턴값 지정**
    - `mockMvc.perform()` 사용때 post, get 잘 구분해서 적용
    - `Member member = mock(Member.class);` 처럼 함수내에서 Mocking 객체도 간단 (@MockBean이랑 동작은 비슷할 듯)
    - 진짜 **컨트롤러 코드만 테스트** 하는것!

  - **MockMvc의 목적은 3가지**

    * **request** 잘 받는지 - **content()**
    * 서비스 메서드로 **데이터 잘 전달** 되는지 - **verify()**
      * times() 도 같이 많이 사용
      * 왜냐하면 여러번 서비스 불리는 경우 몇번 불리는지 times로 명시해줘야 TooMany 에러 방지

    * 서비스 반환값으로 **response** 잘 받는지 - **when, given 필수!**
      * when, given 으로 해당 서비스의 반환값을 직접 설정가능!
      * response(응답) 관련 검증 메소드는 매우 다양!
        * andExpect() - status(), view(), redirectedUrl(), model(), content(), jsonPath() 등등.. 응답 관련 메소드..
        * andDo() : 요청, 응답관련 전체 메시지 출력

  - 로그인 세션 같이 **세션은??**

    - `MockHttpSession()` 활용 -> 가짜 세션으로 로그인 인증 "인터셉터" 통과

    - 반대로 세션을 반환하는 "로그인" 컨트롤러 테스트 경우?

      - 로그인 로직은 로그인 성공시 세션을 생성! **(request 역할)**

        <img src="https://github.com/user-attachments/assets/34989c07-a468-46cd-b796-d9dafa4abc17" alt="image" style="zoom:150%;" /> 

      - @WebMvcTest 테스트 수행한 로그 request쪽에 session atr 보면 생성된 HttpServletRequest 정보 확인 가능

      - 다만, 응답 쿠키는 확인이 불가능했다. 쿠키는 웹에서는 자동 처리해주다보니 환경 차이인 것 같다.

  - 아래는 참고
    
    <details><summary><b>Mock관련 메서드들 참고</b></summary>
    <div markdown="1">
    1. perfom()<br>
    - HTTP 요청을 할 수 있다.<br>
    - 체이닝이 지원되어 여러 검증 기능을 이어서 선언할 수 있다.<br>
    2. andExcept()<br>
    - mvc.perform의 결과를 검증한다.<br>
    - status() : 상태 코드를 검증할 수 있다.<br>
    - view() : 리턴하는 뷰에 대해 검증한다.<br>
      - ex) andExcept(view().name("/detailpage"))<br>
    - redirectedUrl() : 리다이렉트 url을 검증한다.<br>
      - ex) redirectedUrl("/where");<br>
    - model() : 컨트롤러의 Model에 관해 검증한다.<br>
      - ex) model().attribute("alcoholDetails", alcoholDetails)<br>
      - ex) model().attributeHasFieldErrorCode("loginForm", "userId", "NotBlank")<br>
    - content() : 응답에 대한 정보를 검증한다.<br>
    - jsonPath() : response body에 들어갈 json 데이터를 검증한다.<br>
      - ex) jsonPath("$.status").value("400")<br>
    3. andDo()<br>
    요청/응답 전체 메세지를 확인할 수 있다.<br>
    4. 추가 참고
    - Mock() - 모의 객체를 생성하는 역할<br>
    - when() - 협력객체 메소드 반환 값을 지정해주는 역할(stub)<br>
    - verify() - stub안의 협력객체 메소드가 호출 되었는지 확인<br>
    - times() - 지정한 횟수 만큼 협력 객체 메소드가 호출 되었는지 확인<br>
    - never() - 호출되지 않았는지 여부 검증<br>
    - atLeastOnce() - 최소 한 번은 특정 메소드가 호출되었는지 확인<br>
    - atLeast() - 최소 지정한 횟수 만큼 호출되었는지 확인<br>
    - atMost() - 최대 지정한 횟수 만큼 호출되었는지 확인<br>
    - clear() - stub을 초기화 한다<br>
    - timeOut() - 지정된 시간 안에 호출되었는지 확인
    </div>
    </details>
    
  - **HttpServletRequest, HttpServletResponse 관련 테스트** -> `MockHttpServletRequest, MockHttpServletResponse` 을 사용! (아직 사용해보지는 않았음)

  - **NoSuchBeanDefinitionException 주의)** 컨트롤러에서 사용한 레포,서비스 등등 은 꼭 Mock해주자. 테스트코드에는 없고 컨트롤러에서만 사용중이어도 무조건 **@MockBean 등록은 일단 필수다!!**
  
    - **예로 @Test 회원가입() 로직에 memberService만 사용**하고 exp, character서비스는 사용하지 않았다.
    - 근데, 실제 register() 로직인 회원가입 컨트롤러 코드를 보면 exp, character서비스 전부 사용한다.
    - 그렇다면 아래처럼 @MockBean 등록은 일단 필수다!!

    <img src="https://github.com/user-attachments/assets/7b1c7fb2-c740-45c5-9018-49490c2dd293" alt="image" style="zoom:80%;" />  

  - `mockMvc.perform` 함수에서 **json 내용**을 체크 하고 싶을때? -> `jsonPath()`
  
    - 배열 처리는?
       `.andExpect(jsonPath("$[0].content").value("알림테스트1")) // 응답 body 의 json 확인 .andExpect(jsonPath("$[2].content").exists()) .andExpect(jsonPath("$[3]").doesNotExist()) // 없어야 정상`
    - 일반 적인 {}는?
       `.andExpect(*jsonPath*("$.data.uid").value("12345")) // 응답 body 의 json 확인`

<br>

**예시 코드 참고**

<details><summary><b>도메인</b></summary>
<div markdown="1">
```java
@Slf4j
class MemberTest {
  @Test
  public void 생성_편의메서드() throws Exception {
    // given
    Member member = null;
    // when
    member = Member.createMember("test", "테스트1");
    // then
    Assertions.assertInstanceOf(Member.class, member);
  }
//
  @Test
  public void 연관관계_편의메서드() throws Exception {
    // given
    Member member = Member.createMember("test", "테스트2");
    Lists lists = Lists.createLists(member, LocalDateTime.now(), new ArrayList<>());
    log.info("{}", member.getLists().size()); //=0
    // when
    member.addLists(lists);
    log.info("{}", member.getLists().size()); //=1
    // then
    Assertions.assertEquals(member.getLists().size(), 1);
  }
}
```
</div>
</details>

<details><summary><b>레포지토리</b></summary>
<div markdown="1">
```java
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
@SpringBootTest
@Slf4j
class MemberRepositoryTest {
  @Autowired
  MemberRepository memberRepository;
  @Autowired
  EntityManager em;
//
  /**
   * save, findOne, findByUid, findAllWithPage
   */
  @Test
  @Order(1)
  @Transactional // 롤백
  @Rollback(value = false)
  public void 회원가입_조회() throws Exception {
    // given
    Member member = Member.createMember("test", "test1");
    // 혹시모를 FK 에러 방지
    Exp exp = Exp.createExp(0L, 0L, 1L);
    em.persist(exp); // FK id 위해
    Character character = Character.createCharacter(exp, new ArrayList<>(), new ArrayList<>(),
        new ArrayList<>());
    em.persist(character); // FK id 위해
    member.setCharacter(character);
//
    // when
    memberRepository.save(member); // persist
    log.info("{}", member.getId());
//        em.flush(); // 롤백 true 때문에 insert 쿼리 생략시 flush 추가로 볼 수 있음
//        em.clear(); // flush 사용시 이것까지 해줘야 아래 select 문 전송 쿼리도 볼 수 있음
    Member findMember = memberRepository.findOne(member.getId());
    log.info("{}", member.getId());
//
    // then
    Assertions.assertEquals(member.getId(), findMember.getId());
    // 단, 위에서 em.clear를 한 경우 영속성이(캐시) 비어있으므로 findMember가 새로운 주소!!
    // 따라서 아래 출력으로 틀리다는 결론이 나온다.
    Assertions.assertEquals(member, findMember);
  }
//
  @Test
  @Order(2)
  @Transactional
  public void 회원조회_UID() throws Exception {
    // given
    Member findMember;
    // when
    findMember = memberRepository.findByUid("test");
    Member findMember2 = memberRepository.findByUid("testtest");
    // then
    Assertions.assertEquals(findMember.getNickname(), "test1");
    Assertions.assertEquals(findMember2, null);
  }
//
  @Test
  @Order(3)
  @Transactional
  public void 회원조회_Page() throws Exception {
    // given
    List<FindMemberResponseDto> memberList = new ArrayList<>();
    // when
    memberList = memberRepository.findAllWithPage(1); // order by desc
    log.info("memberList : {}", memberList.size());
    log.info("memberList.get(0) : {}", memberList.get(0));
    // then
    for (FindMemberResponseDto dto : memberList) {
      log.info("member id : {}, nickname : {}", dto.getId(), dto.getNickname());
    }
    Assertions.assertEquals(memberList.get(0).getNickname(), "test1");
  }
}
```
</div>
</details>

<details><summary><b>서비스</b></summary>
<div markdown="1">
```java
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
@SpringBootTest
@Transactional // 쓰기모드 -> 서비스코드에 트랜잭션 유무 반드시 확인
@Slf4j
public class MemberServiceTest {
  @Autowired
  EntityManager em;
  @Autowired
  MemberService memberService;
  static final String UID = "12345";
  static final String MESSAGE = "이미 존재하는 회원입니다.";
  static Long memberId;
//
  /**
   * join(중복검증 포함), findOne, findByUid, {findAllWithPage, initCacheMembers}(=회원 최신순_페이징 조회+캐시)
   *
  @Test
  @Order(1)
  @Rollback(value = false)
  public void 회원가입_조회() throws Exception {
    // given
    Member member = Member.createMember(UID, "테스트 닉네임");
    Exp exp = Exp.createExp(0L, 0L, 1L);
    em.persist(exp);
    Character character = Character.createCharacter(exp, new ArrayList<>(), new ArrayList<>(),
        new ArrayList<>());
    em.persist(character);
    member.setCharacter(character);
    // when
    memberService.join(member);
    Member findMember = memberService.findOne(member.getId());
    Member findMember2 = memberService.findByUid(UID);
    // then
    Assertions.assertEquals(member.getId(), findMember.getId());
    Assertions.assertEquals(member.getId(), findMember2.getId());
    memberId = member.getId();
  }
//
  @Test
  @Order(2)
  public void 중복검증_예외() throws Exception {
    // given
    Member member = memberService.findOne(memberId);
    // when
    // then
    Throwable exception = Assertions.assertThrows(IllegalStateException.class, () -> {
      memberService.join(member); // 예외발생 로직
    });
    Assertions.assertEquals(MESSAGE, exception.getMessage());
    log.info("exception.getMessage() : {}", exception.getMessage());
  }
//
  @Test
  @Order(3)
  public void 회원_페이징_캐시_조회() throws Exception {
    // given
    // when
    List<FindMemberResponseDto> dto = memberService.findAllWithPage(1);
    log.info("캐시되었으면 쿼리 안날라감1");
    memberService.findAllWithPage(1);
    log.info("캐시되었으면 쿼리 안날라감2");
    memberService.findAllWithPage(1);
    log.info("캐시되었으면 쿼리 안날라감3");
    memberService.initCacheMembers(); // 캐시 초기화
    log.info("캐시 초기화 했으므로 쿼리 날라가야 함");
    memberService.findAllWithPage(1);
    // then
    for (FindMemberResponseDto m : dto) {
      log.info("member.id : {}", m.getId());
      log.info("member.nickName : {}", m.getNickname());
    }
  }
}
```    
</div>
</details>

<details><summary><b>컨트롤러</b></summary>
<div markdown="1">
```java
@WebMvcTest(controllers = MemberApiController.class)
class MemberApiControllerTest {
    @Autowired
    MockMvc mockMvc;
    @MockBean // 가짜 객체이므로 실제 동작은 X
    ExpService expService;
    @MockBean
    CharacterService characterService;
    @MockBean
    MemberService memberService;
//
    /**
     * login, register, logout, findAllWithPage
     */
    @Test
    public void 회원가입() throws Exception {
        // given
        String content = "{\"uid\":\"12345\", \"nickname\":\"회원가입 테스트\"}"; //입력 json 흉내
//        Member member = Member.createMember("12345", "회원가입 테스트");
//        member.setId(1L);
        // Mocking Member 객체
        Member member = mock(Member.class);
        when(member.getId()).thenReturn(1L); // ID 반환 설정
        when(member.getUid()).thenReturn("12345");
        when(member.getNickname()).thenReturn("회원가입 테스트");
        // when
        when(memberService.join(any())).thenReturn(member);
        mockMvc.perform(
                        post("/api/v1/members/register")
                                .contentType(MediaType.APPLICATION_JSON)
                                .accept(MediaType.APPLICATION_JSON)
                                .characterEncoding("UTF-8")
                                .content(content)
                )
                .andExpect(status().isCreated()) // 예상 응답
                .andExpect(jsonPath("$.data.uid").value("12345")) // 응답 body 의 json 확인
                .andDo(print());
        // then
        verify(memberService).join(any());
        verify(expService).join(any());
        verify(characterService).join(any());
    }
//
    @Test
    public void 로그인() throws Exception {
        // given
        String content = "{\"uid\":\"123\"}"; // {"name":"value"} 형태로 작성해야 JSON 형태!
//        Member member = Member.createMember("123", "test");
//        member.setId(1L);
        // Mocking Member 객체
        Member member = mock(Member.class);
        when(member.getId()).thenReturn(1L); // ID 반환 설정
        when(member.getUid()).thenReturn("123");
        when(member.getNickname()).thenReturn("로그인 테스트");
        // when
        when(memberService.findByUid(any())).thenReturn(member);
        mockMvc.perform(
                        post("/api/v1/members/login")
                                .contentType(MediaType.APPLICATION_JSON)
                                .accept(MediaType.APPLICATION_JSON)
                                .characterEncoding("UTF-8")
                                .content(content)
                )
                .andExpect(status().isOk()) // 예상 응답
                .andDo(print());
        // then
        verify(memberService).findByUid(any());
    }
//
    @Test
    public void 로그아웃_성공과중복() throws Exception {
        // given
//        Member member = Member.createMember("123", "test");
//        member.setId(1L);
        // Mocking Member 객체
        Member member = mock(Member.class);
        when(member.getId()).thenReturn(1L); // ID 반환 설정
        when(member.getUid()).thenReturn("123");
        when(member.getNickname()).thenReturn("로그아웃 테스트");
        MockHttpSession session = new MockHttpSession();
        session.setAttribute(SESSION_NAME_LOGIN, member.getId());
        // when
        mockMvc.perform(
                        post("/api/v1/members/logout")
                                .session(session)
                                .contentType(MediaType.APPLICATION_JSON)
                                .accept(MediaType.APPLICATION_JSON)
                                .characterEncoding("UTF-8")
                )
                .andExpect(status().isOk()); // 로그아웃 성공
        mockMvc.perform(
                        post("/api/v1/members/logout")
                                .contentType(MediaType.APPLICATION_JSON)
                                .accept(MediaType.APPLICATION_JSON)
                                .characterEncoding("UTF-8")
                )
                .andExpect(status().isConflict()); // 로그아웃 중복
        // then
    }
//
    @Test
    public void 멤버_조회_페이징() throws Exception {
        // given
        int pageId = 1;
        MockHttpSession session = new MockHttpSession();
        session.setAttribute(SESSION_NAME_LOGIN, 1); // 회원 인증 인터셉터 통과위해
        // 테스트용 members 세팅
        List<FindMemberResponseDto> members = new ArrayList<>();
        FindMemberResponseDto m1 = new FindMemberResponseDto(111L, "테스트1", 1L);
        FindMemberResponseDto m2 = new FindMemberResponseDto(222L, "테스트2", 1L);
        FindMemberResponseDto m3 = new FindMemberResponseDto(333L, "테스트3", 1L);
        members.add(m1);
        members.add(m2);
        members.add(m3);
        // when
        when(memberService.findAllWithPage(1)).thenReturn(members);
        mockMvc.perform(
                        get("/api/v1/members/" + pageId)
                                .session(session)
                                .contentType(MediaType.APPLICATION_JSON)
                                .accept(MediaType.APPLICATION_JSON)
                                .characterEncoding("UTF-8")
                )
                .andExpect(status().isOk()) // 예상 응답
                .andDo(print());
        // then
        verify(memberService).findAllWithPage(1);
    }
} 
```
</div>
</details>
<br><br>

### InitDB -> 데이터 삽입 자동

`InitDB.java` **로 개발 모드에서 간편하게 시작과 동시에 데이터를 미리 넣어두려는 목적(테스트 용이)**

```java
/**
 * 개발 모드에서 간편하게 시작과 동시에 데이터 생성 미리 넣어두려는 목적
 * 안 사용할 거면 스프링 빈 등록 안되게끔(스캔X) @Component 주석 ㄱㄱ + @PostConstruct 주석도 마찬가지
 * 실행흐름: InitService 라는 서비스를 inner class 로 간단히 추가 및 빈 등록하고, @PostConstruct 로 바로 실행
 */
@Slf4j
@Component
@RequiredArgsConstructor // 생성자 주입
public class InitDB {
    private final InitService initService;

    // 해당 클래스 인스턴스 생성(construct)된 후 자동 실행
    @PostConstruct
    public void init() {
        initService.initItem();
    }

    @Service
    @RequiredArgsConstructor
    @Transactional // 쓰기모드 -> 바로 DB 저장
    public static class InitService {
        private final EntityManager em;
        private final ItemRepository itemRepository;

        public void initItem() {
            Item item1 = Item.createItem("김익명", "123", "최근에 있었던 대외비", "최근에 이름 들으면 알 만한 회사랑 어떤 프로젝트 협업할 뻔했는데, 중간에 갑자기 뭐가 바껴서 결국 나랑 하기로 한 건 무산되었다. 너무 아까운 일이었는데 대외비라 어디에 이름 까고 말도 못해서 답답했음. 근데 최근에 보니까 그 프로젝트 초대박났더라고 하......^^^^;;; 또 생각해도 개빡치네*발ㅠㅠ", "test.jpeg");
            Item item2 = Item.createItem("", "1234", "", "", "");
            Item item3 = Item.createItem("철수", "123", "", "테스트", "test.jpeg");
            itemRepository.save(item1);
            itemRepository.save(item2);
            itemRepository.save(item3);
            List<Item> items = itemRepository.updateAllNo();
//            for(int i= 0 ; i<150; i++) {
//                String name = "test"+i;
//                Item item = Item.createItem(name, "12345", "테스트", "테스트123123", "");
//                itemRepository.save(item);
//            }
        }

    }
}
```

<br>

<br>

## application.yml

**application.yml 에 세팅한 내용을 총 정리 -> API와 WEB(타임리프), 테섭과 본섭(프로필 차이 등)**

사용법은 "목차"에서 알아서 찾아서 참고 (다 내용 정리 되어 있을거임)

- `spring: datasource: url, username, password...` 에서 h2 db 연결 세팅
  - **테스트에선 db연동 이부분을 주석 해야지 "메모리DB" 사용.**
  - **db걱정 없음!! h2가 자바기반이라 제공**
  - **단, boot-data-jpa 의존성 필요 + 테이블에 @Entity, @Id, @GeneratedValue로 JPA 어노테이션 사용 필수**
- `spring: jpa: hibernate, properties` 에서 create, none 옵션으로 테이블 생성 또는 기존 테이블 사용 설정 + show_sql로 jpa가아닌 실제 sql문 체크(튜닝 하려면 sql 필수로 체크)
  - 근데 show_sql 사용 안하고, 아래 **logging.level의 SQL을 사용 하자.**
- `server: servlet: session: timout: 30m` 으로 세션 타임아웃 설정
- `logging.level: org.hibernate.SQL: debug` 는 SQL쿼리를 디버그 레벨로 로깅
  - `com.level: debug`로 level설정도 debug로 하면 프로젝트 로그레벨은 debug 레벨 사용
  - **로그레벨 (왼쪽 가장 상세): trace > debug > info > warn > error > fatal 포함 관계**
- `management` 에서 endpoint 노출 설정했음. (모니터링 때문에)
  - ex) shutdown 활성화해서 해당 앱을 외부에서 종료할 수 있게 엔드포인트 노출.
    - 기본적으로 비활성화지만 활성화하면 관리자가 **http 요청으로 앱 종료 가능**
  - exclude옵션에는 env, beans 엔드포인트 노출을 제외했음. (환경정보, 빈 정보)
- `spring.cache.caffeine.spec=maximumSize=100` 는 캐시 사이즈 설정이다. **물론 자바 코드에서도 설정 가능하다.**
- `spring.messages.basename=message,errors` 로 **"메시지, 국제화 기능" 사용**
  - `messages.properties` 에 공통 관리할 messages 내용 세팅
  - `erros.properties` 에도 가능. 즉, 여러개 추가 가능
- `spring.config.activate.on-profile: prod` 로 **프로필 설정 가능**
  - 아무 설정 안하면?? -> default 프로필로 실행 (기본값임)
- `spring.thymeleaf.cache: false` 로 **thymeleaf 캐시 사용X** -> 실시간 reload
- `my.datasource.imgPath: "C:/images/` 로 **"외부설정”** 사용 -> 경로 등등
- `server.port.8080` 로 포트 8080(개발용), 포트 80(배포용) 를 지정 가능
  - http는 80포트를 기본값으로 사용

<details><summary><b>본섭API:application.yml</b></summary>
<div markdown="1"><br>
application.yml 과 application.properties 는 똑같은 역할 (문법만 다를 뿐)<br>
```yaml
spring:
  datasource:
    url: jdbc:h2:tcp://localhost/~/lepldb/lepl
    username: sa
    password:
      driver-class-name: org.h2.Driver
#    url: jdbc:oracle:thin:@localhost:1521:STR
#    username: testUser
#    password: 1234
#    driver-class-name: oracle.jdbc.OracleDriver
  jpa:
    hibernate:
#      ddl-auto: create # table 자동 생성
      ddl-auto: none # db 초기화 없앰
  properties:
    hibernate:
#        show_sql: true
      format_sql: true
server:
  servlet:
    session:
      timeout: 30m # session timeout 30min
logging.level:
  org.hibernate.SQL: debug
  org.hibernate.type: trace
logging:
  level:
    com.lepl: debug
management:
  endpoint:
    shutdown:
      enabled: true
  endpoints:
    web:
      exposure:
        include: "*"
        exclude: "env,beans"
```
</div>
</details>
<details><summary><b>테섭API:application.yml</b></summary>
<div markdown="1"><br>
application.yml 과 application.properties 는 똑같은 역할 (문법만 다를 뿐)<br>
```yaml
# 테스트 코드는 메모리 DB를 사용하기 위해 datasource 주석
spring:
#  datasource:
#    url: jdbc:h2:tcp://localhost/~/lepldb/lepl
#    username: sa
#    password:
#      driver-class-name: org.h2.Driver
jpa:
#    hibernate:
#      ddl-auto: create # table 자동 생성
###      ddl-auto: none
properties:
hibernate:
#        show_sql: true
format_sql: true
logging.level:
org.hibernate.SQL: debug
org.hibernate.type: trace
logging:
level:
com.lepl: debug
```
</div>
</details>

<details><summary><b>프로필WEB:application.yml</b></summary>
<div markdown="1">
```yaml
# default 프로필 -> 개발모드로 사용
spring:
  # thymeleaf 캐시 사용 X로 실시간 reload
  thymeleaf:
    cache: false
    prefix: file:src/main/resources/templates/ # thymeleaf 경로지정
  # H2 DB
  datasource:
    url: jdbc:h2:tcp://localhost/~/secret-art-typing-gallery/secret
    username: sa
    password:
      driver-class-name: org.h2.Driver
  jpa:
    hibernate:
      ddl-auto: create # DB 초기화 사용
    properties:
      hibernate:
#        show_sql: true
        format_sql: true
  # 메시지 - thymeleaf 연동
  messages:
    basename: messages
server:
  port: 8080
#  address: 223.39.212.163
  servlet:
    session:
      timeout: 30m # session timeout 30min
logging.level: # DB 쿼리 로그
  org.hibernate.SQL: debug
  org.hibernate.type: trace
my: # 외부설정 값
  datasource:
    imgPath: C:/images-spring/
logging:
  level:
    com.dau.secretarttypinggallery.controller: debug # 컨트롤러에서만 로그확인
--- # 프로필 구분자
# prod 프로필 -> 배포모드로 사용
spring:
  config:
    activate:
      on-profile: prod
  # thymeleaf 캐시 사용!!
  thymeleaf:
    cache: true
    prefix: classpath:/templates/ # 리눅스용(배포환경) 경로지정
  # MYSQL DB
  datasource:
    url: jdbc:mysql://localhost:3306/secret?useSSL=false&useUnicode=true&serverTimezone=Asia/Seoul
    username: secretUser
    password: 1234
    driver-class-name: com.mysql.cj.jdbc.Driver
  jpa:
    hibernate:
#      ddl-auto: none # DB 초기화 사용 X
      ddl-auto: create # DB 초기화 사용
  # 메시지 - thymeleaf 연동
  messages:
    basename: messages
server:
  port: 8080 # 포트 포워딩으로 http(80) -> 8080 설정하면 "URL에 포트없이 바로 접속가능"
  servlet:
    session:
      timeout: 30m # session timeout 30min
my: # 외부설정 값
  datasource:
    imgPath: /var/www/images-spring/
logging:
  level:
    com.dau.secretarttypinggallery.controller: info # 배포는 기본값(=info) 사용
```
</div>
</details>
<br>

<br>

## 리팩토링 - AOP(+캐시), 메시지와 외부설정, 검증과 예외 등

<br><br>

### AOP(공통 해결 관심사)

**기능 확장법 + 브라우저,서버 캐시 설정법**

- 스프링은 주로 `WebMvcConfigurer` 를 **상속받아서 구현**하면 스프링 빈에 자동 등록과 기능확장!

  * 주로 클래스단에 @Configuration 선언 후 @Override로 기능 확장! (물론 @Bean도 같이 써도 되는데, 이 경우엔 생략 가능. 그치만 구분위해 붙이자.)

  * **예로 인터셉터, ArgumentResolver** 도 새로 구현한 후에는 이곳에 등록해서 확장(추가)해야 한다.

    <details><summary><b>예시 코드<br>interceptor, argumentresolver, 정적이미지경로 핸들링과 브라우저 캐시, CORS 해결</b></summary>
    <div markdown="1"><br>
    정적이미지 경로 핸들링 + 브라우저 캐시(클라 쪽 메모리 활용) 추가 -> `addResoucreHandler()`<br>
    CORS(Cross-Origin Resource Sharing) 해결 -> `addCorsMappings()`
    ```java
    @Configuration // 설정 파일임을 알림
    @Slf4j
    public class ApiConfig implements WebMvcConfigurer {
    //
      @Override
      public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(new LoginMemberArgumentResolver());
      }
    //
      @Override
      public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new MemberCheckInterceptor())
            .order(2)
            .addPathPatterns("/**") // 모든 경로 접근
            .excludePathPatterns("/", "/api/v1/members/login", "/api/v1/members/register",
                "/api/v1/members/logout", "/api/v1/members/*",
                "/image/**", "/css/**", "/*.ico", "/error"); // 제외 경로!
      }
    //
      // 정적이미지 경로 핸들링 + 브라우저 캐시
      // 경로 매핑 작업을 하는 오버라이딩이며 특정 경로(=static/) 에 브라우저 캐시까지 추가한 로직!!
      @Override
      public void addResourceHandlers(ResourceHandlerRegistry registry) {
        CacheControl cacheControl = CacheControl.maxAge(Duration.ofDays(365));
        registry.addResourceHandler("/image/**")
            .addResourceLocations("file:///C:/images-spring/") // LF 조심
            .setCacheControl(cacheControl); // 브라우저 캐시 추가
    //                .addResourceLocations("file:///var/www/images-spring/");
      }
    //
      // CORS
      @Override
      public void addCorsMappings(CorsRegistry registry) {
      	registry.addMapping("/**").allowedOrigins("http://localhost:9000");
      }
    }
    ```
    </div>
    </details>
    <details><summary><b>예시 코드<br>서버 메모리 캐시 위해 CachingConfigurerSupport 상속 및 구현(기능확장)</b></summary>
    <div markdown="1"><br>
    CacheManager를 오버라이딩!! 물론, 간단히 설정파일(yaml)에서 설정도 지원 중<br>
    main 함수있는 클래스에서 `@EnableCaching` 필수 선언!
    코드는 사용법과 만드는 법을 간단히 소개
    ```java
    // 사용법: 서비스단 메소드에 이런식으로 적용
    @Cacheable(value = "users", key = "#memberId", cacheNames = "users", cacheManager = "cacheManager2")
    //
    // 만드는 법: Caffeine 활용 하여 간단히 설정!
    CaffeineCacheManager cacheManager = new CaffeineCacheManager("users");
        cacheManager.setCaffeine(Caffeine.newBuilder()
            .initialCapacity(1) // 내부 해시 테이블의 최소한의 크기 (캐릭터 어차피 1개만 기록)
            .maximumSize(200) // 캐시에 포함할 수 있는 최대 엔트리 수 (멤버 200명 정도한테 적용하자)
    //                .weakKeys() // 직접 키를 설정하므로 주석처리
            .recordStats());
    ```
    </div>
    </details>

- 일반적으로 스프링 빈 등록은 **클래스단에 @Configuration 선언 후 메소드 단에 @Bean을 여러개 등록(이 패턴을 주력으로 사용하자)**한다.

  - 내 기억엔 @Configuration 선언 후 메소드 1개면 @Bean 생략 가능했던 것 같다.
  - 그치만 그냥 안전하게 @Configuration 선언 후 메소드 단에 @Bean여러개 등록 패턴  사용하자

- **@Component 로도 간단히 빈 등록**이 가능하다. 이를 가지면서 세분화 한 애노테이션은 **@Service, @Repository, @Entity** 이며 자동 빈 등록해준다.

<br>

#### 일반 상황(@Aspect, @Around)

동작흐름 : **프록시 객체 생성 -> 실제 객체 생성 의 흐름**

만들 때 : **@Aspect** 사용 및 "**스프링 빈** 등록 필수"

사용할 때 : **@Around**로 원하는 곳에 적용

- **@Around**는 AOP의 어드바이스 타입 중 하나, **메서드 실행 전후** 로직 실행 지원
- **proceed()** 를 통해 원하는 시점 때 **"실제 실행"**을 지원 -> AOP 말고 실제 메서드

```java
@Aspect // AOP
@Component // "빈" 등록
@Slf4j
public class TimeTraceAop {

  @Around("execution(* com.lepl..*(..))")
  public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
    // 프록시 실행
    long start = System.currentTimeMillis();
    log.debug("START: {}", joinPoint.toString());
    try {
      return joinPoint.proceed(); // 실제 실행
    } finally {
      long finish = System.currentTimeMillis();
      long timeMs = finish - start;
      log.debug("END: {} {}ms", joinPoint.toString(), timeMs);
    }
  }
}
```

<br>

#### 웹 통신(HTTP) 상황 - 필터, 인터셉터, 리졸버 등

**동작흐름: HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 인터셉터1 -> 인터셉터2 -> 컨트롤러**

**interceptor 먼저 수행 후 -> argumentresolver 수행 순서**

서블릿 필터 보다는 **“스프링 인터셉터” 와 "ArgumentResolver" 활용 권장**

- **ArgumentResolver** 를 통해서 공통 작업할 때 컨트롤러를 더욱 편리하게 사용 가능 (**컨트롤러 메서드의 매개변수에 적절한 값을 바인딩**하는 과정을 담당)

  - 서블릿(=Dispatcher Servlet) 과 컨트롤러(핸들러) 사이에는 사실 핸들러 어댑터가 동작을 하며, 이때 “**ArgumentResolver**” 가 중간에 있어서 이를 거치고 “**컨트롤러(핸들러)**” 가 동작

  - **ArgumentResolver 덕분에 수많은 애노테이션 속 데이터들을 컨트롤러(핸들러)로 정상 전달**

    - EX) **@Login** 애노테이션 만들어서 “멤버 정보” 객체 반환 이런게 가능!

    - **기존** : `@SessionAttribute(name = "loginMember", required = false) Member loginMember`

    - **적용** : `@Login Member loginMember`

- “**인터셉터**” 는 “필터” 보다 더 많이 호출 (**컨트롤러 도달 전과 후에 공통 수행**)
  - “로그인 인증” 을 예시로 구현 가능
    - `preHandle` 에 “세션정보(쿠키)” 인증을 시도 및 실패시 다시 “로그인창” 으로 이동
      - 이를 다양한 **URL 패턴으로 적용 가능** - 적용URL, 미적용URL 구분도 간편
    - 컨트롤러 도달 후인 postHandle() 도 있다는 것만 기억.

<details><summary><b>예시코드 - ArgumentResolver</b></summary>
<div markdown="1"><br>
Login.java(인터페이스) -> @Login 애노테이션 생성
```java
@Target(ElementType.PARAMETER) // 파라미터에 사용
@Retention(RetentionPolicy.RUNTIME) // RUNTIME 까지 살아남게 설정
public @interface Login {
}
```
LoginMemberArgumentResolver.java -> 애노테이션이 @Login이고, 타입이 Long인지 체크 후 세션에 있는 memberId 반환해주는 로직
```java
@Slf4j
public class LoginMemberArgumentResolver implements HandlerMethodArgumentResolver {
//
  @Override
  public boolean supportsParameter(MethodParameter parameter) {
    log.info("supportsParameter 실행");
//
    boolean hasLoginAnnotation = parameter.hasParameterAnnotation(Login.class);
    boolean hasLongType = Long.class.isAssignableFrom(parameter.getParameterType());
    // 어노테이션이 @Login 이고, 해당 파라미터 타입이 Long 라면 true 반환
    return hasLoginAnnotation && hasLongType;
  }
//
  // 위 supportsParameter 가 true 면 아래 함수가 실행
  @Override
  public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer,
      NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
    log.info("resolveArgument 실행");
//
    HttpServletRequest request = (HttpServletRequest) webRequest.getNativeRequest();
    HttpSession session = request.getSession(false); // false : 없으면 null
    if (session == null) {
      log.info("null");
      return null;
    }
    Long memberId = Long.valueOf(session.getAttribute(SESSION_NAME_LOGIN).toString());
    return memberId;
  }
}
```
ApiConfig.java -> ArgumentResolver 등록 방법
```java
@Configuration // 설정 파일임을 알림
@Slf4j
public class ApiConfig implements WebMvcConfigurer {
//
  @Override
  public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
    resolvers.add(new LoginMemberArgumentResolver());
  }
}
```
</div>
</details>

<details><summary><b>예시코드 - Interceptor + 적용,미적용 URL 설정</b></summary>
<div markdown="1"><br>
MemberCheckInterceptor.java -> 로그인 인증 유무 체크 로직
```java
@Slf4j // log
public class MemberCheckInterceptor implements HandlerInterceptor {
//
  @Override
  public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
      throws Exception {
    String requestURI = request.getRequestURI();
    log.info("인증 체크 인터셉터 실행 {}", requestURI);
//
    HttpSession session = request.getSession();
    if (session == null || session.getAttribute(SESSION_NAME_LOGIN) == null) {
      log.info("미인증 사용자 요청");
      // 회원 아님을 알림
      response.setStatus(HttpStatus.UNAUTHORIZED.value()); // status code : 401
      return false;
    }
    return true;
  }
}
```
ApiConfig.java -> Interceptor 적용법 + 인증 URL과 미인증 URL 설정 로직
```java
@Configuration // 설정 파일임을 알림
@Slf4j
public class ApiConfig implements WebMvcConfigurer {
//
  @Override
  public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(new MemberCheckInterceptor())
        .order(2)
        .addPathPatterns("/**") // 모든 경로 접근
        .excludePathPatterns("/", "/api/v1/members/login", "/api/v1/members/register",
            "/api/v1/members/logout", "/api/v1/members/*",
            "/image/**", "/css/**", "/*.ico", "/error"); // 제외 경로!
  }
}
```
</div>
</details>

<br><br>

### 메시지 국제화 기능 + 외부설정(@Value -> @ConfigurationProperties)

<br>

#### 메시지 국제화

메시지 국제화 패턴을 사용하면 좋은점이 **여러 외국어를 지원**하게 만들기 수월!!

* `application.properties` 에 `spring.messages.basename=messages` 를 추가!! (properties에서 설정했으므로 따로 `MessageSource`를 스프링 빈에 수동 등록할 필요가 없어짐! 부트가 자동으로 만들어 줌!)
  * 이후 `messages.properties` 에 공통 관리할 messages에 담을 내용을 세팅
  * `MessageSource` 는 이렇게 등록한 메시지를 사용하게 해주는 빈 (@Autowired 로 간단히 주입해서 사용 ㄱㄱ)
* 여러개 추가할거면?? 
  * 예로 `errors.properties` 추가한다고 하면 `spring.messages.basename=message, errors` 이렇게 이어적으면 됨

<details><summary><b>예시 코드 - application.yml, messages.properties, messages_en.properties, TEST_CODE</b></summary>
<div markdown="1"><br>
application.yml 설정 -> 여기서 안하면 직접 클래스 만들어서 수동 스프링 빈 등록해야함(MessageSource메소드)
```java
spring:
  # 메시지 - thymeleaf 자동 연동
  messages:
    basename: messages
```
<br>
messages.properties, messages_en.properties 메시지 국제화!!
```properties
# messages.properties
hello=안녕
page.lobby=로비
page.gallery=전시실
page.studio=작품 제작실
label.item.location=작품위치
label.item.nickName=작가명
label.item.title=작품명
label.item.password=비밀번호
label.item.content=작품설명
label.item.product.date=제작일자
button.ok=확인
button.cancel=취소
button.next=다음
button.prev=다른 전시실 둘러보기
button.exit=전시실 나가기
button.product=갤러리에 전시하기
button.product.add=작품 전시하기
footer.tag=Copyright © 2023 YE-BEEN. All Rights Reserved
#
#
# messages_en.properties
hello=hello
page.lobby=lobby
page.gallery=gallery
page.studio=studio
label.item.location=location
label.item.nickName=nickName
label.item.title=title
label.item.password=password
label.item.content=content
label.item.product.date=date
button.ok=ok
button.cancel=cancel
button.next=next
button.prev=prev
button.exit=exit
button.product=product
button.product.add=add
footer.tag=Copyright © 2023 YE-BEEN. All Rights Reserved
```
<br>
**TEST CODE**
```java
@SpringBootTest
public class MessageTest {
    @Autowired
    MessageSource ms;
    @Test
    void helloMessage() {
        String result = ms.getMessage("hello", null, null);
        System.out.println(result);
        Assertions.assertEquals(result, "안녕");
    }
}
```
</div>
</details>

<br>

#### 외부 설정 -> @ConfigurationProperties 사용 하자!

**외부 설정(application.yml) 에서 설정한 내용을 -> 자바객체로 바꿔서 -> 스프링 빈에 등록하여 사용하자는 방법! (messages랑 다른거다 오해하지말자!)**<br>`@ConfigurationProperties` 로 간단히 가능하다.

- `application.yml` 에 **my.datasource 하위 내용(경로)**를 설정해서 사용하고자 한다면?
- `MyDataSourceProperties.java` 를 만들어서 **@ConfigurationProperties("my.datasource")** 로 설정 등록이 먼저다.
- `MyDataSourceConfig.java` 를 만들어서 **@EnableConfigurationProperties(MyDataSourceProperties.class)** 로 추가 설정하고 **@Bean**로 스프링 빈 등록(기능확장)을 하면 끝이다. -> `MyDataSource.java` 를 직접 만들어서 이를 "자바객체"로 사용하게 하자.
  - **@Value("${my.datasource.imgPath}")** 로 간단히 가져올 수 있지만, 우린 이걸 쓰려는게 아니니까 이건 잠시 잊자.
- 사용은 그냥 **MyDataSourceConfig 객체**를 가져다가 바로 사용하면 됨!

<details><summary><b>자세한 예시 코드 - application.yml, MyDataSourceProperties.java, MyDataSourceConfig.java, MyDataSource.java, TEST_CODE</b></summary>
<div markdown="1"><br>
위 구현 순서대로 코드 나열하겠다.<br>
application.yml (다시 언급하지만 메시지 국제화인 messages랑 다른거다!)
```yaml
my:
  datasource:
    imgPath: C:/images-spring/
```
MyDataSourceProperties.java
```java
/**
 * @ConfigurationProperties 로 application.yml 에 저장한 my.datasource 하위 내용
 * 가져와서 "자바 객체" 로 변환 + 검증까지
 */
@Getter
@ConfigurationProperties("my.datasource")
@Validated
public class MyDataSourceProperties {
    @NotEmpty
    private String imgPath;
    public MyDataSourceProperties(String imgPath) {
        this.imgPath = imgPath;
    }
}
```
MyDataSourceConfig.java
```java
/**
 * @EnableConfigurationProperties 로 MyDataSourceProperties 를 사용 및 스프링 빈 등록
 * @Import 를 통해서 "컴포넌트 스캔 대상!" -> 방식은 자유. 본인은 이걸로 스캔 했을 뿐
 */
@Slf4j
@EnableConfigurationProperties(MyDataSourceProperties.class)
public class MyDataSourceConfig {
    private final MyDataSourceProperties properties;
    public MyDataSourceConfig(MyDataSourceProperties properties) {
        this.properties = properties;
    }
    @Bean
    public MyDataSource getMyDataSource() {
        log.info("BeanTest");
        return new MyDataSource(
                properties.getImgPath()
        );
    }
}
```
MyDataSource.java
```java
/**
 * @PostConstruct 는 로그 확인용으로 추가! 외부설정 내용!!
 */
@Slf4j
@Data
public class MyDataSource {
    private String imgPath;
//
    // 생성자
    public MyDataSource(String imgPath) {
        this.imgPath = imgPath;
    }
//
    @PostConstruct // 로딩완료 후 바로 실행
    public void init() {
        log.info("imgPath={}", imgPath);
    }
}
```
TEST_CODE
```java
//필요한 곳에서 불러오고
private final MyDataSourceConfig source;
//이를 바로 사용하면 끝
.addResourceLocations("file:///"+source.getMyDataSource().getImgPath());
```
</div>
</details>
<br><br>

### 검증(Valid)과 예외처리(Exception)

주의: 웹이 아닌 **API의 경우 JSON으로 변경된 데이터를 Valid로 검증**하는것. **JSON으로 변경될 때 에러나 그 전에 이미 발생한 에러는 Exception(예외처리)**으로 해결!

**Valid는 클라, 서버 둘다에서 하면 더 안전하고 좋다.** 왜 그런지는 아래를 참고

- **id에는 적용하는가??? 언제써야 하는가???**
  *  id에 왜 검증을 넣냐고 볼 수도 있지만, POSTMAN같은 툴로 충분히 악의적 접근이 가능하기 때문에 "최종 검증은 서버에서 진행하는 것이 안전"
  *  예로 "등록 폼" 에서는 보통 id가 자동생성하므로 필요없기 때문에 @NotNull 같은걸 적용안해도 됨. 그러나 "수정 폼" 에서는 id가 필요하기에 "검증"을 해주는것이 안전
  *  **즉, 필요한 경우가 있을때는 "서버에서 최종 검증" 을 하는것이 안전!**

**검증과 예외 예시**

* 예외처리 예시 => ex: String 타입에 int가 넘어온 에러를 처리
* 검증 예시 => ex: 0~9999 숫자범위"를 지정

**예외란??** -> **Error**는 주로 JVM에서 발생하는 문제라서 에러처리 하지 않아도 된다. Exception을 보자.

<img src="https://github.com/user-attachments/assets/e192334c-077e-42d8-a2a0-4f70a4c89e96" alt="image" style="zoom: 50%;" /> 

* **"가장 좋은 예외(복구가능 예외)"**는 **컴파일(체크) 예외**, 그리고 **애플리케이션 로딩 시점에 발생하는 예외**<br>ex: IOException, SQLException -> try-catch(처리), throws(회피)로 주로 해결
  * 검증기 사용시 **"앱 로딩 시점 예외"로 나타내 줄 수 있다는 장점!**

* **"가장 나쁜 예외(복구불가능 예외)"**는 고객 서비스중에 발생하는 **런타임(언체크) 예외**<br>ex: NullPointerException, IndexOutOfBoundException -> throw(전환)로 주로 해결
  * 참고: try-catch, throws, throw는 어디서든 사용할 수 있다.


- 하지만, 기본적으로 **런타임(언체크) 예외를 사용하자(문사화 필수!)**<br>왜 WHY??<br>SQLException 의 경우 쿼리를 수정 후 재배포하지 않는 이상 복구할 수 없다.<br>try-catch로 잡고 아무 조치도 하지 않는 건 위험하다.<br>무분별한 throws Exception는 어떤 에러인지 알기 어려워 try-catch처리가 힘들다.
  - 따라서 **"체크 예외"는 의도를 가지고 던지는 경우**만 try-catch, throws로 처리하고, **그 외는 언체크 예외로 전환**하자!
  - 그리고 예외를 **공통으로 처리**하는 `ExceptionHandler`를 만들어서 처리하자.

- **주의점:** 스프링 프레임워크가 제공하는 **선언적 트랜잭션(@Transactional)**안에서 **에러 발생 시 체크 예외는 롤백이 되지 않고, 언체크 예외는 롤백이 된다.** 이는 **자바 언어와는 무관**하게 프레임워크의 기능임을 반드시 알고 넘어가도록 하자. (물론 옵션을 변경할 수 있다.)<br>**또한, 예외변환** `throw new 커스텀예외(e);` 는 현재 error를 담는 e를 꼭 생성자 매개변수에 넘겨줘야 하위의 에러내용들을 다 기록한다는걸 기억!

**검증이란** Form 데이터같은 것들이 POST 요청왔을때 원하는 "검증"을 진행하는 것  

* HTTP 요청 **"Form데이터, URL파라미터" 는 "검증"** 만으로 충분 - @ModelAttribute
* HTTP 요청 **"API" 는 "검증 + 예외처리"** 까지 필요 - @RequestBody
  * 또한, **API의 경우에는 여기서 사용한 메커니즘들을 활용하되 메시지는 꼭 API스펙에 맞춰 잘 반환**해주면 됨 (참고로 타임리프는 그냥 th문법으로 알아서 잘 가져옴)

<br>

#### 검증(Valid)

**Valid검증: “@Validated + {HTTP요청 + BindingResult} + 검증 애노테이션 + errors.properties(메시지)”**

- ex: `public ResponseEntity<ApiResponse<String>> login(@RequestBody @Validated LoginMemberRequestDto loginDto, BindingResult bindingResult, HttpServletRequest request) {}`

  - **{HTTP요청(@ModelAttribute or @RequestBody) + BindingResult}** 순서 지켜서 파라미터 작성

- 오류 메시지를 **erros.propeties**활용하면 부트는 이 파일 먼저 우선 체크 및 사용.

  - **@NotNull(message ="...")** 이런식으로 메시지 바로 설정도 가능 / **@Pattern(정규식)**도 자주사용

  - **BeanValidation의 메시지 찾는순서**는 `errors.properties` 를 먼저 찾고 `검증 애노테이션의 message 속성` 사용

    ```properties
    NotBlank.item.itemName=상품 이름을 적어주세요.
    NotBlank={0} 공백X
    Range={0}, {2} ~ {1} 허용
    Max={0}, 최대 {1}
    ```

    * NotBlanck 보다 NotBlank.item.itemName 같이 세부 필드를 더 우선순위 높게 출력

- **bindingresult 미사용 시?** @Valid랑 @NotNull 사용했다 가정하면, 검증 오류 발생 시 **"자동 처리"**

  - 참고: **실제 객체말고 DTO**에서 @NotNull 하는게 좋다!! 안전하기도 하고!

- **bindingresult 사용 시?** 오류가 이곳에 담기다보니 **"컨트롤러가 정상처리!!"**<br>따라서 if문으로 `if(a.getId()!=null && b.getId()!=null) 이나 if(bindingResult.hasErros())` 이런걸로 검증오류 처리를 **"직접 하는 방식"**

  - 보통 **API응답 양식**을 정해놓고 반환한다. 커스텀 가능하니까. => `ApiResponse.java` 추가 + `제네릭`까지 활용

    - **제네릭을 왜??** -> `ResponseEntity<ResDto>` 처럼 반환 타입이 ResDto이지 bindingResult 가 아니라서 **타입 문제가 발생ㅜ**

      <details><summary><b>해결 예시</b></summary>
      <div markdown="1"><br>
      `ApiResponse.java` 에서 제네릭을 사용해서 해결 (아래 코드)<br>**POINT: error함수 사용할 때 제네릭 필드부분에 null을 주기 때문에 타입에서 자유로워 질 수 있다, 반대로 success함수 사용할 때 ResDto같은 데이터가 들어오면 이를 반환할 수도 있다!**
      ```java
      //ApiResponse.java
      public static <T> ApiResponse<T> success(int status, T data) {
          return new ApiResponse<>(status, "정상", data, null, null, null);
      }
      public static <T> ApiResponse<T> error(int status, BindingResult bindingResult) {
          return new ApiResponse<>(status, "검증 오류", null, bindingResult.getFieldError().getDefaultMessage(), bindingResult.getFieldError().getRejectedValue(), bindingResult.getObjectName());
      }
      public static <T> ApiResponse<T> errorObject(int status, BindingResult bindingResult) {
          return new ApiResponse<>(status, "검증 오류", null, bindingResult.getGlobalError().getDefaultMessage(), null, bindingResult.getGlobalError().getObjectName());
      }
      ```
      </div>
      </details>

  - **FiledError -> erros.properties 활용 권장 (메시지)**<br>**ObjectError -> bindingResult.reject() 활용 권장 (메시지)**

    - **FieldError** 는 도메인에 "검증 애노테이션" 사용 + "bindingResult.hasErrors()" 필수

    - **ObjectError** 는 "검증 직접 작성 -> bindingResult.reject() 함수 권장"

      * `bindingResult.hasErrors()` 는 errors가 있는지 여부를 반환하고, 
      * `errors` 에는 "검증결과 에러" 들을 기록하며 이는 "검증 애노테이션"에 걸린 에러들을 의미

    - **POST**에서 주로 사용하게 되는 것 같다.? 즉, **외부 입력이 필요할때**

    - 아래 예제에선 errors.properties는 굳이 안썼다. 메시지 국제화에는 좋겠지만 뭐 기본제공 메시지도 넘 좋고, **@NotNull("메시지내용")** 처럼 직접 작성도 있어서!

      <details><summary><b>코드 예시</b></summary>
      <div markdown="1"><br>
      FiledError 예시
      ```java
      //이런 느낌으로 사용 -> FiledError
      public ResponseEntity<ApiResponse<String>> login(@RequestBody @Validated LoginMemberRequestDto loginDto, BindingResult bindingResult, HttpServletRequest request) {
          log.info("bindingResult 때문에 검증에 걸려도 정상 동작");
          if (bindingResult.hasErrors()) {
              log.info("검증 오류 발생 errors={}", bindingResult);
              ApiResponse res = ApiResponse.error(HttpStatus.BAD_REQUEST.value(), bindingResult);
              return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(res);
          }
          ApiResponse res = ApiResponse.success(HttpStatus.OK.value(), SUCCESS_LOGIN);
          return ResponseEntity.status(HttpStatus.OK)
      ```
      ![image](https://github.com/user-attachments/assets/bb52c3c4-3040-4194-8726-167afa20136b)
      <br>
      ObjectError 예시
      ```java
      //이런 느낌으로 사용 -> ObjectError
      if (request.getPrice() != null && request.getPurchase_quantity() != null) {
          int resultPrice = request.getPrice() * request.getPurchase_quantity();
          if (resultPrice <= 0) {
              bindingResult.reject(null, null, "전체 가격은 0원 초과야 합니다. 현재 가격은 "+resultPrice); //errors.properties 안써서 그냥 null, null 한거임.
              log.info("검증 오류 발생 errors={}", bindingResult.getAllErrors());
              ApiResponse res = ApiResponse.errorObject(HttpStatus.BAD_REQUEST.value(), bindingResult);
              return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(res);
          }
      }
      ```
      ![image](https://github.com/user-attachments/assets/5dcb9c41-1332-4ccb-94e4-b9065bba7bcd)
      </div>
      </details>

**타임리프에서 검증**

* `th:field, th:errors, th:errorclass` 문법을 주로 같이 활용
  * `<div th:errors="*{id}"...` 등 이런형태로 바로 사용!! 에러 시 해당 태그 출력!!
  * @ModelAttribute("item") 사용이 중요!!
    * 왜냐하면, 컨트롤러 단에서 파라미터로 `AddItemDto form` 로 선언하면 Model에 "AddItemDto"객체 형태로 자동으로 담기 때문!
    * 하지만, 여기선 타임리프의 th:object를 "item"로 사용하여 반드시 Model에 "item"으로 담아야 한다는 점!
    * **그래서 @ModelAttribute("item") 방식을 사용해서 Model에 "item"객체 형태로 담자!**

<br>

#### 예외처리(Exception)

**예외처리 - Spring Exception**

* **html**

  * **자동으로 에러에 필요한 로직을 등록하므로 바로 활용가능 (스프링 부트가 에러 페이지도 자동으로 제공해준다는 것!! 물론 직접 추가도 되고)**

  * `ErrorPage, BasicErrorController` 자동 등록 및 `/error` 경로로 기본설정

  * `BasicErrorController` 는 `ErrorPage` 에서 등록한 `/error` 를 매핑해서 처리하는 컨트롤러

  * **뷰선택 우선순위(BasicErrorController 가 제공하는 기능)**

    1. 뷰템플릿

       - resources/templates/error/500.html

       - html resources/templates/error/5xx.html

    2. 정적리소스( static , public ) resources/

       - static/error/400.html

       - resources/static/error/404.html

       - resources/static/error/4xx.html

    3. 적용대상이없을 때뷰이름( error )
       - resources/templates/error.html

    <details><summary><b>html 적용 예시</b></summary>
    <div markdown="1"><br>
    `errorTest.java` -> 예외 페이지 확인 용 (직접 커스텀 페이지인 4xx.html, 404.html, 500.html 테스트 코드!)
    ```java
    @Controller
    @RequiredArgsConstructor
    @Slf4j
    public class errorTest {
        /**
         * 일부러 에러 발생 시키기 -> 예외 페이지 확인위해
         * 테스트가 아닌 실제로 에러 발생시 해당 에러내용들이 예외 페이지로 정리되어 출력
         */
        @GetMapping("/error-404")
        public void error404(HttpServletResponse response) throws IOException {
            response.sendError(404, "404 오류!");
        }
        @GetMapping("/error-500")
        public void error500(HttpServletResponse response) throws IOException {
            response.sendError(500);
        }
        @GetMapping("/error-403")
        public void error403(HttpServletResponse response) throws IOException {
            response.sendError(403, "403 오류!");
        }
    }
    ```
    <div markdown="1"><br>
    `/templates/error/4xx.html` 코드 (404, 500도 이런식)
    ```html
    <!DOCTYPE HTML>
    <html xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="utf-8">
    </head>
    <body>
    <div class="container" style="max-width: 600px">
        <div class="py-5 text-center">
            <h2>4xx 오류 화면 스프링 부트 제공</h2>
        </div>
        <div>
            <p>오류 화면 입니다.</p>
        </div>
        <ul>
            <li>오류 정보</li>
            <ul>
                <li th:text="|timestamp: ${timestamp}|"></li>
                <li th:text="|path: ${path}|"></li>
                <li th:text="|status: ${status}|"></li>
                <li th:text="|message: ${message}|"></li>
                <li th:text="|error: ${error}|"></li>
                <li th:text="|exception: ${exception}|"></li>
                <li th:text="|errors: ${errors}|"></li>
                <li th:text="|trace: ${trace}|"></li>
            </ul>
            </li>
        </ul>
        <hr class="my-4">
    </div> <!-- /container -->
    </body>
    </html>
    ```
    </div>
    </div>
    </details>

* **API**

  * API는 html보다 예외처리가 세부적이므로 `ExceptionHandlerExceptionResolver` 로 해결

  * **즉, 자동 등록한 에러 로직을 사용하지 않고 @ExceptionHandelr + @ControllerAdvice 조합 사용**<br>참고: @RestControllerAdvice = @ControllerAdvice + @ResponseBody

    * `@RestControllerAdvice` 를 통해서 컨트롤러를 "기존코드, 예외코드" 나눠서 분류 가능
    * ResponseBody 덕분에 에러 반환 형식도 **JSON**으로 처리 됨!
    * 컨트롤러에 예외가 **@ExceptionHandler** 덕분에 **"정상처리"** 되므로 상태코드 변환은 필수

    <details><summary><b>API 적용 예시</b></summary>
    <div markdown="1"><br>
    음.. 에러는 `IllegalArgumentException`(사용자가 값 잘못입력) JSON 변경전 잘못 입력된 값인 400에러(BAD REQUEST)랑 중복검증처럼 `IllegalStateException`(Conflict:409)이랑 그냥 서버자체에서 오류뜬 500에러(모든에러인 `Exception`으로 하겠음) 까지 총 3개를 기준으로 예외처리 컨트롤 해보겠음.
    - (1)API 예외처리 응답 양식은 **ErrorResult** 객체로 code, message 필드 가지게 만들자.
    - (2)**ApiControllerAdvice** 를 추가 개발했다. 코드보면 이해 잘 될거다.
    <div markdown="1">
    ```java
    /**
     * API 에외처리 응답 양식
     */
    @Data
    public class ErrorResult {
      private String code;
      private String message;
      public ErrorResult(String code, String message) {
        this.code=code;
        this.message=message;
      }
    }
    //
    //
    //
    @Slf4j
    @RestControllerAdvice(basePackages = "com.lepl.api") //컨트롤러 예외들 여기서 처리하게끔 역할
    public class ApiControllerAdvice {
    //
      @ResponseStatus(HttpStatus.BAD_REQUEST) //이거 덕분에 ResponseEntity 없이 응답 코드 설정! (간단하게 하려고 추가함)
      @ExceptionHandler(IllegalArgumentException.class) //해당 예외가 잡히면 이 함수를 실행 역할!
      public ErrorResult illegalApiHandler(IllegalArgumentException e) {
        log.error("[exceptionHandler] ", e);
        return new ErrorResult("BAD REQUEST", e.getMessage());
      }
    //
      @ResponseStatus(HttpStatus.CONFLICT) //중복 충돌이니까:409
      @ExceptionHandler(IllegalStateException.class)
      public ErrorResult illegalApiHandler(IllegalStateException e) {
        log.error("[exceptionHandler] ", e);
        return new ErrorResult("CONFLICT", e.getMessage());
      }
    //
      @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
      @ExceptionHandler
      public ErrorResult exceptionApiHandler(Exception e) {
        log.error("[exceptionHandler] ex", e);
        return new ErrorResult("SERVER ERROR", e.getMessage());
      }
    }
    ```
    </div>
    아래는 결과 확인해보기 위한 테스트를 진행! -> register, login
    - register에 있는 **중복검증** 예외처리가 **첫번째**이다.(IllegalStateException)
    - 요청할 때 body에 아무것도 입력 안했을때 예외처리가 **두번째**이다.(Exception)
    - 요청할 때 body에 요청 json형식에 안맞게 입력했을 때가 **세번째**이다.(Exception)
    - 아쉽지만 IllegalArgumentException 에러는 확인 못했다. Json 입력 에러는 다른 예외처리 사용하더라구.<br>
    ![image](https://github.com/user-attachments/assets/d02fb4a3-ac85-46d7-b139-c08d537669f9)<br>
    ![image](https://github.com/user-attachments/assets/fcffeb21-92f1-4df4-9fee-e45bcac12e76)<br>
    ![image](https://github.com/user-attachments/assets/cdaad4cd-a69c-491c-8d34-03bc4e00df65)
    </div>
    </details>

<br>

<br>

## 배포 (+원하는 프로필로)

**AWS나 cafe24나 로컬 등에 서버 배포(구동) ㄱ ㄱ ㄱ**

**jar vs war**

- **jar은 내부 톰캣 사용, war은 외부 서버 사용**으로 이해하면 됨 -> 보통 본인은 그냥 내부 톰캣 사용해서 배포
- 주의: 외부 톰캣에서 **webapp 경로**를 사용하는 편인데, 실제로 war은 사용가능하나 jar은 사용불가

<br>

**포트생략법**

* 배포할 때 `http://localhost:8080` 이 아닌 `http://localhost` 로 접속법(포트생략법)
* **http는 80포트를 기본값**으로 사용하고, 개발할 때 사용한 포트는 8080포트
* 따라서 **"포트포워딩"** 을 사용해서 **80포트 접속시 -> 8080포트로 변경**해주면 끝
* 80포트 -> 8080포트 포트포워딩 예시
  * 등록 : `sudo iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j REDIRECT --to-port 8080`
  * 조회 : `iptables -nL PREROUTING -t nat --line-numbers`
  * 삭제 : `iptables -t nat -D PREROUTING {number}`

<br>

**"실행 시점" 에 원하는 프로필 사용!!**

1. IDE에서 Application 에 `--spring.profiles.active=prod` 하거나 (물론 값도 가능)

   <details><summary><b>인텔리J에서 활용한 예시(사진)</b></summary>
   <div markdown="1"><br>
   ![image](https://github.com/user-attachments/assets/a4cebe08-935c-44fe-ac9c-2ab3ad7ebd3c)<br>
   **prod(배포 프로필)**
   ![image](https://github.com/user-attachments/assets/45ae772d-5122-45ac-9fb4-6f564d29c8be)<br>
   **default(개발 프로필)**
   ![image](https://github.com/user-attachments/assets/5df1afac-634c-4fc6-b7ac-0a8c0cc1a356)
   </div>
   </details>

2. 터미널에서 명령어로 jar or war 실행. 2가지 방법 소개 (해당 파일 경로에서 꼭 입력)

   nohup은 백그라운드에서 실행!! -> nohup.out에 로그남음

   `nohup java -jar secret-art-typing-gallery-0.0.1-SNAPSHOT.jar --spring.profiles.active=prod &`

   `nohup java -Dspring.profiles.active=prod -jar secret-art-typing-gallery-0.0.1-SNAPSHOT.jar &`

<br>

<br>

## JPQL(JPA-ORM) vs SQL(MyBatis-SQL Mapper)

**MyBatis는 "스프링 DB 관련 추가 지식" -> "MyBatis" 파트를 보자  **
=> JPA와 MyBatis 개발은 확연히 다르단걸 인지하자. 그차이도 "MyBatis"파트를 보자

> [JPA vs Mybatis 참고 문헌](https://www.elancer.co.kr/blog/detail/231?seq=231)

ORM과 SQL Mapper 는 둘 다 객체와 SQL 매핑을 도와줘서 유사하긴 하다.<br>

- ORM은 SQL을 작성 할 필요 없을 정도로 더 많은 걸 지원한다. (JPQL을 작성 하긴 함)

- SQL Mapper는 Java 코드와 SQL(XML) 문을 아예 분ㄴ리 + 동적쿼리를 세밀하게 지원 해준다.

<br><br>

### JPA - 영속성 컨텍스트

**중요!! - 영속성 컨텍스트**

- **4가지 상태 : 영속, 비영속, 준영속, 삭제**
  - 비영속(transient)은 말 그대로 persist(=영속) 하지 않은 상태 **-> 영속성과 아예 무관**
  - 준영속(detached)은 영속성 컨텍스트에서 **분리(em.detach()) 된 상태**
  - 삭제(removed)는 말 그대로 영속성에서 **삭제 된 상태**

* **flush : 영속성 내용 DB 반영 (쿼리)**

  * **(1)em.createQuery** 같이 쿼리문 작성하는 것들이 **flush 자동 발생**
  * **(2)트랜잭션**은 당연히 **flush 자동 발생**
    * 목적: 쌓인 영속성 내용을 DB에 먼저 반영해줘야 jpql로 조회할 때 데이터 **불일치 방지**
    * 단, 이건 내가 테스트해서 확인한거긴 한데 **jpql 에 member 를 사용중이고 이미 member가 영속성(변경내역)에 존재할 때 flush 자동발생. 그게 아니면 굳이 영속성 내용을 먼저 반영할 필요가 없다보니 자동발생 안함.**  
      => 예로 `select m from Member m` 를 사용하기전 Member 영속성(변경할 내역)을 먼저 체크 후 flush 동반 유무 결정

* **persist : 영속성 등록**

  * 우선 em(엔티티매니저) 사용하려면 **트랜잭션이 필수!!**

  * save(=persist) : **(1)영속성 등록**, (2)쓰기지연 SQL저장소에 insert쿼리, (3)순차번호 구하기위해 select전송

    * 쓰기지연 SQL저장소의 insert 는 **flush 때 전송**

    * **기본 키 자동 생성**의 경우, 순차번호 구하는 **select 는 바로 전송**

      <details><summary><b>generatedKey 보충 설명 : nextval 쿼리</b></summary>
      <div markdown="1"><br>
      persist때 generatedKey 등록한 엔티티는 자동 nextval 시퀀스 쿼리 발생해 줌<br>
      - 단, identity 방식 사용중이면 insert쿼리 시점에 DB에서 키를 생성<br>
        - `IDENTITY` 전략을 사용한다고 해서 **JPA의 쓰기 지연 기능을 전혀 쓸 수 없는 것은 아닙니다**. 엔티티에 기본 키가 필요할 때만 즉각적으로 `flush()`가 발생하고, 나머지 쿼리들은 여전히 지연될 수 있습니다.<br>
        - `SEQUENCE` 전략은 기본 키를 미리 할당받기 때문에 **쓰기 지연과 더 원활**하게 작동할 수 있습니다.<br>
      - **H2, PostgreSQL, Oracle** 등에서는 기본적으로 `GenerationType.SEQUENCE` 를 사용<br>
        - 오라클12c 부턴 시퀀스 뿐만 아니라 identity 문법도 제공<br>
      - **MySQL, SQL Server** 같은 DB에서는 기본적으로 `GenerationType.IDENTITY`가 사용<br>
      </div>
      </details>

      <details><summary><b>save() 보충 설명: JPA와 Spring Data JPA</b></summary>
      <div markdown="1"><br>
      `JPA`는 직접 구현 필요 (본인은 persist만 사용), `Spring Data Jpa`는 save()함수 제공(persist+merge 형태)<br>
      * isNew() 함수로 엔티티가 이미 있는지 체크하는데 isNew()는 엔티티의 Id가 null인지 체크한다. 따라서 @GeneratedValue를 사용하지 않은 id의 경우엔 임의로 id를 넣어 줄텐데 이러면 여기서 문제가 발생한다. **-> Spring Data Jpa의 save() 함수 로직임.**<br>
        * 왜 문제? id가 null이어야 persist(영속성등록) 할테니까<br>
      * id가 null이 아니니까 persist가 아닌 merge 가 발생하는데 DB에 해당 엔티티를 찾아내려고 select쿼리가 나가는 문제이다.(없을텐데 불필요하게 나가는..)<br>
        - 해결법은 [이 분꺼 참고](https://velog.io/@pjh612/JPA-Spring-Data-JPA의-save의-동작-과정)<br>
        - 참고) merge동작은 이미 있는 엔티티(준영속상태)를 영속성상태로 바꿔줘서 업뎃이 가능하게 만드는 것. 이 과정에서 DB 조회도 발생! (해당 엔티티 찾으려고)
      </div>
      </details>

  * em.find : **(1)영속성 등록**, (2)찾는 id 인 값 select전송

    * **(1)영속성 등록이란** 엔티티를 find() 할 때 **바로 영속성에 있다면 거기서 해당 주소**를 가져와 줌. => db에 쏘는 **쿼리 발생 안함.**
    * **영속성에 없다면, select 는 바로 전송** -> flush 아님.

  * em.remove : **(1)영속성 해제**, (2)쓰기지연 SQL저장소에 delete쿼리, (3)삭제할 아이템 구하기 위해 select전송

    * 쓰기지연 SQL저장소의 delete 는 **flush 때 전송**

    * **영속성에 없다면,** 삭제할 아이템 구하는 **select 는 바로 전송**

      <details><summary><b>remove 예시 코드</b></summary>
      <div markdown="1">
      ```java
      // given
      Task findTask = taskService.findOne(taskId); // Task 찾기
      log.info("findTask : {}", findTask);
      // when
      //1. 영속성 컨텍스트에서 삭제 된걸 확인했기 때문에 아래 findOne 에서 db 조회까지 안가고 종료
      taskService.remove(findTask); // 영속성 컨텍스트에서 상태4개 중 "삭제 상태"로 운영 됨. (taskStatus 랑 cascade 이므로 지연로딩이였어서 여기서 taskStatus 조회가 발생)
      findTask = taskService.findOne(taskId); // 로그 없음. (삭제되어서) -> 영속성에서 이미 삭제된 걸 확인. 이해 안되면 정리한 em.find 확인
      //
      //2. 원래 em.find() 는 영속성 컨텍스트에 엔티티 없으면 db 조회까지 하기 때문에 그 부분을 로그로 보려고 함.
      em.flush();  // 삭제된 엔티티를 DB에 반영 (강제 플러시)
      em.clear();  // 영속성 컨텍스트 초기화
      //
      // 이제 다시 조회를 시도하면 DB에서 조회
      findTask = taskService.findOne(taskId); // 삭제된 엔티티 조회 시도. 쿼리 발생! (null이어야 함)
      ```
      </div>
      </details>

- **FK오류 방지**

  * id를 @GeneratedValue 로 지정했기 때문에 em.persist를 해야 id를 생성 해준다.
  * 따라서 em.persist를 안한 엔티티를 외래키로 쓸때 id가 없어서 FK오류가 발생!!
  * **추가 Update 쿼리(더티체킹) 방지**
    * FK오류를 방지하면서 코드를 짰으면 사실 문제없을텐데, 그게아니라면??  
      => 추가 update 쿼리가 나갈 수 있다.
    * 예로 em.persist를 하지않은 엔티티를 먼저 외래키로 사용한 후에 persist한 경우  
      나중에 flush 할때 더티체킹(id가 생겼으니)으로 update 쿼리가 추가 생성된다.
  * **따라서 그냥 FK오류 방지를 준수하면서 꼭 작성!**

- **Update 참고 (더티체킹,벌크 추천)**: `em.merge()` 로 준영속을 영속성 만들면 데이터 수정시 "더티체킹(flush시점)" 발생  
  하지만 `em.merge()` 보단 `em.find()` 로 영속성 가져와 데이터 수정을 더 추천

  - **update 예시 -> em.find() 영속성 활용 방식**

    <details><summary><b>예시 코드</b></summary>
    <div markdown="1">
    ```java
    // entity part
    // 준속성 엔티티 -> 영속성 엔티티 에 사용
    public void change(String name, int price, int stockQuantity) {
      this.name = name;
      this.price = price;
      this.stockQuantity = stockQuantity;
    }
    // service part
    @Transactional
    public void updateItem(Long id, UpdateItemDto itemDto){
      Item item = itemRepository.findOne(id); // 영속성 엔티티
      item.change(itemDto.getName(), itemDto.getPrice(), itemDto.getStockQuantity()); // 준속성 엔티티(itemDto) -> 영속성 엔티티
    }
    // controller part
    public String updateItem(@PathVariable Long itemId, @ModelAttribute("form") BookForm form){
      UpdateItemDto itemDto = new UpdateItemDto(form.getName(), form.getPrice(), form.getStockQuantity());
      itemService.updateItem(itemId, itemDto);
      return "redirect:/items"; // 위에서 "상품 목록" 매핑한 부분으로 이동
    }
    ```
    </div>
    </details>

  - 단, 변화가 넘 많으면 **"벌크연산 추천"** -> 간단함. (우리가 잘 쓰는 쿼리문일 뿐)  
    -> 예: `UPDATE Task t SET t.status = :status WHERE t.dueDate < :currentDate`

  - **벌크 연산 -> 여러 데이터 한번에 "수정, 삭제" 연산**

    * JPA 는 보통 실시간 연산에 치우쳐저 있는데, 대표적인 예가 "더티 체킹"
      * 100개 데이터가 변경되었으면 100개의 Update 쿼리가 나가게 되는 문제
      * **이런건 "벌크 연산" 으로 해결하자**
    * **올바른 사용법**
      * **벌크 연산을 먼저 실행**
      * **벌크 연산 수행 후 영속성 컨텍스트 초기화 -> em.clear()**
        * 더 이상 더티 체킹 일어나지 않게 하기 위함!! (중요)

<br><br>

### JPQL + 페이징(limit,offset)

#### JPQL (distinct, 연관관계, sql vs jpql)

<details><summary><b>반환 방식(TypeQuery, Query) -> `query.getResultList()`</b></summary>
<div markdown="1">
* TypeQuery: 반환 타입이 명확할 때 사용<br>
  * **일반적(자주 사용!)** : `List<Member> findMembers = em.createQuery("select m from Member m", Member.class)`<br>
  * **Dto** : `List<MemberDTO> result = em.createQuery("select new jpql.MemberDTO(m.username, m.age) from Member m", MemberDTO.class)`<br>
    * **QueryDSL 사용 시 패키지 명(jpql.)까지 없앨 수 있음 (자주 사용!)**<br>
* Query: 반환 타입이 명확하지 않을 때 사용<br>
  * `List<Query> findMembers = em.createQuery("select m from Member m")`<br>
  * 이땐 굳이 반환타입 `Member.class`를 명시할 필요가 없음<br>
* **query.getResultList(): 결과가 하나 이상일 때, 리스트 반환** **-> 자주사용!! (1개 여도!)**<br>
* query.getSingleResult(): 결과가 정확히 하나, 단일 객체 반환
</div>
</details>

<details><summary><b>경로 표현식 3가지(.을 찍어 "탐색") -> 상태, 연관 필드(단일 연관, 컬렉션 연관)  </b></summary>
<div markdown="1">
* **상태 필드(탐색X)**: 단순히 값을 저장하기 위한 필드 (ex: `m.username`)<br>
* **단일 값 연관 필드(탐색O)**: @ManyTo**One**, @OneTo**One**, 대상이 엔티티(ex: `m.team`)<br>
  * **"즉시 로딩" 이 기본값**이므로 꼭 "지연 로딩" + "join fetch" 사용 권장 <br>
  * 탐색가능 예시: `select m.team.id from Member m` <br>
* **컬렉션 값 연관 필드(탐색X)**: @OneTo**Many**, @ManyTo**Many**, 대상이 컬렉션(ex: `m.orders`) <br>
  * 컬렉션은 "Object"객체 여러개로 생각하면 되며, **"지연 로딩" 이 기본값**<br>
* **주의: 일반 join 필요 시 "묵시적 내부 조인" 이 아닌 "명시적 조인" 을 사용할 것**<br>
  * `select m.team from Member m` 이나 `select m.orders from Member m` 처럼 사용 가능한데, 이 경우 **묵시적 내부 조인이 발생(자동 inner join)** **-> 매우 비권장!!**<br>
  * 쿼리 튜닝하기에 매우 힘듦
</div>
</details>

<details><summary><b>엔티티 직접 사용 -> count(m) == count(m.id) 로 자동 SQL 변형!</b></summary>
<div markdown="1">
* **JPQL에서 엔티티를 직접 사용하면 SQL에서 해당 엔티티의 "기본 키" 값을 사용**<br>
  * (JPQL) : select count**(m)** from Member m <br>
  * (SQL) : select count**(m.id)** as cnt from Member m <br>
* **참고: JPQL은 SQL의 m.* 조회를 m 으로 동일하게 가능.** <br>
  * 예: JPQL의 `select m from Member m` 와 SQL의 `select m.* from Member m` 동일!!
</div>
</details>

<details><summary><b>연관관계(1:1,1:N,N:1,N:N)의 DB관점(SQL) vs JPA관점(JPQL)</b></summary>
<div markdown="1"><br>
참고로 **SQL은 영속성 컨텍스트 자체를 사용X**, 따라서 SQL의 join은 "객체"가 아닌 "row"만 반환할 뿐!
- **DB관점**은 1:N, N:1 이든 1쪽은 데이터 중복 발생. 1쪽이 N만큼 생성되는건 조인이라면 자명. 따라서 **결과(row)는 같고**, **관점을 양방향**으로 봤을 뿐이다. 
- **JPA 컬렉션(1:N) 관점**은 객체가 생성이 되는데 해당 객체 주소가 동일 해버리는 문제 발생할 수 있다.**(1:1, N:1은 발생X)**
  - **그럼 DB관점은** N:N이 아니면 항상 튜플은 중복이 아니니까 걱정안해도 되자나? 왜 distinct를 쓸 때가 있었을까?
  - 만약 `order:orderItem=1:N` 에서 **order쪽 컬럼만 사용**하면?? 중복 데이터니까 distinct나 group by로 중복제거 하여 해결하는 것!!
- **직관적 이해를 위해 각각의 관점 출력 예시를 보여주겠다.**
  - **DB관점 조인 (Order:OrderItem=1:N 관계, OrderItem:Order=N:1 관계)**<br>
  ![image](https://github.com/user-attachments/assets/c087ebc7-3e42-43c7-aa4b-c0507eb76f05) 
  - **JPA 컬렉션 관점 조인 (Order:OrderItem=1:N 관계)**  → jpql의 distinct 미사용.<br>
  객체다보니 같은 ORDER_ID인 COUNT 2개가 한번에 묶여있는 형태를 가지다보니 조인의 결과가 완전 동일한 객체 2개가 생성이 되는것.<br>
  ![image](https://github.com/user-attachments/assets/3cfd43eb-cd18-4960-838b-f7e4545a0cf4) 
</div>
</details>

<details><summary><b>DB관점(SQL) JOIN문과 JPA관점(JPQL) JOIN, FETCH JOIN문</b></summary>
<div markdown="1"><br>
참고로 **“조인문”**은 두 테이블 필드 정보가 필요하거나, 한 개 테이블 필드 정보만 출력하더라도 외래키로 이어진 두 관계를 활용해서 연관있는 정보를 조회해야 할 때 사용하는거.
- **DB관점 JOIN**은 위에서 설명했듯이 **영속성 컨텍스트 자체를 사용X**, 따라서 SQL의 join은 “객체(엔티티)”가 아닌 **“row”만 반환할 뿐!**
- **JPA관점 JOIN**은 **FETCH JOIN과 차이**가 있다. **JOIN은** **"지연로딩+연관된 엔티티 자동 포함X"** 라는 점! 
  - 지연로딩으로 N+1 문제 해결하고자 평범히 `select o, oi from o랑 oi 조인;` 한다면 해결은 가능!!
  - 다만, order랑 orderItem 각각을 개별적으로 반환! -> order이 `List<OrderItem>` 를 가진다고 해도 이 관계에 자동 주입이 안된다는 말!! **(fetch join은 자동 포함!!)**
  - **JPA관점 FETCH JOIN**은 애초에 JPA만 존재하는 문법이다. 해당 문법은 **"지연로딩->즉시로딩(N+1방지), 연관된 엔티티 자동 포함"**(`List<OrderItem>`) 한다.
</div>
</details>

<details><summary><b>distinct의 DB관점(SQL) vs JPA관점(JPQL)</b></summary>
<div markdown="1"><br>
**SQL에서 distinct**는 전체 row가 모두 똑같아야 중복이 제거<br>
**JPA에서 distinct**는 위 기능 + pk(식별자 , id값)가 같다면 중복을 제거 (영속성 컨텍스트 상 엔티티 같은 주소들 제거 해버리는 거지)<br>
중복은 앞서 정리한 JPA 출력사진 참고
</div>
</details>

<details><summary><b>fetch join(즉시로딩) -> `XToOne`는 바로 페치조인O, `XToMany`는 페치조인O+distinct / 만약 페이징 필요하면? 페치조인X + BatchSize (일반 Select!, 글로벌로 100정도 깔아두고 개발ㄲㄲ)</b></summary>
<div markdown="1">
* 주의: **페치 조인**은 **객체 그래프 유지**할 때 사용 시 효과적인 반면, 여러 테이블을 조인해서 엔티티가 가진 모양이 아닌 전혀 다른 결과를 내야 하면, 페치 조인 보다는 **일반 조인**을 사용해서 필요한 데이터들만 조회해서 **DTO**로 반환하는 것이 효과적.<br>
* `XToOne` 문제없음, `XToMany` 는 Distinct 함께 사용(컬렉션이라 중복 문제!! -> 중복은 앞서 정리한 JPA 출력사진 참고)<br>
  * 근데, **하이버네이트6 부터 Distinct 자동 적용**해주는듯. 아직 테스트 못해봤다.<br>
* **페치 조인 대상에는 별칭X** - 유일하게 연속으로 join 가져오는 경우에만 사용<br>
* **둘 이상의 컬렉션은 페치 조인 사용하지 말 것**<br>
* 페치조인X + BatchSize 는 페이징 정리 부분 참고
</div>
</details>


<details><summary><b>Named 쿼리 -> Spring Data JPA의 interface에서 간단히 쿼리 커스텀!!</b></summary>
<div markdown="1">
* **실무에서는 Spring Data JPA 를 사용하는데 @Query("select...") 문법이 바로 "Named 쿼리"** **-> 원하는 쿼리문 바로 작성가능한 편리성**<br>
  * JPQL 예: `@Query("SELECT m FROM Member m")`<br>
  * Natvie SQL 예: `@Query(value="SELECT m FROM Member m", nativeQuery=true)`<br>
* **참고: 물론 일반 JPA로 구현로직 추가해서 인터페이스 상속 추가해서 사용도 좋다~** <br>
  * 본인은 이 방식으로 JPA+Spring Data JPA 함께 쓰는 편
</div>
</details>
**JAP 사용 때 동적 쿼리**는 **Querydsl** 을 권장

<br>

#### 페이징 (+최적화 예시)

**페이징**

* x번째 row를 구하라 하면 **페이징(sql: limit, offset)** 으로 간단히 구할 수 있다.

  * 다만, offset은 설정한 범위까지 데이터 전부 scan한다는 단점이 있다.
  * 이를 극복하기 위해 보통 인덱스(소트생략)를 활용해서 부분범위 처리를 가능하게 한다.
  * 따라서 페이징은 **"부분범위 처리에서 좋은 효과"**를 가진다.

* **JPA에서** limit, offset을 대신할 간단한 **문법**을 제공한다.

  * `setFirstResult(int startPosition)` : 조회 시작 위치 (0부터 시작)
  * `setMaxResults(int maxResult)` : 조회할 데이터 수

* **만약 조인문에서 페이징을 원할 때는??** -> 컬렉션 조인을 제외하고 문제 없다.<br>참고로 **Order:OrderItem=1:N** 테이블 사용 가정

  * **"관리자가 최신순 주문상품 내역"** 보려고 한다. 이때는 주문상품 기준 최신순으로 나타내도 충분하다. N:1로 페이징하면 될거니까 어려움이 없다.

  * **"관리자가 최신순 주문 내역"**을 보려고 한다. 이때는 주문이 중복없이 나열되고, 해당 주문에 해당되는 주문상품들이 함께 포함되어 나열되어야 한다. 1:N(컬렉션)로 페이징해야 하니까 어려움이 생긴다.<br>왜? 데이터 뻥튀기 때문. Order가 중복 생성되므로 동일한 OrderId 때문에 순서를 정할 수가 없으니까!

    * ToOne 관계는 fetch join으로 쿼리 수를 줄이고, 지금같은 컬렉션(ToMany)는 **default_batch_fetch_size로 최적화** 한다. JPA가 IN절로 size만큼 자동 처리하여 해결!!

    <details><summary><b>예시 - Batch Size (In절)</b></summary>
    <div markdown="1"><br>
    **Order, OrderItem, Item** 연관 관계를 가질 때 총 **3개 쿼리문**으로 IN절 활용해서 중복없고 1+N문제도 없이 해결이 가능한 예시.<br>
    <br>
    아래 잘 보면, Order를 우선 페이징 적용하고 나서 이와 연관된 엔티티들을 따로 쿼리문 더 날려서(In절 포함) 데이터를 가져와 해결!
    <div markdown="1">
    ```java
    select order0_.order_id       as order_id1_6_0_,
           member1_.member_id     as member_i1_4_1_,
           delivery2_.delivery_id as delivery1_2_2_,
           order0_.delivery_id    as delivery4_6_0_,
           order0_.member_id      as member_i5_6_0_,
           order0_.order_date     as order_da2_6_0_,
           order0_.status         as status3_6_0_,
           member1_.city          as city2_4_1_,
           member1_.street        as street3_4_1_,
           member1_.zipcode       as zipcode4_4_1_,
           member1_.name          as name5_4_1_,
           delivery2_.city        as city2_2_2_,
           delivery2_.street      as street3_2_2_,
           delivery2_.zipcode     as zipcode4_2_2_,
           delivery2_.status      as status5_2_2_
    from orders order0_
             inner join
         member member1_ on order0_.member_id = member1_.member_id
             inner join
         --     페이징이 적용된다.
             delivery delivery2_ on order0_.delivery_id = delivery2_.delivery_id limit ?
    offset ?
    //
    select orderitems0_.order_id      as order_id5_5_1_,
           orderitems0_.order_item_id as order_it1_5_1_,
           orderitems0_.order_item_id as order_it1_5_0_,
           orderitems0_.count         as count2_5_0_,
           orderitems0_.item_id       as item_id4_5_0_,
           orderitems0_.order_id      as order_id5_5_0_,
           orderitems0_.order_price   as order_pr3_5_0_
    from order_item orderitems0_
    -- in 절로 땡겨온다.
    where orderitems0_.order_id in (
                                    ?, ?
        )
    //
    select item0_.item_id        as item_id2_3_0_,
           item0_.name           as name3_3_0_,
           item0_.price          as price4_3_0_,
           item0_.stock_quantity as stock_qu5_3_0_,
           item0_.artist         as artist6_3_0_,
           item0_.etc            as etc7_3_0_,
           item0_.author         as author8_3_0_,
           item0_.isbn           as isbn9_3_0_,
           item0_.actor          as actor10_3_0_,
           item0_.director       as directo11_3_0_,
           item0_.dtype          as dtype1_3_0_
    from item item0_
    -- in 절로 땡겨온다.
    where item0_.item_id in (
                             ?, ?
        )
    ```
    </div>
    </div>
    </details>


<br>

**페이징 + 캐시 예시 2개**

- 굉장히 좋은 참고 문서: [페이지네이션 최적화 - Offset 문제 가져간 이유](https://taegyunwoo.github.io/tech/Tech_DBPagination) 참고!

- 여기서 기억에 남은 말: **가장 먼저 서브쿼리를 통해서 커버링 인덱스로 페이징을 진행합니다. 그리고 그 결과와 기존 테이블을 조인시켜서 ‘인덱스에 포함되지 않은 칼럼’을 가져옵니다.**

<details><summary><b>(1)회원 랭킹 보여주는 페이지(정렬필수) -> 30분 마다 갱신</b></summary>
<div markdown="1"><br>
**레포지토리**<br>
- **서브쿼리에 인덱스로 member 테이블을 빠르게 조회 (정렬 된)**<br>
  * 서브쿼리, limit, offset 사용 위해 Native Query 사용<br>
- 이후 기존 테이블과 조인해서 결과 반환<br>
<div markdown="1">
```java
//Limit, Offset -> SQL
List<Object[]> objects = em.createNativeQuery(
"select m.member_id, m.nickname, e.level " + 
"from (select * from member order by member_id desc limit " + offset + "," + limit + ") m " +
"inner join character c on m.character_id=c.character_id " +
"inner join exp e on c.exp_id=e.exp_id;")
.getResultList();
```
</div>
**서비스**<br>
- 30분 마다 갱신이라 **@CacheEvict, @Cacheable, @Scheduled로 충분**<br>
- 페이지별로(pageId) 묶어서 캐시 관리가 좋아서 이렇게 진행. (게시물마다 하는건 너무 많은 캐시 메모리 사용?) + 캐시사이즈 설정<br>
<div markdown="1">
```java
/** 회원 최신순 조회 + 캐시 */
@Cacheable(value = "members", key = "#pageId") // [캐시 없으면 저장] 조회
public List<FindMemberResponseDto> findAllWithPage(int pageId) {
    return memberRepository.findAllWithPage(pageId);
}
// 캐시에 저장된 값 제거 -> 30분 마다 실행하겠다.
// 초(0-59) 분(0-59) 시간(0-23) 일(1-31) 월(1-12) 요일(0-6) (0: 일, 1: 월, 2:화, 3:수, 4:목, 5:금, 6:토)
@Scheduled(cron = "00 30 * * * *") // 30분 00초 마다 수행
@CacheEvict(value = "members", allEntries = true)
public void initCacheMembers() {
}
```
</div>
</div>
</details>
<details><summary><b>(2)게시물 10개씩 출력하는 페이지(홈페이지) -> 수정, 삭제, 추가에 갱신</b></summary>
<div markdown="1"><br>
**레포지토리**<br>
- 서브쿼리 사용할 필요 없어서 바로 JPQL의 페이징 기법 활용 -> 이 또한, 인덱스 사용<br>
<div markdown="1">
```java
//setFirstResult(), setMaxResults() -> JPQL
public List<Item> findAllWithPage(int pageId) {
  return em.createQuery("select i from Item i" +
                        " order by i.id desc", Item.class)
      .setFirstResult((pageId-1)*10)
      .setMaxResults(10) // 개수임!!
      .getResultList();
}
```
</div>
**서비스 -> 여기선 이게 중요!!**<br>
- 페이지별로 url(?page=1) 접근하면 해당 페이지별로 데이터를 가져올거고 이 데이터를 **@CachePut로 기록하고, @Cacheable로 조회, 삭제는 @CacheEvict**<br>
  - 만약 게시물 삭제되면 애초에 게시물No(순번)이 갱신되어야해서 그냥 @CacheEvict로 삭제후 다시 기록하면 됨.<br>
  - 게시물 수정이면 @CachePut으로 해당 PageId 부분만 갱신하면 됨. 게시물 개수는 그대로니까!<br>
- 페이지별로(pageId) 묶어서 캐시 관리가 좋아서 이렇게 진행. (게시물마다 하는건 너무 많은 캐시 메모리 사용?) + 캐시사이즈 설정<br>
<div markdown="1">
```java
// page 단위로(key) 캐시 기록 -> 참고 : value 로 꼭 캐시 영역을 지정해줘야 함
@Cacheable(value = "posts", key = "#pageId") // [캐시 없으면 저장] 조회
public List<Item> findAllWithPage(int pageId) {
    return itemRepository.findAllWithPage(pageId);
}
// page 단위로(key) 캐시 기록 -> 참고 : value 로 꼭 캐시 영역을 지정해줘야 함
@CachePut(value = "posts", key = "#pageId") // [캐시에 데이터 있어도] 저장
public List<Item> updateAllWithPage(int pageId) {
    // pageId 로 간단히 캐시 업데이트용 함수
    return itemRepository.findAllWithPage(pageId); // 반환값을 캐시에 기록하기 때문에 만든 함수
}
// 캐시에 저장된 값 제거
@CacheEvict(value="posts", allEntries = true)
public void initCachePosts(){}
// totalCount 이름으로 캐시 메모리에 기록 [캐시 없으면 저장] 조회
@Cacheable(value = "totalCount")
public Long findTotalCount() { return itemRepository.findTotalCount(); }
// [캐시에 데이터 있어도] 저장
@CachePut(value = "totalCount")
public Long updateTotalCount() { return itemRepository.findTotalCount(); }
```
</div>
</div>
</details>
<br><br>

### 엔티티 조회 권장 순서 - 필수!

**엔티티 조회 권장 순서**

1. **엔티티 조회 방식**으로 우선접근 : **지연로딩+DTO변환 방식 추천**
   
   1. **일반 엔티티(ToOne)** 최적화 : 페치조인으로 쿼리 수를 최적화
   2. **컬렉션(ToMany)** 최적화
      1. 페이징 필요O : `hibernate.default_batch_fetch_size` , `@BatchSize` 로 최적화
      2. 페이징 필요X : 페치조인 사용
   
2. 엔티티 조회 방식으로 해결이 안되면 DTO 조회 방식 사용

   <details><summary><b>엔티티 조회(DTO변환 방식) vs DTO직접 조회</b></summary>
   <div markdown="1">
   - 요즘 스펙상 **재사용성이 좋은 엔티티 조회(DTO변환)**가 낫다고 생각
   - 그래도 너무 쿼리 성능이 좋지 않다면 DTO직접 조회로 넘어가자. **(확실히 select절의 필드 수가 줄어듦, 물론 엔티티가 아니니까 일반 join문을 사용해야 함)**
   </div>
   </details>

3. DTO 조회 방식으로 해결이 안되면 NativeSQL or 스프링 JdbcTemplate or QueryDSL 등

<br>

**엔티티 조회 권장 순서 자세히**

1. **엔티티 조회 -> JPA가 최적화를 많이 제공**

   - 엔티티 조회 후 DTO로 변환해서 반환하는게 훨씬 안전. (**엔티티 외부노출 방지**) -> **DTO 변환 방식**

     - **요청, 응답 둘다 DTO**사용해주자. → +**Valid** 적용하기도 좋다. 
     - 참고: “DTO 직접 조회”는 select절에 바로 DTO조회하는거 의미.

     <details><summary><b>페치조인으로 쿼리 수 최적화. -> N+1방지</b></summary>
     <div markdown="1"><br>
     연관된 엔티티를 함께 조회하는 방식으로 지연로딩을 즉시로딩! **(N+1방지)**
     - **LAZY 강제 초기화**까지 해줘야 메모리에 null로 저장되는걸 방지
       - 1:N, N:1, 1:1 이든 조인을 할 때 `select new com.example.dto.OrderItemDto(oi.id, i.name, oi.orderPrice, oi.count)` 처럼 **“DTO직접조회”**가 아니라 `select oi from OrderItem oi` 처럼 **“엔티티 조회”**하게 되면 나중에 응답 DTO로 변환할 때 꼭 LAZY 강제 초기화를 해줘야 null 방지한다는 의미
       - 내부적으로 연관 엔티티를(조인한) 필드로 가지고 있을텐데 그 부분을 활성화 해야 **“영속성 등록”으로 메모리에 가지니까!!**
     - **distinct유무는?** 1:1, N:1 은 distinct가 필요없지만 1:N 은 필요!!
       - 설명 생략 (이미 정리했으니까)
     - **작성 코드 형태 예시**
       - `("select m from Member m " + "join fetch m.character c " + "where m.id = :memberId", Member.class)`
         - 이 정보만 봐도 얼마나 **객체지향적**인지 알 수 있다. **(1:1조인중)**
           - **Member 내에 필드로 Character를 가지고 있는**데, 그 흐름 그대로 jpql을 작성가능 하다.
           - **반환된 해당 Member로 Character 정보를 사용가능!** `member.getCharacter().getId() 등..`
         - 실제 SQL이라면 **select c from character c, member m where m.id = :memberId** 이런 느낌이었을거다. -> from절에서 두개 자동 inner조인 하고 where절에서 필터링 되는..
           - 물론 나라면 **select c from character c, (select \* from member where m.id = :memberId)** 이런 형태로 튜닝할거 같다.
             - member가 굉장히 데이터 많았다면 비효율이여서 수정했다. 물론, 조인 자체는 문제없다! memberId 덕분에 튜닝 포인트가 생긴것일 뿐이지! <br>→ memberId 없었으면 애초에 이렇게 전부 조인하는게 맞으니까!
           - **실제 SQL**뭐가나가는지 로그 찍어보니 예상대로다.<br>![image](https://github.com/user-attachments/assets/54fc3a64-7022-4770-ad76-925a0f8f6173) 
       - `"select distinct l from Lists l" + " join fetch l.tasks t" + " join fetch t.taskStatus ts" + " where l.member.id = :memberId"`
         - 앞서 언급한것처럼 **1:N은 distinct 권장! (1:N조인중)**
         - **다만, 엔티티 조회란걸 생각!**
       - `"select t from Task t" + " join fetch t.lists l" + " where t.id = :taskId and" + " l.member.id = :memberId"`
         - 앞서 언급한것처럼 N:1은 중복 걱정 노! **(N:1조인중)**
         - **다만, 엔티티 조회란걸 생각!**
       - `Character character = characterService.findCharacterWithMember(memberId);`<br>`List<Follow> follows = followService.findAllWithFollowing(character.getId());`
         - API로직인데 Follow 조회하려고 캐릭터를 먼저 조회해서 총 2번의 쿼리가 나간다. 당연히 1개로 합칠 수 있다. (튜닝POINT)
           - 기존 jpql: `"select f from Follow f" + " where f.character.id = :characterId"`
           - `:characterId` 부분만 서브쿼리로 삽입하면 된다. **=(select id from character where memberId = :memberId)**
         - 근데, API로직에서 character쪽을 또 활용한다면?? 이런걸 생각하면 상황에 따라 맞게 개발해야한다.
           - 본인 생각엔 굳이 서브쿼리 넣으면서까지 하지말고, **“객체지향적”** 답게 **기존 API로직처럼 사용을 우선** 하는게 좋다고 생각! **쿼리 2개→1개 줄어봤자 너무 미미**한 성능 차이니까
     </div>
     </details>

     <details><summary><b>컬렉션(1:N) 페이징은 페치조인이 불가능. 따라서 Batch활용 ㄱ (=JPA에서 Batch는 In절 만듦) 
     → In절 만드는 원리는 아래에 DTO 컬렉션(1:N) 조회 코드방식 임!</b></summary>
     <div markdown="1"><br>
     [Batch 방법 참고 - In절 쿼리 출력문 참고](https://velog.io/@guns95/컬렉션-FetchJoin의-문제점과-한계-돌파BetchSize)
     - **컬렉션 페이징은 페치조인이 불가능한 이유는 1:N**은 1쪽이 N개 복사된다고 봐야하니까 **순서를 정확히 알 수가 없는 문제 (물론 조인은 어느 관계든 복제 ㅇㅇ)**
       - ex: orderId=1 과 연관있는 orderItemId가 3개라면 orderId=1이 3개가 복제된다. 그럼 orderId 기준으론 순서를 정할 수 없으니까!
       - **물론, orderItemId 기준**으론 순서 정할 수 있음. **양방향 관점(원래1:N 관점에서 N:1로)**에선 순서가 이렇게 정해지니까 페이징 가능하단겨~
       - **다만, 여기선 비지니스 상 orderId기준을 원하고 있는거임.**
     - 따라서 **ToOne에 페치조인한 쿼리에만 페이징 적용ㄲ**. 나머진 Batch를 통해 **JPA가 IN절에서 연관엔티티(지연로딩)를 Batch Size만큼 1번의 추가 쿼리에 가져와서 해결하자**
       - in절은 or절과 거의 동일하게 동작하던걸로 기억한다. 특히 SQL 튜닝관점에서는 OR Expansion, IN-List Iterator 로 union all 형태로 동작 시킨다.(아마도? 까먹음ㅋ) 수직적 탐색이 그만큼 발생할거고.
       - **어쨋든 in절에 연관엔티티(ex:orderItemId)들을 나열해서 1번만에 추가 쿼리(1+N) 없이 가져오는 좋은 해결방안이다!!**
     - 물론, 1:N 페치조인시 **중복제거(그룹핑, distinct)**하면 페이징이 가능할거다. 다만, 너무 비효율! **FULL SCAN** 해야하니까!
       - 단순 정렬문제면 서브쿼리로 먼저구하고 + 인덱스 활용하면 효과적이지만, **중복제거는 인덱스를 쓴다해도 FULL SCAN**일거다. (물론, UIQUE INDEX 사용 시 빠르다지만 전체 컬럼에 쓸것도 아니잖아?)
     - **이것도 별로일때 DTO직접 조회로 해결도 해볼 수 있는것!**
     </div>
     </details>

2. **DTO직접 조회 -> 원하는 컬럼 지정하는 일반 SQL과 유사**

   - **fetch join은 절대 불가!**(DTO는 영속성관리 엔티티X) → **join 사용!**

   <details><summary><b>DTO직접 조회 - select new com.example.dto.OrderDto(컬럼)</b></summary>
   <div markdown="1"><br>
   이런다고 엔티티 조회에서의 응답DTO는 안 만들어도 되나? 라는 바보같은 생각은X!!<br>
   그 DTO가 여기서 select절에 쓰이고 있는거다.
   </div>
   </details>

   <details><summary><b>DTO 컬렉션(1:N) 조회는 IN절을 직접 활용! → (1+N해결!)<br>JPA의 Batch처럼 유사한 동작원리. (참고: JPA Batch Size를 설정하면 Lazy 로딩된 연관 엔티티를 해당 Size 만큼 IN절 활용해 가져옴)</b></summary>
   <div markdown="1"><br>
   아래 결과는 쿼리2개로 개선 한방 쿼리는?? → 플랫 조회 방식으로!
   <div markdown="1">
   ```java
   // dto보면 컬럼명 oi.order.id 기준이 되는 중. Order:OrderItem:Item=1:N:1 관계인 상태.
   // orderId는 매개변수로 입력 받은 값이다. orderId가 N개이면 1+N이 발생할거다.
   private List<OrderItemDto> fun(Long orderId) {...}
   "select new com.example.dto.OrderItemDto(oi.order.id, i.name, oi.orderPrice, oi.count) " +
       "from OrderItem oi " +
       "join oi.item i " +
       "where oi.order.id = :orderId"
   //
   // 1+N 해결법은?? in활용 -> 쿼리 2개 발생 (findOrders랑 아래 jpql)
   // orderIds는 findOrders()로 구한 값
   private List<OrderItemDto> fun() {...}
   List<OrderItemDto> result = findOrders();
   List<Long> orderIds = result.stream().map...
   "select new com.example.dto.OrderItemDto(oi.order.id, i.name, oi.orderPrice, oi.count) " +
       "from OrderItem oi " +
       "join oi.item i " +
       "where oi.order.id " +
       "in :orderIds"
   result.forEach(o -> o.setOrderItems(...)); // orderItem 넣기
   return result;
   ```
   </div>
   </div>
   </details>

   <details><summary><b>플랫 조회는 말 그대로 JOIN 결과를 전부 조회 후 앱단에서 모양을 커스텀한다. → 한방쿼리! (1개쿼리)</b></summary>
   <div markdown="1">
   - 우리가 잘 아는 일반 SQL의 join 결과는 아래처럼 중복이 있다. (참고로 1:N 조인 모습 사진!) → 보통은 그룹핑해서 해결하면 된다. 여기서도 마찬가지로 **Order쪽 그룹핑**으로 해결
   - Order:OrderItem=1:N 이고 Order(1)쪽 중복 모습(order_id)<br>
     ![image](https://github.com/user-attachments/assets/180282b1-203a-4198-ad1e-49baf25ba5ff)<br>
   - (1)위 결과처럼 가져오게끔 일반적인 join문 쓰면 됨 ㅇㅇ. -> **"쿼리1개"**
   - (2)위 그림에서 원하는 필드들 가져다 사용하자
   - (3)핵심은 여기서 orderId 필드 사용해서 기준으로 쓸거면 **그룹핑을(orderId 기준)** 먹여서 **"중복해결"**
   - 개인적으로 후처리가 넘 많고, **그룹핑 먹이는건 뭐.. 성능이 좋진 않겠지.(FULL SCAN)**
       - 물론, 엔티티 조회가 아니라 DTO직접 조회 방식 사용할때의 최적화 방안이니까!! 필요할때 꼭 사용!
   </div>
   </details>

<br>

<details><summary><b>참고용) 엔티티 조회 에러 해결</b></summary>
<div markdown="1">
* **엔티티를 외부에 노출하면서 발생하는 문제들이라서 사실상 이부분을 알 필요는 없다.**
* @JsonIgnore : **양방향 무한 반복의 문제**를 해결
  * 하지만 지양하고 **DTO** 방식으로 해결을 지향
* Hibernate5Module을 @Bean 등록 까지 해주면 **Lazy 문제**도 해결 
  * 하지만 이 또한 지양하고 **LAZY 강제 초기화**로 다 해결
</div>
</details>
<br>

<br>

## 필독! 스프링 DB 관련 추가 지식 

앱에서 DB 접근시 **커넥션**뿐만 아니라 DB 내부에서는 **“세션”** 생성 -> **세션이 SQL을 실행!**

- 커넥션은 DB와 클라이언트 간의 TCP 소켓을 통한 **물리적인 연결(채널)**

- 세션은 DB와 클라이언트 간의 **논리적인 연결**

<img src="https://github.com/user-attachments/assets/ffb210a9-867e-4e4e-9090-c286f642d468" alt="image" style="zoom:67%;" /> 

<br><br>

### DataSource

DataSource 가 `(1)my.datasource.하위` 와 `(2)spring: datasource: url, username, password...` 등 여러곳에서 언급되다 보니 헷갈릴 수 있겠다고 생각한다.

`(1)my.datasource.하위` 는 **외부설정이다!** 리팩토링에 정리한 **메시지와 외부설정 파트를 참고**하자

`(2)spring: datasource: url, username, password...` 은 **DB관련 설정**으로 보자!

- **커넥션 풀**은 **실무**에서 무조건 사용하는데, **성능테스트**를 통해 크기를 결정하는 편이다.<br>또한, **hikariCP(=커넥션풀 오픈소스)** 를 **기본으로 스프링 부트가 사용중**

- **hikariCP** 는 DataSource 인터페이스의 **구현체**이고, 스프링부트는 **DataSource**를 자동으로 **application.yml** 같은 설정파일의 DB정보(URL,ID,PW 등)을 읽기 때문에 해당 설정파일을 잘 작성하면 된다.<br>물론, 직접 **Datasource**를 새로 만들어 스프링 빈에 직접 등록해서 사용해도 된다!

- **H2는 Java로 만들어진 DB이다. 따라서 메모리모드(=임베디드 모드)를 지원한다.**
  - 기존 방식: 임베디드모드(메모리모드)를 사용하기 위해 Datasource를 새로 만들어 스프링 빈에 등록한다. -> JVM(메모리)에 DB를 만들어 사용
    - `src/test/resources/schema.sql` 에 원하는 create table 을 작성 (메모리DB는 OFF시 데이터 사라져서)
  - **스프링부트 방식**: 스프링부트는 테스트의 설정파일(application.yml)에 datasource관련내용이 없다면(주석)?? 바로 임베디드 모드(메모리모드)를 자동 생성! (매우간편)
    - create table 도 코드 한줄로 매우쉽게 설정 가능

<br><br>

### 스프링 JdbcTemplate, MyBatis, JPA, 스프링 데이터 JPA, Querydsl

**[스프링 DB 2편 - 데이터 접근 활용 기술](https://github.com/BH946/spring-first-roadmap/tree/main/spring_study_7/itemservice-db)에서 JdbcTemplate, JPA 참고**

참고: Mybatis, Jpa는 귀찮은 setId가 필요 없다. (jdbc의 PreparedStatement구문엔 필요)

**(주관적)데이터접근 무엇을 추천하는가??**

- **(기본적인 CRUD 가정) JPA, 스프링 데이터 JPA, Querydsl** 을 기본으로 사용 -> **[적용 플젝(레포): v1~v3](https://github.com/BH946/LePl_Spring/tree/kbh/lepl/src/main/java/com/lepl/Repository/member), [적용 플젝(서비스): v1~v2](https://github.com/BH946/LePl_Spring/tree/kbh/lepl/src/main/java/com/lepl/Service/member)**

  - 어댑터 추가ver, 단순ver 은 상황에 맞게 선택!
  - 상관없다면?? **단순ver을 사용**하자 -> **스프링데이터JPA + QueryDSL** 를 기본으로 잡자!<br>스프링데이터JPA의 **@Query로 충분히 JPQL작성**도 가능하기 때문!

- **복잡한 쿼리나 SQL사용**의 경우 JdbcTemplate이나 **MyBatis를 함께 사용 추천!**

  - JPA랑 JDBC는 트랜잭션 매니저가 다를텐데 어떡하나??
  - `JpaTransactionManager` 가 대부분 기능들을 제공해서 괜찮다고 한다.
  - 단, JPA 플러시 타이밍에 주의

- **특히, 동적쿼리**는 **MyBatis나 QueryDSL 추천!**

  - findAll() 메서드 쿼리에 "검색 조건이 없을 때, 상품명으로 검색 때, 최대가격 검색 때, 상품명과 최대가격 둘다 검색 때" 총 4가지를 전부 동적으로 SQL 생성이 필요하다면 코드가 복잡..!

  - MyBatis나 QueryDSL로 해결 추천<br>why?? 기존엔 여러 메소드(DAO)를 더 구현하여 사용해야 하기 때문 (아래 예시코드)

  - <details><summary><b>findAll() 동적쿼리 예전 방식 예시</b></summary>
    <div markdown="1"><br>
    ```java
    //QueryDSL 사용하지 않았을 때 동적 쿼리 구현 방식
    public List<Item> findAll(ItemSearchCond cond) {
        String itemName = cond.getItemName();
        Integer maxPrice = cond.getMaxPrice();
        if (StringUtils.hasText(itemName) && maxPrice != null) {
            return repository.findItems("%" + itemName + "%", maxPrice);
        } else if (StringUtils.hasText(itemName)) {
            return repository.findByItemNameLike("%" + itemName + "%");
        } else if (maxPrice != null) {
            return repository.findByPriceLessThanEqual(maxPrice);
        } else {
            return repository.findAll();
        }
    }
    ```
    </div>
    </details>
  
  - <details><summary><b>findAll() 동적쿼리 현재 방식 예시 - MyBatis</b></summary>
    <div markdown="1"><br>
    ```xml
    <!-- (동적쿼리)검색+페이징 -->
    <select id="findAllWithPage" resultMap="item">
    	SELECT *
    	FROM item
    	<where>
    		<if test="searchKeyword != null and searchKeyword != ''">
    			<choose>
    				<when test="searchCondition == 0">
    					and id like concat('%', #{searchKeyword}, '%')
    				</when>
    				<when test="searchCondition == 1">
    					and title like concat('%', #{searchKeyword}, '%')
    				</when>
    			</choose>
    		</if>
    	</where>
    	order by item_id DESC 
    	LIMIT #{recordCountPerPage} OFFSET #{firstIndex}
    </select>
    ```
    </div>
    </details>

<br>

#### MyBatis

> MyBatis만 사용할거면 JPA 어노테이션(ex:@OneToMany) 사용할 필요 없음. SQL 매퍼(xml)에서 해야함.  
> => JPA는 엔티티 개념을 사용했지만, MyBatis는 직접SQL이라 DB개념으로 좀 더 생각하자.  
>
> 즉, JPA의 영속성 컨텍스트로 도메인 패턴 필요 없음.   
> update도 더티체킹 없이 전부 SQL로 처리 해야 함.
>
> PK값 자동증가 ID도 SQL 매퍼에서 MySQL은 useGeneratedKeys로 적용(auto_increment)  
> => [Mybatis 키 자동 생성 - useGeneratedKeys(MySQL), selectKey(Oracle)](https://sesoc.tistory.com/41)
>
> N+1 문제는 JPA-"페치조인(즉시로딩)+컬렉션은 distinct까지" 로 해결  
> MyBatis는 SQL문 사용하므로 조인이나 서브쿼리 덕분에 직면할 문제가 아님
>
> update는 JPA에선 더티체킹 방식이지만, SQL인 MyBatis는 아님.  
> @Transactional에서 JPA는 영속성컨텍스트 덕분에 쿼리모아서 한번에 전송지만, MyBatis는 매순간 전송

**MyBatis 테스트를 위해 꼭 참고할 점:**

1. main/resources하위 XML수동 빈 등록은 test/resources 하위로 복제하자   
   (단, XML내 빈에 property로 연동한 XML은 복제 안해도 됨. 자동으로 main하위도 찾아줌.  
   예로 XML내 빈에 연동된 XML이 아닌 context-common.xml의 경우  `@ImportResource("classpath:/spring/*.xml")` 로 main함수위에 직접 등록해야하는데,  
   빈 내에서 XML연동한 파일의 경우 "test하위로 복제 안해도 main에서 찾아 주더라"  
   **=> 따라서 SQL을 작성한 XML은(=따로 빈에서 연동된) main에서만 관리하면 됨!**

   테스트를 위한 XML 관리법을 다시 정리하자면,   
   **부트니까 @SpringBootTest 를 사용하여 메인환경의 빈을 자동 등록(자동 빈, 수동 빈 둘다)  
   메인환경의 수동 빈 등록법은 "@ImportResource("classpath:..) 필수 + 테스트환경에 ImportResource로 등록한 XML 파일 복제"**

2. boot-jpa-data 의존성으로 자동 테이블 생성 사용 시 "데이터 타입 매핑과 자동 언더스코어(db)에서 카멜케이스(java)"를 알자.  
   테이블 자동 생성 JPA: `Long` → `bigint`, `String` → `varchar(255)`, `LocalDateTime` → `datetime(6)`    
   `@Id+@GeneratedValue(strategy = GenerationType.IDENTITY)` → `PK+not null auto_increment`  
   **MyBatis에서 맞춰주자: XML에서 언더스코어에서 카멜케이스 자동 매핑 설정.   **
   **예: sql에 ${imgSrc} <- img_src 매핑**  
   **객체 매핑 주의점:** sql에 아예 필드명 다른건 select **item_id as id** .. where item_id = #{id} 이런식으로 하거나,  
   **Result Maps**을 사용 -> **as 별칭을 대체!**

<br>

**MyBatis 참고 문법**

1. namespace="패키지.매퍼인터페이스": @Mapper 선언한 매퍼 인터페이스 클래스와 연결  
   -> xml쿼리 id와 인터페이스 메소드명과 반드시 동일

2. id 값(중복불가): 이 속성으로 SQL 구분+매퍼 인터페이스의 메서드 이름과 연결되어 쿼리를 더 쉽게 호출

3. useGeneratedKeys="true": 이 속성은 데이터베이스에서 생성된 키를 반환하도록 MyBatis에 지시합니다.

4. keyColumn="user_id": db의 user_id 필드명이 generatedKeys 임을 알려줌.(아마 keyProperty를 한다면 이건 생략해도 될거임 ㅇㅇ)

5. keyProperty="user_id": 생성된 키가 User 객체의 user_id 필드에 설정되도록 합니다.

6. resultType="패키지.Item": 쿼리결과 반환타입 지정  
   -> MyBatis별칭 사용중이면 "Item"

7. SQL의 데이터 입력 매핑?

   - 단일 데이터 입력: parameterType 설정안해도 어차피 1개라 자동 매핑
   - 다중 테이터 입력: 인터페이스에서 @Param 활용해서 xml의 #{}과 매핑 or 같은 필드명 가진 객체(Object)로 매핑(=parameterType설정)  
     @Param예: `void update(@Param("id") Long id, @Param("updateParam") ItemUpdateDto updateParam)`

8. SQL에 as 별칭 사용 or  resultMap 사용: 객체 필드와 DB 컬럼을 매핑

9. 동적 SQL: `if, choose (when, otherwise) , trim (where, set), foreach`

10. 기타: 

    - `insert, update, delete` 의 반환값은 영향을 받은 행수를 반환
    - `${}`는 문자열로써 sql인젝션에 취약(대신 sql에서 수식연산 가능)  
    - `#{}`는 파라미터 형식으로써 sql인젝션에 안전(대신 sql에서 수식연산 불가해서 미리 연산해서 주기!)  
    - `<=, <, > 등` 이런 연산자는 xml 파싱 문제가 있을 수 있어서 "<![CDATA[ 사용 or xml문법 사용"

    <details><summary><b>매퍼 예시 코드</b></summary>
    <div markdown="1"><br>
    ```xml
    <mapper namespace="com.secretgallery.service.impl.ItemMapper">
    	<insert id="save" useGeneratedKeys="true" keyProperty="id">
    		insert
    		into item (nickname, password, title, content, img_src, date1, date2)
    		values (#{nickname}, #{password}, #{title}, #{content}, #{imgSrc},
    		#{date1}, #{date2})
    	</insert>
    	<update id="update">
    		update item
    		set password=#{password},
    		title=#{title},
    		content=#{content}
    		where item_id = #{id}
    	</update>
    	<select id="findById" resultType="Item">
    		select item_id as id, nickname,
    		password, title, content, img_src, date1,
    		date2
    		from item
    		where item_id =
    		#{id}
    	</select>
        <resultMap id="item" type="Item">
            <result property="id" column="item_id" />
            <result property="nickname" column="nickname" />
            <result property="password" column="password" />
            <result property="title" column="title" />
            <result property="content" column="content" />
            <result property="imgSrc" column="img_src" />
            <result property="date1" column="date1" />
            <result column="date2" property="date2" />
        </resultMap>
        <!-- (동적쿼리)검색+페이징 -->
    	<select id="findAllWithPage" resultMap="item">
    		SELECT *
    		FROM item
    		<where>
    			<if test="searchKeyword != null and searchKeyword != ''">
    				<choose>
    					<when test="searchCondition == 0">
    						and id like concat('%', #{searchKeyword}, '%')
    					</when>
    					<when test="searchCondition == 1">
    						and title like concat('%', #{searchKeyword}, '%')
    					</when>
    				</choose>
    			</if>
    		</where>
    		order by item_id DESC 
    		LIMIT #{recordCountPerPage} OFFSET #{firstIndex}
    	</select>
    </mapper>
    ```
    ```java
    public class Item {
        @Id //db에 pk
        @GeneratedValue(strategy = GenerationType.IDENTITY) //db에 not null auto_increment
        @Column(name="item_id") //db 필드명
        private Long id; //엔티티 필드명
        private String nickname;
        private String password;
        private String title;
        private String content;
        private String imgSrc; //db엔 img_src
        @DateTimeFormat(pattern = "yy.MM.dd.HH:mm")
        private LocalDateTime date1;
        @DateTimeFormat(pattern = "yy년 MM월 dd일 HH시 mm분")
        private LocalDateTime date2;
    }
    ```
    </div>
    </details>

<br>

**MyBatis 사용한 3가지 예시 + 동적쿼리**<br>

**(1) 스프링을 사용하지 않은 MyBatis 방식**<br>**4개의 파일 활용:** mybatis-config.xml, SqlSessionTemplate.java, UserMapper.xml, UserMapper.interface

- **라이브러리 추가**: `build.gradle` 의존성 추가: `implementation 'org.mybatis:mybatis:3.5.7' // MyBatis`

- **사용흐름**

  1. `mybatis-config.xml` 에 세팅 및 `UserMapper.xml` 매핑 추가
  
  2. `UserMapper.xml` 파일 생성 및 sql문 작성+매핑에 namespace=UserMapper인터페이스 추가
     - `UserMapper인터페이스`  파일 생성 및 기능 함수 작성 (insert쪽은 void로!)
     
  4. `UserRepository` 를 마지막으로 생성 및 DAO 로직 작성
     - 참고로 `SqlSession, SqlSessionFactory` 를 추가 활용
  
  5. `SqlSessionTemplate` 는 `UserRepository` 에 **SqlSessionFactory를 파라미터로** 넣어 사용 위함
     - mybatis-config.xml 설정 내용도 SqlSessionFactory 자바 객체에 담게 된다.
  
  <details><summary><b>전체코드</b></summary>
  <div markdown="1"><br>
  **mybatis-config.xml**
  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <!-- -->
  <!-- -->
  <configuration>
    <!-- config.xml 안에서 태그 작성시 태그의 사용 순서 -->
    <!--
      properties, settings, typeAliases, typeHandlers,
      objectFactory, objectWrapperFactory, refletorFactory,
      plugins, environments, databaseIdProvider, mappers
     -->
  <!-- -->
  <!-- -->
    <!-- typeAliases를 통해서 사용하고자 하는 객체를 등록 -->
    <typeAliases>
      <typeAlias type="org.example.v2.domain.User" alias="User"/>
      <typeAlias type="org.example.v2.domain.Profile" alias="Profile"/>
      <typeAlias type="org.example.v2.domain.ChatRoom" alias="ChatRoom"/>
      <typeAlias type="org.example.v2.domain.Message" alias="Message"/>
    </typeAliases>
  <!-- -->
  <!-- -->
    <environments default="development">
      <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
          <property name="driver" value="oracle.jdbc.OracleDriver"/>
          <property name="url" value="jdbc:oracle:thin:@localhost:1521:STR"/>
          <property name="username" value="testUser"/>
          <property name="password" value="1234"/>
        </dataSource>
      </environment>
    </environments>
    <mappers>
      <mapper resource="org/example/UserMapper.xml"/>
      <mapper resource="org/example/ChatRoomMapper.xml"/>
      <mapper resource="org/example/MessageMapper.xml"/>
    </mappers>
  </configuration>
  ```
  **UserMapper.xml**
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
  <!--
  id 값(중복불가): 이 속성으로 SQL 구분+매퍼 인터페이스의 메서드 이름과 연결되어 쿼리를 더 쉽게 호출
  useGeneratedKeys="true": 이 속성은 데이터베이스에서 생성된 키를 반환하도록 MyBatis에 지시합니다.
  keyColumn="user_id": db의 user_id 필드명이 generatedKeys 임을 알려줌.
  keyProperty="user_id": 생성된 키가 User 객체의 user_id 필드에 설정되도록 합니다.
  ...
  sql의 데이터 입력은 매핑이 되어야 함.
  다중 테이터 입력: 인터페이스에서 @Param 활용해서 xml의 #{}과 매핑 or 같은 필드명 가진 객체(Object)로 매핑(=parameterType설정)
  단일 데이터 입력: parameterType 설정안해도 어차피 1개라 자동 매핑 됨
  -->
  <mapper namespace="org.example.v2.repository.mapper.UserMapper">
    <select id="findByIdNPw" resultType="User">
      select * from Member where id = #{id} and pw = #{pw}
    </select>
    <insert id="saveUser" parameterType="User" useGeneratedKeys="true" keyColumn="user_id" keyProperty="user_id">
      insert into Member (id, pw, nickname) values (#{id}, #{pw}, #{nickname})
    </insert>
    <insert id="saveProfile" parameterType="Profile">
      insert into Profile (profile_id, name, col, email) values (#{profile_id}, #{name}, #{col}, #{email})
    </insert>
    <select id="findById" resultType="User">
      select * from Member where id = #{id}
    </select>
    <select id="findByNickname" resultType="User">
      select * from Member where nickname = #{nickname}
    </select>
  </mapper>
  ```
  **UserMapper인터페이스**
  ```java
  public interface UserMapper {
    /**
     * 회원가입
     */
    void saveUser(User user);
    void saveProfile(Profile profile);
    /**
     * 로그인
     */
    User findByIdNPw(@Param("id") String id, @Param("pw") String pw); //id,pw로 찾기
    /**
     * 조회
     */
    User findById(String id); //아이디로 찾기
    User findByNickname(String nickname); //닉네임으로 찾기
  }
  ```
  **UserRepository**
  ```java
  public class UserRepository {
    private SqlSessionFactory sqlSessionFactory;
    public UserRepository(SqlSessionFactory sqlSessionFactory) {
      this.sqlSessionFactory = sqlSessionFactory;
    }
    /**
     * 회원가입
     */
    public User save(User user) {
      try (SqlSession session = sqlSessionFactory.openSession()) {
        UserMapper mapper = session.getMapper(UserMapper.class);
        mapper.saveUser(user);
        session.commit(); //트랜잭션 commit
        return user; //생성된 generatedKeys 포함
      }
    }
    public Profile save(Profile profile) {
      try (SqlSession session = sqlSessionFactory.openSession()) {
        UserMapper mapper = session.getMapper(UserMapper.class);
        mapper.saveProfile(profile);
        session.commit(); //트랜잭션 commit
        return profile; //생성된 generatedKeys 포함X
      }
    }
    /**
     * 로그인
     */
    public User findByIdNPw(String id, String pw) {
      try (SqlSession session = sqlSessionFactory.openSession()) {
        UserMapper mapper = session.getMapper(UserMapper.class);
        return mapper.findByIdNPw(id, pw);
      }
    }
    /**
     * 조회
     */
    public User findById(String id) {
      try (SqlSession session = sqlSessionFactory.openSession()) {
        UserMapper mapper = session.getMapper(UserMapper.class);
        return mapper.findById(id);
      }
    }
    public User findByNickname(String nickname) {
      try (SqlSession session = sqlSessionFactory.openSession()) {
        UserMapper mapper = session.getMapper(UserMapper.class);
        return mapper.findByNickname(nickname);
      }
    }
  }
  ```
  **SqlSessionTemplate**
  ```java
  public class SqlSessionTemplate {
    private static SqlSessionFactory sqlSessionFactory;
    public static SqlSessionFactory getSqlSessionFactory() {
      return sqlSessionFactory;
    }
    //1번 초기화 하면 충분. 싱글패턴. 정적 초기화로 ㄱ.
    static {
      try {
        String resource = "mybatis-config.xml";
        Reader reader = Resources.getResourceAsReader(resource);
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader); //Builder 로 설정 간단히.
      } catch (IOException e) {
        throw new RuntimeException("Failed to initialize SqlSessionFactory: " + e.getMessage(), e);
      }
    }
  }
  ```
  </div>
  </details>

<br>

**(2) 순수 스프링을 사용한 MyBatis 방식 + 동적쿼리**

**구현흐름(eGov를 감미한..)**

1. **MyBatis 인터페이스 방식 구현(=@Mapper 방식)**: ItemService.class 인터페이스 + ItemServiceImpl.class (@Service로 자동 빈) + ItemMapper.class 인터페이스(@Mapper로 프록시로 자동 빈)  
   => 특히, ItemServiceImpl 클래스 = EgovAbstractServiceImpl 상속 + ItemService 구현체 역할

2. **(부트와 큰 차이점)xml설정 부분**: **Mybatis-boot-starter 의존성 없으니 빈 등록 필수!** 
   - context-mybatis.xml 필수(=빈 등록: SqlSessionFactoryBean:sql-mybatis-config.xml등록, MapperConfigurer:@Mapper 인터페이스 자동 스캔 위치 지정) +  
   - sql-mybatis-config.xml 에서 \<mapper Item.xml> + lazy설정, 별칭, 캐시 등
   - Item.xml 에는 SQL문 작성!  

3. **xml 사용 위해:** main함수 위에 `@ImportResource("classpath:/spring/*.xml")` 로 등록

   <details><summary><b>전체코드와 사용 파일 한눈에 보기</b></summary>
   <div markdown="1"><br>
   {src/main/resources/}spring/context-mybatis.xml -> sqlSession빈<br>
   {src/main/resources/}sqlmap/sql-mybatis-config.xml -> Item.xml(매퍼)연결 및 별칭,캐시 등<br> 
   {src/main/resources/}sqlmap/mappers/Item.xml -> SQL<br>
   {src/main/java/}...service.impl/ItemMapper.java -> @Mapper<br>
   {src/main/java/}...service.impl/ItemServiceImpl.java -> @Service<br>
   {src/main/java/}...service/ItemService.java -> (그냥 확장성 위해 인터페이스 추가한거일 뿐)<br>
   **context-mybatis.xml**
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
   	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd">
   	<!-- 애초에 boot-mybatis-starter 라이브러리 사용했으면 이부분 자동 설정해 줌. 안 사용해서 추가한거임. -->
   <!-- -->
   	<!-- SqlSession setup for MyBatis Database Layer -->
   	<!-- MyBatis와 Spring 연동 설정
   	물론, 스프링부트는 java 파일에서 빈 등록을 권장 -->
   	<bean id="sqlSession"
   		class="org.mybatis.spring.SqlSessionFactoryBean">
   		<property name="dataSource" ref="dataSource" />
   		<property name="configLocation"
   			value="classpath:/sqlmap/sql-mybatis-config.xml" />
   		<!-- <property name="mapperLocations" value="classpath:**/lab-*.xml" /> -->
   	</bean>
   	<!-- MapperConfigurer setup for @Mapper -->
   	<!-- MyBatis의 Mapper Interface 자동스캔 설정 
   	물론, 스프링부트는 java 파일에서 빈 등록을 권장 -->
   	<bean class="org.egovframe.rte.psl.dataaccess.mapper.MapperConfigurer ">
   		<property name="basePackage"
   			value="com.secretgallery.service.impl" />
   	</bean>
   </beans>	
   ```
   **sql-mybatis-config.xml**
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
   <!--  -->
   	<settings>
   		<setting name="cacheEnabled" value="true" />
   		<setting name="lazyLoadingEnabled" value="true" />
   		<setting name="multipleResultSetsEnabled" value="true" />
   		<setting name="mapUnderscoreToCamelCase" value="true" />
   	</settings>
   	<!-- 별칭 지정시 sql문쪽 resultType 이런곳에서 "클래스명"만으로 바로 사용 가능! 
   	단, 클래스단위임. 패키지 단위는 application.properties에서 해야함. -->
   	<typeAliases>
   		<typeAlias alias="Item"
   			type="com.secretgallery.vo.Item" />
   	</typeAliases>
   <!--  -->
   	<typeHandlers>
   		<typeHandler
   			handler="org.egovframe.rte.psl.dataaccess.typehandler.CalendarMapperTypeHandler" />
   	</typeHandlers>
   <!--  -->
   	<mappers>
   		<mapper resource="sqlmap/mappers/Item.xml" />
   	</mappers>
   </configuration>
   ```
   **Item.xml**
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="com.secretgallery.service.impl.ItemMapper">
   	<insert id="save" useGeneratedKeys="true" keyProperty="id">
   		insert
   		into item (nickname, password, title, content, img_src, date1, date2)
   		values (#{nickname}, #{password}, #{title}, #{content}, #{imgSrc},
   		#{date1}, #{date2})
   	</insert>
   	<update id="update">
   		update item
   		set password=#{password},
   		title=#{title},
   		content=#{content}
   		where item_id = #{id}
   	</update>
   	<select id="findById" resultType="Item">
   		select item_id as id, nickname,
   		password, title, content, img_src, date1,
   		date2
   		from item
   		where item_id =
   		#{id}
   	</select>
   	<delete id="delete">
   		delete from item where item_id = #{id}
   	</delete>
   	<select id="findAll" resultType="Item">
   		select item_id as id, nickname,
   		password, title, content, img_src,
   		date1,
   		date2
   		from item
   	</select>
   <!--  -->
   	<!-- DBIO 써보기 + as별칭 말고 resultMap 써보기 -->
   	<resultMap id="item" type="Item">
   		<result property="id" column="item_id" />
   		<result property="nickname" column="nickname" />
   		<result property="password" column="password" />
   		<result property="title" column="title" />
   		<result property="content" column="content" />
   		<result property="imgSrc" column="img_src" />
   		<result property="date1" column="date1" />
   		<result column="date2" property="date2" />
   	</resultMap>
   	<!-- (동적쿼리)검색+페이징 -->
   	<select id="findAllWithPage" resultMap="item">
   		SELECT *
   		FROM item
   		<where>
   			<if test="searchKeyword != null and searchKeyword != ''">
   				<choose>
   					<when test="searchCondition == 0">
   						and id like concat('%', #{searchKeyword}, '%')
   					</when>
   					<when test="searchCondition == 1">
   						and title like concat('%', #{searchKeyword}, '%')
   					</when>
   				</choose>
   			</if>
   		</where>
   		order by item_id DESC 
   		LIMIT #{recordCountPerPage} OFFSET #{firstIndex}
   	</select>
   	<select id="findTotalCount" resultType="int">
   		SELECT count(*) FROM Item
   		<where>
   			<if test="searchKeyword != null and searchKeyword != ''">
   				<choose>
   					<when test="searchCondition == 0">
   						and id like concat('%', #{searchKeyword}, '%')
   					</when>
   					<when test="searchCondition == 1">
   						and title like concat('%', #{searchKeyword}, '%')
   					</when>
   				</choose>
   			</if>
   		</where>
   	</select>
   	<!-- 자동검색(ajax) -->
   	<select id="findTitleListForSuggest" resultType="string">
   		SELECT TITLE
   		FROM item
   		where title like '%' || #{value} || '%'
   		<!-- where title like concat('%', #{value}, '%') -->
   	</select>
   	<select id="findPrevNextById" resultMap="item">
   		<![CDATA[
   		SELECT * FROM Item WHERE
   		item_id >= #{prevId} and item_id <= #{nextId}
   		]]>
   	</select>
   </mapper>
   ```
   **ItemMapper.java**
   ```java
   //Mapper Interface - 메서드명과 쿼리ID를 매핑하여 쿼리호출
   @Mapper
   public interface ItemMapper {
   	public Long save(Item item);
   	public Long update(Item item);
   	public Long delete(Item item);
   	public Item findById(Long id);
   	public List<Item> findAll();
   //	
   	public List<Item> findAllWithPage(ItemDefault searchItem);
   	public int findTotalCount(ItemDefault searchItem);
   	public List<String> findTitleListForSuggest(String value);
   	public List<Item> findPrevNextById(@Param("prevId") Long prevId, @Param("nextId") Long nextId);
   }
   ```
   **ItemServiceImpl.java**
   ```java
   @Service
   @Transactional(readOnly = true) 
   @RequiredArgsConstructor
   @Slf4j
   public class ItemServiceImpl extends EgovAbstractServiceImpl implements ItemService {
   	private final ItemMapper itemMapper;
   	//CRUD
   	@Override
   	@Transactional // 쓰기모드
   	public Long save(Item item) throws Exception {
   		return itemMapper.save(item);
   	}
   	@Override
   	@Transactional // 쓰기모드
   	public Long update(Item item) throws Exception {
   		// TODO Auto-generated method stub
   		return itemMapper.update(item);
   	}
   	@Override
   	@Transactional // 쓰기모드
   	public Long delete(Item item) throws Exception {
   		// TODO Auto-generated method stub
   		return itemMapper.delete(item);
   	}
   	@Override
   	public Item findById(Long id) throws Exception {
   		return itemMapper.findById(id);
   	}
   	@Override
   	public List<Item> findAll() throws Exception {
   		// TODO Auto-generated method stub
   		return itemMapper.findAll();
   	}
   //
   	//추가 함수
   	@Override
   	public List<Item> findAllWithPage(ItemDefault searchItem) throws Exception {
   		// TODO Auto-generated method stub
   		return itemMapper.findAllWithPage(searchItem);
   	}
   	@Override
   	public int findTotalCount(ItemDefault searchItem) throws Exception {
   		// TODO Auto-generated method stub
   		return itemMapper.findTotalCount(searchItem);
   	}
   	@Override
   	public List<String> findTitleListForSuggest(String value) throws Exception {
   		// TODO Auto-generated method stub
   		return itemMapper.findTitleListForSuggest(value);
   	}
   	@Override
   	public List<Item> findPrevNextById(Long id) throws Exception {
   		// TODO Auto-generated method stub
   		return itemMapper.findPrevNextById(id-1, id+1);
   	}
   }
   ```
   **ItemService.java**
   ```java
   /**
    * CRUD + 
    * findAllWithPage + findTotalCount + findTitleListForSuggest + findPrevNextById
    	* 총 게시물 수 구하는 함수: findTotalCount()
    	* 이전, 이후 전시실 버튼 생성용: findPrevNextById()
    	* 검색 자동완성 함수: findTitleListForSuggest()
    */
   public interface ItemService {
   	/**
   	 * CRUD - C
   	 * @param item
   	 * @return count(개수)
   	 * @throws Exception
   	 */
   	public Long save(Item item) throws Exception;
   	/**
   	 * CRUD - U
   	 * @param item
   	 * @return count(개수)
   	 * @throws Exception
   	 */
   	public Long update(Item item) throws Exception;
   	/**
   	 * CRUD - D
   	 * @param item
   	 * @return count(개수)
   	 * @throws Exception
   	 */
   	public Long delete(Item item) throws Exception;
   	/**
   	 * CRUD - R
   	 * @param id
   	 * @return 
   	 * @throws Exception
   	 */
   	public Item findById(Long id) throws Exception;
   	/**
   	 * CRUD - R
   	 * @return 
   	 * @throws Exception
   	 */
   	public List<Item> findAll() throws Exception;
   	//
   	//추가 함수
   	/**
   	 * 해당 페이지 Item 전부 조회 by desc
   	 * @param pageId
   	 * @return
   	 * @throws Exception
   	 */
   	public List<Item> findAllWithPage(ItemDefault searchItem) throws Exception;
   	/**
   	 * 전체 Item의 총 개수
   	 * @return
   	 * @throws Exception
   	 */
   	public int findTotalCount(ItemDefault searchItem) throws Exception;
   	/**
   	 * 검색에 자동완성 기능
   	 * @param value
   	 * @return
   	 * @throws Exception
   	 */
   	public List<String> findTitleListForSuggest(String value) throws Exception;
   	/**
   	 * Item의 이전, 이후 Item 구하기
   	 * @param id
   	 * @return
   	 * @throws Exception
   	 */
   	public List<Item> findPrevNextById(Long id) throws Exception;
   }
   ```
   </div>
   </details>

<br>

**(3) 스프링 부트를 사용한 MyBatis 방식 + 동적쿼리 -> 자세한 코드 예시는 [Spring Boot MyBatis](https://github.com/BH946/spring-first-roadmap/tree/main/spring_study_7/itemservice-db#mybatis)**<br>**3개의 파일 활용:** application.properties, ItemMapper.interface, ItemMapper.xml, ItemRepository

<details><summary><b>주요 동작방식(그림)</b></summary>
<div markdown="1"><br>
<img src="https://github.com/user-attachments/assets/e7639c7e-498d-40b3-aa93-998b66719ed0" alt="image" style="zoom:80%;" />
</div>
</details>


- **라이브러리 추가**: `implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.2.0' //spring boot mybatis`

- **사용 흐름**

  1. **(큰 차이점)mybatis-boot-starter 의존성** 덕분에 SqlSessionFactory가 자동 빈 등록 되고, MapperConfigurer도 필요없이 자동으로 @Mapper 붙은 인터페이스를 찾아 줌.
  1. `application.properties` 로 type-aliases(별칭), underscore와 camel-case 매핑, 로그 등 설정
  2. `ItemMapper.class`는 Mybatis 매핑 XML(=ItemMapper.xml)을 호출해주는 "매퍼 인터페이스"
     - **@Mapper** 를 반드시 사용 -> 최종적으로 스프링은 프록시 구현체 만들어 **"빈 등록!"**
  4. `ItemMapper.xml`는 부트니까 `src/main/resources` 하위에 위치 및 사용할 sql을 작성한다.
     - 매핑에 namespace=UserMapper인터페이스 추가
  5. `ItemRepository` 를 마지막으로 생성 및 DAO 로직 작성
     - 빈에 등록된 ItemMapper 를 바로 가져다 사용하면 됨.
  
  <details><summary><b>전체코드</b></summary>
  <div markdown="1"><br>
  **application.properties**
  ```properties
  #MyBatis
  mybatis.type-aliases-package=hello.itemservice.domain 
  mybatis.configuration.map-underscore-to-camel-case=true 
  logging.level.hello.itemservice.repository.mybatis=trace
  ```
  **ItemMapper인터페이스**
  ```java
  @Mapper
  public interface ItemMapper {
      void save(Item item);
      void update(@Param("id") Long id, @Param("updateParam") ItemUpdateDto updateParam);
      Optional<Item> findById(Long id);
      List<Item> findAll(ItemSearchCond itemSearch);
  }
  ```
  **ItemMapper.xml**
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <mapper namespace="hello.itemservice.repository.mybatis.ItemMapper">
      <insert id="save" useGeneratedKeys="true" keyProperty="id">
          insert into item (item_name, price, quantity)
          values (#{itemName}, #{price}, #{quantity})
      </insert>
      <update id="update">
          update item
          set item_name=#{updateParam.itemName},
              price=#{updateParam.price},
              quantity=#{updateParam.quantity}
          where id = #{id}
      </update>
      <select id="findById" resultType="Item">
          select id, item_name, price, quantity
          from item
          where id = #{id}
      </select>
      <select id="findAll" resultType="Item">
          select id, item_name, price, quantity
          from item
          <where>
              <if test="itemName != null and itemName != ''">
                  and item_name like concat('%', #{itemName}, '%')
              </if>
              <if test="maxPrice != null">
                  and price &lt;= #{maxPrice}
              </if>
          </where>
      </select>
  </mapper>
  ```
  **ItemRepository**
  ```java
  @Slf4j
  @Repository
  @RequiredArgsConstructor
  public class ItemRepository {
      private final ItemMapper itemMapper;
      @Override
      public Item save(Item item) {
          log.info("itemMapper class={}", itemMapper.getClass()); // 프록시 확인
          itemMapper.save(item);
          return item;
      }
      @Override
      public void update(Long itemId, ItemUpdateDto updateParam) {
          itemMapper.update(itemId, updateParam);
      }
      @Override
      public Optional<Item> findById(Long id) {
          return itemMapper.findById(id);
      }
      @Override
      public List<Item> findAll(ItemSearchCond cond) {
          return itemMapper.findAll(cond);
      }
  }
  ```
  </div>
  </details>

<br>

#### JPA, Spring Data JPA, QueryDSL의 조합

**참고: [적용 플젝(레포): v1~v3](https://github.com/BH946/LePl_Spring/tree/kbh/lepl/src/main/java/com/lepl/Repository/member), [적용 플젝(서비스): v1~v2](https://github.com/BH946/LePl_Spring/tree/kbh/lepl/src/main/java/com/lepl/Service/member)**

**Spring Data JPA를 바로 사용(=단순ver) vs 중간에 어댑터 추가하여 추상화(=어댑터 추가ver)**

- **단순ver**은 Spring Data JPA 구현체를 바로 사용하므로 **JpaRepository를 상속받는 인터페이스만 만들어**서 바로 사용하면 된다. 인터페이스 바로 사용이 가능한 이유는 **스프링이 프록시 구현체를 만들**어 주기 때문이다.<br>예시: `public interface MemberRepository extends JpaRepository<Item, Long> { }`

- **어댑터 추가ver**은 중간에 **인터페이스를 도입**하는 방식이다!<br>레포지토리 추상화를 통해서 **서비스단의 코드 변경없이** 레포지토리단만 수정하는 효과!!

- **트레이드 오프 -> 구조의 안정성 vs 단순한 구조와 개발 편리성**

  - **어댑터 추가ver** : DI, OCP를 지키기 위해 어댑터를 도입하고(장), 더 많은 코드를 유지한다.(단)
  - **단순ver** : 어댑터를 제거하고 구조를 단순하게 가져가지만(장), DI, OCP를 포기하고, ItemService 코드를 직접 변경한다.(단)

  <details><summary><b>트레이드 오프 유지보수 관점 (자세히) -> 결론: 단순ver 을 기본 사용 (주관적 생각)</b></summary>
  <div markdown="1">
  - **추상화를 시킬수록** 당연히 서비스단을 고칠필요 없이 레포지토리만 고치면 되는 큰 **장점**이 있다 (DI, OCP를 지킬 수 있겠지)
  - 근데, **단점**으로는 추상화 자체에도(인터페이스 도입) 큰 비용이 발생한다. 코드 조금 추가한다고 생각하면 바보다. 구조가 한단계 커지는건데 개발자의 경우 해당 레포지토리를 수정하려고 찾아가다보면 구조가 많으니까 코드 한줄 수정하는게 훨씬 오래걸릴 수 있다. 비용이 크다는 거다.
  - **반대로, 추상화없이 단순**하게 바로 레포지토리를 서비스단에서 가져와 사용한 경우에 **장점**으로는 굉장히 직관적이므로 바로 레포지토리로 들어가서 코드 한줄 수정 쉽게 가능하므로 비용을 크게 단축시킬 수 있다! 
  - 근데, 서비스단의 로직이 많이 변경될 수 있어서 (DI, OCP를 지킬 수 없는거지) 이 **단점**도 감안해야한다.
  - 그렇기에 프로젝트 마다 잘 선택해야하는 트레이드 오프 부분이라 볼 수 있겠다.
  - **결론적으로 권장 방안은?**
    - 누가봐도 대규모 프로젝트이고 구조적으로 개선해야할 일들이 많다면 추상화를 선택한다. 
    - 다만, 그런 상태가 아니거나 선택하기 애매하거나 할 때는 추상화를 선택하지 않고 오로지 **최대한 빠르게 개발하는걸 권장(=단순ver)**한다. 이후에 리팩토링 하면서 추상화가 꼭 필요하다면 그때 도입해도 되니까! 이게 빨리빨리 개발해야하는 우리에게 좀 더 실용적이라 판단이 된다. (feat:김영한 강사님 생각)
    - QueryDSL도 마찬가지로 단순ver처럼 따로 구현해서 바로 가져와 사용하겠다!
  </div>
  </details>

<br>

**단순ver 사용한 예시** -> **JPA+Spring Data JPA+QueryDSL** 의 모습을 소개할거고, Spring Data JPA만 사용할거면 뒤에 JPA추가는 고려 안해도 됨<br>**1+1개의 파일 활용:** SpringDataJpaItemRepository 인터페이스 + ItemQueryRepository 클래스 + JpaRepository 인터페이스 (이건 부트가 제공!)

<details><summary><b>이 방법의 동작구조(그림)</b></summary>
<div markdown="1"><br>
<img src="https://github.com/user-attachments/assets/eab41981-fd80-42b4-956f-b186f2581cd9" alt="image" style="zoom:80%;" /><br>
<img src="https://github.com/user-attachments/assets/b3bcbe64-5cb5-431b-98eb-ee8457635ca7" alt="image" style="zoom:80%;" /><br><br>
**QueryDSL까지 합류한다면?**
<img src="https://github.com/user-attachments/assets/d745de16-af58-4e9d-830a-211a84be0e4f" alt="image" style="zoom:80%;" />
</div>
</details>

- **사용흐름**

  1. `SpringDataJpaItemRepository 인터페이스` 를 추가하여 JpaRepository 인터페이스를 상속하자 -> Spring Data Jpa 사용 목적!

  2. `ItemQueryRepository 클래스` 를 추가하여 복잡한 쿼리(+동적) 이곳에서 처리하자!

     - QueryDSL -> JPQL -> SQL 변환

     - **라이브러리 추가 방법**

       - 자동 생성 Q클래스(QueryDSL의 사용타입)를 gradle clean으로 제거 및 gradle 에 라이브러리 추가

       - ```groovy
         //Querydsl 추가 - 스프링 2.x
         implementation 'com.querydsl:querydsl-jpa'
         annotationProcessor "com.querydsl:querydsl-apt:${dependencyManagement.importedProperties['querydsl.version']}:jpa"
         annotationProcessor "jakarta.annotation:jakarta.annotation-api"
         annotationProcessor "jakarta.persistence:jakarta.persistence-api"
         
         //Querydsl 추가 - 스프링 3.x
         implementation 'com.querydsl:querydsl-jpa:5.0.0:jakarta'
         annotationProcessor "com.querydsl:querydsl-apt:${dependencyManagement.importedProperties['querydsl.version']}:jakarta"
         annotationProcessor "jakarta.annotation:jakarta.annotation-api"
         annotationProcessor "jakarta.persistence:jakarta.persistence-api"
         
         //Querydsl 추가, 자동 생성된 Q클래스 gradle clean으로 제거
         clean {
         	delete file('src/main/generated')
         }
         ```

     - **사용할 때 Q클래스 import 필수!!**

       - `import static hello.itemservice.domain.QItem.*;`
       - Q클래스 위치: build/generated/sources/annotaionProcessor 하위

  3. 마지막으로 서비스단에서 SpringDataJpaItemRepository, ItemQueryRepository 를 가져와 사용하면 성공!

     - 이처럼 단순ver은 굉장히 구조가 단순하다.

  4. 일반 JPA도 추가하고 싶다면 단순ver 취지에 맞게 ItemQueryRepository 처럼 바로 추가 레포지토리 클래스를 만들어서 서비스에서 사용하면 된다! (생략)

  <details><summary><b>전체코드</b></summary>
  <div markdown="1"><br>
  **SpringDataJpaItemRepository 인터페이스**
  ```java
  /**
   * CRUD 는 기본으로 제공
   * 아래 메소드들은 "쿼리 메서드"
   */
  public interface SpringDataJpaItemRepository extends JpaRepository<Item, Long> {
    //변환 JQPL : select i from Item i where i.name like ?
    List<Item> findByItemNameLike(String itemName);
    //변환 JQPL : select i from Item i where i.price <= ?
    List<Item> findByPriceLessThanEqual(Integer price);
    //쿼리 메서드 (아래 메서드와 같은 기능 수행)
    List<Item> findByItemNameLikeAndPriceLessThanEqual(String itemName, Integer price);
    //쿼리 직접 실행 -> 수동 JPQL 작성 지원
    @Query("select i from Item i where i.itemName like :itemName and i.price <= :price")
    List<Item> findItems(@Param("itemName") String itemName, @Param("price") Integer price);
  }
  ```
  **ItemQueryRepository 클래스** -> JPAQueryFactory 필수 사용!<br>그리고 동적 쿼리 위해 BooleanExpression 조건문 클래스 사용한 예시이다.
  ```java
  import static hello.itemservice.domain.QItem.*;
  @Repository
  public class ItemQueryRepositoryV2 {
      private final JPAQueryFactory query;
  //
      public ItemQueryRepositoryV2(EntityManager em) {
          this.query = new JPAQueryFactory(em);
      }
      // 자동생성 Q클래스 덕분에 item 이 바로 사용 가능
      public List<Item> findAll(ItemSearchCond cond) {
          return query.select(item)
                  .from(item)
                  .where(
                          likeItemName(cond.getItemName()),
                          maxPrice(cond.getMaxPrice())
                  )
                  .fetch();
      }
  //
      private BooleanExpression likeItemName(String itemName) {
          if (StringUtils.hasText(itemName)) {
              return item.itemName.like("%" + itemName + "%");
          }
          return null;
      }
      private BooleanExpression maxPrice(Integer maxPrice) {
          if (maxPrice != null) {
              return item.price.loe(maxPrice);
          }
          return null;
      }
  }
  ```
  **서비스단 예시 코드**
  ```java
  @Service
  @RequiredArgsConstructor
  @Transactional
  public class ItemService {
      private final SpringDataJpaItemRepository springDataJpaItemRepository;
      private final ItemQueryRepositoryV2 itemQueryRepositoryV2;
  //
      public Item save(Item item) {
          return springDataJpaItemRepository.save(item);
      }
      public void update(Long itemId, ItemUpdateDto updateParam) {
          Item findItem = springDataJpaItemRepository.findById(itemId).orElseThrow();
          findItem.setItemName(updateParam.getItemName());
          findItem.setPrice(updateParam.getPrice());
          findItem.setQuantity(updateParam.getQuantity());
      }
      public Optional<Item> findById(Long id) {
          return springDataJpaItemRepository.findById(id);
      }
      public List<Item> findItems(ItemSearchCond cond) {
          return itemQueryRepositoryV2.findAll(cond);
      }
  }
  ```
  </div>
  </details>

 <br>

**어댑터 추가ver 를 사용한 2가지 예시 -> 결론: 첫번째 방식 선호!**

**(1) 첫번째 예시 -> 본인은 이게 더 직관적이라 생각**<br>**3+1개의 파일 활용:** ItemRepository인터페이스, JpaItemRepositoryV2 클래스, SpringDataJpaItemRepository 인터페이스 + JpaRepository 인터페이스 (이건 부트가 제공!)

<details><summary><b>이 방법의 동작구조(그림)</b></summary>
<div markdown="1"><br>
<img src="https://github.com/user-attachments/assets/9ad62419-38b1-429c-a36f-55ad47f2ee94" alt="image" style="zoom:80%;" />
</div>
</details>

- **사용흐름**

  1. `ItemRepository 인터페이스` 를 추가하여 인터페이스 도입! (어댑터 역할) -> **아무것도 상속받지 않는다!!!**
  2. `JpaItemRepositoryV2 클래스` 를 추가하여 ItemRepository인터페이스를 구현한다. 단, 이때 아래 SpringDataJpaItemRepository(=Spring Data JPA) 를 가져와 사용함!!
     - 여기서 레포 구현체를 만들때 JPA + Spring Data JPA 를 사용하게 된다!
  3. `SpringDataJpaItemRepository 인터페이스` 를 추가하여 JpaRepository 인터페이스를 상속하자 -> Spring Data Jpa 사용 목적!
  4. 마지막으로 서비스단에서 ItemRepository 를 가져와 사용하면 성공!

  <details><summary><b>전체코드</b></summary>
  <div markdown="1"><br>
  **ItemRepository 인터페이스**
  ```java
  public interface ItemRepository {
      Item save(Item item);
      void update(Long itemId, ItemUpdateDto updateParam);
      Optional<Item> findById(Long id);
      List<Item> findAll(ItemSearchCond cond);
  }
  ```
  **JpaItemRepositoryV2 클래스**<br>
  JPA+SpringDataJPA 사용 모습이며, QureyDSL도 원한다면 따로 클래스 생성 후 사용하면 됨!
  ```java
  @Repository
  @Transactional
  @RequiredArgsConstructor
  public class JpaItemRepositoryV2 implements ItemRepository {
      private final SpringDataJpaItemRepository repository;
  //
      @Override
      public Item save(Item item) {
          return repository.save(item);
      }
      @Override
      public void update(Long itemId, ItemUpdateDto updateParam) {
          Item findItem = repository.findById(itemId).orElseThrow();
          findItem.setItemName(updateParam.getItemName());
          findItem.setPrice(updateParam.getPrice());
          findItem.setQuantity(updateParam.getQuantity());
      }
      @Override
      public Optional<Item> findById(Long id) {
          return repository.findById(id);
      }
  //
      //QueryDSL 사용하지 않았을 때 동적 쿼리 구현 방식
      @Override
      public List<Item> findAll(ItemSearchCond cond) {
          String itemName = cond.getItemName();
          Integer maxPrice = cond.getMaxPrice();
          if (StringUtils.hasText(itemName) && maxPrice != null) {
  //            return repository.findByItemNameLikeAndPriceLessThanEqual("%" + itemName + "%", maxPrice);
              return repository.findItems("%" + itemName + "%", maxPrice);
          } else if (StringUtils.hasText(itemName)) {
              return repository.findByItemNameLike("%" + itemName + "%");
          } else if (maxPrice != null) {
              return repository.findByPriceLessThanEqual(maxPrice);
          } else {
              return repository.findAll();
          }
      }
  }
  ```
  **SpringDataJpaItemRepository 인터페이스**
  ```java
  /**
   * CRUD 는 기본으로 제공
   * 아래 메소드들은 "쿼리 메서드"
   */
  public interface SpringDataJpaItemRepository extends JpaRepository<Item, Long> {
    //변환 JQPL : select i from Item i where i.name like ?
    List<Item> findByItemNameLike(String itemName);
    //변환 JQPL : select i from Item i where i.price <= ?
    List<Item> findByPriceLessThanEqual(Integer price);
    //쿼리 메서드 (아래 메서드와 같은 기능 수행)
    List<Item> findByItemNameLikeAndPriceLessThanEqual(String itemName, Integer price);
    //쿼리 직접 실행 -> 수동 JPQL 작성 지원
    @Query("select i from Item i where i.itemName like :itemName and i.price <= :price")
    List<Item> findItems(@Param("itemName") String itemName, @Param("price") Integer price);
  }
  ```
  **서비스단 예시코드**
  ```java
  @Service
  @RequiredArgsConstructor
  public class ItemService {
      private final ItemRepository itemRepository;
      public Item save(Item item) {
          return itemRepository.save(item);
      }
      public void update(Long itemId, ItemUpdateDto updateParam) {
          itemRepository.update(itemId, updateParam);
      }
      public Optional<Item> findById(Long id) {
          return itemRepository.findById(id);
      }
      public List<Item> findItems(ItemSearchCond cond) {
          return itemRepository.findAll(cond);
      }
  }
  ```
  </div>
  </details>

<br>

**(2) 두번째 예시 -> [레포지토리 챕터에서 정리한 내용](https://bh946.github.io/checklist/CHECK_LIST_SPRING/#%EB%A0%88%ED%8F%AC%EC%A7%80%ED%86%A0%EB%A6%ACdao)**<br>**3+1개의 파일 활용:** MemberRepository 인터페이스 + MemberRepositoryCustom 인터페이스 + MemberRepositoryCustomImpl 클래스 의 조합 + JpaRepository 인터페이스 (이건 부트가 제공!)

- **인터페이스 도입은 동일하되 JPA 사용 로직을 인터페이스로 추가한 구조!**<br>여기선 대표 인터페이스가 2개의 인터페이스를 상속(=Spring Data Jpa, 일반 Jpa)하는 구조<br>1번 예시는 애초에 인터페이스 상속을 받지 않았고, 구현체를 만들었을 뿐. 구조 차이를 알자.

  - 자바는 상속되는 인터페이스들의 메소드 시그니처들이 자동 제공되니까 이런 구조가 가능
  - 물론, 메소드 오버라이딩으로 구현은 필수니까 구현체도 따로 만듬 

- **사용흐름**

  1. `MemberRepository 인터페이스` 를 추가 -> 서비스 계층에서 사용하게 될 본체! (1번과 비교하면 ItemRepository 를 의미!)
     - **여기서 2개의 인터페이스를 상속: JpaRepository, MemberRepositoryCustom**
       - JpaRepository<Member, Long> 인터페이스가 바로 Spring Data JPA 이다. (부트가 제공)
       - MemberRepositoryCustom 인터페이스가 바로 일반 JPA 이다. (우리가 만듬!!)
  2. `MemberRepositoryCustom 인터페이스` 를 추가 -> 일반 JPA 사용 목적
  3. `MemberRepositoryCustomImpl` 를 추가 -> MemberRepositoryCustom 의 구현체!
  4. 마지막으로 서비스단에서 MemberRepository 를 가져와 바로 사용하면 적용 성공!

  <details><summary><b>전체코드</b></summary>
  <div markdown="1"><br>
  ```java
  /**
   * MemberRepository 인터페이스 -> 서비스 계층에서 사용하게 될 본체
   * 여기서 2개의 인터페이스를 상속 받는다.
   * - JpaRepository<Member, Long> 이 바로 Spring Data JPA 이다.
   * - MemberRepositoryCustom 이 바로 일반 JPA 이다. 
   */
  public interface MemberRepository extends JpaRepository<Member, Long>, MemberRepositoryCustom {
    //CRUD 자동 제공
  }
  /**
   * MemberRepositoryCustom 인터페이스 -> 일반 JPA로 커스텀
   */
  public interface MemberRepositoryCustom {
    Member findOne(Long id);
    Member findByUid(String uid);
    List<FindMemberResponseDto> findAllWithPage(int pageId);
  }
  /**
   * MemberRepositoryCustomImpl 클래스 -> 일반 JPA로 커스텀 구현체
   * - save() 주석 -> JpaRepository 꺼 사용하려고
   * - @Override -> 인터페이스 구현 때 당연히 필수 키워드
   */
  @Repository
  @RequiredArgsConstructor // 생성자 주입 + 엔티티매니저 주입 제공
  public class MemberRepositoryCustomImpl implements MemberRepositoryCustom {
    private final EntityManager em;
    /**
     * save, findOne, findByUid, findAllWithPage
     */
  //  public void save(Member member) {
  //    if (member.getId() == null) {
  //      em.persist(member); // db 저장
  //    }
  //  }
    @Override
    public Member findOne(Long id) {
      return em.find(Member.class, id);
    }
    @Override
    public Member findByUid(String uid) {
      List<Member> findMembers = em.createQuery("select m from Member m where m.uid = :uid",
              Member.class)
          .setParameter("uid", uid)
          .getResultList(); // List로 반환 받아야 null처리가 쉬움
      return findMembers.isEmpty() ? null : findMembers.get(0);
    }
    @Override
    public List<FindMemberResponseDto> findAllWithPage(int pageId) {
      int offset = (pageId - 1) * 10;
      int limit = 10;
      List<Object[]> objects = em.createNativeQuery("select m.member_id, m.nickname, e.level " +
              "from (select * from member order by member_id desc limit " + offset + "," + limit + ") m "
              +
              "inner join character c on m.character_id=c.character_id " +
              "inner join exp e on c.exp_id=e.exp_id;")
          .getResultList();
      return objects.stream()
          .map(o -> new FindMemberResponseDto((Long) o[0], (String) o[1], (Long) o[2]))
          .collect(Collectors.toList());
    }
  }
  ```
  </div>
  </details>

<br><br>

### 트랜잭션 전파 + 주의사항

**(1)트랜잭션 AOP 주의 사항 - 프록시 내부 호출**

![image](https://github.com/user-attachments/assets/d8d35f31-ee92-45d3-a174-ed6126163647) 

![image](https://github.com/user-attachments/assets/40872f1f-626b-4c61-8759-6a901c07e1dc)   

- "클라요청" -> "프록시 호출" -> "실제 트랜잭션 호출" 순서가 일반적인데,
- "클라요청" -> "실제 트랜잭션 호출" 인 경우를 마주칠 수 있다. => **3. 트랜잭션 적용X**
  - 자바 문법상 `internal();` 은 `this.internal();` 로 호출하므로 이런 경우가 생긴다.

<img src="https://github.com/user-attachments/assets/d7c6149e-4dfb-489d-a978-d9db1d460b7c" alt="image" style="zoom:50%;" /> 

- 가장 간단한 해결방안은 **"internal 메소드를 별도의 클래스로 분리"**

  - 예로 `static class` 로 `InternalService` 를 하나 만들어서 `internal()` 메소드 구현

  - 사용할때 `InternalService.internal();` 형태로 사용함으로써 `this` 문제 해결 완!

![image](https://github.com/user-attachments/assets/9c2e6fc2-e9af-4d5a-8ba8-77b115148b40) 

**(2)트랜잭션 AOP 주의 사항 - 트랜잭션 적용 범위**

- 클래스 레벨에 “트랜잭션 적용”시 스프링은 **public 메서드만** 트랜잭션 적용하도록 기본 설정
  - 물론, 설정 변경 가능하다.

**(3)트랜잭션 AOP 주의 사항 - 초기화 시점**

- @PostConstruct로 선언된곳에 @Transactional 선언시 트랜잭션은 미적용
  - WHY?? **초기화코드(@PostConstruct)가 먼저 호출되고 그 다음 트랜잭션 AOP가 적용되기 때문이다. 따라서 초기화 시점에 해당 메서드는 트랜잭션 흭득 불가.**
  - 해결법?? **@EventListener(ApplicationReadyEvent.class) 사용**

<br>

레포1, 레포2 에서 각각 트랜잭션 사용 및 이 둘의 데이터가 연관된다면? **데이터 정합성 문제**가 발생

서비스에만 트랜잭션 사용시 **데이터 정합성 해결** -> 레포 하나라도 롤백이면 전부 롤백되니까.

**이때 자동 사용된 개념이 “스프링 트랜잭션 전파”** 이고, 굳이 **독립적으로 트랜잭션** 사용하겠다 한다면 **REQUIRES_NEW** 옵션을 사용해서 처리가능. -> **기본값은 REQUIRED**

- 트랜잭션이 이미 진행중인데 추가로 수행한다면?? -> 이 경우 어떻게 동작할지 결정하는 것을 **“트랜잭션 전파(propagation)”**
- 여러 트랜잭션들이 중복되어 사용된다면 이들을 구분짓기 위해 **“물리,논리 트랜잭션” 개념** 사용
  - **모든 트랜잭션 매니저(물리,논리=외부,내부)를 커밋해야 물리 트랜잭션이 커밋된다. 하나의 트랜잭션 매니저라도 롤백하면 물리 트랜잭션은 롤백된다.**
  - **트랜잭션 참여 : 외부 트랜잭션과 내부 트랜잭션이 하나의 물리 트랜잭션으로 묶이는 것**
  - **같은 물리 트랜잭션 사용 == 같은 동기화 커넥션 사용**
  - <img src="https://github.com/BH946/bh946.github.io/assets/80165014/1bd94b66-7022-47a1-a4a3-eaa68bfb6e90" alt="image" style="zoom: 80%;" />  

**스프링 트랜잭션 전파 - 트랜잭션 두 번 사용 할 때??**

- **(1) 트랜잭션을 순차적으로 2번 사용은?**
  - **"같은 커넥션(=conn0)"**를 사용 + **"다른 프록시 커넥션"**을 사용.
  - ![image](https://github.com/user-attachments/assets/dd3045ba-bea1-48b5-a718-f0c5c0becb07) 
- **(2) 트랜잭션이 순차가 아닌 중복되게 사용하면? (커밋)**  
  아래 코드처럼 "외부 시작 -> 내부 시작,커밋 -> 외부 커밋" 라면?
  - 신규 트랜잭션 이라면 -> 커밋O
  - 신규 트랜잭션 아니라면 -> 커밋X (신규가 젤 외부니까 거기서 커밋1번 해야지!!!)
  - ![image](https://github.com/user-attachments/assets/82493805-7bd4-4042-bb43-1e7f1ff0ef52) 
  - ![image](https://github.com/user-attachments/assets/c7d58ced-b0c2-4bcc-955a-1fdb102a2038) 
  - ![image](https://github.com/user-attachments/assets/d58018d7-b26a-4645-a0a7-c9e0c764a77a) 
  - ![image](https://github.com/user-attachments/assets/6855dc0c-1bf1-4996-9b3d-16e3df0879a9) 
- **(3) 트랜잭션이 순차가 아닌 중복되게 사용하면? (롤백)**  
  - 내부 롤백은 내부적으로 따로 롤백 마킹
    - 외부 커밋 시점에 이를 확인해서 롤백 -> **rollbackOnly 설정을 확인**
  - 이때, **UnexpectedRollbackException** 런타임 예외를 추가로 던진다.
    - **롤백은 중요한 일**이므로 이런 예외를 추가로 던진다.
- **(4) 트랜잭션이 순차가 아닌 중복되게 사용하면? (REQUIRES_NEW)**  
  - REQUIRES_NEW 라는 옵션을 사용하면 **"외부 트랜잭션과 내부 트랜잭션을 분리 가능"**
  - 즉, **독립적인** 트랜잭션이 2개 생기는 것이며 2개가 **동시에 사용**되는 것 -> 정확히는 1개씩 커넥션이 순차적으로 처리 된다.

**그럼 독립적으로 트랜잭션을 사용하는 경우는 뭐가 있을까??**

(1)기본값: 트랜잭션 그대로 전파 사용은 @Transactional의 `Propagation.REQUIRED` 속성

(2)트랜잭션 독립 사용은 @Transactional의 `Propagation.REQUIRED_NEW` 속성

- 사례1는 **결제 처리와 알림**이다. 결제가 성공하면 알림을 보낼건데, 알림 전송 실패하더라도 결제는 성공적으로 이루어져야 한다. 이것도 충분히 가능하다. -> 이게 실제 서비스에서 고려할 만한 부분인 것 같다.

- 사례2은 **배치 작업 및 상태 업데이트**다. 대량 데이터를 처리하는 배치 로직에서 "상태 업데이트"를 독립 트랜잭션으로 실행해서 롤백을 당해도 상태는 업데이트 할 수 있는 방안이다.


```java
@Transactional
public void processBatch(List<Data> dataList) {
  for (Data data : dataList) {
      try {
          // 데이터 처리
          processData(data);
          statusUpdateService.updateStatus(data.getId(), "Processed");
      } catch (Exception e) {
          // 처리 중 오류가 발생해도 상태 업데이트는 독립적으로 진행
          statusUpdateService.updateStatus(data.getId(), "Failed");
      }
  }
}

-------------------------------
    
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void updateStatus(Long dataId, String status) {
// 상태 업데이트
	statusRepository.updateStatus(dataId, status);
}
```

<br><br>

### Oracle+Orange, H2, MySQL (비교)

참고: 테스트용 메모리DB가 아닌 실제 DB를 사용할 경우 **반드시 DB를 실행해서 오픈해야 함!**

H2와 MySQL 사용은 너무 간단하기도 하고 많이 했어서 생략! (**자세한건 application.yml 챕터 참고!**)

**Oracle은 build.gradle에 라이브러리 추가해서 연동하는건 H2나 MySQL처럼 간단하지만, Oracle 자체를 다운받아서 실행하는게 좀 난항이 많다.**

**웬만하면 전부 관리자로 설치 ㄱㄱ**

**용어 참고**

- **HOST** : IP나 도메인을 가진 장치(컴퓨터).
- **CLIENT** : HOST중에 정보를 요청하는 장치(컴퓨터).
- **SERVER** : 요청받은 정보를 제공하는 장치(컴퓨터).
- **오라클에서의 서버** : 오라클 데이터베이스가 설치된 장치(컴퓨터).
- **오라클에서의 클라이언트** : 네트워크를 통해서 데이터베이스를 제어하기 위한 소프트웨어(SQL Plus, SQL Developer, TOAD)를 사용하는 장치(컴퓨터).

- 오라클 버전별 특징도 다르니 주의
  - **시퀀스와 트리거**: 구버전 오라클이나 특정 요구사항에 따라 사용.
  - **IDENTITY 컬럼**: 오라클 12c 이상에서 지원, 더 간편하게 사용할 수 있음.

<br>

**오라클 필수 개념**<br>[문서 참고](https://okky.kr/questions/206807)

- **Oracle Server**는 말 그대로 서버 쪽의 Oracle DB고, **Oracle Client**는 이 Oracle Server(DB)에 접속하는 용도이다. Java의 경우 JDBC를 제공하기에 바로 Oracle 접속이 가능했던 것이다.

- **Orange(Tool)** 의 경우 이 Oracle Client를 이용해서 연동한다고 볼 수 있다! (주로 32bit 활용)

  <details><summary><b>문서속 내용 참고</b></summary>
  <div markdown="1"><br>
  **오라클 DBM 클라이언트에는 오라클 DBM 서버에 접속할 수 있는 프로그램들이 내장**되어 있습니다. 그런데, **Java 에서는 DB 에 상관없이 JDBC 라는 드라이버를 통해 직접 DB 에 연결**하는 표준을 가지고 있습니다. 오라클도 이 JDBC 드라이버를 제공하고 있는 것입니다. 즉, **Oracle Clinet 의 접속 관련 부분이 JDBC 에 이미 구현**되어 있고, 그 외 SQL*PLUS, orange 같은 툴들은 없습니다. JDBC 을 내장해서 관리하는 것이 구현된 것이 SQLDeveloper (Java로 만듦) 이구요. 단순히 Java 환경에서는 JDBC 만 있으면 되므로 Oracle client 는 필요없고, Java 가 아닌 토트, 오렌지 등에서는 Oracle Clinet 가 있어야 하는 거구요. 일부 툴에서는 Oracle Client 와 같이 많은 것을 설치해야만 사용 가능하지만, 일부 툴은 **Oracle instant client 와 같이 설치 없이 간단한 설정 만으로 접속을 가능하게 해주는 JDBC 와 같은 것으로 연결이 가능**합니다. 토드 최신 버젼 등은 지원할 겁니다. (12년 전 자료. 지금은 대부분 지원)
  </div>
  </details>

<br>

**오라클 vs MySQL 간단 비교**

MySQL의 Database 단위로 만들어서 접속하는것과 오라클에 하나의 DB에 접속해서 User단위로 만들어 사용하는것과 유사  
=> 오라클 DB는 각 사용자 별로 테이블, 인덱스, 뷰 등의 **객체가 따로 생성**

1. MySQL

   - 물리적으로 분리된 여러 데이터베이스 생성 가능

   - DATABASE = SCHEMA 개념 사용

2. Oracle

   - 하나의 큰 데이터베이스 인스턴스 사용
   - USER = SCHEMA 개념 사용
   - **또한, 너무 메모리 사용이 큼. "대기업"이 선호하지 "중소는 MySQL 사용!"**

**명령어로 참고 오라클 - sql*plus**

```sql
-- db 서버(STR)와 리스너 실행 필수
sqlplus username/password            -- 일반 접속 (본인: testUser/1234)
sqlplus / as sysdba                 -- SYSDBA로 접속 (대장)
sqlplus /nolog                      -- DB 접속없이 SQL*Plus 실행[1][3]
show user							-- 현재 사용자 확인
SELECT name FROM v$database;         -- 데이터베이스 이름 확인 (본인: STR)
SELECT table_name FROM user_tables;  -- 현재 사용자의 테이블 확인
EXIT 또는 QUIT   -- SQL*Plus 종료[3]
```

<br>

**오라클 설치**<br>[문서 참고](https://velog.io/@yeoonnii/Oracle-%EC%98%A4%EB%9D%BC%ED%81%B4-19c-%EC%84%A4%EC%B9%98%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C#%EC%84%A4%EC%B9%98-%EC%A4%91-%EC%98%A4%EB%A5%981--ins-20802-oracle-database-configuration-assistant%EC%9D%84%EB%A5%BC-%EC%8B%A4%ED%8C%A8%ED%96%88%EC%8A%B5%EB%8B%88%EB%8B%A4)

- 설치버전: Oracle(19LTS) + Oracle Client(11) + Orange(7) -> 아마도 Client, Orange는 32bit

  <details><summary><b>오라클 설치 자세히</b></summary>
  <div markdown="1">
  - [Oracle Software Delivery Cloud](https://edelivery.oracle.com/osdc/faces/SoftwareDelivery) 에서 oracle database download 검색 + oracle database client download 검색 -> 19c, 11 버전 설치!!
    - <img src="https://github.com/user-attachments/assets/3175e10e-37b0-4de3-81ed-e419eeb1f4f9" alt="image" style="zoom: 80%;" /> 
    - 소프트웨어만 설치로 간단히 설치 후 **Database Configuration Assistant 실행(=dbca)**
      - "관리자권한" cmd-dbca실행: db 이름 STR, SGA(1024),PGA(128) 에서 에러. 메모리 확인이 왜 안 된다지?!
      - `dbca -J-Doracle.assistants.dbca.validate.ConfigurationParams=false` 로 dbca 실행 시 해결!!!
      - 계정 비밀번호 oracle로 설정 했음
  - **sqlplus**
    - lsnrctl status 로 리스너 확인 - 잘 연결되어 있는 상태
    - sqlplus / as sysdba 로 db 접속 (sysdba는 root 권한)
    - select instance_name from v$instance; 로 db 잘 생성 되었나 db명 체크
    - exit 로 탈출
    - **db start?**
      - sqlplus / as sysdba 로 root로 접속 후
      - startup 로 db 실행
      - lsnrctl start 로 리스너 실행!
    - **db stop?**
      - lsnrctl stop 로 외부 접속 가능하게 해주는 리스너 종료
      - sqlplus / as sysdba 로 root로 접근 후 
      - shutdown immediate 로 db 종료
    - **services.msc 윈도우 서비스 화면 이동**
      - 오라클이 자동으로 실행되는 (리붓마다) 방지하기 위해 여기서 "수동"으로 바꾸기
      - 따로 만들어둔 **dbstart.bat** 파일로 오라클 필요 때 마다 실행하는 식으로 바꾸자.
      - ```
        //dbstart.bat 소스 -> 주석 다 지우기!
        //STR, Listener 필수 구동
        net start OracleServiceSTR
        net start OracleOraDB19Home1TNSListenerLISTENERR
        ```
        - OracleServiceSTR -> DB STR 기동!
        - OracleOraDB19Home1TNSListenerLISTENERR -> 리스너 기동!
      - <img src="https://github.com/user-attachments/assets/2f179e6e-b465-474e-8ebe-c1670cae6a82" alt="image" style="zoom:80%;" /> 
    - **오라클 삭제는 오라클 홈 디렉토리에 deinstall로 삭제하는거니까 주의**
  </div>
  </details>

**오렌지 설치**<br>[Orange 공홈](https://www.warevalley.com/ko/support/download-list) 에서 설치!! -> Oracle Server, Oracle Client, 64 or 32bit 지원유무 구분하여 설치!<br>본인은 32bit dbau(유니코드) 버전 dba - DBA Edition 설치 했음 (client를 32bit 설치해서 그랬을거임)

- 참고로 dba 버전은 체험용으로 1달인가? 만 무료ㅜㅜ

  <details><summary><b>오렌지 설치 자세히</b></summary>
  <div markdown="1">
  - **[추가 오렌지 참고 문서](https://m.blog.naver.com/skeletonflower/222147717396), [추가 참고+instance client 활용ver](https://hoing.io/archives/153#__70)**
    - instance client 활용 ver은 Oracle(19LTS) + Oracle Client(11) + Orange(7) 조합과 다른 방식일거다.<br>다음에 할 일 있으면 하고! 지금은 11버전 client 설치하는 방식으로 성공 했다.
  - **Oracle Client 에러 참고**
    - Oracle Base: C:\developer\app\oracle-client
    - Oracle Home: C:\developer\app\oracle-client\product\11.2.0\client_1
    - **sibar 에러 ORA-12560: TNS:프로토콜 어댑터 오류 뜸. 환장하겠네;**
      - 리스너가 문제 있을 시 뜨는 오류라 하나보다. [해결 문서](https://200-rush.tistory.com/entry/ORA-12560-Listener)
      - 현재 오라클 서버, 클라이언트 모두 설치했기에 sqlplus가 서버꺼인지 클라꺼인지 확인필요.
        - virtualbox 리눅스로 했으면 오라클 서버 따로 구동했으니 이런 에러도 안떳을텐데 ㅠ
        - **환경변수에 보면 "클라"께 위로 우선순위 되어있다 (PATH경로). "서버"를 위로 올리자.**
      - <img src="https://github.com/user-attachments/assets/b230f2da-7f14-47ff-b09f-3e6b3e7177db" alt="image" style="zoom:80%;" />
  -  **내 컴퓨터 (윈도우) 의 오라클 CLIENT의 TNSNAMES.ORA 파일의 TNS 정보 <-> ORACLE DATABASE 서버(LINUX.난 윈도우.)가 (LISTENER) 연결되어야한다.**
    - port=1521, ip=?
    - 오라클 CLIENT 설치된 서버의 tnsnames.ora 수정!!
      - ```
        TNS별칭 =
          (DESCRIPTION =
            (ADDRESS = (PROTOCOL = TCP)(HOST = 오라클 db 서버 ip)(PORT = db port))
            (CONNECT_DATA =
              (SERVER = DEDICATED)
              (SERVICE_NAME = db sid)
            )
          )
        ```
      - ```
        // 해당 내용 추가
        STR =
          (DESCRIPTION =
            (ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.??.??)(PORT = 1521))
            (CONNECT_DATA =
              (SERVER = DEDICATED)
              (SERVICE_NAME = STR)
            )
          )
        ```
    - 이러면 이제 오렌지 TNS에 뜬다!! 물론, ORA-28040 에러 도함꼐 ㅋㅋㅋ
      - Oracle Database, Oracle Database Client 둘의 버전이 맞지 않아서 뜨는 오류!
      - Oracle Database쪽의 sqlnet.ora 를 수정하자.
      - ```
        // sqlnet.ora 파일 수정 (추가) 
        SQLNET.ALLOWED_LOGON_VERSION_CLIENT = 10
        SQLNET.ALLOWED_LOGON_VERSION_SERVER = 10
        ```
    - **참고 설정과 팁 - 오렌지)**
      - 한번에 조회되는 SQL 길이 늘이기 -> 값이 너무 길면 중간에 잘리는 현상 방지
        - Tools -> Orange Options -> Common -> Session -> Array Size, Long 수정(5000,10000쯤?)
      - 한번에 조회하는 row 수 늘리기 
        - Tools -> Orange Options -> SQL Tool -> General -> Initial Fetch , Next Fetch 수정
        - 또한 Fetch as Need 체크박스 해제하면 그냥 전체 row건이 항상 조회!
      - 단축키
        - sql 실행 : ctrl + enter / 전체 실행 : F5
        - 주석처리 : ctrl + - / 주석처리 해제 : ctrl + shift + -
        - sql문 재배치 : (드래그)ctrl + shift +f
        - 저장 : ctrl + s / 불러오기 : ctrl + o / 새로운 탭 : ctrl + t
        - 모두 소문자 : ctrl + u / 모두 대문자 : ctrl + shift + u
        - Logon 창 띄우기 : ctrl + n 
          - 계정 변경 (계정마다 소유의 테이블이 다르기 때문)
          - 한번 맺었던 세션은 위에 남아있음
  </div>
  </details>

<br>

<br>

## 스프링 부트의 애노테이션들

중요: **스프링 빈** 설정은 자동 설정보다 **수동 설정이 우선순위 높다.**   
예로, @Bean으로 수동 등록과 @Service, @Component같이 자동 등록이면 @Bean 등록 우선

<details><summary><b>엔티티</b></summary>
<div markdown="1">
* @Entity : 스프링빈 자동 등록
* @Inheritance(strategy = InheritanceType.SINGLE_TABLE) : 상속을 **SINGLE_TABLE** 전략 사용선언
  * @DiscriminatorColumn(name = "dtype") : 상속의 **부모** 구분
  * @DiscriminatorValue("A") : 상속의 **자식** 구분
* @Getter @Setter : getter, setter 제공 -> lombok
* @Id @GeneratedValue : PK 할때 세트로 활용 -> @ID:PK, @GeneratedValue:자동숫자생성
* @Table(...) @Column(name = "member_id") : 테이블명, 컬럼명 매핑
* @ManyToOne(fetch=LAZY) : 지연로딩(LAZY), 다대일 관계 표시
  * @JoinColumn(name = "member_id") : 외래키 사용 - 주인이 할 일
  * @OneToMany(mappedBy = "member") : 주인은 Order라서 Order의 member를 의미
  * @JsonIgnore : 양방향 무한 반복의 문제를 해결 -> 안쓸거임.
    * **하지만 지양하고 DTO 방식으로 해결하는걸 지향한다.**
* @Embedded : JPA 내장 타입 쓸 때 사용
  * @Embeddable : JPA 내장 타입 선언
</div>
</details>

<br>

<details><summary><b>레포지토리</b></summary>
<div markdown="1">
* @Repository : 스프링빈으로 등록, JPA 예외를 스프링 기반 예외로 예외 변환
* @PersistenceContext : 엔티티메니저( EntityManager ) 주입
* @PersistenceUnit : 엔티티메니터팩토리( EntityManagerFactory ) 주입
* **`@RequiredArgsConstructor` : lombok의 어노테이션인데 이것도 `엔티티매니저` 주입을 제공해주므로, 이것과 `final` 조합으로 리포지토리, 서비스 둘다에서 활용하는것을 권장**
  * 예를 들면, @Repository 로 스프링 빈에 등록한 레퍼지토리를 다른 코드에서 사용하고 싶을때 "주입" 을 통해서 사용할 수 있다.
  * @Autowired를 생성자위에 선언하면 "생성자 주입" 으로 사용할 수 있고, 필드위에 선언하면 "필드주입" 으로 사용할 수 있다.
    * 여기서 **"생성자 주입"** 관점이 **@RequiredArgsConstructor** 로 대체 가능하다.
    * 자동으로 생성자 생성해서 제공해준다.
* 보통 만드는 DB와 연관 메서드 : save(), findOne(), findAll(), findByName() 등등...
  * `EntityManager` 의 함수들은 잘 구현되어있어서 `find()` 함수를 쓸때 값 없을때 null로 잘 반환해줌
  * **우리가 직접 쿼리문 날려서 `find()` 함수를 구현할 때는 이 null 처리를 잘 해줘야한다는점**
    * **TIP : `getResultList()` 형태로 (즉, List) 값을 받으면 null처리가 매우 간단**
</div>
</details>
<br>

<details><summary><b>서비스</b></summary>
<div markdown="1">
* @Service : 스프링빈으로 등록
* @Transactional : 트랜잭션, 영속성 컨텍스트
  - readOnly=true : 데이터의 변경이 없는 읽기 전용 메서드에 사용, **영속성 컨텍스트를 flush 하지 않으므로 약간의 성능 향상**(읽기 전용에는 다 적용)
  - 데이터베이스 드라이버가 지원하면 DB에서 성능향상
  - **이와 같은 이유로 `@Transactional(readOnly = true)` 로 사용 및 쓰기모드 필요할때면 해당 메소드단에 `Transactional` 을 또 선언해서 쓰기모드까지 사용**
    - 기본값이 `readOnly=false` 
* @Autowired : 필드 주입
  - **앞전에 언급한것처럼 `@Autowired` 대신에 `@RequiredArgsConstructor` 와 `final ` 를 활용**
- 보통 만드는 비지니스 로직 메서드 : join(), findMembers(), findOne() 등등...
</div>
</details>
<br>

<details><summary><b>컨트롤러</b></summary>
<div markdown="1">
* @Controller : 스프링빈으로 등록, 핸들러 매핑 대상으로 기억
* @RequestMapping, @GetMapping, @PostMapping 등등..  : HTTP 매핑
  * **@RequestMapping을 클래스단**에 **@GetMapping, @PostMapping 을 메서드단**에 사용 권장
    * @RequestMapping은 **전역으로 경로** 지정하기 좋기 때문
    * @RequestMapping 은 GET,POST,PUT... 등 전부 허용하기 때문
  * @PostConstruct : 테스트용 데이터를 코드 실행하자마자 바로 넣을 수 있음 (테스트하기 수월!)  
    => 이름 그대로 생성자 생성된 후 시점으로 생각하자!
* @RestController : 뷰로 반환하는게 아닌 HTTP Body에 반환값 기입반환 **(API 만들시 적극 권장)**
  * @RestController는 @Contorller, **@ResponseBody** 등등을 포함하는 어노테이션
* @PathVariable("userId"), @RequestParam => @PathVariable 방식 권장
  - @RequestParam : 기존 url 쿼리 파라미터 방식 : ?userId=userA
    - 단, POST-HTML Form 방식도 body를 쓰지만 쿼리 파라미터 형식으로 저장되기 때문에 @RequestParam 을 사용 가능
    - 또한, 생략도 가능한데 **본인은 넣는걸 권장** 
    - 값이 반드시 넘어오지 않아도 됨을 명시할 수 있음 (required=false)
    - 기본값 설정도 가능 (defaultValue = "-1")!!
      - 널 뿐만아니라 "/username=" 이렇게 "" 빈값으로 넘어온 데이터도 기본값을 설정해줌
    - Map, MultiValueMap 형태로 값을 받아올수도 있음
  - @PathVariable("itemId") : 최신 트랜드인 경로 변수 방식 : /mapping/userA
    - 중요한점은 @PathVariable 로 매핑한 userA가 따로 Model을 활용하지 않아도,
    - 백엔드뿐만 아니라 프론트에서도 userA값을 사용가능하단 점이다.
      - 프론트에서 URL 문자열을 가져와 사용할 수 있으니까.
    - 물론, 그냥 **Model을 항상 데이터 보내는 용도로 사용**하고,
    - **@PathVariable을 url로 받은 값을 사용하는 목적**으로 활용하는게 젤 좋아보임.
* @ModelAttribute("form")
  * model.addAttribute 에도 담기고, form서밋 때 html에 있는 form 데이터를 매핑해서 변수에도 자동으로 담아줘서 변수선언도 따로 할 필요 없음
  * 또한, @ModelAttribute 를 생략하고 바로 **HelloData** 가 와도 동일하게 가능
  * **단, 너무 생략하면 햇갈릴 수 있어서 조심하자**
<div markdown="1">
```java
public String modelAttributeV1(@ModelAttribute HelloData helloData) {
  log.info(helloData.getUsername()); // 바로 변수 사용 가능!!
}
```
</div>
**참고로 특별한 사용법인 전역으로 Model에 항상 적용법**<br>
물론 static으로 따로 구현해두는게 성능상 더 좋음
<div markdown="1">
```java
@ModelAttribute("regions") // regions 이름으로 Model에 넣음
public Map<String, String> regions() {
  Map<String, String> regions = new LinkedHashMap<>();
  regions.put("SEOUL", "서울");
  regions.put("BUSAN", "부산");
  regions.put("JEJU", "제주"); 
  return regions;
}
```
</div>
* @RequestBody, @ResponseBody : HttpEntity 처럼 **HTTP 메시지 컨버터**가 **HTTP 메시지 바디**의 내용을 우리가 원하는 문자나 **객체 등으로 자동 변환**!!
  * 요청파라미터 @RequestParam, @ModelAttribute 랑은 전혀 관계없으니까 혼동 X
    * **왜냐하면, HTTP 메시지 Body 를 통해서 데이터가 넘어오는 경우이기 때문!!**
  * **요청오는건 RequestBody, 응답으론 ResponseBody**
    * **@RequestBody**를 활용할거면 꼭 Dto 형태 타입으로 인수 받을것(경험상)
      * **ObjectMapper 필요없이 자동**으로 바꿔줘서 편리
    * 요청으로 들어오는 json 데이터를 @RequestBody HelloData data 로 인해 Hellodata 객체로 바꾸고,
    * 반환 타입을 String이 아닌 HelloData로 하면 @ResponseBody 로 인해 return할때 응답body에 문자로 넣어준다 했는데 덕분에 json로 집어넣어준다.
    * 즉, json(요청)->객체->json(응답) 로 동작한다.
  * **(핵심!) 만약 @ResponseBody 가 없으면 뷰 리졸버가 실행 되어서 뷰를 찾아서 렌더링!!**
    * **참고로 @RestController 는 @ResponseBody 가지고있음!!**
<div markdown="1">
```java
@ResponseBody
@PostMapping("/request-body-json-v5")
public HelloData requestBodyJsonV5(@RequestBody HelloData data) {
  log.info(data.getUsername());
  return data;
}
```
</div>
* 뷰 반환, 데이터 반환 정리
  * **@Controller - View 반환**
  * @Controller + @ResponseBody - Data 반환
  * **@RestController - Data 반환**
* 쿠키 편리하게 조회 **@CookieValue**
</div>
</details>
<br>

<details><summary><b>ETC</b></summary>
<div markdown="1">
* @Slf4j : 로그를 바로 log로 사용 가능
  * **Log Level : error > warn > info(기본값) > debug > trace**
  * 보통 debug를 "개발서버용", info를 "운영서버용" 으로 사용
  * 기본값이 info라서 debug로 개발서버 프로필에 따로 설정 해주자
* @NotEmpty("에러 메세지 관련")
* @Data -> 롬복 -> **toString과 Equals오버라이딩도 자동**
  * @Getter, @Setter, @ToString, @EqualsAndHashCode, @RequiredArgsConstructor 적용
  * `member.equals(findMember)` 바로 사용 가능
* @SpringBootApplication : 톰캣 내장(서버)
* @Valid + @NotEmpty(message="회원 정보필수") 이런식으로 같이 사용
  * 예전꺼라서 의존성 추가 필수
  * `implementation 'org.springframework.boot:spring-boot-starter-validation'`
* **@AllArgsConstructor : 생성자 대신 만들어줘서 필드만 선언**
  * 참고로 EM 생성자 주입 방식인 `@RequiredArgsConstructor` 와 햇갈리지 말것
* @Value : application.properties에 선언한 변수 사용
* @EnableCaching : Spring Boot Cache 사용을 선언
</div>
</details>

<br>

<br>

## Thymeleaf 로 웹 개발 TIP

<details><summary><b>build.gradle 참고</b></summary>
<div markdown="1">
```groovy
plugins {
	id 'java'
	id 'org.springframework.boot' version '3.0.2'
	id 'io.spring.dependency-management' version '1.1.0'
}
group = 'com.dau'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '17'
configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}
repositories {
	mavenCentral()
}
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'org.springframework.boot:spring-boot-devtools' // 빠른 reload
	compileOnly 'org.projectlombok:lombok'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	runtimeOnly 'com.h2database:h2' // H2
	// https://docs.spring.io/spring-boot/docs/current/reference/html/dependency-versions.html#appendix.dependency-versions.coordinates
	implementation group: 'com.mysql', name: 'mysql-connector-j', version: '8.0.33' // MYSQL
	//test lombok 사용
	testCompileOnly 'org.projectlombok:lombok'
	testAnnotationProcessor 'org.projectlombok:lombok'
	// 검증기 사용
	implementation 'org.springframework.boot:spring-boot-starter-validation'
	implementation 'org.springframework.boot:spring-boot-starter'
	// 캐시
	implementation 'org.springframework.boot:spring-boot-starter-cache'
}
tasks.named('test') {
	useJUnitPlatform()
}
```
</div>
</details>

<br><br>

### (1) HTTP 중요지식

<details><summary><b>redirect vs forward</b></summary>
<div markdown="1">
- **비유 예시(고객:클라, 상담원:서버, 123or124:URL)**
  - **첫번째 사례(redirect)**
    1. 고객이 고객센터로 상담원에게 123번으로 전화를 건다.
    2. 상담원은 고객에게 다음과 같이 이야기한다. "고객님 해당 문의사항은 124번으로 다시 문의 해주시겠어요?"
    3. 고객은 다시 124번으로 문의해서 일을 처리한다.
  - **두번째 사례(forward)**
    1. 고객이 고객센터로 상담원에게 123번으로 전화를 건다.
    2. 상담원은 해당 문의사항에 대해 잘 알지 못해서 옆의 다른 상담원에게 해당 문의사항에 답을 얻는다.
    3. 상담원은 고객에게 문의사항을 처리해준다.
* **redirect의 경우 최초 요청을 받은 URL1에서 클라이언트에 redirect할 URL2를 리턴하고, 클라이언트에게 전혀 새로운 요청을 생성하여 URL2에 다시 요청을 보낸다. 따라서 처음 보냈던 최초의 요청정보는 더이상 유효하지 않게된다.**
  * web container는 redirect 명령이 들어오면 웹 브라우저에게 다른 페이지로 이동하라는 명령을 내린다.(첫번째 사례의 경우, 고객은 전화를 끊고 124번으로 다시 전화를 건다)
    * 다른 web container에 있는 주소로 이동 가능(ex: 123 -> 124)
  * 새로운 페이지에서는 request, response객체가 새롭게 생성된다.(123번에서 고객이 요청했던 문의사항은 사라지고 124번으로 다시 걸어서 요청한 문의사항을 다시 말해야한다.)
* **forwar방식은 다음 이동한 URL로 요청정보를 그대로 전달한다. 말 그대로 forward(건네주기)하는 것이다. 그렇기 때문에 사용자가 최초로 요청한 요청정보는 다음 URL에서도 유효하다.**
  * web container 차원**(서버단)**에서의 페이지 이동, 실제로 웹 브라우저는 다른 페이지로 이동했는지 알 수 없다.(두번째 사례의 경우, 고객은 상담원이 누구한테 물어봤는지 알 수 없다.)
    * 동일한 web container에 있는 페이지로만 이동이 가능하다.
  * 현재 실행중인 페이지와 forward에 의해 호출될 페이지는 request, response 객체를 공유한다.(고객이 요청한 문의사항은 고객이 전화를 끊을 때까지 유효하다.)
* **차이점**
  * URL의 변화여부: redirect(변화O), forward(변화X)
  * 객체의 재사용여부 : redirect(재사용X), forward(재사용O)
* **언제 사용하는게 바람직한가?**
  * 시스템(session, DB)에 변화가 생기는 요청(로그인, 회원가입, 글쓰기)의 경우 **redirect**방식으로 응답하는 것이 바람직
  * 시스템에 변화가 생기지 않는 단순조회(리스트보기, 검색)의 경우 **forward**방식으로 응답하는 것이 바람직
</div>
</details>
<br>

<details><summary><b>PRG Post/Redirect/Get - POST를 무한한 재요청 문제 해결 패턴</b></summary>
<div markdown="1">
* **웹 브라우저의 새로고침은 마지막 서버에 전송한 데이터를 다시 전송한다.**  
* **따라서 POST 적용후 새로고침을 하면 계속 POST 보내는 문제가 발생하므로 이를 Redirect를 통해서 GET으로 요청하는 방식으로 해결할 수 있다.**
  * Redirect를 사용해야지만 POST 보내는 URL을 벗어나기 때문!!
* **RedirectAttributes 추천** -> Redirect는 원래 연결 끊어지니까 자원 재전송 필요시!!
  * Redirect 할때 Model처럼 파라미터를 추가해서 간편히 넘겨줄 수 있고, URL 인코딩 문제에서 자유롭다!
    * `"redirect:/basic/items/" + item.getId()` 는 인코딩 문제가 발생할 수 있는데,
    * `"redirect:/basic/items/{itemId}"` 는 인코딩 문제에서 자유롭다.
  * **특히 Status 정보를 파라미터로 넘김으로써 `th:if` 문법으로 "저장완료" 표시도 나타내는데 많이 사용한다.**
    <div markdown="1">
    ```java
    // 저장 성공 상태를 파라미터로 전달 (파라미터로 URL에 추가됨)
    // ex: URL...?status=success
    redirectAttributes.addAttribute("status", "success");
    <!-- "저장 완료" 메시지 표시 -->
    <div th:if="${status == 'success'}">
        <p>저장 완료!</p>
    </div>
    ```
    </div>
  * **따라서 Redirect 할때는 RedirectAttributes.addAttribute() 추천, html 반환(렌더링) 할때는 Model.addAttribute() 추천**
    * `redirectAttributes.addAttribute` 는 파라미터로 전송되므로 **@RequestParam(defaultValue = "")** 등으로 간편히 사용가능!!
    * 참고: `redirectAttributes.addFlashAttribute` 의 경우 불가능!! 파라미터 전송이 아니고 딱 한번 세션에 저장해서 전달하는 방식이라서!! 아마 @ModelAttribute로 받아질걸??
</div>
</details>
<br>

<details><summary><b>웹 브라우저에서의 redirect 특징</b></summary>
<div markdown="1"><br>
웹 브라우저는 3xx 응답의 결과에 **Location 헤더가 있으면, Location 위치로 자동 이동**한다.<br>
<img src="https://github.com/user-attachments/assets/888d11b2-be6e-4089-b52b-5b6f852e9eb3" alt="Image" style="zoom:80%;" />
<br>
1. url : /event로 get을 요청
2. 하지만 url이 /event에서 /new-event로 바뀌어서 서버가 다시 Location으로 응답
3. 자동 리다이렉트로 url 변경, 클라이언트 단에서 스스로 자동 리다이렉트
4. 다시 /new-event로 get 요청 -> 처음부터 다시 요청한다.
5. 정상적으로 성공했기에 200 OK 응답
</div>
</details>

<br>

**Content-Type 헤더 기반 Media Type 과 Accept 헤더 기반 Media Type**

* text/html, application/json 같은 content-type 을 의미
* **요청때나 응답할때나 body를 사용할때는 필수로 존재 및 서로 맞게 요청해야 함**

<br>

**API URI 설계에는 "리소스"가 중요하다. "행위"는 메서드(get, post 등)로 구분하자.**  
**단, 실무에서는 행위(동사)를 URI에 작성해야 할 때도 좀 있는데 이를 "컨트롤 URI"라 부른다.**

<details><summary><b>HTTP로 클라이언트 -> 서버 데이터 전송 4가지 상황 (참고: 전달 방식은 크게 2가지-쿼리 파라미터, 메시지 바디)</b></summary>
<div markdown="1"><br>
1. **정적** 데이터 조회 -> 쿼리 파라미터 미사용
   - 이미지, 정적 텍스트 문서
2. **동적** 데이터 조회 -> 쿼리 파라미터 사용
   - 주로 검색, 게시판 목록에서 정렬 필터(검색어)
3. **HTML Form**을 통한 데이터 전송 -> GET은 쿼리 파라미터로, POST는 메시지 바디로 자동 작성!
   - 회원 가입, 상품 주문, 데이터 변경 
   - Content-Type: application/x-www-form-urlencoded
     - form의 내용을 메시지 바디를 통해서 전송(POST로 해보면 나옴)
     - 전송 데이터를 url encoding 처리
       - 예) abc김 -> abc%EA%B9%80
   - Content-Type: multipart/form-data
     - 파일 업로드 같은 바이너리 데이터 전송시 사용
     - 다른 종류의 여러 파일과 폼의 내용 함께 전송 가능(그래서 이름이 multipart)
4. **HTTP API**를 통한 데이터 전송 -> 이하 동문
   - 회원 가입, 상품 주문, 데이터 변경
   - 서버 to 서버, 앱 클라이언트, 웹 클라이언트(Ajax)
   - Content-Type: application/json
</div>
</details>

<details><summary><b>HTTP API 설계 2가지로 "컬렉션 기반"과 "스토어 기반" (+HTML FORM)</b></summary>
<div markdown="1"><br>
**크게 2가지로 "컬렉션 기반"과 "스토어 기반"으로 나눠볼 수 있다. (대부분 컬렉션 방식 씀)**<br>
**HTML FORM 방식은 애초에 GET, POST만 지원한다. (PUT기반 아니니까 컬렉션이지)**
<br>
**1. API 설계 - 컬렉션 기반(POST)**
- 회원 목록 /members -> GET  
  회원 등록 /members -> POST  
  회원 조회 /members/{id} -> GET  
  **회원 수정 /members/{id} -> PATCH, PUT, POST**  
  회원 삭제 /members/{id} -> DELETE
  - **회원 수정에 개념적으론 PATCH 사용이 제일 좋다.**
- 클라이언트는 등록될 리소스의 URI를 모른다.
  - 회원 등록 /members -> POST
  - POST /members
- **서버가 새로 등록된 리소스 URI를 생성**해준다.
  - HTTP/1.1 201 Created   
    Location: /members/100
- 컬렉션(Collection)
  - 서버가 관리하는 리소스 디렉토리
  - 서버가 리소스의 URI를 생성하고 관리
  - 여기서 **컬렉션은 /members**
<br>
**2. API 설계 - 스토어 기반(PUT)**
- 파일 목록 /ﬁles -> GET  
  파일 조회 /ﬁles/{ﬁlename} -> GET  
  파일 등록 /ﬁles/{ﬁlename} -> PUT  
  파일 삭제 /ﬁles/{ﬁlename} -> DELETE  
  파일 대량 등록 /ﬁles -> POST
- 클라이언트가 리소스 URI를 알고 있어야 한다.
  - 파일 등록 /ﬁles/{ﬁlename} -> PUT
  - PUT /ﬁles/star.jpg
- **클라이언트가 직접 리소스의 URI를 지정**한다.
- 스토어(Store)
  - 클라이언트가 관리하는 리소스 저장소
  - 클라이언트가 리소스의 URI를 알고 관리
  - 여기서 **스토어는 /ﬁles**
<br>
**3. HTML FORM 사용 - GET, POST**
- 회원 목록     /members -> GET  
  **회원 등록 폼 /members/new -> GET**  
  **회원 등록     /members/new, /members -> POST**  
  회원 조회     /members/{id} -> GET  
  회원 수정 폼 /members/{id}/edit -> GET  
  회원 수정     /members/{id}/edit, /members/{id} -> POST  
  회원 삭제     /members/{id}/delete -> POST
  - URI 안바뀌는 /members/new 방식을 좀 더 선호하고, /members로 등록하는 사람도 있음.
  - GET, POST만 지원하니까 이런 제약을 해결하고자 동사를 사용한 **컨트롤 URI 방식도 많이 사용.**
<br>
**참고 문서: https://restfulapi.net/resource-naming**
- 문서(document)
  - 단일 개념(파일 하나, 객체 인스턴스, 데이터베이스 row)
  - 예) /members/100, /ﬁles/star.jpg
- 컬렉션(collection) -> **주로 이 방식만 접할거임.ㅇㅇ.**
  - 서버가 관리하는 리소스 디렉터리
  - 서버가 리소스의 URI를 생성하고 관리
  - 예) /members
- 스토어(store) 
  - 클라이언트가 관리하는 자원 저장소
  - 클라이언트가 리소스의 URI를 알고 관리
  - 예) /ﬁles
- 컨트롤러(controller), 컨트롤 URI
  - 문서, 컬렉션, 스토어로 해결하기 어려운 추가 프로세스 실행
  - 동사를 직접 사용
  - 예) /members/{id}/delete
</div>
</details>

<br><br>

### (2) Thymeleaf 문법

**타임리프 문법**

* **핵심** : 서버로 실행(뷰 템플릿 사용)하면 타임리프 문법들이 적용해서 **동적으로 변경!**
  
  * 스프링 부트는 "뷰 리졸버" 를 자동 등록하는데, 이때 설정파일에 등록한  `spring.mvc.view.prefix , spring.mvc.view.suffix` 정보를 사용해서 등록한다.
  * "뷰 리졸버" 에 **필요한 "경로" 를 설정**하는 부분인데 요즘 Thymeleaf 는 이것도 자동으로 등록해줘서 설정할 필요가 없다.
    * **혹시나 JSP 사용할 경우에는 이부분 기억해두자.**
  
* **타임리프 사용 선언**
  
  * `<html xmlns:th="http://www.thymeleaf.org">`
  
* **속성 변경**

  * `th:href="@{/css/bootstrap.min.css}"`
  * `th:onclick="|location.href='@{/basic/items/add}'|"`
  * `<td th:text="${item.price}">10000</td>`
  * `th:value="${item.id}"`
  * `th:action`
  * ... 등등 매우 다양
  
* **URL 링크표현식 - @{...}**
  
  * `th:href="@{/css/bootstrap.min.css}"`
  * `th:href="@{/basic/items/{itemId}(itemId=${item.id})}"`
    * 생성된 링크: `http://localhost:8080/basic/items/1`
  * `th:href="@{gallery/productDetail/(id=${item.id})}"`
    * 생성된 링크: `http://localhost:8080/gallery/productDetail/?id=1`
    * 심화) `th:href="@{/basic/items/{itemId}(itemId=${item.id}, query='test')}" `
      * 위 2개 둘 다 사용한 방식임. **{itemId}(itemId=${item.id})랑 (id=${item.id})**
      * 생성된 링크: `http://localhost:8080/basic/items/1?query=test`
  * **간편) `th:href="@{|/basic/items/${item.id}|}"`**
    * **"리터럴 대체" 문법도 적용가능 => 이거 함께 쓰자(아래 참고)**
  
* **리터럴 대체 - \|\...\|**
  
  * 타임리프에서 **문자**와 **표현식** 등은 분리되어 있기 때문에 **더해서 사용**해야 한다.
    * `<span th:text="'Welcome to our application, ' + ${user.name} + '!'">`
  * 다음과같이 리터럴 대체문법을 사용하면, **더하기 없이 편리**하게 사용할 수 있다.
    * `<span th:text="|Welcome to our application, ${user.name}!|">`
    * `th:onclick="|location.href='@{/basic/items/{itemId}/edit(itemId=${item.id})}'|"`
  
* **변수표현식 - ${...}**

  * `<td th:text="${item.price}">10000</td>`
  
* **반복출력 - th:each**

  * `<tr th:each="item : ${items}">`
  
  * 컬렉션의 수 만큼 `<tr>..</tr>` 이 하위 태그 반복 생성!
  
  * ```html
    <table>
        <tr th:each="item : ${items}">
            <td th:text="${item.id}"></td>
            <td th:text="${item.name}"></td>
            <td th:text="${item.price}"></td>
        </tr>
    </table>
    ```
  
* **조건문 - th:if or Default**
  
  * **th:if 문들은 false인 경우 아예 태그를 렌더링을 안함. 그럴 경우 사용!!**  
    param?의 ?는 null safe 처리 지원
    * `<h2 th:if="${param?.status}" th:text="'저장 완료'"></h2>`
    * `<h2 th:unless="${param?.status}" th:text="'저장 실패'"></h2>`
  * **삼항 연산자 + Default(=Elvis 연산자) 경우**
    * 삼항 연산자: `th:text="|B1 ~ B${(totalCount!=null) ? (totalCount/10+1) : '??'}F|"`
      * 실제로 **B1 ~ B4F** 이런식으로 출력
    * Default(=Elvis 연산자) 활용 : `th:text="|B1 ~ B${(totalCount) ?: '??'}F|"`
      * Default는 **totalCount**가 유효한 값이 있으면 그 값을 사용!<br>0이거나 null이면 **"??"가 출력!** -> 즉, 자동으로 null을 잡아줌! 
      * 다만, `(totalCount/10+1)` 이라면?<br>null/10+1 로 에러떠서 이런 경우는 위처럼 **it-then-else 사용**
    * 추가정보) `"${data}? : _"` 라면? 
      * No-Operation : "_" 로써 마치 타임리프 실행 안한것처럼 동작
  
* **변수선언 - th:with**
  
  * `th:with="first=${users[0]}"` -> frist 로 재사용 가능
  
  * ```html
    <div th:with="first=${users[0]}">
        <p th:text="${first.name}">User Name</p>
    </div>
    ```
  
* **태그 인식 유무 - text, utext == [[...]], [(...)]**
  
  * text vs utext -> 속성 사용
    - th:text = Hello \<b>Spring!\</b>
    - th:utext = Hello **Spring!** -> 진하게 태그(\<b>) 자동 적용된 모습
  
  * [[...]] vs [(...)] -> 속성이 아니라 컨텐츠 안에서 직접 출력!
    - [[...]] = Hello \<b>Spring!\</b>
    - [(...)] = Hello **Spring!**
  
* **편의 객체 제공 - param, session 등**
  
  * `param.title` 같이 파라미터 바로 접근 가능하게 **편의 객체를 제공**
  * `session.user.name` 은 세션 바로 접근
  
* **비교연산(ex: >) - HTML 엔티티 주의!!** 
  
  * `>` : gt 로 표기 한다.
  
* **타임리프 파서 주석 :** `<!--/* [[${data}]] */-->`
  
  * 참고로 `/*사이에서 여러줄 가능*/`
  * 일반 HTML 주석과 비슷하지만, `/*`와 `*/` 사이에 있는 내용은 **타임리프의 파서에 의해 처리 된 후 렌더링 때 삭제**되는 것
  
* **블록 - \<th:block>**
  
  * `<th:block>` 는 타임리프가 제공하는 유일한 자체 **"태그"**
  
  * **렌더링 할때는 아예 태그가 삭제**
  
  * **\<div> 로 데이터 잘 표현 했을 경우 \<th:block> 추가하면 깔끔**
  
  * ```html
    <!-- 렌더링 후 아래 div 태그만 남게 되는 것 -->
    <th:block th:each="item : ${items}" th:object="${item}">
      <div th:class="${item.id==items[0].id}? 'gallery-item-first' : 'gallery-item-many'" style="flex:1; padding-top: 30px;">
      </div>
    </th:block>
    ```
  
* **fragment, js**
  
  * fragment : 코드 재사용
  
    * fragment생성: `<head th:fragment="head(title)">`
  
    * ```html
      <!DOCTYPE html>
      <html xmlns:th="http://www.thymeleaf.org">
        <head th:fragment="head(title)">
          <!-- Required meta tags -->
          <meta charset="utf-8">
          <meta name="viewport" content="width=device-width, initial-scale=1">
      
          <!-- Bootstrap CSS -->
          <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.1/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-4bw+/aepP/YC94hEpVNVgiZdgIC5+VKNBQNGCHeKRQN+PtmoHDEXuppvnDJzQIu9" crossorigin="anonymous">
      
          <!-- 커스텀(기본) CSS -->
          <link rel="stylesheet" type="text/css" media="all" 
                href="../static/basic.css" th:href="@{/basic.css}"/>
      
          <title th:text="${title}">타이틀 화면</title>
      
        </head>
        <body>    
        </body>
      </html>
      ```
  
    * fragment 적용: `<head th:replace="fragments/head :: head('스튜디오')">`<br>**fragments/head** 는 **resources/templates/fragments/head.html** 경로를 의미
  
    * ```html
      <head th:replace="fragments/head :: head('스튜디오')">
      </head>
      ```
  
  * js : 타임리프 문법 inline으로 javascript 에서 사용 가능 + css도 마찬가지
  
    * ```html
      <script th:inline="javascript">
      var pageCount = /*[[${(totalCount/10)+1}]]*/ null;
      // 물론, /* */ 주석을 제거해도됨. 단지 빨간줄 떠서 추가함.
      ```
  
* **\<input>과\<label> 에서 th:for로 id값 연결 하는 편 -> 보통 form 에 잘 구성**
  
  * 동적 id 인식도 지원 됨! - `#ids.prev()`
  
  * 참고: \#ids.prev('regions')를 사용한 이유는 label에서 for 속성에 input의 id 속성과 맞추기 위해서 입니다.
  
    이것을 맞추어 두어야 label을 선택했을 때, 그러니까 **글자를 선택했을 때도 input 요소가 선택되기 때문**입니다.
  
  * HTML의 기본 동작: `label`의 `for` 속성이 `input`의 `id`와 일치하면, 사용자가 `label`을 클릭할 때 브라우저가 자동으로 해당 `input` 요소에 포커스를 주도록 설계되어 있습니다.
  
    ```html
    <!-- 대충 이런느낌으로 연결 해두면 된다는 거~! -->
    <div class="container" th:each="region : ${regions}">
        <label th:for="${region.id}">지역 선택:</label>
        <input type="text" th:id="${region.id}" placeholder="지역을 입력하세요">
    ```
  
* **보통 \<table> \<tr> \<td> \<th> 형태로 데이터를 표현** 

* **젤 중요!! th:field 는 "검증"에 매우 유용해서 그냥 사용가능하면 무조건 사용**<br>**여러개 쓸땐 th:object로 관리쉽게끔 함께 고고**
  
  * value 속성에 값 자동 삽입 + **id, name 속성 자동 생성** + 체크박스도 자동체크 등 -> **input태그나 체크박스**에 주로 사용 (그냥 다 사용하면 되긴 해~)
  * `*{...}` : 선택 변수 식으로써 **th:object** 에서 선택한 객체에 접근
    * **\*\{\{...}}**: `*{date1}` 은 @DateTimeFormat 표현식(타입컨버터)이 안 먹히기 때문에 \*\{\{date1}} 로 지정하는 것이다.

<br>


**타임리프 + 스프링 통합 문법**

* **th:object, th:field, *{itemName} 활용**
  
  * Form과 함께 Input, 체크박스, 라디오버튼, 셀렉트 박스에서 주로 활용
  * TIP) addForm, editForm 이렇게 2개 따로 만드는게 개발하기 수월
  
* **(중요)"메시지, 국제화 기능"**
  
  * `application.properties` 에 `spring.messages.basename=messages` 를 추가!!
  * `messages.properties` 를 생성해서 messages에 담을 내용을 세팅
    * properties에 `page.addItem=상품 등록` 메시지 추가
    * 타임리프로 `<h2 th:text="#{page.addItem}">상품 등록</h2>` `
  * 여러개 추가할거면?? 예로 errors.properties 추가한다고 하면 =message, errors 이렇게 이어적으면 됨
  
* **(참고) nullsafe**
  * `th:if="${errors?.containsKey('globalError')}"` 에서 ?를 통해 null 로 나타나므로 if는 false로 반환
  * ?가 없으면 null.containesKey... 로 에러

* **"컨트롤러"에서 @GET** 으로 페이지 로딩할때 **item을 빈값이라도 선언**해둬서 Model에 담아 반환하는걸 권장

  * **검증 실패 때 forward로 "자원 재활용"이 됨.**

  * HTML 코드도 더 깔끔 -  if문으로 null인지 확인하는 코드 필요없이 그냥 item을 타임리프 문법으로 사용하면 되기때문

  * **Model에 빈 객체를 미리 담아 전달**하면, 타임리프가 폼을 렌더링할 때 **객체가 null인지 확인할 필요 없이 바로 사용할 수 있기 때문 (코드 더 깔끔)**

    * **폼 페이지에서 제출된 데이터**가 검증에 실패하면, 다시 폼 페이지로 돌아가야 합니다. 이때, 새로운 GET 요청(redirect)을 발생시키지 않고, **forward**로 같은 페이지를 다시 렌더링합니다.
    * 이렇게 하면 **입력한 값들이 그대로 유지**되므로, 사용자는 데이터를 다시 입력할 필요가 없습니다.<br>타임리프에서 `th:field`를 사용하면, **검증 실패 시 자동으로 폼 필드에 사용자가 입력한 값**을 다시 채워줍니다. 이게 가능하려면, 컨트롤러에서 **빈 값이 아닌 객체**를 넘겨주는 것이 중요합니다.

  * ```java
    @Controller
    public class ItemController {
    
        @GetMapping("/items/new")
        public String showItemForm(Model model) {
            // 빈 Item 객체를 Model에 추가
            model.addAttribute("item", new Item());
            return "itemForm";  // form 템플릿을 반환
        }
    
        @PostMapping("/items/new")
        public String createItem(@ModelAttribute("item") Item item, 
                                 BindingResult bindingResult,
                                 RedirectAttributes redirectAttributes) {
            // 검증 실패 시
            if (bindingResult.hasErrors()) {
                return "itemForm";  // 다시 폼을 forward하여 자원 재활용
            }
    
            // 성공적으로 저장한 후
            redirectAttributes.addAttribute("status", "success");
            return "redirect:/items/" + item.getId();
        }
    }
    ```

  * ```html
    <form th:object="${item}" th:action="@{/items/new}" method="post">
        <label for="name">Item Name</label>
        <input type="text" th:field="*{name}" id="name" />
    
        <label for="price">Price</label>
        <input type="number" th:field="*{price}" id="price" />
    
        <button type="submit">Save</button>
    </form>
    ```

<br><br>

### (3) Thymeleaf TIP - 필독!

참고 공식문서: [부트스트랩 공문](https://getbootstrap.com/docs/5.3/components/navbar/#toggler), [타임리프 공문](https://www.thymeleaf.org/doc/tutorials/3.1/usingthymeleaf.html#including-template-fragments)

**웹 개발은 VSCode(정적) + IntelliJ(동적)로 개발 -> 본인은 그냥 IntelliJ로 개발! (캐시설정ㄱㄱ)**

- **보통 VSCode 가 html 문법 잘 지원해서(플러그인 다양함+Go Live로 바로 실행 지원) 이것으로 개발<br>IntelliJ는 서버 실행하여 Thymeleaf 문법 적용할 때 주로 사용**
  - 근데, 이것도 캐시만 잘 설정하면 reload를 빠르게 가능!! -> 본인은 그래서 IntelliJ로 개발
  - 배포할땐 꼭 true로!! : `spring.thymeleaf.cache=false`,  `spring.thymeleaf.prefix=file:src/main/resources/templates/`

- **서버 동작 없이 html(정적)에서 텍스트 확인 위해 span태그 사이에 "23.05.22.16:00k"입력까지 한 것**<br>**\<span class="pt-2" th:field="\*\{\{date1}}" th:text="*\{\{date1}}">23.05.22.16:00k\</span>**

<br>

**반응형 웹 개발을 위해 "부트스트랩(+인라인스타일링)"+"basic.css(전체), 커스텀.css(부분)"**

* 부트스트랩은 자동으로 반응형(해상도)에 맞게 스타일 지정되어 있다. 따라서 가져다 사용만 하면 됨!<br>**단, 원하는 스타일대로 커스텀하고 싶다면 “부트스트랩 문법에 인라인 style" or "일반 CSS를 직접 커스텀” 하자.** 이때, 반복(재사용)되는것들만 일반CSS로 따로 커스텀하고, 그게 아니면 부트스트랩 사용하면서 인라인 스타일로 바로 작성하는게 좋겠다.
* **본인은 보통 basic.css로 전체 페이지에 적용할 css파일 만들고, 부분적으로 커스텀할 부분은 gallery.css같이 해당 페이지명으로 만들어서 적용 중이다.**

  <details><summary><b>주의점?</b></summary>
  <div markdown="1">
  - **부트스트랩**은 수 많은 것을 **자동 제공!!**
    - 일반적인 방식 `font-size:100px;` 는 `100px`로 글자가 **"고정"**
      - **따라서 반응형 웹 CSS는 원래 em, rem, vw, vh 같은 단위를 사용해줘야 한다.**
        - **em은 "부모"의 font-size의 크기에 따라서 결정**
        - **rem은?? -> em과 동일하지만 제일 root 부모의 font-size를 따름**
        - **vw, vh는 뷰포트 화면(장치:예로 브라우저)크기에 따라 사이즈 결정됨**
      - **따라서 본인은 `vw, vh`로 "부모"부분 먼저 크기 결정후 `em`으로 "자식" 부분 크기 결정** (장치 기준이 젤 부모가 되는게 좋다고 생각해서)
    - 부트스트랩 방식 `fs-1, fs-2 등` 은 **"반응형"** 으로 글자 크기 제공 (fs: font-size)
    - 부트스트랩만으로 해결이 안되어서 **css 혼합하여 커스텀 예시**
      - `<div class="d-flex custom-header-flex">`
      - **d-flex**는 부트스트랩이 제공하는 display:flex를 의미 (flex:1을 지원안함 ㅠ)
      - 따라서 **custom-header-flex** 부분은 style태그로 따로 flex:1을 적용한 방식
  - **스타일 우선순위**: inline stylet속성 -> style태그 -> css파일 (차례대로 우선순위)
  - **class, id 별 접근자**: id=#, class=.
  - **웹 폰트 적용??**: `font-family: 'SUITE-Regular', sans-serif;` 로써 해당 우선순위대로 폰트 적용 (suite 폰트는 외부파일)
  - **전역 변수 정의??: basic.css**
      <div markdown="1">
      ```css
      // 글꼴, 색상, 스타일 전역변수 정의
      @font-face {
        font-family: 'SUITE-Regular';
        /* src: url('../static/SUITE-Regular.woff2') format('woff2'); */
        src: url('/SUITE-Regular.woff2') format('woff2');
        font-weight: 400;
        font-style: normal;
      }
      :root {
        --line: #393A40;
        --main-1: #0C0C0C;
        --main-2: #323338;
        --text-2: #8E8F9B;
      }
      ```
      </div>
  - `&times;` **는 "X" 표시**: close 버튼을 이것으로 입력된 구조가 많음
  - **웹(프론트)에서의 검증(ex:비밀번호)**: 정규식 검사하는 js 문법 활용
    - `onkeypress` 방식을 사용! 단, 복붙을 구별X. 따라서 서버단에서도 추가 검증이 더욱 안전
      <div markdown="1">
      ```html
      <input type="password" class="no-spin" th:field="*{password}" id="password" name="password" pattern="\d+" required onkeypress='return checkNumber(event)'/>
      <script th:fragment="scripts3">
        // 비밀번호 정규식 등록
        function checkNumber(event) {
          var pw = event.key
          if (/^\d+$/.test(pw)) {
              return true;
            } else {
              return false;
            }
          return false;
        }
      </script>
      ```
      </div>
  * **텍스트 가로유지(텍스트 크기 초과 방지)**: `white-space: nowrap;` 스타일 적용
  * **flex로 0.98:0.02 로 비율 나눠도 덮어 씌어질 경우**: `max-width:92%` 로 해결
  * **hover, active 문법 실수 조심** : `.page-link:hover` 과 `.page-link.active` 처럼 hover은 ":", active는 "." 사용
  * **백그라운드 컬러 변경으로 개발 테스트 용이**: `bg-info, primary, secondary` 등으로 간편하게 적용!
  * **border은 항상 style을 지정**:  `solid` 나 굵기는 `width` 함께해야 잘 적용!
  </div>
  </details>

<br>

**Page 생성 기본!!**

- **HTML 태그 위치별 기본 사용 구조**

  - **\<html>** → thymeleaf 선언

    - **\<head>**

      - **\<meta>** → utf-8(문자인코딩), viewport(모바일 뷰크기 설정) : 이 2개는 필수 사용

      - **\<link>** → 주로 bootstrap(css), 커스텀css(ex:/basic.css) 선언

        - bootstrap(js)의 경우 \<script>에서!!!!

        - **css코드 예시?! basic.css, custom.css**<br>basic은 전역(기본) css, custom은 basic말고 적용하려고 추가로 만든 css

          <details><summary><b>basic.css → 글꼴, 색상, 스타일 전역변수 정의도 함!</b></summary>
          <div markdown="1">
          ```css
          /*
           * 글꼴, 색상, 스타일 전역변수 정의
           */
          @font-face {
            font-family: 'SUITE-Regular';
            /* src: url('../static/SUITE-Regular.woff2') format('woff2'); */
            src: url('/SUITE-Regular.woff2') format('woff2');
            font-weight: 400;
            font-style: normal;
          }
          :root {
            --line: #393A40;
            --main-1: #0C0C0C;
            --main-2: #323338;
            --text-2: #8E8F9B;
          }
          /*
           * Base structure
           */
           body {
              background-color: var(--main-1);
              /* SUITE 못 찾으면 sans-serif 사용 */
              font-family: 'SUITE-Regular', sans-serif;
              margin-top: 15vh;
           }
           .field-error {
              color: red; font-weight: 700; padding:10px;
           }
          /*
           * Header
           */
            nav {
              border-left-width: 0px;
              border-right-width: 0px;
              border-top-width: 0px;
              border-bottom-width: 0px;
              border-style: solid;
              border-color: var(--line);
            }
            .nav-item {
              position: relative;
              text-align: center;
              font-size: 1.2vw;
              width: 15vw;
              padding: 20px;
              padding-bottom: 30px;
            border-left-width: 0px;
            border-right-width: 1px;
            border-top-width: 0px;
            border-bottom-width: 0px;
            border-style: solid;
            border-color: var(--line);
          }
          /* 여기서 header는 navbar-nav 의 높이를 기준으로 정해진다고 볼 수 있음 */
          .navbar-nav {
            height: 15vh;
          }
          #nav-item-first {border-left-width: 1px; }
          .nav-link {
            color: white; font-weight: 700;
          }
          #custom-nav-item:hover,
          #custom-nav-item:active,
          #custom-nav-item.visited {
            background-color: var(--main-2);
          }
          .nav-item-inner {
            position:absolute; 
            bottom:0;
            left:0;
            font-size: 0.7em;
            color:white; font-weight: 400; letter-spacing: -0.14px; opacity: 0.6;
            padding: 16px;
          }
          /*
           * Main
           */
              .custom-container-default {
              padding-top: 5vh;
              padding-bottom: 5vh;
              padding-left: 15vw;
              padding-right: 15vw;
              }
              #enterBtn {
              background-color: var(--main-1); color: white;
              border-radius: 0px;
              }
              #enterBtn:hover{
              background-color: white;
              color: var(--main-1);
              border-radius: 10px;
              }
            /* 모달에 스타일링 */
            /* 스크롤을 숨기는 스타일 */
            .no-scroll::-webkit-scrollbar {
            width: 0px;
            }
            /* 스핀 버튼 숨기기 */
            input[type="number"]::-webkit-inner-spin-button,
            input[type="number"]::-webkit-outer-spin-button {
            -webkit-appearance: none;
            }
            /*
          * Footer
          */
          .custom-footer {
            color:var(--text-2);
            display: flex;
            justify-content: center; 
            align-items: center; 
            border-style: solid;
            border-top-width: 1px;
            border-left-width: 0px;
            border-right-width: 0px;
            border-bottom-width: 0px;
            border-color: var(--line);
            margin-left: 15vw; /* custom-container-default 의 padding 과 너비 맞출것 */
            margin-right: 15vw;
          }
          ```
          </div>
          </details>
          
          <details><summary><b>custom.css(=gallery.css)</b></summary>
          <div markdown="1">
          ```css
          .gallery-item-first{
            display: flex;
            flex-direction: row;
            padding-top: 20px;
            padding-bottom: 30px;
            /*height: 25vh;*/
          }
          .gallery-item-many{
            display: flex;
            flex-direction: row;
            padding-top: 20px;
            padding-bottom: 30px;
            /*height: 25vh;*/
            /* first와 차이점은 아래 border부분뿐 */
            border-color: var(--line);
            border-style: solid;
            border-left-width: 0px;
            border-right-width: 0px;
            border-bottom-width: 0px;
            border-top-width: 1px;
          }
          .page-link{
          background-color: var(--main-1);
          border-width:0px;
          font-weight: 500;
          text-align: center;
          color:white;
          }
          .page-link.active{
          background-color: white;
          color:var(--main-1);
          }
          .page-link:hover{
          background-color: var(--main-2);
          color:white;
          }
          ```
          </div>
          </details>

      - **\<title>** → 브라우저의 URL링크 상단바에 실제 제목

    - **\<body>** → 배경색, 커서 설정하기 좋지, 전체 폰트도~!

      - **\<style>** → css처럼 스타일링. 위 \<link> 스타일 보다 더 높은레벨 (실제 \<div style=""> 처럼 태그안에 스타일은 최상위레벨)
      - **\<nav>** → header 부분으로 시작~!
      - **\<div class="container">** 처럼 이제 쭉 레이아웃 형성 + 실제 웹 화면 구성 ㄱㄱ
      - **\<footer>** → 마지막은 footer로 마무리~!

    - **\<script>** → javascript 관련 모든 것 (+jquery, bootstrap 설치도 포함)
       bootstrap4까지는 jquery 사용 때문에 jquery먼저 설치코드 필요하지만, bootstrap5부터는 그런 의존성 없애서 꼭 상관없다~
       **물론, jquery 유용하니까 항상 설정하는것도 좋지.**

    <details><summary><b>html 전체 코드</b></summary>
    <div markdown="1">
    ```html
    <!doctype html>
    <html xmlns:th="http://www.thymeleaf.org">
      <head th:replace="fragments/head :: head('갤러리')">
      </head>
      <body>
        <style>
          .page-link{
            font-size:1.2vw;
            padding: 1vw;
          }
          /* body태그, id=enterBtn 에 적용 */
          @media all and (min-width: 1921px) and (max-width: 4096px) {
              body, #enterBtn {
                  cursor: url('/cursor.svg') 10 60, auto;
              }
          }
          @media all and (max-width: 1920px) {
              body, #enterBtn {
                  cursor: url('/cursor_1920.svg') 10 60, auto;
              }
          }
        </style>
        <!-- header -->
        <nav th:replace="fragments/header :: header">
        </nav>
        <!-- main 개발 -->
        <!-- 메인 그림 -->
        <div class="container-fluid p-0">
          <img class="img-fluid" src="../static/6.png" 
            th:attr="src=@{/6.png},title=#{logo},alt=#{logo}"
          style="width:100%;"/>
        </div>
        <!-- 본문  -->
        <div class="custom-container-default">
          <!-- 제목(층수) -->
          <div class="d-flex flex-column" style="min-height: 0vh; padding-bottom: 5vh;">
            <div class="container" style="text-align: center;">
            ...
            </div>
          </div>
          <!-- 작품 나열 -->
          <div class="d-flex flex-column" style="min-height: 80vh;"
          th:replace="fragments/item :: item">
            <div class="gallery-item-first">
              ...
            </div>
            <div class="gallery-item-many">
              ...
            </div>
          </div>
          <!-- pagination -->
          <br><br><br><br>
          <nav aria-label="Page navigation">
            <ul id="dyn_ul" class="pagination" style="justify-content: center;">
            </ul>
          </nav>
        </div>
        <!-- footer -->
        <footer th:replace="fragments/footer :: footer"
        class="custom-footer">
        </footer>
        <!-- Jquery CDN 로드 : 항상 최신 버전 사용 -->
        <script th:replace="fragments/scripts :: scripts4" src="https://code.jquery.com/jquery-latest.min.js"></script>
        <!-- bootstrap5(JS) CDN 로드 -->
        <script th:replace="fragments/scripts :: scripts1"  src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.1/dist/js/bootstrap.bundle.min.js" integrity="sha384-HwwvtgBNo3bZJJLYd8oVXjrBZt8cqVSpeBNS5n7C8IVInixGAoxmnlMuBnhbgrkm" crossorigin="anonymous">
        </script>
        <script th:replace="fragments/scripts :: scripts2">
          // nav 클릭때마다 배경색 변경 커스텀
          // 스크롤시 NavBar 불투명도 변경
        </script>
        <!-- 페이징 -->
        <!--    var pageCount = /*[[${(totalCount/10)+1}]]*/ null; // 총 페이지 크기 -> 통신으로 받음-->
        <script th:inline="javascript">
        ...
        </script>
      </body>
    </html>
    ```
    </div>
    </details>

- **conatiner+flex+position?! -> div태그로 class=container 사용해서 레이아웃 구성 꼭 해주고! 필요에 따라 flex, position사용을 하면서 구현 ㄱㄱ**

  - **container** 는 보통 위에서 아래로 쌓지. 그럼 내부엔 수직, 수평, 절대위치로 삽입하고 싶다?
  - 난 grid는 안쓰고 **flex** 사용해서 하는중. **아래 속성도 유용함.**
    - `text-center` : 텍스트 중앙 정렬(좌우)
    - `align-items-start,center,end` : items 정렬이므로 자신의 자식들 정렬
    - `align-self-start,center,end` : self 정렬이므로 자신이 정렬
  - 절대위치는 그냥 **position:absolute랑 relative** 씀
    - absolute 사용하는데, 중앙에 위치하고 싶어서 `top:50%, left:50%, transform: translate(-50%, -50%)` 
      - `top: 50%;`: 이 요소의 상단이 부모 요소의 50% 위치에 배치됩니다.<br>`left: 50%;`: 이 요소의 왼쪽이 부모 요소의 50% 위치에 배치됩니다.<br>`transform: translate(-50%, -50%);`: 이 속성은 요소를 자신의 너비와 높이의 50%만큼 위와 왼쪽으로 이동시킵니다. 결과적으로 이 두 가지 설정을 조합하면 요소가 화면 중앙에 위치하게 됩니다.

- **padding, margin?! → 굉장히 많이 사용!! 이것도 위와 같이 사용 굉장히 많이 함!**

  - `padding` : 요소 “내부”의 여백을 설정
    - **내부다 보니 배경색에 영향O**
    - **내부다 보니 전체 너비와 높이에 “포함”**
    
  - `margin` : 요소 “외부”의 여백을 설정
    - **외부다 보니 배경색에 영향X**
    - **외부다 보니 전체 너비와 높이에 “추가”!!!!!!!!**
    
    <details><summary><b>동작원리(사진포함)</b></summary>
    <div markdown="1"><br>
    **총 3개 박스: margin, padding, margin + padding**
    ```html
    <div style="margin: 20px; background-color: lightblue;">
      콘텐츠가 있는 박스
    </div>
    <div style="padding: 20px; background-color: lightblue;">
      콘텐츠가 있는 박스
    </div>
    <div style="padding: 20px; margin: 20px; background-color: lightblue;">
      콘텐츠가 있는 박스
    </div>
    ```
    <br>
    ![image](https://github.com/user-attachments/assets/da89b7e3-849d-4c82-b1e9-d5793d0800ba)<br>
    ![image](https://github.com/user-attachments/assets/ee779963-18e1-4fc8-a9de-db2540c18ed9)<br>
    ![image](https://github.com/user-attachments/assets/f3817275-edb9-422b-800e-1255bc10ba9f)<br>
    ![image](https://github.com/user-attachments/assets/6b0b8162-0d79-4c39-a002-12cc4c865bce)
    </div>
    </details>
  

* **fragment를 head, header, footer, modal, scripts는 꼭 사용 중**
  
  - head는 \<head>에, header는 \<nav>에, footer은 \<footer>에, modal은 \<div>(class명은 modal)에, scripts는 \<script>에 사용 중!
  - **modal은 공.문 참고!**
  
* **데이터 표현** : `table, td, tr 등등` 또는 `div로 잘 구현`

  * `th:each` + `<th:block>` 도 적절히 함께 활용

    <details><summary><b>참고 코드</b></summary>
    <div markdown="1">
    ```html
    <!-- 렌더링시 제거 block -->
    <th:block th:each="item : ${items}" th:object="${item}">
      <div th:class="${item.id==items[0].id}? 'gallery-item-first' : 'gallery-item-many'"
      style="flex:1; padding-top: 30px;">
        <div class="d-flex flex-column pe-4" style="flex:0.15;">
          <span class="" style="font-weight: 500; color: white; font-size: 1.2vw;" th:text="'No.'+*{No}">No.15</span>
          <span class="pt-2" style="font-weight: 400; color: var(--text-2); font-size: 1vw; letter-spacing: -0.16px;
          " th:field="*{{date1}}" th:text="*{{date1}}">23.05.22.16:00k</span>
        </div>
        <div class="d-flex flex-column pe-4" style="flex:0.42;">
          <span class="text-truncate" style="font-weight: 500; color: white; font-size: 1.2vw; width:25vw;" th:text="*{title}">최근에 있엇던 대외비</span>
          <span class="text-truncate pt-2" style="font-weight: 400; color: var(--text-2); font-size: 1vw; letter-spacing: -0.16px; width:25vw;
          " th:text="*{nickName} ">방문자가작성한닉네임</span>
          <a class="btn btn-light mt-4" type="button" id="enterBtn" onclick="redirectSavedBgm()"
          style="font-size: 1vw;
          align-self: flex-start; width: 12vw; padding:0.5em;"
          href="#" th:href="@{|/gallery/${pageId}/itemDetail/*{id}|}">
            <span class="fw-bold" id="enterBtn1" style="font-size: 1em; white-space: nowrap;" th:text="|*{No}전시실 입장|">15전시실 입장 test용</span>
          </a>
        </div>
        <div class="d-flex flex-column" style="flex:0.43;">
          <img class="img-fluid" src="../static/6.png"
             th:src="@{|/image/*{imgSrc}|}"
          style="height:15vh; border-radius: 10vh 10vh 0 0;"/>
        </div>
      </div>
    </th:block>
    ```
    </div>
    </details>

* **FORM 데이터** : `label, input, 체크박스 등등` 권장
  * `th:field`(name,id,value자동생성) 와 `*{...}` 랑 `th:object`(데이터 관리쉽게) 함께 사용 권장
  * `th:errors` 등등 도 함께 사용<br>-> ex: `<div class="field-error" th:errors="${item.imgSrc}">이미지 오류</div>`
    * 이 문장은 **`item.imgSrc` 필드에 오류가 발생했을 경우**에만 해당 `<div>` 태그가 렌더링되며, 오류 메시지가 출력됩니다. 오류 없으면 이 태그는 렌더링 되지 않음!
    * \<div> 태그의 기본 텍스트 "이미지 오류"는 오류 메시지가 없을 경우 기본 메시지로 사용할 수 있습니다. 그러나 보통은 **Spring Validation에서 오류 메시지를 자동**으로 가져옵니다.

* **문법 잘 활용**

  * \|\...\| : `<span th:text="|Welcome to our application, ${user.name}!|">`
  * @{} : 간편) -`th:href="@{|/basic/items/${item.id}|}"`
  * 등등 문법 정리 챕터 참고...

<br>

**PRG 패턴 적용(위에 PRG 정리한 내용 참고) - 무한 POST 방지**

* **추가로 forward 사용가능한 건 forward 형태로 하는게 효과적**

- **자원 재활용(forward) : 폼... 분리 가능한건 분리해서 작성 권장 - addForm, editForm**

  * **GET에 꼭 빈 값이라도 엔티티 Model에 삽입 - th:object 함께 사용**  

    * 예로 `@ModelAttribute("item") Item item` 사용 시 "넘어온 form데이터 없어도 Item은 생성!"
    * Employee employee = new Employee();  
      //... 데이터있으면 필드 setter  
      model.addAttribute("employee", employee); 를 자동으로 해주거덩 (그냥 Model이면 직접해야!)
  
  * POST에는 `@Validated @ModelAttribute("item") AddItemDto form, BindingResult bindingResult, RedirectAttributes redirectAttributes)` 이런식으로 파라미터 필수
  
  * **장점은 검증 실패시 html로 바로 return!! -> forward 이기 때문에 자원 재활용!! (redirect 안해도 되는거즤~)**
  
  * ```java
    if(bindingResult.hasErrors()) {
      log.info("error={}", bindingResult);
      return "studio-complete"; // studio-complete.html 반환 -> forward 로 자원 재활용
      // 어차피 "검증" 에 걸려서 DB 사용안하기에 PRG 패턴 상관없움
    }
    //성공로직...
    return "redirect:/gallery/{pageId}/itemDetail/{itemId}"; // PRG 패턴 적용
    ```

<br>

<details><summary><b>Model, @ModelAttribute("item"), forward, RedirectAttributes, UpdateItemDto, @PathVariable, @RequestParam</b></summary>
<div markdown="1">
* **API 말고 여기 웹 개발 컨트롤러에서 주로 사용한 것들인데 꼭 동작을 전부 이해해둬야 함**
* **Model 과 @ModelAttribute 잘 구분** <br>@ModelAttribute는 넘어온 form값을 Model.addAttribute() 를 자동! 결국 둘다 Model인거고, 직접 addAttribute() 할 거를 @를 붙여서 구현된 애노테이션을 활용해서 더 쉽게 해주는것
  * **(1)html로 return**때 데이터 자주 넘겨줬음(**forward로 볼 수 있음**)
  * **(2)return을 forward:/ 형태로 동일 컨트롤러내에서는 다른 곳으로 요청가능(서버내에서)**
    * **즉, forward로써 서버 내에서 요청을 다시 처리하는 것**이기 때문에, 실제로 **페이지가 이동하지 않고(URL변경X)** 서버 내부에서 **다른 컨트롤러 메서드로 요청을 넘기는** 역할(forward뜻=전달)
  * **(3)return을 html이 아닌 redirect로** 넘길시 새롭게 웹브라우저가 다시요청 하는것이므로 Model 값 사라져서 이땐 사용안하고 **RedirectAttributes 를 사용**
    * RedirectAttributes 로 데이터 쉽게 다시 넘겨줄 수 있기 때문
  * `@ModelAttribute("item") UpdateItemDto form` - Post때 주로 사용하며 Form데이터 자동 매핑 후 **Model에 item 이름으로 기록!** 
    * **UpdateItemDto** 만 사용시 @ModelAttribute 과 동일하되 Model에 UpdateItemDto 이름으로 기록하므로 이렇게 한 것!
    * form 데이터 넘길땐 UpdateItemDto 관련 데이터를 전부 서버로 form에서 넘겨줘야하며 이때 View에 안 보여줄 데이터는 **\<input> + hidden** 으로 넘겨주면 간단하다.
  <div markdown="1">
  * ```java
    //예시 컨트롤러 -> forward와 redirect
    @GetMapping() // default
    public String gallery() {
        log.debug("debug 테스트");
        log.debug("gallery() : 입장");
        return "forward:gallery/1"; // -> galleryPage() 함수로 토스 (서버 내에서)
    }
    @GetMapping("/{pageId}")
    public String galleryPage(@PathVariable int pageId, Model model) {
        log.debug("galleryPage() : 입장");
        log.debug("pageId : {}", pageId);
        List<Item> items = itemService.findAllWithPage(pageId);
        Long totalCount = itemService.findTotalCount();
        log.debug("items : {}, totalCount : {}", items.size(), totalCount);
        List<ItemDto> itemsDto = items.stream()
            .map(o -> new ItemDto(o))
            .collect(Collectors.toList());
        //        log.debug("item Id check : {}",items.get(0).getId());
        model.addAttribute("items", itemsDto); // gallery.html 에 넘길 데이터
        model.addAttribute("totalCount", totalCount);
        for(Item it : items)
            log.debug("itemId : {}, itemNo : {}", it.getId(), it.getNo());
        return "gallery"; // gallery.html 반환 (forward일걸)
    }
    @PostMapping("{pageId}/delete/{itemId}")
    public String deleteGalleryItem(@PathVariable Long pageId, @PathVariable Long itemId, @RequestParam String password, RedirectAttributes redirectAttributes) {
        Item item = itemService.findOne(itemId); // 이미 없으면 null
        if (item != null) {
            if(item.getPassword().equals(password)){
                log.debug("비번통과");
                itemService.initCachePosts(); // 캐시 초기화(페이지들 새로 No 업데이트 하기 때문)
                itemService.remove(item);
                //                List<Item> items = itemService.findAllWithNoPage(pageId.intValue()); // 캐싱
                List<Item> items = itemService.updateAllNo();
                itemService.updateTotalCount(); // 캐싱
                redirectAttributes.addAttribute("status", "deleteON");
                return "redirect:/gallery"; // gallery() 함수로 이동
            }
            else log.debug("비번실패");
        }
        redirectAttributes.addAttribute("pageId", pageId);
        redirectAttributes.addAttribute("itemId", itemId);
        redirectAttributes.addAttribute("status", "deleteOFF");
        return "redirect:/gallery/{pageId}/itemDetail/{itemId}"; // 기존 화면 다시 로딩
        // PRG 패턴 위해 Redirect
    }
    ```
  </div>
* **RedirectAttributes** -> return을 html이 아닌 **redirect로** 넘길시 새롭게 웹브라우저가 다시요청 하는것이므로 Model 값 사라져서(forward면 안사라지지만!) 이땐 사용안하고 **RedirectAttributes 를 사용**
  * **Model과 RedirectAttributes 사용방식이 파라미터에 선언해서 사용!! (코드 속 파라미터 참고)**
  <div markdown="1">
  * ```java
    @GetMapping("studio") // URL 매핑(GET)
    public String studio(Model model) {
        Long totalCount = itemService.findTotalCount();
        model.addAttribute("totalCount", totalCount);
        return "studio"; // studio.html 반환
    }
    //
    // @ModelAttribute("item") 매우중요!!
    //=> th:object를 item 사용하므로 반드시 Model에 "item"으로 담기게끔!
    @PostMapping("studioComplete")
    public String studioAdd(@Validated @ModelAttribute("item") AddItemDto form, BindingResult bindingResult,
                            RedirectAttributes redirectAttributes) throws IOException {
        redirectAttributes.addAttribute("status", "addON");
        return "redirect:/gallery/{pageId}/itemDetail/{itemId}";
    ```
  </div>
* **@PathVariable** - URL 뒤의 값 바로 사용 (자주 사용)
* **@RequestParam** - 쿼리 파라미터 값 가져올때 자주사용 (기본값 설정도 가끔사용)
* **상식**: get은 쿼리파라미터(url) 방식이고, post는 body에 데이터 담겨옴. from 데이터는 name,value 방식으로써 데이터 바인딩하기 수월
</div>
</details>

<br>

**타입 컨버터**

* DB에는 LocalDateTime(원하는..) 형태로 저장 후 HTML 출력 때 원하는 "타입 컨버터" 사용

* `@DateTimeFormat(pattern = "yy.MM.dd.HH:mm"), @NumberFormat(pattern = "###,###")` 등등 사용 가능

* 타임리프에 적용법 : **th:field=*\{\{\...}}**

  <details><summary><b>(보충) 타입 컨버터</b></summary>
  <div markdown="1">
  * **(1) 웹 - `@Requestparam, @ModelAttribute, @PathVariable` 스프링이 기본 지원**
    * 예로 `@PathVariable Long itemId` 는 자동으로 String->Long 타입변환
    * "확장 가능" 하고, "**애노테이션**"을 제공
      * **@DateTimeFormat**예시 : DB엔 LocalDateTime타입, Thymeleaf는 지정한 pattern 사용
      * **예로) th:field="*\{\{date1}}" 이런식으로 사용**
      <div markdown="1">
      ```java
      @Data
      static class Form {
          @NumberFormat(pattern = "###,###") // 타입 컨버터
          private Integer number;
          @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
          private LocalDateTime localDateTime; 
          // db엔 LocalDateTime 형태로 저장
          // Thymeleaf에선 지정한 "패턴"으로 출력
      }
      ```
      </div>
  * **(2) HTTP API (@ResponseBody 등) - 의 경우 지원하지 않는다(HttpMessageConverter 는 "컨버전 서비스 적용 불가")**
    * **이 경우에는 `Jackson 같은` 라이브러리에서 포맷터를 찾아 사용**
    * JSON->객체,  객체->JSON 등등 쉽게 타입 변환 가능
  * **자세히 정리하자면?**
    * **(1) 일반적인 폼 전송 (웹 애플리케이션)**
      - **Thymeleaf** 같은 템플릿 엔진을 사용해 **HTML 폼**을 전송할 때, Spring은 **자동으로 타입 변환**을 지원합니다. 예를 들어, 문자열을 **숫자**나 **날짜**로 변환하는 경우, `@RequestParam`, `@ModelAttribute`, `@PathVariable` 등의 애노테이션을 사용하여 **자동 타입 변환**이 됩니다.
      - 이때 **Spring의 ConversionService**를 사용하여 **String -> Integer** 또는 **String -> LocalDate** 같은 변환이 가능합니다.
    * **(2) HTTP API 응답 (`@ResponseBody`)**
      - **`@ResponseBody`**를 사용하는 경우, **HTML을 반환하는 게 아니라 데이터 (JSON, XML 등)를 반환**하는 것입니다. 이때 **Spring의 ConversionService는 적용되지 않습니다**.
      - 대신, **JSON 변환**을 처리하기 위해 **`HttpMessageConverter`**가 사용됩니다. 일반적으로는 **Jackson** 라이브러리가 Spring Boot에 포함되어 있어 **객체를 JSON으로 변환**해줍니다.
    * **중요한 차이점**은:
      - **HTTP API 응답**에서는 **타입 변환은 `HttpMessageConverter`가 담당**하며, **자동 타입 변환(ConversionService)는 적용되지 않습니다**.
      - **ConversionService**는 주로 **폼 데이터**(예: 템플릿 렌더링)에서 쓰이고, **`HttpMessageConverter`는 JSON 변환**처럼 **HTTP 메시지 본문을 처리**할 때 사용됩니다.
  </div>
  </details>

<br>

**예외처리(HTML 페이지 기본제공 사용)**

* 웹 에러 처리의 경우에는 기존 "스프링 부트 기본제공" (BasicErrorController) 을 규칙에 맞게끔 사용
  * `/error` 경로의 html을 기본 접근
* **뷰선택 우선순위(BasicErrorController 가 제공하는 기능)**
  * **1. 뷰템플릿**
    * resources/templates/error/500.html
    * html resources/templates/error/5xx.html
  * **2. 정적리소스( static , public ) resources/**
    * static/error/400.html
    * resources/static/error/404.html 
    * resources/static/error/4xx.html
  * **3. 적용대상이없을 때뷰이름( error )**
    * resources/templates/error.html

<br>

**정적파일 전부 캐싱 + gzip압축 (속도개선)**

* gzip 압축 -> 보통 이미지나 동영상은 이미 압축되어 있는 상태라 **HTML,CSS,JS 만 압축!!**
* 단, 개발할 땐 캐싱 주석해둘것 -> 캐싱때문에 이미지 바꿔도 적용안된걸로 착각할 위험이 있음

<br>

<details><summary><b>페이지 네비게이션 구현 코드</b></summary>
<div markdown="1">
```html
<!-- 페이징 -->
<!--    var pageCount = /*[[${(totalCount/10)+1}]]*/ null; // 총 페이지 크기 -> 통신으로 받음-->
<script th:inline="javascript">
  var totalCount = /*[[${totalCount}]]*/ null;
  if(totalCount % 10 == 0) {var pageCount = totalCount/10;}
  else {var pageCount = totalCount/10 +1;} // 총 페이지 크기 -> 통신으로 받은값으로 계산
  var pageMax = 10; // 보여줄 페이지 수
  var activePage = /*[[${pageId}]]*/ null; // 현재 페이지 -> 통신으로 받음(active)
  // 보여줄 시작 페이지(계산 여기서 하겠음)
  // 현재페이지/보여줄페이지수 몫 * 보여줄페이지수
  // ex) 23 / 10 * 10 -> 2*10 -> 20
  var startIndex = parseInt((activePage-1)/pageMax)*pageMax + 1;
  var endIndex = startIndex + pageMax -1;
  if(endIndex > pageCount)
    endIndex = startIndex + (pageCount%startIndex);
  // 동적 스타일링
  var arrowLeft = "<img class='img-fluid' src='/arrow-left.svg' style='width:1.2vw;' onclick='redirectSavedBgm()'/>"
  var arrowRight = "<img class='img-fluid' src='/arrow-right.svg' style='width:1.2vw;' onclick='redirectSavedBgm()'/>"
  // [동적 ul 페이징 처리 실시]
  if(pageCount == 1){ //생성해야할 페이지가 1페이지인 경우
    var insertUl = "<li class='page-item'>"; // 변수 선언
    insertUl += insertUl + "<a class='page-link active' href='/gallery/1' onclick = 'redirectSavedBgm()'>";
    insertUl += "1</a></li>";
    $("#dyn_ul").append(insertUl); //jquery append 사용해 동적으로 추가 실시
  }
  else if(pageCount >= 2){ //생성해야할 페이지가 2페이지 이상인 경우
    // (Previous)이전 페이지 추가 실시
    var insertSTR = "<li class='page-item'>"; // 변수 선언
    if(activePage===1) insertSTR = insertSTR + "<a class='page-link disabled' style='background-color: var(--main-1); opacity:0.3;' href='/gallery/"+(activePage)+"' onclick = 'redirectSavedBgm()'>";
    else insertSTR = insertSTR + "<a class='page-link' href='/gallery/"+(activePage-1)+"' onclick = 'redirectSavedBgm()'>";
    // insertSTR = insertSTR + "Previous";
    insertSTR = insertSTR + arrowLeft;
    insertSTR = insertSTR + "</a></li>";
    $("#dyn_ul").append(insertSTR); //jquery append 사용해 동적으로 추가 실시
    // ... 페이지 추가 (앞에 페이지 더 있을경우 맨앞 페이지로 이동)
    if((activePage > pageMax)) {
      var insertMID = "<li class='page-item'>"; // 변수 선언
      insertMID = insertMID + "<a class='page-link' href='/gallery/"+(1)+"' onclick = 'redirectSavedBgm()'>";
      insertMID = insertMID + "...";
      insertMID = insertMID + "</a></li>";
      $("#dyn_ul").append(insertMID); //jquery append 사용해 동적으로 추가 실시
    }
    // 1, 2, 3 .. 페이지 추가 실시
    var count = 1;
    for(var i=startIndex; i<=pageCount; i++){
      if(count > pageMax){ //최대로 생성될 페이지 개수가 된 경우
        page = i - pageMax; //생성된 페이지 초기값 저장 (초기 i값 4 탈출 인경우 >> 1값 저장)
        break; //for 반복문 탈출
      }
      var insertUl = "<li class='page-item'>"; // 변수 선언
      if(i===activePage) insertUl = insertUl + "<a class='page-link active' href='/gallery/"+i+"' onclick = 'redirectSavedBgm()'>";
      else insertUl = insertUl + "<a class='page-link' href='/gallery/"+i+"' onclick = 'redirectSavedBgm()'>";
      insertUl = insertUl + String(i);
      insertUl = insertUl + "</a></li>";
      $("#dyn_ul").append(insertUl); //jquery append 사용해 동적으로 추가 실시
      count ++;
    }
    // ... 페이지 추가 (뒤에 페이지 더 있을경우 끝 페이지로 이동)
    if((activePage < pageCount) && (activePage >= pageMax) && (endIndex < pageCount)) {
      var insertMID = "<li class='page-item'>"; // 변수 선언
      insertMID = insertMID + "<a class='page-link' href='/gallery/"+(pageCount)+"' onclick = 'redirectSavedBgm()'>";
      insertMID = insertMID + "...";
      insertMID = insertMID + "</a></li>";
      $("#dyn_ul").append(insertMID); //jquery append 사용해 동적으로 추가 실시
    }
    // (Next)다음 페이지 추가 실시
    var insertEND = "<li class='page-item'>"; // 변수 선언
    if(activePage === pageCount)  insertEND = insertEND + "<a class='page-link disabled' style='background-color: var(--main-1); opacity:0.3;' href='/gallery/"+(activePage)+"' onclick = 'redirectSavedBgm()'>";
    else insertEND = insertEND + "<a class='page-link' href='/gallery/"+(activePage+1)+"' onclick = 'redirectSavedBgm()'>";
    // insertEND = insertEND + "Next";
    insertEND = insertEND + arrowRight;
    insertEND = insertEND + "</a></li>";
    $("#dyn_ul").append(insertEND); //jquery append 사용해 동적으로 추가 실시
  }
</script>
```
</div>
</details>

<br>

<br>

## JSP 로 웹 개발 TIP

>  스프링 부트 + 타임리프 조합으로 이미 웹 개발을 하여 어느정도 지식이 있다고 가정한다.

일반 순수자바나 순수스프링이 아닌 **"스프링부트 + JSP" 경험**을 작성하고자 한다.

참고로 jQuery 를 많이 사용하기에 jQuery 공식홈페이지 보는것도 추천! (Ajax, 엘리먼트 등에 많이 활용)  
리액트 이런거 사용하는게 아니면 JSP+jQuery+Ajax 방식은 기본.

<br><br>

### (1) 구조 분석

>  구조 비교에 참고한 문헌: [일반 부트와 구조 비교](https://backendcode.tistory.com/121#google_vignette)

**스프링 부트에서 "타임리프"와 "JSP" 접근 비교:** 

- 타임리프?
  - 웹 브라우저 -> 톰캣(서블릿 컨테이너) -> 스프링 컨테이너 순으로 이동하여,  
    Controller를 찾고 있으면 자동 등록된 **viewResolver**로 화면에 응답.
  - 이때, templates/hello.html 처럼 Thymeleaf 템플릿 엔진 처리가 **"기본 경로"**로 가능!
    - 즉, **boot-starter-thymeleaf 의존성이 있다면** 부트가 "뷰 리졸버" 자동 등록 + "경로 설정" 자동 등록
    - 애초에 뷰 리졸버랑 이런건 **boot-starter-web 의존성** 덕분에 기본적으로 부트가 자동 지원.
- JSP?
  - 부트를 사용하니 의존성들 덕분에 뷰 리졸버 이런 등록은 자동이다. (순수 스프링은 하나부터 열까지 직접 xml로...)
  - 단, **"JSP 템플릿 엔진 처리"**를 따로 라이브러리로 설치해야하고 **"경로도 설정"**해야 한다.

<br>

**왼쪽 스프링부트+JSP / 오른쪽 일반 스프링부트 구조 비교: src/main/webapp/WEB-INF 가 있음!**

![Image](https://github.com/user-attachments/assets/65986172-ecce-4a97-9912-1ac386b40341)  ![Image](https://github.com/user-attachments/assets/d3f7731a-54e9-4aa0-a3c5-3c133c887ea2) 

<br><br>

### (2) 실행 과정

**3가지만 지키자:** 

- JAR는 골치아파서 JSP엔 WAR 사용! -> **(정정: 부트의 경우 내장콤캣 덕분에 JAR도 똑같이 잘 구동)**

- 의존성: 

  - implementation 'org.apache.tomcat.embed:tomcat-embed-jasper'
  - 이거 추가 안하면, jsp용 톰캣이없어서 jsp 접근시 걍 파일설치로 뜸.

- application.properties: 

  - spring.mvc.view.prefix=/WEB-INF/views/   
    spring.mvc.view.suffix=.jsp

  - WEB-INF 하위 경로는 클라 URL로 접근 안된다. 서버에서만 접근 가능하다. (보안위해)

  - 그래서 서버(스프링)한테 경로를 알려주는것! (예상이지만 원래는 /templates 일 듯)

![Image](https://github.com/user-attachments/assets/c5f41f27-f2af-4e23-acd0-a25c18a6214d) 

**컨트롤러 코드보면 기존 스프링부트(+타임리프)로 개발한거랑 다를게 없음! 편.안.**

 ![Image](https://github.com/user-attachments/assets/c599c025-c601-4128-96ce-d0e9c66d0c08) 

<br><br>

### (3) JSP 문법

**스크립트 요소**: JSP에서 자바 코드를 작성하는 곳 = 스크립트릿(Scriptlet)

- 형식: `<% 자바 코드 %>`
- 지역변수이며, 메서드 선언은 불가능(불가능이였던가?)

**선언문**: 변수나 메서드를 선언하는 곳.

- 형식: `<%! 자바 코드 %>`

- JSP -> 서블릿 변환될 때 클래스, 멤버로 변환

**표현식**: 값을 웹 브라우저에 출력할 때 사용하는 곳

- 형식: `<%= 자바 코드 %>`
- 세미콜론(;)을 붙이지 않는다.

**주석 두 가지 방식:**

- 형식1: `<!-- HTML 주석 -->`
- HTML 주석: 브라우저 소스 보기에서 보임
- 형식2: `<%-- JSP 주석입니다 --%>`
  - JSP 주석 (브라우저 출력되지 않음)

**지시어**: JSP 페이지의 전반적인 처리 방식을 설정

- 형식: `<%@ 자바 코드 %>`

- | 지시어  | 설명                      | 예시                                                |
  | ------- | ------------------------- | --------------------------------------------------- |
  | page    | 페이지 설정 정보          | `<%@ page contentType="text/html; charset=UTF-8"%>` |
  | include | 다른 파일 포함            | `<%@ include file="header.jsp" %>`                  |
  | taglib  | 태그 라이브러리 사용 선언 | `<%@ taglib uri="..." prefix="c"%>`                 |

**액션태그**: JSP 문서 내에서 간단하게 다양한 구현을 할 수 있도록 만든 태그

- 형식: `<jsp:?>`

- | 액션 태그       | 설명                                |
  | --------------- | ----------------------------------- |
  | jsp:include     | 다른 페이지를 현재 페이지에 포함    |
  | jsp:forward     | 현재 페이지에서 다른 페이지로 이동  |
  | jsp:useBean     | 자바 빈 객체 생성                   |
  | jsp:setProperty | 자바 빈 객체의 프로퍼티 값을 설정   |
  | jsp:getProperty | 자바 빈 객체의 프로퍼티 값을 가져옴 |

- 헷갈리는 부분 체크: `include` 의 경우 -> [지시어 include, 액션태그 include 동작차이](https://doublesprogramming.tistory.com/64)

  - 예로 footer에 "저작권표시"는 정적으로써 지시어 include 사용하면 될거다.
  - "현재 시간"은 해당 파일이 실행될 때 나타내줘야 하니까 동적으로써 액션태그 사용!
    - 즉, 모듈화할 때 사용한다.(웹 특정 영역을 독립된 파일로 나눈다공)

**내장객체**: JSP에서는 별도의 선언 없이 사용할 수 있는 내장 객체가 있다.

- `request`: 클라이언트 요청 정보를 담고 있는 객체
- `response`: 클라이언트에게 응답을 보내기 위한 객체
- `out`: 출력 스트림을 통해 데이터를 출력하기 위한 객체
- `session`: 세션 정보를 저장하고 관리하는 객체
- `application`: 웹 애플리케이션 전체에서 공유되는 데이터를 저장하는 객체

**JSTL**: 자주 사용하는 로직(조건문, 반복문 등)을 태그 형태로 제공하는 JSP 표준 태그 라이브러리

- | 태그 라이브러리     | URI                                      | prefix(접두어) | 주요 기능                           |
  | ------------------- | ---------------------------------------- | -------------- | ----------------------------------- |
  | Core(가장많이 사용) | `http://java.sun.com/jsp/jstl/core`      | `c`            | 조건문, 반복문, 변수 선언, URL 처리 |
  | Formatting          | `http://java.sun.com/jsp/jstl/fmt`       | fmt            | 숫자, 날짜, 시간 포맷 및 국제화     |
  | Functions           | `http://java.sun.com/jsp/jstl/functions` | fn             | 문자열 처리 함수 제공               |
  | XML                 | `http://java.sun.com/jsp/jstl/xml`       | x              | XML 문서 처리                       |
  | SQL                 | `http://java.sun.com/jsp/jstl/sql`       | sql            | JSP 내에서 SQL 처리                 |

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>JSP Example</title>
</head>
<body>

<!-- 선언부(선언만 가능) -->
<%! String name = "김철수"; %>
<%! 
    public String greeting(String name) {
        return "안녕하세요, " + name + "님!";
    }
%>

<!-- 스크립트릿 + 내장객체(request) -->
<%
    String userName = request.getParameter("user");
    if(userName == null) {
        userName = "손님";
    }
%>

<h2>환영합니다!</h2>

<!-- 표현식 -->
<p><%= greeting(userName) %></p>

<!-- 액션 태그 -->
<jsp:include page="footer.jsp" />
```

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<body>
<h2>안녕하세요 테스트 입니다.</h2>
<!-- 컨트롤러에서 모델에 담긴 변수명 'custNm' -->
<c:choose>
    <c:when test="${custNm == '홍길동'}">
        홍길동님 환영합니다.
    </c:when>

    <c:when test="${custNm == '이순신'}">
        이순신님 환영합니다.
    </c:when>

    <c:otherwise>
        등록되지 않은 사용자입니다.
    </c:otherwise>
</c:choose>
</body>
</html>
```

<br>

<br>

## 전자정부프레임워크4.2 학습하기

공식홈페이지에 좋은 가이드 파일들을 제공하므로 해당 가이드를 보고 학습하자.

추가로 현재 4.3도 출시되어서 4.3Dev(개발용) 이클립스가 있음.(2025-03)

<br>

**플젝 진행해보면서 나한테 맞는 쳌리 만들기!!!!!!!!!!! (본인은 부트 많이 사용하려함)**  
순수스프링 플젝(+XML설정)은 easycompany 위주로 복습! 애초에 아래 공부내용 대부분 순수스프링 방식임.   

일단 **MVC패턴으로 egovframe라이브러리 사용하여 레포DAO, 서비스**를 만드는건 필수이고..   
엔티티의 경우도 VO로 하며 인터페이스도 넣는것 같더라. @Entity도 쓰긴 하려고그냥 원래하던것 처럼  
=> **빈 주입 방식은 내가하던 생성자 주입**으로 하게. 짜피 다른것들은 아래 학습한것들 복습하면 충분함.  
Spring Data JPA도 사용해도 될것 같기도 하고(물론, crudRepository를 우선 써야할것 같다만..) + 일단 JPA보단 **MyBatis(egovframe있으+인터페이스방식ver+DBIO Editor활용!)**를 많이 사용하는것 같아 보이니 이거 쓰는게 나을것 같긴 하다만,,  
**DB(내부테스트DB쓰다가 MySQL ㄱㄱㄹ)**는 뭘 써도 상관없어 보이고  
어느정도 SpringBoot2.x 사용해도 괜찮은것 같음. 꼭 XML로 빈을 전부 등록하며 이럴 필요없이 Boot로 애노테이션 활용하여 자바코드로 간단히 해도 딱히 지침에 위반될것 같진 않는..?    
=> 눈치껏 easycompany플젝 흐름대로하다가 웬만하면 내가쓰던 Boot방식 주로 쓰자.   
=> 예로 **로그레벨 설정, 트랜잭션도 딱히따로 등록안할거라 DataSource연결, 빈 등록 등등 properties우선!!**  
=> 혼합 가능하니까 부가적인걸 XML로 설정ㄱ.(**Exception, AOP, Validation-자카트라 커믄 정도려나?**)  
검색은 **ajax써서 자동완성기능** 꼭 넣자.  
로그인쪽(게시판 등?) **"공통기능" 이용 + Spring Security(공통기능)**도 표준프레임워크로 [security](https://arckwon.tistory.com/entry/%EC%A0%84%EC%9E%90%EC%A0%95%EB%B6%80%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%AC-%EC%8A%A4%ED%94%84%EB%A7%81%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0-%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EC%A0%81%EC%9A%A912), [암호화](https://arckwon.tistory.com/entry/%EC%A0%84%EC%9E%90%EC%A0%95%EB%B6%80%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%AC-DB%EC%A0%91%EC%86%8D%EC%A0%95%EB%B3%B4-%EC%95%94%ED%98%B8%ED%99%94-crypto-%EC%84%9C%EB%B9%84%EC%8A%A4), [공문](https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:rte4.0:fdl:server_security) 참고!   
=> 단, Spring Security는.. JWT를 eGov에서도 하지않을까? 맞으면 이건 맨 마지막에 삽입! (형상기록으로 세션방식이랑 구분해두겡)  
이건 **마지막쯤?**에 하장ㅇ. 01.개발환경_교육교재.pdf에 **Jenkins(CI)**설치 사용, 형상관리(SVN, GIT-설명은 안보임. 어차피 GIT은 잘 아니까 뭐) 도 있으니 이걸로 하면 되겠고..   
=> 이것도 마지막쯤.. **Spring Batch**는 간단히 웹하나 만들어서 HTTP로 트리거줘서 egovframe껄로 같이 구현해둬도 좋을듯.

주저리 주저리.. 아래 내용도 참고해서 개발 -> 개발 때 꼭 연구노트에 나만의 쳌리 만들면서 진행하기!

이클립스:live플러그인(애초에 톰캣구동하는데 Python이 아닌이상 이건 필요없을듯), 실행빨리하는 단축키?, 자동완성..플러그인이있는건지..(우선 easycompany플젝은 소스 저장시 자동으로 반영되는듯 했음- **Build Automatically** 일듯. 체크해보쟈)  
**빈 등록이든 뭐든 설정**엔 어노테이션과 XML혼합해서 사용 당연히 가능. **부트는 어노테이션 기반 설정을 주로 쓰되 특정 상황에선 XML도 잘 사용.(부트인데.. 원래하던 어노테이션 꼭 많이쓰자 그냥!)**  
**Validation**은 기존에 했던것도 좋지만 이번엔 Jakarta Commons 써보기.(XML이네. +검증 메시지.)  
=> @Validation 이런걸 안쓸 뿐이지 bindingresult 다 쓰네 뭐 ㅇㅇ. 비슷하네역시. 기존이랑.   
=> 까먹지말고 JSP 클라에도 Valid적용!  
**Exception**은 API아닌 JSP고 부트쓰긴 하지만 /error 이 기능말고 XML+AOP로 해보장.  
=> 그럼 **AOP**는 Exception뿐만아니라 메소드시간구하는 것도 XML로 같이 해보자.   
=> EgovAbstractServiceImpl의 processException, leaveaTrace도 활용하자!  
**국제화메시지, 로그인, ajax(자동완성)**은 "실행환경 - 화면처리(Presentation)" + easycompany 보는게 나을것 같음~ -> 로그인은 나랑 똑같이 세션이라. 유사한데 @Login 쓰는게 다름ㅇㅇ.  
**로그레벨**은 xml말고 properties에서 해도 충분할 듯!(부트 사용시)  
**페이징**은 Pagination Tag 정리한 공.문 꼭 참고

<br><br>

### 프레임워크 환경(아키텍처), 적용지침

<details><summary><b>재사용 방식의 발전 흐름: 소스 재사용 -> 재사용 메소드 -> 재사용 객체 -> 디자인 패턴 -> 프레임워크</b></summary>
<div markdown="1">
- 소스 재사용 예: 클래스 A에서 ‘Date’를 ‘String’으로 변환하는 코딩을 해놓았다고 한다면 클래스 B에서 동일한 로직이 필요해서 클래스A에서 해당부분을 복사해서 사용할 수 있습니다. (진짜 코드를 복붙)
- 재사용 메소드 예: C언어에서 라이브러리로 만들어 재사용하는것 처럼, 유사한 기능을 모아 하나의 클래스에 정의해 놓으면 메소드 라이브러리로 구성되며 해당 라이브러리를 가져와서 재사용 가능. 이후에 JDK내용이 바뀌거나 향상된 기능을 제공하고 싶을 경우에도 해당 메소드만 수정하여 제공하면 됨
- 재사용 객체 예: 앞이랑 유사한데 이건 객체 지향 언어에서 새로 소개된 방식임. Person 클래스의 printBirthDate()의 내용이 변경되면 이를 상속 받고 있는 Client, Employee의 printBirthDate()도 자동적으로 변경
- 디자인 패턴 예: "클래스의 재사용 방식"이 객체의 수직적인 재사용에 초점을 맞추었다면 "디자인 패턴"은 상황적인 문제를 해결하여 주는 재사용 방식. 즉, 공통적인 로직 문제에 대한 일반화된 해결 
  - 상황: DB를 관계형이나 txt, xml 등으로 저장하고 싶을때 -> Adapter패턴을 활용!
  - Adapter패턴으로 객체를 DB에 저장하거나 운영 시스템에 다양한 SW에 Adapter패턴을 적용(꼭 DB저장이 아니여도 Adapter 패턴을 쓸 수 있다는거). 단지 동일한 패턴 하에서 유사한 역할을 하고 있을 뿐임.
- 프레임워크 예: 디자인 패턴을 전체적인 앱 시스템에서 보면 부분적인 해결책일 뿐임. 전체적인 관점에서 통합하여 애플리케이션의 설계 및 구현 틀을 제공하는 것이 프레임워크
</div>
</details>

<details><summary><b>전자정부프레임워크로 개발자는 비즈니스 로직 개발에만 전념한다는 장점</b></summary>
<div markdown="1"><br>
<img src="https://github.com/user-attachments/assets/5efb9915-7170-4edf-a373-a8e9af76908d" alt="Image" style="zoom:80%;" /><br>  
**프레임워크(Spring) VS 전자정부 표준프레임워크**<br>
<img src="https://github.com/user-attachments/assets/b1e67524-b5b5-4b09-826e-1ba6481f0dc5" alt="Image" style="zoom:80%;" /> 
</div>
</details>
<details><summary><b>표준프레임워크는 "실행,개발,관리,운영" 4개의 환경과 "모바일, 공통컴포넌트"로 구성</b></summary>
<div markdown="1"><br>
<img src="https://github.com/user-attachments/assets/4ac6060d-de89-438b-9005-b55a31ff681a" alt="Image" style="zoom:80%;" /><br> **표준프레임워크 실행환경**<br>
<img src="https://github.com/user-attachments/assets/18e66285-25bd-446a-b204-fb3ab69d4b45" alt="Image" style="zoom:80%;" /><br>
**표준프레임워크 개발환경**<br>
<img src="https://github.com/user-attachments/assets/da0e76ab-8bc2-4869-b5c6-db45164bac52" alt="Image" style="zoom:80%;" /><br>
**표준프레임워크 관리환경**<br>
<img src="https://github.com/user-attachments/assets/7e167e44-8cfa-42ab-af5c-8f4b92c70638" alt="Image" style="zoom:80%;" /><br>
**표준프레임워크 운영환경**<br>
<img src="https://github.com/user-attachments/assets/b9aca047-073b-40ae-91a4-eb990daa61fe" alt="Image" style="zoom:80%;" /><br>
**공통컴포넌트**<br>
<img src="https://github.com/user-attachments/assets/25aa899f-6100-48a7-a58f-3f154e0678ba" alt="Image" style="zoom:80%;" /><br>
**모바일 표준프레임워크**<br>
<img src="https://github.com/user-attachments/assets/9feaf183-2d6a-43bc-b4bc-023f851b0c6d" alt="Image" style="zoom:80%;" />
</div>
</details>
<details><summary><b>표준프레임워크 개발(iBatis) 예시 - 전체흐름+코드 </b></summary>
<div markdown="1"><br>
<img src="https://github.com/user-attachments/assets/d26f7392-1a4b-4f63-b577-4318aba5a4e3" alt="Image" style="zoom:80%;" /><br>
<img src="https://github.com/user-attachments/assets/e6a9ad68-7331-4fc5-97f5-8c80c2ed65f8" alt="Image"  /><br>
<img src="https://github.com/user-attachments/assets/56b598b8-6519-438b-ac1e-62e91dbc75c5" alt="Image" style="zoom:80%;" /><br>
<img src="https://github.com/user-attachments/assets/dc4a9bcf-4750-413c-9c69-40baa5d54df7" alt="Image" style="zoom:80%;" /><br>
<img src="https://github.com/user-attachments/assets/51357a57-d2b8-498b-a1f3-6387554fcdc1" alt="Image" style="zoom:80%;" /><br>
<img src="https://github.com/user-attachments/assets/485a5494-3979-418d-9d6c-dacbea9d5086" alt="Image" style="zoom:80%;" /><br>
<img src="https://github.com/user-attachments/assets/3a68ca81-4f03-44c8-bbc3-57e89ab50a3d" alt="Image" style="zoom:80%;" /><br>
<img src="https://github.com/user-attachments/assets/86dbb1c2-eb44-4b01-a37e-4af59a217fe8" alt="Image" style="zoom:80%;" /><br>
<img src="https://github.com/user-attachments/assets/60af2603-d44a-45d8-8cb4-546ad8b8768b" alt="Image" style="zoom:80%;" /> 
</div>
</details>
<details><summary><b>적용 지침서 보기: 공홈>알림마당>관련참고문서>정보시스템 구축 발주자를 위한 표준프레임워크 적용가이드</b></summary>
<div markdown="1">
- 권고사항: 
  - 수정없이 사용: "실행환경", "모바일표준프레임워크" 
  - 수정가능: "개발환경"(다른 상용 솔루션 조합도 가능), "운영환경 및 공통컴포넌트"
- **기본 2가지 적용 확인:**
  1. 표준프레임워크 실행환경의 정상적인 설치 여부 점검
     - 운영서버(WAS)의 "[웹어플리케이션 루트 디렉토리]/WEB-INF/lib/" 폴더에 "org.egovframe.rte"로 시작하는 .jar 파일이 존재하는지 확인  
       **=> 즉, egovframe 라이브러리 사용하는지 체크**
  2. 실제 소스코드에서 실행환경이 활용되고 있는지 점검
     - import org.egovframe.rte 검색되는지 체크
     - EgovAbstractDAO(EgovAbstractMapper) 와 EgovAbstractServiceImpl(또는 AbstractServiceImpl) 클래스를 상속한 구문이 존재하는지 체크
       - (예: public class NotificationDAO extends EgovAbstractDAO)
       - (예: public class NotificationServiceImpl extends EgovAbstractServiceImpl)
- 상세한 적용 확인:
  1. 아키텍처 규칙 
     - Annotation 기반 Spring MVC 준수 : **@Controller 및 @RequestMapping**을 통한 URL mapping 활용 (View 부분과 model(business logic 및 data) 부분을 controller를 통해 분리) 
     - Annotation 기반 layered architecture 준수 : 화면처리, 업무처리, 데이터처리에 부분에 대하여 각각 **@Controller, @Service, @Repository** 활용 (인접 layer간 호출만 가능) 
     - 업무처리를 담당하는 서비스 클래스(@Service)는 **EgovAbstractServiceImpl**(또는 AbstractServiceImpl)을 확장하고 업무에 대한 특정 인터페이스를 구현하여야 함 
     - 데이터처리를 담당하는 DAO 클래스(@Repository)는 EgovAbstractDAO(iBatis) 또는 **EgovAbstractMapper**(MyBatis)를 상속하여야 함 (**Hibernate/JPA를 적용한 경우는 예외**이며 자세한 사항은 하단 ‘데이터처리 규칙’ 참조) 
  2. 데이터처리 규칙 
     - Data Access 서비스 준수 : 데이터처리 부분은 iBatis 활용 (SqlMapClientDaoSupport 를 상속한 EgovAbstractDAO 활용) 또는 MyBatis 활용 (SqlSessionDaoSupport를 상속 한 **EgovAbstractMapper** 활용)<br>※ MyBatis의 경우 **Mapper interface 방식**으로 사용가능(권장)하며, 이 경우는 **interface 상에 @Mapper를 지정**하여 사용되어야 함 
     - ORM 서비스 준수 : 데이터처리 부분은 Hibernate/JPA 적용 (DAO에서 SessionFactory 또는 EntityManagerFactory 설정을 통해 HibernateTemplate/JpaTemplate를 활용하거나, HibernateDaoSupport/JpaDaoSupport를 상속하여 활용) 
     - Data 서비스 준수 : 데이터 처리 부분은 다양한 persistence store(Big Data, NoSQL 등)를 지원하기 위한 Spring Data 적용 (**DAO에서 CrudRepository를 상속하는 interface 방식의 Repository를 활용**) 
       - JpaRepository와 다르게 진짜 CRUD만 제공
  3. 활용 및 확장 규칙 
     - 표준프레임워크 실행환경 준수 : 표준프레임워크 실행환경은 적극적으로 활용되어야 함 (실행환경 부분 임의 변경 금지) 
     - 확장 규칙 : 업무 클래스는 org.egovframe.rte 패키지 내에 정의될 수 없음 
  4. 기타 
     - 이외에 개발환경, 운영환경 및 공통컴포넌트 부분은 선택적으로 적용 가능하며, 임의 변경 및 확장 가능함 
     - UI부분에 RIA(Rich Internet Appliation)가 적용되는 경우는 UI Adaptor 또는 RESTful 방식을 적용 활용해야 함
</div>
</details>

<br><br>

### 기본 프로젝트 생성

**순수(스프링) Web**은 Maven Install로 WAR 빌드 후 톰캣과 함께 Server 실행이 필요  
**스프링부트 Web**은 starter-web 있으니까 내장 톰캣으로 바로 실행  
=> init: Eclipse + Spring Framework(+Boot) + Maven(=Build Tool) + MyBatis + HSQLDB(=테스트용) + 리팩토링{message + Validation + Exception}

- 순수스프링: 
  - 빈을 XML 에서 설정. 이 빈을 @Resource(name)으로 주입하여 사용
    - 참고: @Resource(이름기반주입), @Autowired(타입기반주입), setter주입(EX:XML빈에 property사용시 자동setter주입), **생성자주입(제일권장!)**
    - **헷갈리는 Autowired, Qualifier, Resource**: **@Autowired**와 함께 @*Qualifier*를 사용하고, **@Resource는** @*Autowired*와 @*Qualifier*를 한번에 간결하게 표현
  - 테스트 코드엔 이 2가지 사용(jUnit4 기준)  
    @RunWith(SpringJUnit4ClassRunner.**class**)  
    @ContextConfiguration(locations= {"/context-helloworld.xml"})
- 스프링부트:
  - 빈을 자바코드로 설정 가능 (EX: @Bean). 보통 생성자 주입하여 사용
  - 테스트 코드엔 이 2가지 사용(jUnit4 기준)   
    @RunWith(SpringRunner.**class**)  
    @SpringBootTest

**eclipse 응답없음** 자주 뜬다면? 힙 메모리 사용량이라도.. 최적화하자 ㅠ  

- **주의!!** 윈도우의 경우 알집으로 압축풀면 몇개는 에러뜨는데 그대로 사용시 이클립스가 미쳐 날뛰는걸 확인할 수 있음... 한참 찾았네 ㅠ  
  bak log파일이 미친듯이 발생하는데, 압축 제대로 안풀려서 미친듯이 충돌 난건가 봄. 에효ㅠㅠ  
  **7-zip 외부 파일로 압축풀어서 간단히 해결했음!**
- window>preferences>general>show heap status 체크 -> 메모리 부족하다면?
- eclipse.ini 파일에서 Xms, Xmx 수정 (최소, 최대 힙 메모리 용량)
- 추가TIP) 안 사용하는 플젝은 close project / General>Appearance>Theme>Classic, Use mixed fonts and colors for labels 체크X

**pom.xml** - Run As Maven {Build, Clean, Install}: 

- Clean은 target 폴더 삭제랑 이전 빌드내용 삭제
- Build는 의존성 Install 전까지 (JAR나 WAR패키징 까지)
- Install은 Build를 포함하여 의존성 Install 까지

**boot의 config 코드 분석? (직접 주석 달음. 주석과 코드 함께 보기)**

<details><summary><b>EgovConfigAspect</b></summary>
<div markdown="1"><br>
```java
package egovframework.example.config;
import egovframework.example.exception.EgovAopExceptionTransfer;
import egovframework.example.exception.EgovSampleExcepHndlr;
import egovframework.example.exception.EgovSampleOthersExcepHndlr;
import org.egovframe.rte.fdl.cmmn.aspect.ExceptionTransfer;
import org.egovframe.rte.fdl.cmmn.exception.handler.ExceptionHandler;
import org.egovframe.rte.fdl.cmmn.exception.manager.DefaultExceptionHandleManager;
import org.egovframe.rte.fdl.cmmn.exception.manager.ExceptionHandlerService;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;
import org.springframework.util.AntPathMatcher;
/*
Spring 기반의 AOP-예외처리 설정을 위한 클래스
Transfer, Handler, Manager, Service 등은 egovframe 라이브러리에서 가져옴.
예외 발생: 애플리케이션에서 예외가 발생하면, Spring의 AOP 설정에 의해 예외 처리기가 호출됩니다.
예외 처리기 실행: 예외 처리기는 발생한 예외를 처리하고 적절한 응답을 반환합니다.
예외 전송: ExceptionTransfer 빈은 예외를 전송하는 역할을 하며, 이는 여러 예외 처리 매니저를 통해 이루어집니다.
 */
@Configuration //빈 정의 목적
@EnableAspectJAutoProxy //AspectJ를 사용해 AOP 활성화 (자동 프록시 생성후 Aspect 적용)
public class EgovConfigAspect {
	@Bean //스프링 컨테이너에 등록
	public EgovSampleExcepHndlr egovHandler() {
		return new EgovSampleExcepHndlr(); //예외 처리기(Handler): 예외처리 후 응답반환
	}
	@Bean
	public EgovSampleOthersExcepHndlr otherHandler() {
		return new EgovSampleOthersExcepHndlr(); //예외 처리기(Handler): 예외처리 후 응답반환
	}
	//AntPathMatcher(URL패턴 매치)하고, 위 예외 처리기 활용1
	@Bean 
	public DefaultExceptionHandleManager defaultExceptionHandleManager(AntPathMatcher antPathMatcher, EgovSampleExcepHndlr egovHandler) {
		DefaultExceptionHandleManager defaultExceptionHandleManager = new DefaultExceptionHandleManager();
		defaultExceptionHandleManager.setReqExpMatcher(antPathMatcher);
		defaultExceptionHandleManager.setPatterns(new String[]{"**service.impl.*"});
		defaultExceptionHandleManager.setHandlers(new ExceptionHandler[]{egovHandler});
		return defaultExceptionHandleManager;
	}
	//AntPathMatcher(URL패턴 매치)하고, 위 예외 처리기 활용2
	@Bean
	public DefaultExceptionHandleManager otherExceptionHandleManager(AntPathMatcher antPathMatcher, EgovSampleOthersExcepHndlr othersExcepHndlr) {
		DefaultExceptionHandleManager defaultExceptionHandleManager = new DefaultExceptionHandleManager();
		defaultExceptionHandleManager.setReqExpMatcher(antPathMatcher);
		defaultExceptionHandleManager.setPatterns(new String[]{"**service.impl.*"});
		defaultExceptionHandleManager.setHandlers(new ExceptionHandler[]{othersExcepHndlr});
		return defaultExceptionHandleManager;
	}
	//예외 전송 서비스 역할, 위 예외 처리 매니저 활용
	@Bean
	public ExceptionTransfer exceptionTransfer(
		@Qualifier("defaultExceptionHandleManager") DefaultExceptionHandleManager defaultExceptionHandleManager,
		@Qualifier("otherExceptionHandleManager") DefaultExceptionHandleManager otherExceptionHandleManager) {
		ExceptionTransfer exceptionTransfer = new ExceptionTransfer();
		exceptionTransfer.setExceptionHandlerService(new ExceptionHandlerService[] {
			defaultExceptionHandleManager, otherExceptionHandleManager
		});
		return exceptionTransfer;
	}
	//AOP 기반 예외 전송 설정, 위 예외 전송 서비스 활용
	@Bean
	public EgovAopExceptionTransfer aopExceptionTransfer(ExceptionTransfer exceptionTransfer) {
		EgovAopExceptionTransfer egovAopExceptionTransfer = new EgovAopExceptionTransfer();
		egovAopExceptionTransfer.setExceptionTransfer(exceptionTransfer);
		return egovAopExceptionTransfer;
	}
}
```
</div>
</details>

<details><summary><b>EgovConfigCommon</b></summary>
<div markdown="1"><br>
```java
package egovframework.example.config;
import org.egovframe.rte.fdl.cmmn.trace.LeaveaTrace;
import org.egovframe.rte.fdl.cmmn.trace.handler.DefaultTraceHandler;
import org.egovframe.rte.fdl.cmmn.trace.handler.TraceHandler;
import org.egovframe.rte.fdl.cmmn.trace.manager.DefaultTraceHandleManager;
import org.egovframe.rte.fdl.cmmn.trace.manager.TraceHandlerService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.support.MessageSourceAccessor;
import org.springframework.context.support.ReloadableResourceBundleMessageSource;
import org.springframework.util.AntPathMatcher;
/*
Spring 기반의 공통 설정을 위한 클래스입니다. 
이 클래스는 메시지 소스, 트레이스(Trace) 설정, 그리고 Ant 경로 매처와 같은 공통적인 설정
LeaveaTrace, Handler, Manager, Service 등은 egovframe 라이브러리에서 가져옴.
 */
@Configuration
public class EgovConfigCommon {
	//요청 경로를 매칭
	@Bean
	public AntPathMatcher antPathMatcher() {
		return new AntPathMatcher();
	}
	//트레이스 정보를 처리기.
	@Bean
	public DefaultTraceHandler defaultTraceHandler() {
		return new DefaultTraceHandler();
	}
	//메시지 소스를 제공하며, 여러 리소스 번들 파일에서 메시지를 읽어옴.
	@Bean
	public ReloadableResourceBundleMessageSource messageSource() {
		ReloadableResourceBundleMessageSource reloadableResourceBundleMessageSource = new ReloadableResourceBundleMessageSource();
		reloadableResourceBundleMessageSource.setBasenames(
				"classpath:/egovframework/message/message-common",
				"classpath:/org/egovframe/rte/fdl/idgnr/messages/idgnr",
				"classpath:/org/egovframe/rte/fdl/property/messages/properties");
		reloadableResourceBundleMessageSource.setDefaultEncoding("UTF-8");
		reloadableResourceBundleMessageSource.setCacheSeconds(60); //캐시60초
		return reloadableResourceBundleMessageSource;
	}
	//메시지 소스를 쉽게 접근하게 함. 위 메소드 활용. 
	@Bean
	public MessageSourceAccessor messageSourceAccessor() {
		return new MessageSourceAccessor(this.messageSource());
	}
	//AntPathMatcher로 요청 경로 매칭 후 트레이스 정보 처리기를 매칭 
	@Bean
	public DefaultTraceHandleManager traceHandlerService() {
		DefaultTraceHandleManager defaultTraceHandleManager = new DefaultTraceHandleManager();
		defaultTraceHandleManager.setReqExpMatcher(antPathMatcher());
		defaultTraceHandleManager.setPatterns(new String[]{"*"});
		defaultTraceHandleManager.setHandlers(new TraceHandler[]{defaultTraceHandler()});
		return defaultTraceHandleManager;
	}
	//트레이스 서비스 설정. 위 메소드 활용.
	@Bean
	public LeaveaTrace leaveaTrace() {
		LeaveaTrace leaveaTrace = new LeaveaTrace();
		leaveaTrace.setTraceHandlerServices(new TraceHandlerService[]{traceHandlerService()});
		return leaveaTrace;
	}
}
```
</div>
</details>

<details><summary><b>EgovConfigDatasource</b></summary>
<div markdown="1"><br>
```java
package egovframework.example.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseBuilder;
import org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseType;
import javax.sql.DataSource;
/*
데이터베이스를 설정 -> HSQL로.
addScript 메서드를 통해 초기 데이터베이스 스크립트를 실행하여 데이터베이스를 초기화
 */
@Configuration
public class EgovConfigDatasource {
	@Bean(name="dataSource") //생성된 데이터소스를 빈 등록
	public DataSource dataSource() {
	    EmbeddedDatabaseBuilder builder = new EmbeddedDatabaseBuilder();
	    return builder.setType(EmbeddedDatabaseType.HSQL).addScript("classpath:/db/sampledb.sql").build();
	}
}
```
</div>
</details>

<details><summary><b>EgovConfigIdGeneration</b></summary>
<div markdown="1"><br>
```java
package egovframework.example.config;
import org.egovframe.rte.fdl.idgnr.impl.EgovTableIdGnrServiceImpl;
import org.egovframe.rte.fdl.idgnr.impl.strategy.EgovIdGnrStrategyImpl;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import javax.sql.DataSource;
/*
ID 생성 설정을 위한 클래스
Egov 프레임워크의 ID 생성기 서비스를 설정하여 애플리케이션에서 사용할 수 있는 고유한 ID를 생성
아래 메소드의 반환타입 전부 egovframe 라이브러리에서 가져옴.
 */
@Configuration
public class EgovConfigIdGeneration {
	@Bean
	public EgovIdGnrStrategyImpl mixPrefixSample() {
		EgovIdGnrStrategyImpl egovIdGnrStrategyImpl = new EgovIdGnrStrategyImpl();
		egovIdGnrStrategyImpl.setPrefix("SAMPLE-"); //ID의 접두사
		egovIdGnrStrategyImpl.setCipers(5); //ID의 숫자부분 길이
		egovIdGnrStrategyImpl.setFillChar('0'); //ID의 숫자부분 채울 문자
		return egovIdGnrStrategyImpl;
	}
	@Bean(destroyMethod="destroy")
	public EgovTableIdGnrServiceImpl egovIdGnrService(@Qualifier("dataSource") DataSource dataSource) {
		EgovTableIdGnrServiceImpl egovTableIdGnrServiceImpl = new EgovTableIdGnrServiceImpl();
		egovTableIdGnrServiceImpl.setDataSource(dataSource);
		egovTableIdGnrServiceImpl.setStrategy(mixPrefixSample());
		egovTableIdGnrServiceImpl.setBlockSize(10);
		egovTableIdGnrServiceImpl.setTable("IDS"); //ID를 생성할 테이블 이름
		egovTableIdGnrServiceImpl.setTableName("SAMPLE"); //ID를 생성할 테이블 이름
		return egovTableIdGnrServiceImpl;	
	}
}
```
</div>
</details>

<details><summary><b>EgovConfigMapper</b></summary>
<div markdown="1"><br>
```java
package egovframework.example.config;
import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
import javax.sql.DataSource;
import java.io.IOException;
/*
Spring 기반의 MyBatis 설정을 위한 클래스
 */
@Configuration
@MapperScan(basePackages="egovframework.example.sample.service.impl")
public class EgovConfigMapper {
	//dataSource 빈을 주입받아 데이터베이스 연결 + MyBatis 설정 파일, 매퍼 파일 위치 지정
	@Bean
	public SqlSessionFactoryBean sqlSessionFactory(@Qualifier("dataSource") DataSource dataSource) throws IOException {
		PathMatchingResourcePatternResolver pmrpr = new PathMatchingResourcePatternResolver();
		SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
		sqlSessionFactoryBean.setDataSource(dataSource);
		sqlSessionFactoryBean.setConfigLocation(pmrpr.getResource("classpath:/egovframework/sqlmap/example/sql-mapper-config.xml"));
		sqlSessionFactoryBean.setMapperLocations(pmrpr.getResources("classpath:/egovframework/sqlmap/example/mappers/*.xml"));
		return sqlSessionFactoryBean;
	}
	//템플릿화하여 사용
	@Bean
	public SqlSessionTemplate sqlSession(SqlSessionFactory sqlSessionFactory) {
		return new SqlSessionTemplate(sqlSessionFactory);
	}
}
```
</div>
</details>

<details><summary><b>EgovConfigProperties</b></summary>
<div markdown="1"><br>
```java
package egovframework.example.config;
import org.egovframe.rte.fdl.property.impl.EgovPropertyServiceImpl;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.util.HashMap;
import java.util.Map;
/*
프로퍼티 서비스 제공하는 클래스. 공통적인 프로퍼티에 좋겠죠?
EgovPropertyServiceImpl은 egovframe 라이브러리에서 가져옴..
 */
@Configuration
public class EgovConfigProperties {
	@Bean(destroyMethod="destroy") //빈이 소멸될 때 destroy() 메서드 호출되게 설정
	public EgovPropertyServiceImpl propertiesService() {
		Map<String, String> properties = new HashMap<>();
		properties.put("pageUnit", "10");
		properties.put("pageSize", "10");
		EgovPropertyServiceImpl egovPropertyServiceImpl = new EgovPropertyServiceImpl();
		egovPropertyServiceImpl.setProperties(properties);
		return egovPropertyServiceImpl; //리턴값을 통해 앱의 다른 부분에서 이 프로퍼티 접근가능
	}
}
```
</div>
</details>

<details><summary><b>EgovConfigTransaction</b></summary>
<div markdown="1"><br>
```java
package egovframework.example.config;
import org.springframework.aop.Advisor;
import org.springframework.aop.aspectj.AspectJExpressionPointcut;
import org.springframework.aop.support.DefaultPointcutAdvisor;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.TransactionDefinition;
import org.springframework.transaction.interceptor.*;
import javax.sql.DataSource;
import java.util.Collections;
import java.util.HashMap;
/*
트랜잭션 설정을 위한 클래스 - 애초에 부트로 @Transcational... 써도 대부분 ㅇㅋ.
다만, 이 프레임워크대로 여기선 써야지.. (장점:직접 설정한거라 포인트컷 설정이나 예외처리 명시같은게 자유롭)
 */
@Configuration
public class EgovConfigTransaction {
	//dataSource 주입받아 DB연결 설정
	@Bean(name="txManager")
	public DataSourceTransactionManager txManager(@Qualifier("dataSource") DataSource dataSource) {
		DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager();
		dataSourceTransactionManager.setDataSource(dataSource);
		return dataSourceTransactionManager;
	}
	//트랜잭션을 적용할 메서드에 대한 규칙을 설정 - 트랜잭션 필수사용+예외 시 롤백규칙
	@Bean
	public TransactionInterceptor txAdvice(DataSourceTransactionManager txManager) {
		RuleBasedTransactionAttribute txAttribute = new RuleBasedTransactionAttribute();
		txAttribute.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);
		txAttribute.setRollbackRules(Collections.singletonList(new RollbackRuleAttribute(Exception.class)));
		HashMap<String, TransactionAttribute> txMethods = new HashMap<String, TransactionAttribute>();
		txMethods.put("*", txAttribute);
		NameMatchTransactionAttributeSource txAttributeSource = new NameMatchTransactionAttributeSource();
		txAttributeSource.setNameMap(txMethods);
		TransactionInterceptor txAdvice = new TransactionInterceptor();
		txAdvice.setTransactionAttributeSource(txAttributeSource);
		txAdvice.setTransactionManager(txManager);
		return txAdvice;
	}
	//트랜잭션 인터셉터를 적용할 포인트컷을 정의 - 특정 패턴의 메서드에 트랜잭션 적용
	@Bean
	public Advisor txAdvisor(@Qualifier("txManager") DataSourceTransactionManager txManager) {
		AspectJExpressionPointcut pointcut = new AspectJExpressionPointcut();
		pointcut.setExpression("execution(* egovframework.example.sample..impl.*Impl.*(..))");
		return new DefaultPointcutAdvisor(pointcut, txAdvice(txManager));
	}
}
```
</div>
</details>

<details><summary><b>EgovConfigValidation</b></summary>
<div markdown="1"><br>
```java
package egovframework.example.config;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.MessageSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.validation.Validator;
import org.springframework.validation.beanvalidation.LocalValidatorFactoryBean;
/*
검증 설정을 위한 클래스 - Bean Validation API를 사용하여 검증기를 생성 +  MessageSource 주입 받아 검증 메시지 관리
 */
@Configuration
public class EgovConfigValidation {
    @Bean
    public Validator getValidator(@Qualifier("messageSource") MessageSource messageSource) {
        LocalValidatorFactoryBean localValidatorFactoryBean = new LocalValidatorFactoryBean();
        localValidatorFactoryBean.setValidationMessageSource(messageSource);
        return localValidatorFactoryBean;
    }
}
```
</div>
</details>

<details><summary><b>EgovConfigWeb</b></summary>
<div markdown="1"><br>
```java
package egovframework.example.config;
import egovframework.example.pagination.EgovPaginationDialect;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.web.servlet.HandlerExceptionResolver;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
import org.springframework.web.servlet.handler.SimpleMappingExceptionResolver;
import org.springframework.web.servlet.i18n.LocaleChangeInterceptor;
import org.springframework.web.servlet.i18n.SessionLocaleResolver;
import org.thymeleaf.spring5.SpringTemplateEngine;
import org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver;
import org.thymeleaf.spring5.view.ThymeleafViewResolver;
import org.thymeleaf.templatemode.TemplateMode;
import java.util.List;
import java.util.Properties;
/*
Spring MVC 설정을 위한 클래스
Thymeleaf 템플릿 엔진 설정, 리소스 핸들러 설정, 인터셉터 설정, 예외 처리 설정 등을 포함
 */
@Configuration
@Import({
		EgovConfigAspect.class,
		EgovConfigCommon.class,
		EgovConfigDatasource.class,
		EgovConfigIdGeneration.class,
		EgovConfigMapper.class,
		EgovConfigProperties.class,
		EgovConfigTransaction.class,
		EgovConfigValidation.class
})
public class EgovConfigWeb implements WebMvcConfigurer, ApplicationContextAware {
	private ApplicationContext applicationContext;
	public void setApplicationContext(final ApplicationContext applicationContext) {
		this.applicationContext = applicationContext;
	}
	//Thymeleaf 템플릿 파일의 위치와 접미사를 설정
	@Bean
	public SpringResourceTemplateResolver templateResolver() {
		SpringResourceTemplateResolver templateResolver = new SpringResourceTemplateResolver();
		templateResolver.setApplicationContext(this.applicationContext);
		templateResolver.setPrefix("classpath:/templates/thymeleaf/"); //기본경로
		templateResolver.setSuffix(".html"); //확장자
		templateResolver.setTemplateMode(TemplateMode.HTML);
		templateResolver.setCacheable(true);
		return templateResolver;
	}
	//Thymeleaf 템플릿 엔진을 설정
	@Bean
	public SpringTemplateEngine templateEngine() {
		SpringTemplateEngine templateEngine = new SpringTemplateEngine();
		templateEngine.setTemplateResolver(templateResolver());
		templateEngine.setEnableSpringELCompiler(true);
		// add custom tag
		templateEngine.addDialect(new EgovPaginationDialect()); //만든 페이징 기능을 지원
		return templateEngine;
	}
	//Thymeleaf 템플릿을 뷰로 사용
	@Bean
	public ThymeleafViewResolver thymeleafViewResolver() {
		ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
		viewResolver.setCharacterEncoding("UTF-8");
		viewResolver.setTemplateEngine(templateEngine());
		return viewResolver;
	}
	//정적 리소스 파일(CSS, 이미지, 자바스크립트 등)의 경로를 설정
	@Override
	public void addResourceHandlers(ResourceHandlerRegistry registry) {
		registry.addResourceHandler("/css/**").addResourceLocations("classpath:/static/css/");
        registry.addResourceHandler("/images/**").addResourceLocations("classpath:/static/images/");
        registry.addResourceHandler("/js/**").addResourceLocations("classpath:/static/js/");
	}
	//세션 기반의 로케일(지역?) 리졸버를 설정
	@Bean
	public SessionLocaleResolver localeResolver() {
		return new SessionLocaleResolver();
	}
	//URL 파라미터를 통해 로케일을 변경 지원
	@Bean
	public LocaleChangeInterceptor localeChangeInterceptor() {
		LocaleChangeInterceptor interceptor = new LocaleChangeInterceptor();
		interceptor.setParamName("language");
		return interceptor;
	}
	//인터셉터 등록(요청에 적용) - 로케일 변경 인터셉터를 등록하여 로케일 변경 요청을 처리
	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		registry.addInterceptor(localeChangeInterceptor());
	}
	//예외 처리기를 설정하여 예외 발생 시 적절한 응답 - 에러 뷰 매핑
	@Override
	public void configureHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
		Properties prop = new Properties();
		prop.setProperty("org.springframework.dao.DataAccessException", "egovSampleError");
		prop.setProperty("org.springframework.transaction.TransactionException", "egovSampleError");
		prop.setProperty("org.egovframe.rte.fdl.cmmn.exception.EgovBizException", "egovSampleError");
		prop.setProperty("org.springframework.security.AccessDeniedException", "egovSampleError");
		prop.setProperty("java.lang.Throwable", "egovSampleError");
//
		Properties statusCode = new Properties();
		statusCode.setProperty("egovSampleError", "400");
		statusCode.setProperty("egovSampleError", "500");
//
		SimpleMappingExceptionResolver smer = new SimpleMappingExceptionResolver();
		smer.setDefaultErrorView("egovSampleError");
		smer.setExceptionMappings(prop);
		smer.setStatusCodes(statusCode);
		resolvers.add(smer);
	}
}
```
</div>
</details>
<br><br>

### 개발환경 - Code Generation

말 그대로 소스 코드 자동생성.. WOW  

**예로 CRUD 자동생성을 해보자면?** (완료하면 생성된 샘플을 볼 수 있다.)

1. (HSQL)DB 실행 -> (이클립스)Data Source Explorer 에서 DB Connection에 HSQLDB 우클릭 connect

2. (이클립스)window>Show View>Other.. 선택 후 eGovFrame>Templates 선택

3. 원하는 자동생성 중에 CRUD 선택 후 PUBLIC>SAMPLE>디렉토리 정보.. 하면 끝!

   <img src="https://github.com/user-attachments/assets/c0dbf6f4-2a94-4545-9b3c-44b9be178b2a" alt="Image" style="zoom:80%;" /> 

   실제로 해당 패키지에서 보면 CRUD 생성 됨을 확인. (VO=실제 데이터. 엔티티)

4. src/main/resources 에서 본인 패키지의 mapper-config.xml 파일을 우클릭 > Open With > Other… > mapperConfiguration Editor > OK 후 생성된 Sample_MAPPER.xml 파일을 추가되어 있는지 확인

4. maven clean -> maven install 후 server 실행해보면 잘 구동된 웹 확인 가능

**생성된 파일들 모습**  

SampleDefaultVO는 SampleVO(**"엔티티"**)의 부모클래스, SampleService는 인터페이스, SampleDAO는 eGovFram쪽 상속받아 구현한 **"레포"**, SampleServiceImpl은 위 인터페이스 구현체인 **"서비스"**, SampleController는 **"컨트롤러"**  
+) SampleMapper, mapper-config, Sample_MAPPER는 MyBatis!!

@Resource로 여기선 빈 등록 받는중.

<img src="https://github.com/user-attachments/assets/8c36b51e-e8d3-46c1-be24-f1dc5c620719" alt="Image" style="zoom:80%;" /> 

![Image](https://github.com/user-attachments/assets/623bba22-fef4-44d6-9c41-7e239d5648ec) 

<br><br>

### 개발환경 - 테스트, 공통컴포넌트, 템플릿

**플젝>Run As>jUnit Test** or Maven test -> **jUnit4 사용 (JUnit뷰 콘솔에서 결과 확인)**

(아래사진)**Data Source Explorer**에서 간편히 **DB연결 및 테이블 확인** 가능  
=> DB연결(실행)이 되어야 계층 구조가 나타나며, DB추가는 우클릭 NEW로 간단히 가능(별첨 참고)

- <img src="https://github.com/user-attachments/assets/08b270c7-9195-4c6a-9dd8-8b7d42fb65cb" alt="Image" style="zoom:50%;" /> 

프로젝트 우클릭> New > eGovFrame Common Component > 원하는 **공통 컴포넌트** 선택  
예로 "게시판, 역할관리 컴포넌트" 선택시 아래 사이트 자동 생성 (Java, DB 소스도 자동!)

- <img src="https://github.com/user-attachments/assets/c94c43b7-e84d-4094-8d69-b0e5c57a43ff" alt="Image" style="zoom: 80%;" />  

eGovFrame>Start>New Template Project 생성,실행 후 "admin / 1" 로그인하면 홈페이지 확인 가능  
**템플릿 프로젝트는 아래와 같은 기능을 제공!!**

- ![Image](https://github.com/user-attachments/assets/adbcbf81-b4d2-4890-90db-500845262154) 

<br><br>

### 개발환경 - DBIO Editor 실습(EX:MyBatis)

MyBatis는 예전에 토이플젝에 직접 세팅해서 해봄. 여기선 다른점있나?   
**=> 여기선 XML세팅때 우클릭>NEW>Open With>Other>mapper 에디터 를 활용!**

1. DB실행

2. DBIO 실습(자세히는 PDF)

   - Mapper Configuration 파일 생성(sample_config.xml): 프로젝트 우클릭 > New > mapperConfiguration

   - Mapper 파일 생성(sample_map.xml):  Mapper Configuration Editor > New

   - Mapper 파일 편집 -> mapper 에디터를 활용!  
     **에디터로 간단히 설정하는데 "xml코드가 자동 생성되는 편리!!"**

     1. Result Map 작성: Mapper Editor> ResultMap 우클릭> Add resultMap
     2. Query 작성: Mapper Editor> Query 우클릭> Add Select Query

     자동생성 코드 예시(sample_map.xml): 

     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
     <mapper ><resultMap id="resultMap" type="java.lang.String"><result property="deptName" column="DEPT_NAME"/>
     </resultMap>
     <select id="selectDept" parameterType="java.lang.String" resultMap="resultMap">
     SELECT DEPT_NAME
       FROM PUBLIC.DEPT
       WHERE DEPT_NO = #{deptNo}
     </select>
     </mapper>
     ```
3. Query  테스트

   <img src="https://github.com/user-attachments/assets/66c16c69-5daa-4854-9fc4-4df12ad1254e" alt="Image" style="zoom:80%;" /> 

<br><br>

### 실행환경-공통기반(Foundation)

**여기까지 개발환경 PART였고, 아래부턴 실행환경 PART 교육자료이다.**

**개발프레임워크 아키텍처는 항상 기억하고 있자.**

<img src="https://github.com/user-attachments/assets/415e6678-2e01-43ad-9b0f-7a7225464266" alt="Image" style="zoom:80%;" /> 

<img src="https://github.com/user-attachments/assets/8bf68d75-6b56-42e9-8e95-1b0b273d934d" alt="Image" style="zoom:80%;" /> 

<br>

**XML 설정 방식의 Spring Bean vs Annotation로 설정하는 Spring Bean**

- XML
  - 개발 양식에 맞춰서(물론 제네릭하게! 전자정부라이브러리는 미사용 상태)   
    EmpService interface, EmpVo class를 만듬 -> DAO, SeriveImpl도 만듬 
  - text만 할거라 test 하위에 META-INF엔 context-emp xml만들고, test코드엔 EmpServiceTest class 만듬
    - xml에 빈 등록은 생각보다 간단. 특히, Service의 경우 XmlEmpDAO를 필드로 가지는데 이를위해 Bean등록에 property로 등록해줌. **-> 이게 setter 주입방식**
      - `property` **태그가 setter 메서드를 자동 호출!**
    - Test코드엔 순수스프링+JUnit4 사용을 위해 @RunWith와 @ContextConfiguration를 사용 및 @Resource(name="xmlEmpService") 이런식으로 주입했음 -> Autowired해도 잘 돌아갈거임.

- Annotation
  - Annotation에선 DAO, SeriveImpl에 @Repository, @Service를 사용했고, **@Resource로 필드 주입**했음.
  - test만 할거라 META-INF엔 context-common xml만들고 컴포넌트 스캔을 지정함. (Boot 썻을땐 Main에서 했던 그것!)

**XML으로 설정해보는 AOP**

- AOP 자바코드를 만들고, XML에서 이 AOP를 빈으로 추가 및 AOP 설정을 함(targetMethod 등)
- 예제: 코드위주 해석 -> 2개의 핵심 파일인 AdviceUsingXML.java, context-advice.xml
  - AdviceUsingXML.java 에는 beforeTargetMethod(객체 정보를 로깅로직), afterTargetMethod(실행 완료 로깅로직), afterReturningTargetMethod(결과 값 로깅로직), afterThrowingTargetMethod(예외랩핑-BizException발생로직), aroundTargetMethod(실행시간 측정로직)
  - context-advice.xml 에는 Pointcut 설정으로 어느 패키지에 AOP적용할지, Aspect설정으로 AdviceUsingXML클래스의 메소드들을 Advice로 등록!
    - **Before Advice**: 실행 전 로깅
    - **After Returning Advice**: 정상 실행 후 결과값 로깅
    - **After Throwing Advice**: 예외 발생 시 `BizException`으로 변환
    - **After Advice**: 실행 완료 후 로깅
    - **Around Advice**: 실행 전후 시간을 측정
- JSP 뷰에 에러를 반환하고 싶다면 -> WEB-INF/config/springmvc/context-servlet.xml 에서 SimpleMappingExceptionResolver 빈을 등록해서 매핑해줄 것

<details><summary><b>AdviceUsingXML.java, context-advice.xml 코드</b></summary>
<div markdown="1"><br>
```java
public class AdviceUsingXML {
	// TODO [Step 1-6] AdviceUsingXML 작성
	private static final Logger LOGGER = LoggerFactory.getLogger(AdviceUsingXML.class);
	public void beforeTargetMethod(JoinPoint thisJoinPoint) {
		LOGGER.debug("\nAdviceUsingXML.beforeTargetMethod executed.");
		@SuppressWarnings("unused")
		Class<? extends Object> clazz = thisJoinPoint.getTarget().getClass();
		String className = thisJoinPoint.getTarget().getClass().getSimpleName();
		String methodName = thisJoinPoint.getSignature().getName();
		// 현재 class, method 정보 및 method arguments 로깅
		StringBuffer buf = new StringBuffer();
		buf.append("\n== AdviceUsingXML.beforeTargetMethod : [" + className + "." + methodName + "()] ==");
		Object[] arguments = thisJoinPoint.getArgs();
		int argCount = 0;
		for (Object obj : arguments) {
			buf.append("\n - arg ");
			buf.append(argCount++);
			buf.append(" : ");
			// commons-lang 의 ToStringBuilder 를
			// 통해(reflection 을 이용)한 VO 정보 출력
			buf.append(ToStringBuilder.reflectionToString(obj));
		}
		// 대상 클래스의 logger 를 사용하여 method arguments 로깅
		// 하였음.
		LOGGER.debug(buf.toString());
	}
	public void afterTargetMethod(JoinPoint thisJoinPoint) {
		LOGGER.debug("AdviceUsingXML.afterTargetMethod executed.");
	}
	public void afterReturningTargetMethod(JoinPoint thisJoinPoint, Object retVal) {
		LOGGER.debug("AdviceUsingXML.afterReturningTargetMethod executed.");
		@SuppressWarnings("unused")
		Class<? extends Object> clazz = thisJoinPoint.getTarget().getClass();
		String className = thisJoinPoint.getTarget().getClass().getSimpleName();
		String methodName = thisJoinPoint.getSignature().getName();
		// 현재 class, method 정보 및 method arguments 로깅
		StringBuffer buf = new StringBuffer();
		buf.append("\n== AdviceUsingXML.afterReturningTargetMethod : [" + className + "." + methodName + "()] ==");
		buf.append("\n");
		// 결과값이 List 이면 size 와 전체 List 데이터를 풀어
		// reflection 으로 출력 - 성능상 사용 않는 것이 좋음
		if (retVal instanceof List) {
			List<?> resultList = (List<?>) retVal;
			buf.append("resultList size : " + resultList.size() + "\n");
			for (Object oneRow : resultList) {
				buf.append(ToStringBuilder.reflectionToString(oneRow));
				buf.append("\n");
			}
		} else {
		}
		// 대상 클래스의 logger 를 사용하여 결과값 로깅 하였음.
		LOGGER.debug(buf.toString());
		// return value 의 변경은 불가함에 유의!
	}
	public void afterThrowingTargetMethod(JoinPoint thisJoinPoint, Exception exception) throws Exception {
		LOGGER.debug("AdviceUsingXML.afterThrowingTargetMethod executed.");
		LOGGER.error("에러가 발생했습니다. {}", exception);
		// 원본 exception 을 wrapping 하고 user-friendly 한메시지를 설정하여 새로운 Exception 으로 re-throw
		throw new BizException("에러가 발생했습니다.", exception);
		// 여기서는 간단하게 작성하였지만 일반적으로 messageSource 를 사용한 locale 에 따른 다국어 처리 및 egov.
		// exceptionHandler
		// 를 확장한 Biz. (ex. email 공지 등) 기능 적용이 가능함.
	}
	public Object aroundTargetMethod(ProceedingJoinPoint thisJoinPoint) throws Throwable {
		LOGGER.debug("AdviceUsingXML.aroundTargetMethod start.");
		long time1 = System.currentTimeMillis();
		Object retVal = thisJoinPoint.proceed();
		// Around advice 의 경우 결과값을 변경할 수도 있음!
		// 위의 retVal 을 가공하거나 심지어 전혀 다른 결과값을 대체하여
		// caller 에 되돌려줄 수 있음
		long time2 = System.currentTimeMillis();
		// 메서드 실행 시간을 측정
		LOGGER.debug("AdviceUsingXML.aroundTargetMethod end. Time({})", (time2 - time1));
		return retVal;
	}
}
```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
				http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd">
	<!-- TODO [Step 1-7] xml 설정 방식의 AOP 설정 -->
	<!-- 모든 비지니스 메서드(Impl 로 끝나는 모든 class 의 모든 메서드)에 대해 다양한 Advice 기능을 동시에 적용하였음에 유의 -->
	<bean id="adviceUsingXML"
		class="egovframework.lab.aop.xml.AdviceUsingXML" />
	<aop:config>
		<aop:pointcut id="targetMethod"
			expression="execution(* egovframework.lab.aop..Xml*Impl.*(..))" />
		<aop:aspect ref="adviceUsingXML">
			<aop:before pointcut-ref="targetMethod"
				method="beforeTargetMethod" />
			<aop:after-returning pointcut-ref="targetMethod"
				method="afterReturningTargetMethod" returning="retVal" />
			<aop:after-throwing pointcut-ref="targetMethod"
				method="afterThrowingTargetMethod" throwing="exception" />
			<aop:after pointcut-ref="targetMethod"
				method="afterTargetMethod" />
			<aop:around pointcut-ref="targetMethod"
				method="aroundTargetMethod" />
		</aop:aspect>
	</aop:config>
</beans>
```
</div>
</details>

<br><br>

### 실행환경 - 데이터처리(Persistence)

**데이터 처리에 사용중인 오픈소스**

<img src="https://github.com/user-attachments/assets/df4e2121-c8f1-435b-bac7-5bf858e1f6b0" alt="Image" style="zoom:80%;" /> 

<br>

**알고가자:** 

- DataSource를 빈 등록?
  - DB와 연동을 위해서는 DataSource가 필수로 필요한데, "스프링 부트"는 dataSource 빈 이름으로 properties를 보고 자동으로 등록!
  - 반면, "순수 스프링"은 빈 등록도 직접 해줘야 한다.
- Transaction을 빈 등록?
  - 트랜잭션도 직접 빈 등록을 하면 다양한 커스텀이 가능! 물론, @Transactional 이 충분히 좋으므로 이걸로 끝내는게 많다.
  - 직접 빈 등록(EX:트랜잭션 매니저를 등록한 경우)을 한 경우 @Transactional(transactionManager = "txManager") 이런식으로 사용.
- iBatis를 빈 등록?
  - EgovAbstractDAO 에선 이 빈을 사용하므로 **필수로 등록**해줘야 한다.
- Spring에서 제공하는 `PropertyPlaceholderConfigurer`는 **외부 properties 파일을 로드하고, 이를 Bean 설정에서 사용할 수 있도록 해주는 기능**을 담당하는 클래스
  - 예로 context-common.xml 에서 PropertyPlaceholderConfigurer를 설정 시 "빈 등록xml"에서 &{db.drvier} 이런식 사용!

<br>

**iBatis를 사용한 Persistence  Layer 개발 순서**

1. [iBatis  설정  1]  SQL  Mapping  XML  파일  작성
   
   - 실행할  SQL문과  관련  정보  설정
   - SELECT/INSERT/UPDATE/DELETE,  Parameter/Result  Object,  Dynamic  SQL  등
   
2. [iBatis  설정  2]  iBatis  Configuration  XML  파일  작성
   
   - iBatis  동작에  필요한  옵션을  설정
   - \<sqlMap>:  SQL  Mapping  XML  파일의  위치
     - 최신 스프링은 아래 Bean정의에서 mapperLocations로 \<mapper> 역할까지 포함
   
3. **[스프링연동  설정]  SqlMapClientFactoryBean  정의 -> 빈 등록!**  
   **중요: 여기서 sqlMapClient로 등록한 빈을 EgovAbstractDAO에서 사용!!**
   
   - Spring와  iBatis  연동을  위한  설정
   - 역할)  iBatis  관련  메서드  실행을  위한  **SqlMapClient  객체**를  생성
   - dataSource:  DB  Connection  생성
   - configLocation:  iBatis  Configuration  XML  파일의  위치
   - mappingLocations:  모든  SQL  Mapping  XML  파일을  일괄  지정  가능
   
4. DAO  클래스  작성

   • 실행할 SQL문을 호출하기 위한 메서드 구현: SQL Mapping XML 내에 정의한 각 Statement id를 매개변수로 전달
   •  규칙)  SqlMapClientDaoSupport를  상속하는  **EgovAbstractDAO**  클래스를  상속받아  확장/구현

**코드 모음:**

이 실습의 대표적 생성 빈id: empService, empDAO, sqlMapClient, txManager, dataSource

<details><summary><b>DB연동을 위한 DataSource 부터 - bean id="dataSource</b></summary>
<div markdown="1"><br>
{src/test/resources/}META-INF/spring/context-datasource.xml -> DataSource 빈 등록!
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:jdbc="http://www.springframework.org/schema/jdbc"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
						http://www.springframework.org/schema/jdbc  http://www.springframework.org/schema/jdbc/spring-jdbc-4.0.xsd">
	<!-- TODO [Step 1-2] dataSource 설정: 빈 사용 편 -->
	<!-- &{db.?} 는 .properties에 선언한 변수 가져와 사용한 것! -> PropertyPlaceholderConfigurer를 
		따로 설정했으므로 가능! Spring에서 제공하는 PropertyPlaceholderConfigurer는 외부 properties 
		파일을 로드하고, 이를 Bean 설정에서 사용할 수 있도록 해주는 기능을 담당하는 클래스 -->
	<bean id="dataSource"
		class="org.apache.commons.dbcp2.BasicDataSource" destroy-method="close">
		<property name="driverClassName" value="${db.driver}" />
		<property name="url" value="${db.dburl}" />
		<property name="username" value="${db.username}" />
		<property name="password" value="${db.password}" />
		<property name="defaultAutoCommit" value="false" />
		<property name="poolPreparedStatements" value="true" />
	</bean>
	<!-- [Step 1-2] dataSource 설정: jdbc의 ebedded-db 사용 편 -->
	<!-- <jdbc:embedded-database id="dataSource" type="HSQL"> <jdbc:script location= 
		"META-INF/testdata/sample_schema_hsql.sql"/> </jdbc:embedded-database> -->
</beans>
```
META-INF/spring/context-common.xml -> PropertyPlaceholderConfigurer 설정 (properties 읽기 위해)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
				http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd">
	<!-- TODO [Step 1-2] PropertyPlaceholderConfigurer 설정 -->
	<context:property-placeholder location="classpath:/META-INF/spring/jdbc.properties" />
	<!-- TODO [Step 1-7] common 설정 확인 -->
	<!-- MessageSource 설정: 다국어 처리 빈 -->
	<bean id="messageSource" class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
		<property name="basenames">
			<list>
				<value>classpath:/message/message-common</value>
				<value>classpath:/org/egovframe/rte/fdl/idgnr/messages/idgnr</value>
				<value>classpath:/org/egovframe/rte/fdl/property/messages/properties</value>
			</list>
		</property>
		<property name="cacheSeconds">
			<value>60</value>
		</property>
	</bean>
	<!-- 전자정부 TraceHandler 설정 관련: 특정 상황에서 사용자가 핸들러 사용할 수 있게 하는 빈 -->
	<bean id="leaveaTrace" class="org.egovframe.rte.fdl.cmmn.trace.LeaveaTrace">
		<property name="traceHandlerServices">
			<list>
				<ref bean="traceHandlerService" />
			</list>
		</property>
	</bean>
	<bean id="traceHandlerService" class="org.egovframe.rte.fdl.cmmn.trace.manager.DefaultTraceHandleManager">
		<property name="reqExpMatcher">
			<ref bean="antPathMater" />
		</property>
		<property name="patterns">
			<list>
				<value>*</value>
			</list>
		</property>
		<property name="handlers">
			<list>
				<ref bean="defaultTraceHandler" />
			</list>
		</property>
	</bean>
	<!-- 경로 패턴 비교용 빈 -->
	<bean id="antPathMater" class="org.springframework.util.AntPathMatcher" />
	<bean id="defaultTraceHandler" class="org.egovframe.rte.fdl.cmmn.trace.handler.DefaultTraceHandler" />
	<!-- 스테레오 타입 Annotation 을 인식하여 Spring bean 으로 자동 등록하기 위한 component-scan 설정 -->
    <context:component-scan base-package="egovframework"/>
</beans>
```
META-INF/spring/jdbc.properties -> db연동에 사용할 변수 설정
```properties
#TODO [Step 1-2] dataSource 설정
db.driver=org.hsqldb.jdbcDriver
#db.dburl=jdbc:hsqldb:mem:testdb
db.dburl=jdbc:hsqldb:hsql://localhost/sampledb
db.username=sa
db.password=
```
META-INF/spring/context-trasaction.xml -> 여기선 트랜잭션 매니저만 직접 빈 등록(DataSource 연결)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd">
	<!-- TODO [Step 1-3] transaction 설정: 여기서는 transaction manager 만을 설정 -->
	<bean id="txManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource" />
	</bean>
</beans>
```
</div>
</details>

<details><summary><b>IBATIS 연동 설정</b></summary>
<div markdown="1"><br>
{src/test/resources/}META-INF/spring/context-sqlMap.xml -> sqlMapClient 빈 등록! (FactoryBean)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd">
	<!-- TODO [Step 1-4] Spring 의 iBATIS 연동 설정: 빈 등록 -->
	<!-- mappingLocations 영역을 주석 해제하여 Spring 의 ResourceLoader 형식으로 패턴 매칭에 의거한 일괄 로딩으로 처리가 가능하다. -->
	<bean id="sqlMapClient" class="org.egovframe.rte.psl.orm.ibatis.SqlMapClientFactoryBean ">
		<property name="configLocation"
			value="classpath:/META-INF/sqlmap/sql-map-config.xml" />
		<!-- <property name="mappingLocations" value="classpath:/META-INF/sqlmap/mappings/lab-*.xml" 
			/> -->
		<property name="dataSource" ref="dataSource" />
	</bean>
</beans>
```
META-INF/sqlmap/sql-map-config.xml -> iBATIS 연동위한 iBatis  Configuration  XML 설정
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE sqlMapConfig PUBLIC "-//ibatis.apache.org//DTD SQL Map Config 2.0//EN" "http://ibatis.apache.org/dtd/sql-map-config-2.dtd">
<sqlMapConfig>
	<!-- TODO [Step 1-5] iBATIS 의 sql-map-config 설정 파일 작성 -->
	<settings useStatementNamespaces="false"
		cacheModelsEnabled="true" />
	<!-- Spring 2.5.5 이상, iBATIS 2.3.2 이상에서는 iBATIS 연동을 위한 SqlMapClientFactoryBean 
		정의 시 mappingLocations 속성으로 Sql 매핑 파일의 일괄 지정이 가능하다. ("sqlMapClient" bean 설정 
		시 mappingLocations="classpath:/META- INF/sqlmap/mappings/lab-*.xml" 로 지정하였음) 
		단, sql-map-config-2.dtd 에서 sqlMap 요소를 하나 이상 지정하도록 되어 있으므로 아래 의 dummy 매핑 파일을 
		설정한다. -->
	<sqlMap resource="META-INF/sqlmap/mappings/lab-emp.xml" />
</sqlMapConfig>
```
META-INF/sqlmap/mappings/lab-emp.xml -> iBATIS 쿼리위한 SQL  Mapping  XML 설정
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE sqlMap PUBLIC "-//ibatis.apache.org//DTD SQL Map 2.0//EN" "http://ibatis.apache.org/dtd/sql-map-2.dtd">
<sqlMap namespace="Emp">
	<!-- TODO [Step 2-5] lab-emp.xml mapping xml 작성 -->
	<typeAlias alias="empVO"
		type="egovframework.lab.dataaccess.service.EmpVO" />
	<resultMap id="empResult" class="empVO">
		<result property="empNo" column="EMP_NO" />
		<result property="empName" column="EMP_NAME" />
		<result property="job" column="JOB" />
		<result property="mgr" column="MGR" />
		<result property="hireDate" column="HIRE_DATE" />
		<result property="sal" column="SAL" />
		<result property="comm" column="COMM" />
		<result property="deptNo" column="DEPT_NO" />
	</resultMap>
	<insert id="insertEmp" parameterClass="empVO"> 
<![CDATA[
insert into EMP
(EMP_NO,
EMP_NAME,
JOB,
MGR,
HIRE_DATE,
SAL,
COMM,
DEPT_NO)
values   (#empNo#, 
#empName#,
#job#,
#mgr#,
#hireDate#, 
#sal#,
#comm#, 
#deptNo#)
]]>
	</insert>
	<update id="updateEmp" parameterClass="empVO"> 
<![CDATA[
update EMP
set EMP_NAME= #empName#, 
JOB = #job#,
MGR = #mgr#,
HIRE_DATE = #hireDate#, 
SAL = #sal#,
COMM = #comm#, 
DEPT_NO = #deptNo#
where EMP_NO = #empNo#
]]>
	</update>
	<delete id="deleteEmp" parameterClass="empVO"> 
<![CDATA[
delete from EMP
where EMP_NO = #empNo#
]]>
	</delete>
	<select id="selectEmp" parameterClass="empVO"
		resultMap="empResult"> 
<![CDATA[
select EMP_NO, 
EMP_NAME, 
JOB,
MGR,
HIRE_DATE, 
SAL,
COMM, 
DEPT_NO
from EMP
where EMP_NO = #empNo#
]]>
	</select>
	<select id="selectEmpList" parameterClass="empVO"
		resultMap="empResult"> 
<![CDATA[
select EMP_NO, 
EMP_NAME, 
JOB,
MGR,
HIRE_DATE, 
SAL,
COMM, 
DEPT_NO
from EMP 
where 1 = 1
]]>
		<isNotNull prepend="and" property="empNo">
			EMP_NO = #empNo#
		</isNotNull>
		<isNotNull prepend="and" property="empName">
			EMP_NAME LIKE '%' ||
			#empName# || '%'
		</isNotNull>
	</select>
</sqlMap>
```
</div>
</details>

<details><summary><b>DB Sequence 기반의 ID Generation 사용 설정</b></summary>
<div markdown="1"><br>
META-INF/spring/context-idgen.xml -> select next value... 문법은 Hsqldb의 sequence 사용 문법!
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd">
	<!-- TODO [Step 1-6] Id Generation Service 설정 확인 -->
    <!-- from절의 DUAL의 경우 Oracle의 DUAL 테이블과 동일하다. 이를 위해 초기화 스크립트 sql(=sample_chema_hsql.sql)에 create를 미리 한다. -->
	<bean name="primaryTypeSequenceIds" class="org.egovframe.rte.fdl.idgnr.impl.EgovSequenceIdGnrService" destroy-method="destroy">
		<property name="dataSource" ref="dataSource" />
		<property name="query" value="SELECT NEXT VALUE FOR empseq FROM DUAL" />
	</bean>
</beans>
```
META-INF/testdata/sample_schema_hsql.sql -> DUAL, SEQUENCE 생성 및 테스트코드에서 사용하려는 DB 테이블 재생성 sql문
```sql
drop table jobhist IF EXISTS;
drop table emp IF EXISTS;
drop table dept IF EXISTS;
drop table dual IF EXISTS;
drop sequence empseq IF EXISTS;
CREATE SEQUENCE empseq START WITH 8000;
CREATE TABLE dual (
    dummy        VARCHAR(1) PRIMARY KEY
);
CREATE TABLE dept (
    dept_no          NUMERIC(2) NOT NULL,
    dept_name        VARCHAR(14),
    loc             VARCHAR(13),
    CONSTRAINT dept_pk PRIMARY KEY (dept_no),
    CONSTRAINT dept_name_uq UNIQUE (dept_name)
);
CREATE TABLE emp (
    emp_no          NUMERIC(4) NOT NULL,
    emp_name        VARCHAR(10),
    job             VARCHAR(9),
    mgr             NUMERIC(4),
    hire_date       DATE,
    sal             NUMERIC(7),
    comm            NUMERIC(7),
    dept_no         NUMERIC(2),
	CONSTRAINT emp_pk PRIMARY KEY (emp_no),
	CONSTRAINT emp_sal_ck CHECK (sal > 0),
	CONSTRAINT emp_ref_dept_fk FOREIGN KEY (dept_no) REFERENCES dept(dept_no)
);
CREATE TABLE jobhist (
    emp_no           NUMERIC(4) NOT NULL,
    start_date       DATE NOT NULL,
    end_date         DATE,
    job             VARCHAR(9),
    sal             NUMERIC(7),
    comm            NUMERIC(7),
    dept_no          NUMERIC(2),
    chg_desc         VARCHAR(80),
    CONSTRAINT jobhist_pk PRIMARY KEY (emp_no, start_date),
    CONSTRAINT jobhist_ref_emp_fk FOREIGN KEY (emp_no)
        REFERENCES emp(emp_no) ON DELETE CASCADE,
    CONSTRAINT jobhist_ref_dept_fk FOREIGN KEY (dept_no)
        REFERENCES dept (dept_no) ON DELETE SET NULL,
	CONSTRAINT jobhist_date_chk CHECK (start_date <= end_date)
);
-- dual 
INSERT INTO dual VALUES ('X');
--  Load the 'dept' table
--
INSERT INTO dept VALUES (10,'ACCOUNTING','NEW YORK');
INSERT INTO dept VALUES (20,'RESEARCH','DALLAS');
INSERT INTO dept VALUES (30,'SALES','CHICAGO');
INSERT INTO dept VALUES (40,'OPERATIONS','BOSTON');
--
--  Load the 'emp' table
--
INSERT INTO emp VALUES (7369,'SMITH','CLERK',7902,'1980-12-17',800,NULL,20);
INSERT INTO emp VALUES (7499,'ALLEN','SALESMAN',7698,'1981-02-20',1600,300,30);
INSERT INTO emp VALUES (7521,'WARD','SALESMAN',7698,'1981-02-22',1250,500,30);
INSERT INTO emp VALUES (7566,'JONES','MANAGER',7839,'1981-04-02',2975,NULL,20);
INSERT INTO emp VALUES (7654,'MARTIN','SALESMAN',7698,'1981-09-28',1250,1400,30);
INSERT INTO emp VALUES (7698,'BLAKE','MANAGER',7839,'1981-05-01',2850,NULL,30);
INSERT INTO emp VALUES (7782,'CLARK','MANAGER',7839,'1981-06-09',2450,NULL,10);
INSERT INTO emp VALUES (7788,'SCOTT','ANALYST',7566,'1987-04-19',3000,NULL,20);
INSERT INTO emp VALUES (7839,'KING','PRESIDENT',NULL,'1981-11-17',5000,NULL,10);
INSERT INTO emp VALUES (7844,'TURNER','SALESMAN',7698,'1981-09-08',1500,0,30);
INSERT INTO emp VALUES (7876,'ADAMS','CLERK',7788,'1987-05-23',1100,NULL,20);
INSERT INTO emp VALUES (7900,'JAMES','CLERK',7698,'1981-12-03',950,NULL,30);
INSERT INTO emp VALUES (7902,'FORD','ANALYST',7566,'1981-12-03',3000,NULL,20);
INSERT INTO emp VALUES (7934,'MILLER','CLERK',7782,'1982-01-23',1300,NULL,10);
--
--  Load the 'jobhist' table
--
INSERT INTO jobhist VALUES (7369,'1980-12-17',NULL,'CLERK',800,NULL,20,'New Hire');
INSERT INTO jobhist VALUES (7499,'1981-02-20',NULL,'SALESMAN',1600,300,30,'New Hire');
INSERT INTO jobhist VALUES (7521,'1981-02-22',NULL,'SALESMAN',1250,500,30,'New Hire');
INSERT INTO jobhist VALUES (7566,'1981-04-02',NULL,'MANAGER',2975,NULL,20,'New Hire');
INSERT INTO jobhist VALUES (7654,'1981-09-28',NULL,'SALESMAN',1250,1400,30,'New Hire');
INSERT INTO jobhist VALUES (7698,'1981-05-01',NULL,'MANAGER',2850,NULL,30,'New Hire');
INSERT INTO jobhist VALUES (7782,'1981-06-09',NULL,'MANAGER',2450,NULL,10,'New Hire');
INSERT INTO jobhist VALUES (7788,'1987-04-19','1988-04-12','CLERK',1000,NULL,20,'New Hire');
INSERT INTO jobhist VALUES (7788,'1988-04-13','1989-05-04','CLERK',1040,NULL,20,'Raise');
INSERT INTO jobhist VALUES (7788,'1990-05-05',NULL,'ANALYST',3000,NULL,20,'Promoted to Analyst');
INSERT INTO jobhist VALUES (7839,'1981-11-17',NULL,'PRESIDENT',5000,NULL,10,'New Hire');
INSERT INTO jobhist VALUES (7844,'1981-09-08',NULL,'SALESMAN',1500,0,30,'New Hire');
INSERT INTO jobhist VALUES (7876,'1987-05-23',NULL,'CLERK',1100,NULL,20,'New Hire');
INSERT INTO jobhist VALUES (7900,'1981-12-03','1983-01-14','CLERK',950,NULL,10,'New Hire');
INSERT INTO jobhist VALUES (7900,'1983-01-15',NULL,'CLERK',950,NULL,30,'Changed to Dept 30');
INSERT INTO jobhist VALUES (7902,'1981-12-03',NULL,'ANALYST',3000,NULL,20,'New Hire');
INSERT INTO jobhist VALUES (7934,'1982-01-23',NULL,'CLERK',1300,NULL,10,'New Hire');
commit;
```
</div>
</details>

<details><summary><b>aop 설정</b></summary>
<div markdown="1"><br>
/META-INF/spring/context-aspect.xml -> AOP 설정 (Exception 예외처리 핸들)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd">
	<!-- TODO [Step 1-8] aspect 설정 확인 -->
	<aop:config>
		<aop:pointcut id="serviceMethod" expression="execution(* egovframework.lab..impl.*Impl.*(..))" />
		<aop:aspect ref="exceptionTransfer">
			<aop:after-throwing throwing="exception" pointcut-ref="serviceMethod" method="transfer" />
		</aop:aspect>
	</aop:config>
    <!-- 빈 등록 -->
	<bean id="exceptionTransfer" class="org.egovframe.rte.fdl.cmmn.aspect.ExceptionTransfer">
		<property name="exceptionHandlerService">
			<list>
				<ref bean="defaultExceptionHandleManager" />
			</list>
		</property>
	</bean>
	<bean id="defaultExceptionHandleManager" class="org.egovframe.rte.fdl.cmmn.exception.manager.DefaultExceptionHandleManager">
		<property name="reqExpMatcher" ref="antPathMater" />
		<property name="patterns">
			<list>
				<value>**service.impl.*</value>
			</list>
		</property>
		<property name="handlers">
			<list>
				<ref bean="egovHandler" />
			</list>
		</property>
	</bean>
	<bean id="egovHandler" class="egovframework.lab.dataaccess.common.JdbcLoggingExcepHndlr" />
</beans>
```
</div>
</details>

<details><summary><b>DAO, Service 로직은?</b></summary>
<div markdown="1"><br>
EmpService, EmpVO 생략<br>EmpServiceImpl.java -> 서비스 구현 + EgovAbstractServiceImpl 상속<br>insert 부분의 sequence 기반 id generation 이 있어서 이것만 참고
```java
@Service("empService")
public class EmpServiceImpl extends EgovAbstractServiceImpl implements EmpService {
	// TODO [Step 2-3] EmpServiceImpl 작성 추가
	@Resource(name = "primaryTypeSequenceIds")
	EgovIdGnrService egovIdGnrService;
	@Resource(name = "empDAO")
	private EmpDAO empDAO;
	public BigDecimal insertEmp(EmpVO empVO) throws Exception {
		// ID generation Service 를 사용하여 key 를 땀. 여기서
		// primaryTypeSequenceIds 는 Sequence 기반임.
		BigDecimal generatedEmpNo = egovIdGnrService.getNextBigDecimalId();
		egovLogger.debug("EmpServiceImpl.insertEmp - generated empNo : " + generatedEmpNo);
		empVO.setEmpNo(generatedEmpNo);
		empDAO.insertEmp(empVO);
		return generatedEmpNo;
	}
    //...
}
```
EmpDAO.java -> EgovAbstractDAO 상속 (상속받은 메소드 사용하는 스타일)
```java
@Repository("empDAO")
public class EmpDAO extends EgovAbstractDAO {
	// TODO [Step 2-4] EmpDAO 작성
	public void insertEmp(EmpVO vo) {
		insert("insertEmp", vo);
	}
	public int updateEmp(EmpVO vo) {
		return update("updateEmp", vo);
	}
	public int deleteEmp(EmpVO vo) {
		return delete("deleteEmp", vo);
	}
	public EmpVO selectEmp(EmpVO vo) {
		return (EmpVO) select("selectEmp", vo);
		// return (EmpVO) select ("selectEmpUsingCacheModelLRU", vo);
	}
	@SuppressWarnings("unchecked")
	public List<EmpVO> selectEmpList(EmpVO searchVO) {
		return (List<EmpVO>) list("selectEmpList", searchVO);
	}
}
```
</div>
</details>

<br>

**MyBatis를  활용한  Persistence  Layer  개발**

1)  [MyBatis  설정  1]  SQL  Mapper  XML  파일  작성    설정
- 실행할  SQL문과  관련  정보  설정
- SELECT/INSERT/UPDATE/DELETE,  Parameter/Result  Object,  Dynamic  SQL  등
2)  [MyBatis  설정  2]  MyBatis  Configuration  XML  파일  작성
    - MyBatis  동작에  필요한  옵션을  설정
    - \<mapper>:  SQL  Mapper  XML  파일의  위치
      - **최신 스프링은 아래 Bean정의에서 mapperLocations로 \<mapper> 역할까지 포함**
3)  **[스프링연동  설정]  SqlSessionFactoryBean  정의 -> 빈 등록!**  
    **@Mapper 방식 사용시 MapperConfigurer 빈 등록 필수!**
- Spring와  MyBatis  연동을  위한  설정
- 역할)  MyBatis  관련  메서드  실행을  위한  SqlSession  객체를  생성 (IBATIS는 sqlMapClient이름)
- dataSource,  configLocation,  mapperLocations  속성  설정
4)  DAO  클래스  작성
    - 방법1) SqlSessionDaoSupport를  상속하는  **EgovAbstractMapper**  클래스를  상속받아  확장/구현
      - 실행할  SQL문을  호출하기  위한  메서드  구현:  SQL  Mapping  XML  내에  정의한  각  Statement  id를  매개변수로  전달
      - 단, namespace.qureyId 를 매개변수로 전달해서 구분하기도 하는듯. (qureyId만 해도 됨ㅇㅇ.)
    - 방법2) DAO  클래스를  Interface로  작성하고,  각  Statement  id와  **메서드명을  동일하게**  작성  **(Mapper  Interface  방식)**  
      **=> 권장하는 방식!** IBATIS와는 다르게 이런 부분이 MYBATIS가 좋네
      - Annotation을  이용한  SQL문  작성  가능
      - 메서드명을  Statement  id로  사용하기  때문에,  코드  최소화  가능
      - **Mapper Interface 방식을 사용 시 EgovAbstractMapper를 상속할 필요 없음**

**코드 모음:**

이 실습의 대표적 생성 빈id: empService, empDAO(+empMapper), sqlSession, txManager, dataSource

<details><summary><b>DB연동을 위한 DataSource 부터 - jdbc:embedded-database</b></summary>
<div markdown="1"><br>
{src/test/resources/}META-INF/spring/context-datasource.xml -> DataSource 빈 등록!
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:jdbc="http://www.springframework.org/schema/jdbc"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
						http://www.springframework.org/schema/jdbc  http://www.springframework.org/schema/jdbc/spring-jdbc-4.0.xsd">
	<!-- TODO [Step 1-2] DataSource 설정 확인 -->
	<!-- 
	<bean id="dataSource"
		class="org.apache.commons.dbcp2.BasicDataSource" destroy-method="close">
		<property name="driverClassName" value="${db.driver}" />
		<property name="url" value="${db.dburl}" />
		<property name="username" value="${db.username}" />
		<property name="password" value="${db.password}" />
		<property name="defaultAutoCommit" value="false" />
		<property name="poolPreparedStatements" value="true" />
	</bean>
	-->
	<!-- [Step 1-2] DataSource 설정
	embedded-database는 TEST환경에서 주로사용. 임베디드DB지원해줘서!
	그래서 위에처럼 따로 db아이디나 비번 설정 이런게 없이 자동으로 Spring이 설정해줄수 있는것! 
	특히, DB구동도 필요없음. 단, 위 빈등록 방식은 운영환경에서 주로사용하고 반드시 DB구동이 필요함. 메모리DB가 아니니까! 
	특히, script등록할 수 있어서 앱 실행 시 "글로벌"로 1번 수행한다. 
	단, 테스트땐 메소드마다 db초기화 필요할수도 있어서 편의상 @Before에 직접 execute로 외부쿼리 실행하게 하기도 한다. -->
	<jdbc:embedded-database id="dataSource"
		type="HSQL">
		<jdbc:script
			location="META-INF/testdata/sample_schema_hsql.sql" />
	</jdbc:embedded-database>
</beans>
```
META-INF/spring/context-common.xml -> PropertyPlaceholderConfigurer 설정 (여기선 생략가능. .properties 활용 안했었거든)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
				http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd">
   	<!-- dataSource property를 위한 PropertyPlaceholderConfigurer 설정(사용 안했긴 함) 
	<context:property-placeholder location="classpath:/META-INF/spring/jdbc.properties" />       
    -->
	<!-- 스테레오 타입 Annotation 을 인식하여 Spring bean 으로 자동 등록하기 위한 component-scan 설정 -->
    <context:component-scan base-package="egovframework"/>
    <!-- TODO [Step 1-7] 공통 설정 확인 -->
	<bean id="messageSource" class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
		<property name="basenames">
			<list>
				<value>classpath:/message/message-common</value>
				<value>classpath:/org/egovframe/rte/fdl/idgnr/messages/idgnr</value>
				<value>classpath:/org/egovframe/rte/fdl/property/messages/properties</value>
			</list>
		</property>
		<property name="cacheSeconds">
			<value>60</value>
		</property>
	</bean>
	<bean id="leaveaTrace" class="org.egovframe.rte.fdl.cmmn.trace.LeaveaTrace">
		<property name="traceHandlerServices">
			<list>
				<ref bean="traceHandlerService" />
			</list>
		</property>
	</bean>
	<bean id="traceHandlerService" class="org.egovframe.rte.fdl.cmmn.trace.manager.DefaultTraceHandleManager">
		<property name="reqExpMatcher">
			<ref bean="antPathMater" />
		</property>
		<property name="patterns">
			<list>
				<value>*</value>
			</list>
		</property>
		<property name="handlers">
			<list>
				<ref bean="defaultTraceHandler" />
			</list>
		</property>
	</bean>
	<bean id="antPathMater" class="org.springframework.util.AntPathMatcher" />
	<bean id="defaultTraceHandler" class="org.egovframe.rte.fdl.cmmn.trace.handler.DefaultTraceHandler" />
</beans>
```
META-INF/spring/jdbc.properties -> db연동에 사용할 변수 설정 (이것도 사용안했음. 생략.)
```properties
#TODO [Step 1-2] dataSource 설정
db.driver=org.hsqldb.jdbcDriver
#db.dburl=jdbc:hsqldb:mem:testdb
db.dburl=jdbc:hsqldb:hsql://localhost/sampledb
db.username=sa
db.password=
```
META-INF/spring/context-trasaction.xml -> 트랜잭션 매니저 빈 등록하면서 driven을 추가!(스캔)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd">
	<!-- TODO [Step 1-3] Transaction 설정 
	IBATIS에선 driven을 추가안했는데 아마 전역 @Transactional 설정해서 그런듯 하다. 
	driven을 추가하면 메서드에 개별로 따로 지정 가능하다는 말인것 같다. -> @Transactional  Anntation  스캔을  위해서는  <tx:annotation-driven  />을  선언해야  한다. 
	특히, tx:aop 형식으로 트랜잭션 대상을 지정하여 비즈니스 서비스 메서드에 일괄 지정하는 경우가 많다. -->
	<bean id="txManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource" />
	</bean>
	<tx:annotation-driven
		transaction-manager="txManager" />
</beans>	
```
</div>
</details>

<details><summary><b>MyBatis 연동 설정</b></summary>
<div markdown="1"><br>
{src/test/resources/}META-INF/spring/context-mybatis.xml -> sqlSession 빈 등록(FactoryBean)과 @Mapper setup
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd">
	<!-- SqlSession setup for MyBatis Database Layer -->
	<!-- TODO [Step 1-4] MyBatis와 Spring 연동 설정 -->
	<bean id="sqlSession"
		class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource" />
		<property name="configLocation"
			value="classpath:/META-INF/sqlmap/sql-mybatis-config.xml" />
		<!-- <property name="mapperLocations" value="classpath:**/lab-*.xml" /> -->
	</bean>
	<!-- MapperConfigurer setup for @Mapper -->
	<!-- TODO [Step 3-3] MyBatis의 Mapper Interface 자동스캔 설정 -->
	<bean class="org.egovframe.rte.psl.dataaccess.mapper.MapperConfigurer ">
		<property name="basePackage"
			value="egovframework.lab.dataaccess.service.impl" />
	</bean>
</beans>	
```
META-INF/sqlmap/sql-mybatis-config.xml -> MyBatis 연동위한 MyBatis  Configuration  XML 설정(여긴 \<mappers>방식)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- TODO [Step 1-5] MyBatis Configuration File 작성 -->
	<typeAliases>
		<typeAlias alias="empVO"
			type="egovframework.lab.dataaccess.service.EmpVO" />
	</typeAliases>
	<!-- MyBatis 연동을 위한 SqlSessionFactoryBean 정의 시 mapperLocations 속성으로 한 번에 
		모든 Mapper XML File을 설정할 수 있다. (<property name="mapperLocations" value="classpath:**/lab-*.xml" 
		/> 추가) 단, 아래 <mappers> 설정과 mapperLocations 설정 중 한가지만 선택해야 한다. -->
	<mappers>
		<mapper resource="META-INF/sqlmap/mappers/lab-dao-class.xml" />
		<mapper
			resource="META-INF/sqlmap/mappers/lab-mapper-interface.xml" />
	</mappers>
</configuration>
```
META-INF/sqlmap/mappers/lab-dao-class.xml -> MyBatis 쿼리위한 SQL  Mapping  XML 설정(DAO-Class Statement 호출 방식)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper   PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="Emp">
	<!-- TODO [Step 2-3] lab-dao-class.xml 작성 (EgovAbstractMapper 상속한 DAO) -->
	<!-- DAO 클래스의 Statement 호출 방식: 사용자가 직접 지정해준 ID 파라미터 값과 일치하는 Statement를 호출.
		동일한 Statement ID가 있으면, <mapper>의 namespace를 지정한다. 
		− namespace=A, statement id=insertEmp  A.insertEmp으로 호출 
		− namespace=B, statement id=insertEmp  B.insertEmp으로 호출 -->
	<resultMap id="empResult" type="empVO">
		<id property="empNo" column="EMP_NO" />
		<result property="empName" column="EMP_NAME" />
		<result property="job" column="JOB" />
		<result property="mgr" column="MGR" />
		<result property="hireDate" column="HIRE_DATE" />
		<result property="sal" column="SAL" />
		<result property="comm" column="COMM" />
		<result property="deptNo" column="DEPT_NO" />
	</resultMap>
	<insert id="insertEmp" parameterType="empVO"> 
<![CDATA[
insert into EMP (EMP_NO, EMP_NAME, JOB, MGR, HIRE_DATE, SAL, COMM, DEPT_NO) 
values(#{empNo}, #{empName}, #{job}, #{mgr}, #{hireDate}, #{sal}, #{comm}, #{deptNo})
]]>
	</insert>
	<update id="updateEmp" parameterType="empVO"> 
<![CDATA[
update EMP
set EMP_NAME = #{empName}, 
JOB = #{job},
MGR = #{mgr},
HIRE_DATE = #{hireDate}, 
SAL = #{sal},
COMM = #{comm},
DEPT_NO = #{deptNo} 
where EMP_NO = #{empNo}
]]>
	</update>
	<delete id="deleteEmp" parameterType="empVO"> 
<![CDATA[
delete from EMP
where EMP_NO = #{empNo}
]]>
	</delete>
	<select id="selectEmp" parameterType="empVO"
		resultMap="empResult"> 
<![CDATA[
select EMP_NO, EMP_NAME, JOB, MGR, HIRE_DATE, SAL, COMM, DEPT_NO 
from EMP
where EMP_NO = #{empNo}
]]>
	</select>
	<select id="selectEmpList" parameterType="empVO"
		resultMap="empResult"> 
<![CDATA[
Select EMP_NO, EMP_NAME, JOB, MGR, HIRE_DATE, SAL, COMM, DEPT_NO 
From EMP
where 1 = 1
]]>
		<if test="empNo != null">
			AND EMP_NO = #{empNo}
		</if>
		<if test="empName != null">
			AND EMP_NAME LIKE '%' || #{empName} || '%'
		</if>
	</select>
</mapper>
```
META-INF/sqlmap/mappers/lab-mapper-interface.xml -> MyBatis 쿼리위한 SQL  Mapping  XML 설정(Mapper-Interface Statement 호출 방식)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper   PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper
	namespace="egovframework.lab.dataaccess.service.impl.EmpMapper">
	<!-- TODO [Step 3-4] lab-mapper-interface.xml 작성 (Mapper Interface) -->
	<!-- Mapper 인터페이스의 Statement 호출 방식: 메소드명과 일치하는 Statement를 자동 호출. 
	이 때 MyBatis는 호출된 메서드가 포함된 인터페이스의 풀네임을 namespace 값으로 사용하기 때문에, 
	반드시 namesapce 값 을 지정해주어야 한다. 
	− namespace=x.y.z.EmpMapper, statement id=insertEmp -> 내부적으로 x.y.z.EmpMapper.insertEmp을 호출 -->
	<resultMap id="empResult" type="empVO">
		<id property="empNo" column="EMP_NO" />
		<result property="empName" column="EMP_NAME" />
		<result property="job" column="JOB" />
		<result property="mgr" column="MGR" />
		<result property="hireDate" column="HIRE_DATE" />
		<result property="sal" column="SAL" />
		<result property="comm" column="COMM" />
		<result property="deptNo" column="DEPT_NO" />
	</resultMap>
	<insert id="insertEmp" parameterType="empVO"> 
<![CDATA[
insert into EMP (EMP_NO, EMP_NAME, JOB, MGR, HIRE_DATE, SAL, COMM, DEPT_NO) 
values(#{empNo}, #{empName}, #{job}, #{mgr}, #{hireDate}, #{sal}, #{comm}, #{deptNo})
]]>
	</insert>
	<update id="updateEmp" parameterType="empVO"> 
<![CDATA[
update EMP
set EMP_NAME = #{empName}, 
JOB = #{job},
MGR = #{mgr},
HIRE_DATE = #{hireDate}, 
SAL = #{sal},
COMM = #{comm},
DEPT_NO = #{deptNo} 
where EMP_NO = #{empNo}
]]>
	</update>
	<delete id="deleteEmp" parameterType="empVO"> 
<![CDATA[
delete from EMP
where EMP_NO = #{empNo}
]]>
	</delete>
	<select id="selectEmp" parameterType="empVO"
		resultMap="empResult"> 
<![CDATA[
select EMP_NO, EMP_NAME, JOB, MGR, HIRE_DATE, SAL, COMM, DEPT_NO 
from EMP
where EMP_NO = #{empNo}
]]>
	</select>
	<select id="selectEmpList" parameterType="empVO"
		resultMap="empResult"> 
<![CDATA[
Select EMP_NO, EMP_NAME, JOB, MGR, HIRE_DATE, SAL, COMM, DEPT_NO 
From EMP
where 1 = 1
]]>
		<if test="empNo != null">
			AND EMP_NO = #{empNo}
		</if>
		<if test="empName != null">
			AND EMP_NAME LIKE '%' || #{empName} || '%'
		</if>
	</select>
</mapper>
```
</div>
</details>

<details><summary><b>DB Sequence 기반의 ID Generation 사용 설정</b></summary>
<div markdown="1"><br>
META-INF/spring/context-idgen.xml -> select next value... 문법은 Hsqldb의 sequence 사용 문법!
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd">
	<!-- TODO [Step 1-6] Id Generation Service 설정 확인 -->
    <!-- from절의 DUAL의 경우 Oracle의 DUAL 테이블과 동일하다. 이를 위해 초기화 스크립트 sql(=sample_chema_hsql.sql)에 create를 미리 한다. -->
	<bean name="primaryTypeSequenceIds" class="org.egovframe.rte.fdl.idgnr.impl.EgovSequenceIdGnrService" destroy-method="destroy">
		<property name="dataSource" ref="dataSource" />
		<property name="query" value="SELECT NEXT VALUE FOR empseq FROM DUAL" />
	</bean>
</beans>
```
META-INF/testdata/sample_schema_hsql.sql -> DUAL, SEQUENCE 생성 및 테스트코드에서 사용하려는 DB 테이블 재생성 sql문
```sql
drop table jobhist IF EXISTS;
drop table emp IF EXISTS;
drop table dept IF EXISTS;
drop table dual IF EXISTS;
drop sequence empseq IF EXISTS;
CREATE SEQUENCE empseq START WITH 8000;
CREATE TABLE dual (
    dummy        VARCHAR(1) PRIMARY KEY
);
CREATE TABLE dept (
    dept_no          NUMERIC(2) NOT NULL,
    dept_name        VARCHAR(14),
    loc             VARCHAR(13),
    CONSTRAINT dept_pk PRIMARY KEY (dept_no),
    CONSTRAINT dept_name_uq UNIQUE (dept_name)
);
CREATE TABLE emp (
    emp_no          NUMERIC(4) NOT NULL,
    emp_name        VARCHAR(10),
    job             VARCHAR(9),
    mgr             NUMERIC(4),
    hire_date       DATE,
    sal             NUMERIC(7),
    comm            NUMERIC(7),
    dept_no         NUMERIC(2),
	CONSTRAINT emp_pk PRIMARY KEY (emp_no),
	CONSTRAINT emp_sal_ck CHECK (sal > 0),
	CONSTRAINT emp_ref_dept_fk FOREIGN KEY (dept_no) REFERENCES dept(dept_no)
);
CREATE TABLE jobhist (
    emp_no           NUMERIC(4) NOT NULL,
    start_date       DATE NOT NULL,
    end_date         DATE,
    job             VARCHAR(9),
    sal             NUMERIC(7),
    comm            NUMERIC(7),
    dept_no          NUMERIC(2),
    chg_desc         VARCHAR(80),
    CONSTRAINT jobhist_pk PRIMARY KEY (emp_no, start_date),
    CONSTRAINT jobhist_ref_emp_fk FOREIGN KEY (emp_no)
        REFERENCES emp(emp_no) ON DELETE CASCADE,
    CONSTRAINT jobhist_ref_dept_fk FOREIGN KEY (dept_no)
        REFERENCES dept (dept_no) ON DELETE SET NULL,
	CONSTRAINT jobhist_date_chk CHECK (start_date <= end_date)
);
-- dual 
INSERT INTO dual VALUES ('X');
--  Load the 'dept' table
--
INSERT INTO dept VALUES (10,'ACCOUNTING','NEW YORK');
INSERT INTO dept VALUES (20,'RESEARCH','DALLAS');
INSERT INTO dept VALUES (30,'SALES','CHICAGO');
INSERT INTO dept VALUES (40,'OPERATIONS','BOSTON');
--
--  Load the 'emp' table
--
INSERT INTO emp VALUES (7369,'SMITH','CLERK',7902,'1980-12-17',800,NULL,20);
INSERT INTO emp VALUES (7499,'ALLEN','SALESMAN',7698,'1981-02-20',1600,300,30);
INSERT INTO emp VALUES (7521,'WARD','SALESMAN',7698,'1981-02-22',1250,500,30);
INSERT INTO emp VALUES (7566,'JONES','MANAGER',7839,'1981-04-02',2975,NULL,20);
INSERT INTO emp VALUES (7654,'MARTIN','SALESMAN',7698,'1981-09-28',1250,1400,30);
INSERT INTO emp VALUES (7698,'BLAKE','MANAGER',7839,'1981-05-01',2850,NULL,30);
INSERT INTO emp VALUES (7782,'CLARK','MANAGER',7839,'1981-06-09',2450,NULL,10);
INSERT INTO emp VALUES (7788,'SCOTT','ANALYST',7566,'1987-04-19',3000,NULL,20);
INSERT INTO emp VALUES (7839,'KING','PRESIDENT',NULL,'1981-11-17',5000,NULL,10);
INSERT INTO emp VALUES (7844,'TURNER','SALESMAN',7698,'1981-09-08',1500,0,30);
INSERT INTO emp VALUES (7876,'ADAMS','CLERK',7788,'1987-05-23',1100,NULL,20);
INSERT INTO emp VALUES (7900,'JAMES','CLERK',7698,'1981-12-03',950,NULL,30);
INSERT INTO emp VALUES (7902,'FORD','ANALYST',7566,'1981-12-03',3000,NULL,20);
INSERT INTO emp VALUES (7934,'MILLER','CLERK',7782,'1982-01-23',1300,NULL,10);
--
--  Load the 'jobhist' table
--
INSERT INTO jobhist VALUES (7369,'1980-12-17',NULL,'CLERK',800,NULL,20,'New Hire');
INSERT INTO jobhist VALUES (7499,'1981-02-20',NULL,'SALESMAN',1600,300,30,'New Hire');
INSERT INTO jobhist VALUES (7521,'1981-02-22',NULL,'SALESMAN',1250,500,30,'New Hire');
INSERT INTO jobhist VALUES (7566,'1981-04-02',NULL,'MANAGER',2975,NULL,20,'New Hire');
INSERT INTO jobhist VALUES (7654,'1981-09-28',NULL,'SALESMAN',1250,1400,30,'New Hire');
INSERT INTO jobhist VALUES (7698,'1981-05-01',NULL,'MANAGER',2850,NULL,30,'New Hire');
INSERT INTO jobhist VALUES (7782,'1981-06-09',NULL,'MANAGER',2450,NULL,10,'New Hire');
INSERT INTO jobhist VALUES (7788,'1987-04-19','1988-04-12','CLERK',1000,NULL,20,'New Hire');
INSERT INTO jobhist VALUES (7788,'1988-04-13','1989-05-04','CLERK',1040,NULL,20,'Raise');
INSERT INTO jobhist VALUES (7788,'1990-05-05',NULL,'ANALYST',3000,NULL,20,'Promoted to Analyst');
INSERT INTO jobhist VALUES (7839,'1981-11-17',NULL,'PRESIDENT',5000,NULL,10,'New Hire');
INSERT INTO jobhist VALUES (7844,'1981-09-08',NULL,'SALESMAN',1500,0,30,'New Hire');
INSERT INTO jobhist VALUES (7876,'1987-05-23',NULL,'CLERK',1100,NULL,20,'New Hire');
INSERT INTO jobhist VALUES (7900,'1981-12-03','1983-01-14','CLERK',950,NULL,10,'New Hire');
INSERT INTO jobhist VALUES (7900,'1983-01-15',NULL,'CLERK',950,NULL,30,'Changed to Dept 30');
INSERT INTO jobhist VALUES (7902,'1981-12-03',NULL,'ANALYST',3000,NULL,20,'New Hire');
INSERT INTO jobhist VALUES (7934,'1982-01-23',NULL,'CLERK',1300,NULL,10,'New Hire');
commit;
```
</div>
</details>

<details><summary><b>aop 설정</b></summary>
<div markdown="1"><br>
/META-INF/spring/context-aspect.xml -> AOP 설정 (Exception 예외처리 핸들)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd">
	<!-- TODO [Step 1-8] aspect 설정 확인 (예외처리) -->
	<aop:config>
		<aop:pointcut id="serviceMethod" expression="execution(* egovframework.lab..impl.*Impl.*(..))" />
		<aop:aspect ref="exceptionTransfer">
			<aop:after-throwing throwing="exception" pointcut-ref="serviceMethod" method="transfer" />
		</aop:aspect>
	</aop:config>
    <!-- 빈 등록 -->
	<bean id="exceptionTransfer" class="org.egovframe.rte.fdl.cmmn.aspect.ExceptionTransfer">
		<property name="exceptionHandlerService">
			<list>
				<ref bean="defaultExceptionHandleManager" />
			</list>
		</property>
	</bean>
	<bean id="defaultExceptionHandleManager" class="org.egovframe.rte.fdl.cmmn.exception.manager.DefaultExceptionHandleManager">
		<property name="reqExpMatcher" ref="antPathMater" />
		<property name="patterns">
			<list>
				<value>**service.impl.*</value>
			</list>
		</property>
		<property name="handlers">
			<list>
				<ref bean="egovHandler" />
			</list>
		</property>
	</bean>
	<bean id="egovHandler" class="egovframework.lab.dataaccess.common.JdbcLoggingExcepHndlr" />
</beans>
```
</div>
</details>

<details><summary><b>DAO, Service 로직은?</b></summary>
<div markdown="1"><br>
EmpService(인터페이스), EmpVO 생략<br>EmpServiceImpl.java -> EmpService 구현 + EgovAbstractServiceImpl 상속<br>insert 부분의 sequence 기반 id generation 와 EmpDAO vs EmpMapper 만 참고
```java
@Service("empService")
public class EmpServiceImpl extends EgovAbstractServiceImpl implements EmpService {
	// EmpDAO를 사용 (DAO방법1: 직접 쿼리ID 넘겨 매칭)
//	@Resource(name = "empDAO")
//	private EmpDAO empDAO;
	// EmpMapper를 사용 (DAO방법2: 메소드명을 쿼리ID와 같게 만들면 자동 매칭)
	 @Resource(name = "empMapper")
	 EmpMapper empDAO;
	@Resource(name = "primaryTypeSequenceIds")
	EgovIdGnrService egovIdGnrService; // primaryTypeSequenceIds 는 Sequence 기반으로 key 생성
//
	// insert Emp -> 메소드단에서 트랜잭션
	// @Transactional(value="txManager", propagation=Propagation.REQUIRED,
	// rollbackFor=Exception.class)
	@Transactional(value = "txManager", propagation = Propagation.REQUIRES_NEW, rollbackFor = Exception.class)
	public BigDecimal insertEmp(EmpVO empVO) throws Exception {
		// IDGeneration Service 를 사용하여 key 생성
		// primaryTypeSequenceIds 는 Sequence 기반임.
		BigDecimal generatedEmpNo = egovIdGnrService.getNextBigDecimalId();
		egovLogger.debug("EmpServiceImpl.insertEmp - generated empNo : " + generatedEmpNo);
		empVO.setEmpNo(generatedEmpNo);
		empDAO.insertEmp(empVO);
		return generatedEmpNo;
	}
```
EmpDAO.java -> EgovAbstractMapper 상속 (상속받은 메소드 사용하는 스타일) EgovAbstractMapper(MyBatis)나 EgovAbstractDAO(IBATIS) 상속받아서 사용하면 됨.
```java
@Repository("empDAO")
public class EmpDAO extends EgovAbstractMapper {
	// TODO [Step 2-2] EmpDAO 작성 (EgovAbstractMapper 상속한 DAO)
	public void insertEmp(EmpVO vo) {
		insert("Emp.insertEmp", vo);
	}
	public int updateEmp(EmpVO vo) {
		return update("Emp.updateEmp", vo);
	}
	public int deleteEmp(EmpVO vo) {
		return delete("Emp.deleteEmp", vo);
	}
	public EmpVO selectEmp(EmpVO vo) {
		return selectOne("Emp.selectEmp", vo);
	}
	@SuppressWarnings("unchecked")
	public List<EmpVO> selectEmpList(EmpVO searchVO) {
		return selectList("Emp.selectEmpList", searchVO);
	}
}
```
EmpMapper.java 인터페이스 작성 -> 메소드명과 쿼리ID 꼭 동일하게! (자동매핑)<br>Mapper Interface 방식을 사용하는경우 EgovAbstractMapper를 사용할 필요 없음.
```java
@Mapper("empMapper")
public interface EmpMapper {
	// TODO [Step 3-2] EmpMapper 작성 (Mapper Interface)
	public void insertEmp(EmpVO vo);
	public int updateEmp(EmpVO vo);
	public int deleteEmp(EmpVO vo);
	public EmpVO selectEmp(EmpVO vo);
	public List<EmpVO> selectEmpList(EmpVO searchVO);
}
```
</div>
</details>
<br><br>

### 실행환경 - 배치처리(Batch)

대용량 데이터를 한번에 처리하는 Batch 실행환경이고 Spring Batch를 활용한다. 

**자세한 Job, JobLauncher, JobRepository, Job Runner, Step 등 개념은 pdf 보기**

**배치 실행법은 주로 3가지:** 외부통신 트리거(ex:HTTP), 스케줄링(ex:cron), 터미널 명령

<details><summary><b>Spring Batch 구성요소(아키텍처)</b></summary>
<div markdown="1"><br>
<img src="https://github.com/user-attachments/assets/8a5e130b-2149-4c13-85a9-4d4e9ba25b6e" alt="Image" style="zoom:80%;" /><br>
<img src="https://github.com/user-attachments/assets/3facb69b-beeb-4da4-855a-351e7bed3f3b" alt="Image" style="zoom:80%;" /><br> 
<img src="https://github.com/user-attachments/assets/06bb11df-aa77-4427-96cf-340165400805" alt="Image" style="zoom:80%;" /><br> 
<img src="https://github.com/user-attachments/assets/839dc4e7-bde5-498d-9298-15f8b70160ed" alt="Image"  /><br>
<img src="https://github.com/user-attachments/assets/fafd6952-34e3-469f-93dc-286d8a31dbef" alt="Image" style="zoom:80%;" />  
</div>
</details>

<br>

**배치템플릿 프로젝트 생성:**

- 프로젝트 생성은 eGovFrame>Start>New Boot Batch Template Project>File(SAM)>Scheduler
- 테스트코드 생성은 eGovFrame>Test>Batch Job Test

**프로젝트 분석:**

- src/main/java: 

  - domain/trade에 **CustomerCredit.java, CustomerCreditIncreaseProcessor.java**, CustomerCreditRowMapper.java 가 있고
    - **CustomerCredit**는 엔티티!
    - **CustomerCreditIncreaseProcessor**는 비즈니스 로직!

  - jdbc/cubrid/incrementer에 CubridDataFieldMaxValueIncrementerFactory.java, CubridSequenceMaxValueIncrementer.java 가 있고
    - Cubrid DB 사용 시 자동 증가 값 처리!

  - scheduler에 EgovSchedulerJobRunner.java 가 있고  
    scheduler/support에 EgovJobLauncherDetails 가 있다.
    - 스케줄러 기반 배치 작업 자동실행 지원!

- src/main/resources:

  - batch/data/inputs 에 **csvData.csv**, txtData.txt 가 있고

  - batch/job/abstract 에 **eGovBase.xml** 있고
    - 공통적인 배치 Job 및 Step 설정을 정의하여 중복 제거 및 표준화!
    - 예로 미리 스프링빈에 등록한 **jobRepository도 연결** (공통로직이잖)

  - batch/job 에 **delimitedToDelimitedJob.xml**, fixedLengthToFixedLengthJob.xml, fixedLengthToJdbcJob.xml, fixedLengthToMybatisJob.xml 가 있고
    - **배치 Job 설정**. 아래 테스트 코드는 delimitedToDelimitedJob 사용! -> **File To File**이고, FlatFileItemWriter 사용한다!
    - fixedLengthToJdbcJob 의 경우 **File To DB(JDBC방식)**이고, EgovJdbcBatchItemWriter 사용한다!

  - batch/propertie 에 context-batch-datasource.xml, **context-batch-job-launcher.xml**, context-batch-mapper.xml, context-batch-scheduler.xml, context-common.xml, context-batch-scheduler-job.xml 이 있다.
    - **job-launcher**는 배치 JobLauncher 관련 설정!
      - 자세히: eGovBatchRunner빈 등록(Job 실행관리), jobLauncher빈 등록(Job 실행역할), jobRepository빈 등록(작업상태기록 저장소), jobExplorer빈 등록(모니터링), jobRegistry빈 등록(Job관리-여러Job 중 선택가능), jdbcTemplate빈 등록(JDBC 사용한 DB연결이 필요한경우)
      - Cubrid, Tibero 등 DBMS 에 맞게 코드 주석해제
    - scheduler관련은 스케줄링 설정
    - datasource, mapper, common 은 흔하니 PASS

- 나머지 더 있는데 배치와는 관련 없어 보여서 PASS

**아래 테스트 코드의 전체 프로세스 요약:**

```
[Test 코드 실행]
       │ EgovBatchRunner.start()
       ▼ 
[delimitedToDelimitedJob.xml 로딩]
       │ Job 및 Step 구성: CSV 파일 읽기 → 데이터 객체로 변환 → 데이터 처리 → CSV 파일 쓰기
       │ 상속: eGovBase.xml (jobRepository같은 공통로직 적용)
       ▼ 
[FlatFileItemReader]───▶ csvData.csv 읽기 
       │ 라인 매핑(EgovDefaultLineMapper)
       │ └─▶ 필드 분리(EgovDelimitedLineTokenizer) - "," 기준
       │ └─▶ 객체 변환(EgovObjectMapper)───▶ CustomerCredit 객체 생성(엔티티)
       ▼ 
[CustomerCreditIncreaseProcessor]───▶ 데이터 처리(신용점수 증가 등)
       ▼ 
[FlatFileItemWriter]───▶ csvOutput.csv에 결과 저장 
       ▼ 
[Test 코드]───▶ BatchStatus.COMPLETED 검증 (성공 여부 확인)
```

<details><summary><b>테스트코드에 사용한 전체 코드와 결과</b></summary>
<div markdown="1"><br>
테스트코드는 아래와 같다 -> **JOB은 delimitedToDelimitedJob.xml 사용 + Job Launcher도 context-batch-job-launcher.xml 사용**
```java
/** 
 * Test File Information 
 * Job:: /egovframework/batch/job/delimitedToDelimitedJob.xml
 * Job Launcher:: /egovframework/batch/context-batch-job-launcher.xml
 * job Parameters:: Date_Default Timestamp
 */ 
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = { "/egovframework/batch/context-batch-job-launcher.xml", "/egovframework/batch/job/delimitedToDelimitedJob.xml", "/egovframework/batch/context-batch-datasource.xml" })
public class BatchJobTestR{
	@Autowired
	@Qualifier("eGovBatchRunner")
	private EgovBatchRunner egovBatchRunner;
	@Test
	public void testJobRun() throws Exception {
		String jobName = "delimitedToDelimitedJob";
		JobParametersBuilder jobParametersBuilder = new JobParametersBuilder();
		jobParametersBuilder.addLong("timestamp", new Date().getTime());
		String jobParameters = egovBatchRunner.convertJobParametersToString(jobParametersBuilder.toJobParameters());
        //테스트시작 (정상구동 확인위해 Id받음)
		long executionId = egovBatchRunner.start(jobName, jobParameters);
		assertEquals(BatchStatus.COMPLETED, egovBatchRunner.getJobExecution(executionId).getStatus());
	}
}
```
delimitedToDelimitedJob.xml 보기 -> 전체적인 찐 배치 작업과정 설정 부분!!
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
		http://www.springframework.org/schema/batch http://www.springframework.org/schema/batch/spring-batch-3.0.xsd">
    <import resource="abstract/eGovBase.xml" />
    <!-- Job, Step을 결정하는 중요한 부분이 아래 코드이다. 인터벌2는 2개 데이터씩 -->
    <job id="delimitedToDelimitedJob" parent="eGovBaseJob" xmlns="http://www.springframework.org/schema/batch">
        <step id="delimitedToDelimitedStep" parent="eGovBaseStep">
            <tasklet>
                <chunk reader="delimitedToDelimitedJob.delimitedToDelimitedStep.delimitedItemReader" processor="delimitedToDelimitedJob.delimitedToDelimitedStep.itemProcessor"
                    writer="delimitedToDelimitedJob.delimitedToDelimitedStep.delimitedItemWriter" commit-interval="2" />
            </tasklet>
        </step>
    </job>
    <bean id="delimitedToDelimitedJob.delimitedToDelimitedStep.delimitedItemReader" class="org.springframework.batch.item.file.FlatFileItemReader" scope="step">
        <property name="resource" value="file:./src/main/resources/egovframework/batch/data/inputs/csvData.csv" />
        <property name="lineMapper">
            <bean class="org.egovframe.rte.bat.core.item.file.mapping.EgovDefaultLineMapper">
                <property name="lineTokenizer">
                    <bean class="org.egovframe.rte.bat.core.item.file.transform.EgovDelimitedLineTokenizer">
                        <property name="delimiter" value="," />
                    </bean>
                </property>
                <property name="objectMapper">
                    <bean class="org.egovframe.rte.bat.core.item.file.mapping.EgovObjectMapper">
                        <property name="type" value="egovframework.example.bat.domain.trade.CustomerCredit" />
                        <property name="names" value="name,credit" />
                    </bean>
                </property>
            </bean>
        </property>
    </bean>
    <bean id="delimitedToDelimitedJob.delimitedToDelimitedStep.delimitedItemWriter" class="org.springframework.batch.item.file.FlatFileItemWriter" scope="step">
        <property name="resource" value="file:./target/test-outputs/csvOutput.csv" />
        <property name="lineAggregator">
            <bean class="org.springframework.batch.item.file.transform.DelimitedLineAggregator">
                <property name="delimiter" value="," />
                <property name="fieldExtractor">
                    <bean class="org.egovframe.rte.bat.core.item.file.transform.EgovFieldExtractor">
                        <property name="names" value="name,credit" />
                    </bean>
                </property>
            </bean>
        </property>
    </bean>
    <bean id="delimitedToDelimitedJob.delimitedToDelimitedStep.itemProcessor" class="egovframework.example.bat.domain.trade.CustomerCreditIncreaseProcessor" />
</beans>
```
context-batch-job-launcher.xml -> JobLauncher 설정 (수많은 빈 등록)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:p="http://www.springframework.org/schema/p"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
			http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
			http://www.springframework.org/schema/batch http://www.springframework.org/schema/batch/spring-batch-3.0.xsd">
	<import resource="classpath:/egovframework/batch/context-batch-datasource.xml" />
	<import resource="classpath:/egovframework/batch/context-batch-mapper.xml" />
	<bean id="eGovBatchRunner" class="org.egovframe.rte.bat.core.launch.support.EgovBatchRunner">
		<constructor-arg ref="jobOperator" />
		<constructor-arg ref="jobExplorer" />
		<constructor-arg ref="jobRepository" />
	</bean>
	<bean id="jobLauncher" class="org.springframework.batch.core.launch.support.SimpleJobLauncher">
		<property name="jobRepository" ref="jobRepository" />
	</bean>
	<bean class="org.springframework.batch.core.configuration.support.JobRegistryBeanPostProcessor">
		<property name="jobRegistry" ref="jobRegistry" />
	</bean>
	<bean id="jobRepository" class="org.springframework.batch.core.repository.support.JobRepositoryFactoryBean"
		p:dataSource-ref="dataSource" p:transactionManager-ref="transactionManager"
		p:lobHandler-ref="lobHandler" />
	 <!-- tibero 사용시 주석해제-->
	 <!-- 
	<bean id="jobRepository" class="org.springframework.batch.core.repository.support.JobRepositoryFactoryBean"
		p:dataSource-ref="dataSource" p:transactionManager-ref="transactionManager"
		p:lobHandler-ref="lobHandler" p:databaseType="oracle" />
	 -->
	<!-- altibase/oracle 사용시 주석해제-->
	<!-- 
	<bean id="jobRepository" class="org.springframework.batch.core.repository.support.JobRepositoryFactoryBean"
		p:dataSource-ref="dataSource" p:transactionManager-ref="transactionManager"
		p:lobHandler-ref="lobHandler" p:isolationLevelForCreate="ISOLATION_DEFAULT"/>
	-->
	<!-- cubrid 사용시 주석해제 -->
	<!-- 
	<bean id="jobRepository" class="org.springframework.batch.core.repository.support.JobRepositoryFactoryBean"
		p:dataSource-ref="dataSource" p:transactionManager-ref="transactionManager" p:lobHandler-ref="lobHandler"
		p:incrementerFactory-ref="cubridDatabaseTypeFactory" p:databaseType="oracle" />
	<bean id="cubridDatabaseTypeFactory" class="egovframework.example.bat.jdbc.cubrid.incrementer.CubridDataFieldMaxValueIncrementerFactory">
		<constructor-arg index="0" ref="dataSource"/>
	</bean> 
	 -->
	<bean id="jobOperator" class="org.springframework.batch.core.launch.support.SimpleJobOperator"
		p:jobLauncher-ref="jobLauncher" p:jobExplorer-ref="jobExplorer"
		p:jobRepository-ref="jobRepository" p:jobRegistry-ref="jobRegistry" />
	<bean id="jobExplorer" class="org.springframework.batch.core.explore.support.JobExplorerFactoryBean"
		p:dataSource-ref="dataSource" />
	<bean id="jobRegistry" class="org.springframework.batch.core.configuration.support.MapJobRegistry" />
	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource" />
	</bean>
</beans>
```
eGovBase.xml -> 공통로직 추상화 (ex: jobRepository)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
		http://www.springframework.org/schema/batch http://www.springframework.org/schema/batch/spring-batch-3.0.xsd">
    <job id="eGovBaseJob" abstract="true" job-repository="jobRepository" restartable="true" xmlns="http://www.springframework.org/schema/batch" />
    <step id="eGovBaseStep" abstract="true" xmlns="http://www.springframework.org/schema/batch">
        <tasklet allow-start-if-complete="false" start-limit="" transaction-manager="transactionManager">
            <chunk commit-interval="" reader="" writer="" />
        </tasklet>
    </step>
</beans>
```
CustomerCredit.java 는 @Entity만든거라 간단해서 생략<br>CustomerCreditIncreaseProcessor.java
```java
public class CustomerCreditIncreaseProcessor implements ItemProcessor<CustomerCredit, CustomerCredit> {
	// 증가할 수
	public static final BigDecimal FIXED_AMOUNT = new BigDecimal("5");
	/**
	 * FIXED_AMOUNT만큼 증가 시킨 후 return
	 */
	@Override
	public CustomerCredit process(CustomerCredit item) throws Exception {
		return item.increaseCreditBy(FIXED_AMOUNT);
	}
}
```
**실행결과:**
```
input: csvData.csv
customer1	10
customer2	20
customer3	30
customer4	40
output: csvOutput.txt
customer1,15
customer2,25
customer3,35
customer4,45
```
</div>
</details>
<br><br>

### 실행환경 - 화면처리(Presentation)

MVC 패턴 개발은 앞에서도 봤고, 잘 이해하고 있어서 **로그인방식, 전역@ModelAttribute, 국제화 메시지, 자동완성Ajax만 추가적으로 보자.**

**해당 실습은 로그인 방식이 "세션"** 

- 본인은 직접 `httpSession.setAttribute()` 이런식으로 기록했었다.  
  그리고 컨트롤러(java)에서 로그인정보 필요할 경우 `@SessionAttribute(name = "loginMember", required = false) Member loginMember` 대신 AOP를 활용해서 `@Login` 방식으로 구현했었다.

- **이 실습은 @SessionAttributes("login")를 전역에 + @ModelAttribute("login") 으로 자동 세션 등록을 수행했다!**  
  물론, AOP로 @Login 방식으로 가져오는건 생략했고!

  - 로그인 정보를 세션에 기록할 땐 **클래스 전역에서 @SessionAttributes("login")**를 사용시 자동 생성한다.

    - 단, 지정한 네임(ex:"login")으로 로그인 **메소드 인자에서 @ModelAttribute("login")**를 해야한다.

    - ```java
      @Controller
      @SessionAttributes("login") //@ModelAttribute("login") 보고 자동 세션 등록
      public class LoginController {
          @RequestMapping(value = "/loginProcess1.do", method = RequestMethod.POST)
          public String loginProcess(@ModelAttribute("login") LoginCommand loginCommand) {
              return "login/loginSuccess";
          }
      }
      ```

    - ```jsp
      <!--jsp의 표현식 문법 사용하면 "모델,request,session" 등 속성을 바로 참조 가능-->
      <%@ page language="java" contentType="text/html; charset=UTF-8" %>
      <html>
          <head>
              <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
              <title>로그인 성공</title>
          </head>
          <body>
              <p>${login.id} 님은 로그인에 성공했습니다. </p>
          </body>
      </html>
      ```

  - 로그인 정보를 가져올땐 메소드 인자에서 @SessionAttribute를 사용시 가능하다.

<br>

**@ModelAttribute를 메소드인자 에서 사용 시:** 입력 데이터(ex:form)를 자동으로 원하는 객체로 변환!   
만약, 입력 데이터가 없어도 빈 객체를 생성! **(Null Pointer Exception 방지)**

**@ModelAttribute를 메소드에서 사용 시:** 매 요청마다 메소드가 사용!(@GetMapping 보다먼저)   
초기객체를 추가하여 JSP에게 제공하기 좋다. **(Null Pointer Exception 방지 + 전역 항상 적용)**

- `form modelAttribute="login"`: @ModelAttribute의 객체와 바인딩
- `<td><form:select path="loginType">`: 폼 필드 생성하고 path로 객체 속성과 바인딩(login.loginType)
- `<form:options items="${loginTypes}" itemValue="code" itemLabel="value"/>`: select태그 하위로써 드롭다운 데이터 생성
  - **items**: 옵션 데이터를 제공하는 모델 속성의 이름을 지정, `${loginTypes}`가 사용!
  - **itemValue**: 옵션의 값으로 사용할 속성 이름을 지정, `code`가 사용! (login.loginType.code)
  - **itemLabel**: 옵션의 레이블로 사용할 속성 이름을 지정, `value`가 사용! (login.loginType.value)
- `<td><form:errors path="loginType"/></td>`: path로 바인딩된 폼 필드의 오류 메시지를 출력

**@ModelAttribute를 메소드에서 사용 시 -> 사용안해야 할 때:** "공통으로 사용할 이유가 없을 때" or "파라미터 사용으로 인해 URL패턴이 안맞는 경우가 하나라도 존재할 때"

- ```java
  @RequestMapping(value = "/updateEmployee.do", method = RequestMethod.GET)
  public String defaultUpdateEmployee(@RequestParam("employeeid") String employeeid, ModelMap model) {
      model.addAttribute("employee", getEmployeeInfo(employeeid));
      return "modifyemployee";
  }
  
  //@ModelAttribute("employee") //-> 이거 안써야 잘 동작.
  //String employeeid를 스프링이 찾지 못해 에러가 뜬다. 업데이트 URL은 ...?id 로 파라미터 준다고 하지만, 다른 URL은 파라미터 안주는것도 있음.
  //그니까 에러 뜸. 안쓰는게 맞음. 어차피 defaultUpdateEmployee 에서만 필요한거라 "공통으로 쓸 이유도 없음". String id 못 찾는것도 여전히 문제고.
  public Employee getEmployeeInfo(String employeeid) {
      return employeeService.getEmployeeInfoById(employeeid);
  }
  ```

<details><summary><b>@ModelAttribute를 메소드에서 사용 코드:</b></summary>
<div markdown="1"><br>
**Java**
```java
/*
 * TODO [Step 1-2-5] @ModelAttribute - 모델의 초기화 ModelAttribute를 이용하여 loginTypes와
 * login 객체를 초기화 해주는 메소드를 만든다.
*/
@ModelAttribute("loginTypes")
protected List<LoginType> referenceData() throws Exception {
    List<LoginType> loginTypes = new ArrayList<LoginType>();
    loginTypes.add(new LoginType("A", "개인회원"));
    loginTypes.add(new LoginType("B", "기업회원"));
    loginTypes.add(new LoginType("C", "관리자"));
    return loginTypes;
}
@ModelAttribute("login")
protected Object referenceData4login() throws Exception {
    return new LoginCommand();
}
```
**JSP -> form modelAttribute="login" + loginTypes를 select태그의 option절에 사용하는 모습**
```jsp
<%@ page contentType="text/html; charset=UTF-8"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title><spring:message code="login.form.title" /></title>
<link type="text/css" rel="stylesheet" href="<c:url value='/css/egovframework/egov.css'/>"/>
</head>
<body>
<h3>Login Page</h3>
<form:form modelAttribute="login">
	<form:errors />
	<table>
		<tr>
			<!-- TODO [Step 1-2-2] SpringMessage 사용 -->
			<%-- <td><label for="id"><spring:message code="login.form.id" /></label></td> --%>
			<td><label for="id">아이디</label></td>
			<td><form:input id="id" path="id" /></td>
			<td><form:errors path="id" /></td>
		</tr>
		<tr>
			<td><label for="password"><spring:message code="login.form.password" /></label></td>
			<td><form:password id="password" path="password" /></td>
			<td><form:errors path="password" /></td>
		</tr>
		<tr>
			<td><label for="loginType"> 
			<spring:message	code="login.form.type" /></label></td>
			<td><form:select path="loginType">
				<option value="">---선택하세요---</option>
				<form:options items="${loginTypes}" itemValue="code" itemLabel="value" />
			</form:select></td>
			<td><form:errors path="loginType" /></td>
		</tr>
        <tr>
            <td colspan="3" align="right" >
                <input type="submit" value="<spring:message code="login.form.submit" />">
            </td>
        </tr>
	</table>
</form:form>
</body>
</html>
```
</div>
</details>
<br>

**국제화 메시지 설정**도 쉽다.   
**ResourceBundleMessageSource 빈을 message-common.propeties 연결하여 등록**하고,  
**JSP**에서 `<spring:message code="Login.form.id"/>` 이런식으로 사용.(propeties의 Login.form.id 매핑)  

마지막 **국제화(lang)는?**

1. **요청 수신**: 사용자가 `http://example.com?lang=ko` 처럼 lang을 담아 서버에 요청!
2. **인터셉터 실행(context-servlet.xml)**: `RequestMappingHandlerMapping`은 요청을 처리하기 전에 등록된 인터셉터(`LocaleChangeInterceptor`: 로케일 변경 역할)를 호출
3. **로케일 변경(context-servlet.xml)**: 인터셉터는 URL 파라미터에서 `lang=ko`를 추출하고, 이를 **세션**(`SessionLocaleResolver`: 로케일을 세션에 관리하는 리졸버)에 저장하여 로케일을 변경
4. **컨트롤러 호출**: 인터셉터 -> 컨트롤러 순서는 자명.
5. **로케일 적용**: 이후 모든 요청에서 변경된 로케일이 적용되어 국제화된 콘텐츠가 제공!

<details><summary><b>국제화 적용 코드:</b></summary>
<div markdown="1"><br>
```xml
<!-- setting Locale Locale Interceptor 설정하기  -->   
<!-- TODO [Step 1-3-1] Internalization - 국제화 관련 bean 설정  -->
<!-- HandlerMapping 설정방법 참고 -->
<bean id="localeChangeInterceptor" class="org.springframework.web.servlet.i18n.LocaleChangeInterceptor"
      p:paramName="lang" />
<bean id="localeResolver" class="org.springframework.web.servlet.i18n.SessionLocaleResolver" />
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping">
    <property name="interceptors">
        <list>
            <ref bean="localeChangeInterceptor"/>
        </list>
    </property>
</bean>
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter" />
```
</div>
</details>

<br>

**ajax로 jQuery UI 기능인 autocomplete(자동완성), autoSelected(자동완성-select태그) 구현하기**

- **(공통) MappingJackson2JsonView 빈 등록** -> 컨트롤러에서 JSON으로 클라에게 반환 목적

- **autocomplete(자동완성)**

  - ```jsp
    $("#searchName").autocomplete({
      source: function(request, response){
    	   $.ajax({
    		  url:"<c:url value='/autoComplete.do'/>",
    		  contentType: "application/x-www-form-urlencoded; charset=UTF-8",
    		  data: {keyword : encodeURIComponent(request.term)},	 
    		  dataType:'json',
    		  success: function(returnData, status){	  
    			response(returnData.resultList);
    		  }
    	  }); 
      },
      minLength : 1,
      select: function(event, ui){
    	    $("#searchName").val(this.value);
      }
    });
    ```

  - **source 함수**: Autocomplete의 소스코드 설정 -> 사용자가 입력 필드에 텍스트를 입력할 때마다 호출 (input 이벤트 감지는 Autocomplete API에 구현되어 있을거임)

  - **request.term**: 사용자가 입력 필드에 입력한 현재 텍스트

  - **encodeURIComponent(request.term)**: 입력한 텍스트를 URL 인코딩하여 서버로 전송 -> 특수 문자가 올바르게 전송

  - **minLength: 1**: 최소 1글자 이상 입력해야 자동 완성 기능이 작동
  - **select:** 자동 완성 목록에서 항목을 선택할 때 호출 -> searchName 필드로 선택 값(this.value) 저장

- **autoSelected(자동완성-select태그)**

  - ```jsp
    $('#superdeptid').change(function(){ 
    	$.ajax({
    		url: "<c:url value='/autoSelectDept.do'/>",
    		contentType: "application/x-www-form-urlencoded; charset=UTF-8",
    		data: {depth:2, superdeptid:encodeURIComponent($('#superdeptid 					option:selected').val())}, 
    		dataType: "json",
    		success: function(returnData, status){
    			$('#departmentid').loadSelectDept(returnData,"근무부서를 선택하세요."); 
    		}
    	});
    });
    ```

  - **$('#superdeptid').change(function() { ... }):** 부서 번호 선택 필드(`#superdeptid`)의 변경 이벤트를 감지

  - **data: { depth: 2, superdeptid: encodeURIComponent($('#superdeptid option:selected').val()) }:** superdeptid인 select태그 값으로 요청 데이터를 준비한다.

    - **depth**: 부서 깊이를 지정합니다 (이 경우 2) -> DB테이블에 상위부서(1), 하위부서(2), 하위하위부서(3) 이렇게 데이터가 있을 수 있을텐데 depth=2로 지정해서 가져온다는 것.

  - **$('#departmentid').loadSelectDept(returnData, "근무부서를 선택하세요."):** 하위 부서 목록을 로딩하는 함수를 호출

    - **loadSelectDepth** 함수는 직접 JS로 구현한 함수고, select 태그에 option태그를 append하는 로직을 가진다.

<details><summary><b>ajax 사용한 jsp 코드 보기:</b></summary>
<div markdown="1"><br>
```jsp
<%@ page contentType="text/html; charset=UTF-8" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt"%>
<%@ taglib prefix="ui" uri="http://egovframework.gov/ctl/ui"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title><spring:message code="easaycompany.employeelist.title"/></title>
<link type="text/css" rel="stylesheet" href="<c:url value='/css/easycompany.css'/>" />
<!-- jQuery -->
<link rel="stylesheet" href="<c:url value='/css/jqueryui.css'/>"/>
<script src="<c:url value='/js/jquery.js'/>"></script>
<script src="<c:url value='/js/jqueryui.js'/>"></script>
<script src="<c:url value='/js/select_load.js'/>"></script>
<script type="text/javascript">
$(document).ready(function(){
	//검색어에 대한 jquery ajax 자동완성 구현하기(jquery autocomplete)
    $("#searchName").autocomplete({
      source: function(request, response){
    	   $.ajax({
    		  url:"<c:url value='/autoComplete.do'/>",
    		  contentType: "application/x-www-form-urlencoded; charset=UTF-8",
    		  data: {keyword : encodeURIComponent(request.term)},	 
    		  dataType:'json',
    		  success: function(returnData, status){	  
    			response(returnData.resultList);
    		  }
    	  }); 
      },
      minLength : 1,
      select: function(event, ui){
    	    $("#searchName").val(this.value);
      }
    });
	//
   // TODO [Step 2-2-1] 부서번호에 대한 jquery autoSelected 기능 구현하기(jquery autoselected)
    $('#superdeptid').change(function(){ 
        $.ajax({
            url: "<c:url value='/autoSelectDept.do'/>",
            contentType: "application/x-www-form-urlencoded; charset=UTF-8",
            data: {depth:2, superdeptid:encodeURIComponent($('#superdeptid 					option:selected').val())}, 
            dataType: "json",
            success: function(returnData, status){
                $('#departmentid').loadSelectDept(returnData,"근무부서를 선택하세요."); 
            }
        });
    });
});
</script>
</head>
<body>
<br/>
<h2>AJAX : AutoComplete 기능 </h2>
<div id="searchform">
<form:form modelAttribute="searchCriteria" action="employeelist.do">
<table width="80%" border="0">
<tr>
        <td>이름 : <form:input path="searchName"/></td>
</tr>
</table>
</form:form>
</div>
<br/>
<br/>
<h2>AJAX : AutoSelected 기능 </h2>
<div id="editform">
<form:form modelAttribute="employee">
<table>
    <tr>
        <td>부서번호 : </td>
        <td>
            <form:select path="superdeptid">
                <option value="">상위부서를 선택하세요.</option>
                <form:options items="${deptInfoOneDepthCategory}" />
            </form:select>
            </td><td>
            <form:select path="departmentid">
                <option value="">근무부서를 선택하세요.</option>
                <form:options items="${deptInfoTwoDepthCategory}" />
            </form:select>
        </td>
    </tr>
</table>
</form:form>
</div>
</body>
</html>
```
</div>
</details>
<br><br>

### 실행환경 - easycompany 해답

실행환경 잘 적응했나 예제 문제로 학습하기(배치는 없음)

<details><summary><b>프로젝트 구조 분석:</b></summary>
<div markdown="1"><br>
1. **src/main/webapp:**
   - css
     - jqueryui css가 있음 -> 공홈에서 다운
     - images
       - button 이미지가 있음
     - js
       - jquery.js, **jqueryui.js**가 있음 -> 공홈에서 다운
       - **select_load.js** -> 직접 제작 (loadSelectDept함수: select태그 기입함수!)
     - scripts
       - easycompany.css -> 해당 플젝에서 사용하는 css
   - WEB-INF
     - config
       - springmvc/**context-servlet.xml**
         - 뷰리졸버, ajax로 autocomplete위한 MappingJacksonJsonView, Exception, Message, Pagination Tag, mvc:interceptors, mvc:view-controller, 컴포넌트 스캔까지(include-filter, exclude-filter)
       - jsp
       - cmmn/egovBizException.jsp, genneralException.jsp, taglibs.jsp
         -  부트에서 /error 하위 자동 페이지등록 출력해주던 에러페이지처럼 **"에러"페이지**
       - 다양한 페이지들 jsp...
       - lib -> 비어있음
       - tlds/ui.tld -> 뭔지 모르겠음.. pagination tag 관련 같은데..
       - web.xml -> 이건 뭐 DispatcherServlet 설정하는 극 상위 설정
2. **src/main/resources:**
   - db/sampledb.sql -> 초기 create table, insert 데이터 쿼리
     - message/message-common_en, message-common_ko, message-common.properties -> 메시지 국제화
       - jakarta commons validator (검증 메시지), spring message&validator, label(jsp에 사용) 등 메시지 관련 전부 모음
   - property/db.properties -> datasource에 사용할 db설정을 변수로 선언
     - spring
       - context-datasource.xml -> db.properties 변수 활용
       - **context-common.xml** -> 공통부분 설정 (메시지, 빈 스캔, **추적-LeaveaTrace, Pagination Tag**)
         - leaveaTrace는 Exception을 던지지 않고 후처리 로직 수행(로그, 모니터링 등)
           - TraceHandlerService는 로그를 넘어서 별도의 모니터링 연동 등 다양한 확장성을 제공
         - Pagination Tag는 공문의 라이프 사이클 확인: [페이징설명-공식문서](https://www.egovframe.go.kr/wiki/doku.php?id=egovframework%3Arte%3Aptl%3Aview%3Apaginationtag)
       - context-aspect.xml -> AOP 설정 (여긴 Exception AOP만)
       - context-sqlMap.xml -> MyBatis 설정
       - context-transaction.xml -> 트랜잭션 직접 빈 등록 (select절 read-only, 적용범위 등 상세 설정)
       - **context-validator.xml** -> Spring Modules-jakarta commons validator 빈 등록
     - sqlmap
       - config/sql-map-config.xml -> mybatis 설정..
       - sql/com/easycompany/*.xml -> mybatis 설정 sql문..
     - **validator**
       - validator.xml -> 어떤 객체에 어떤 룰 적용할지 설정
         - required: 값 비었나, integer: int형탠가, email: 이메일 형식인가... 등등
       - validator-rules.xml -> jakarta commons validator 의 공식 rules (커스텀 당연히 가능)
   - **log4j2.xml** -> 부트에서 properties에 debug, trace등 로그레벨 설정하던 그 설정을 하게 됨.
     - 부트 사용한다면 이건 properties에서 선언해도 충분할 듯!
3. **src/main/java:** 
   - interceptor(login-authentic), controller, service, dao, vo, mapper, exception, validator, ajax, imagepaginationrenderer 관련 자바 파일
   - 설명은 생략.
4. **pom.xml** -> Maven 빌드 툴 사용 (gradle이면 build.gradle 사용)
</div>
</details>

**WEB-INF 하위의 context-servlet.xml과 resources 하위의 context-*.xml 들이 설정이 많이 겹치는데 역할이 다르다.**

<img src="https://github.com/user-attachments/assets/d8e08cd6-6882-4b5b-b8a3-65b283a1495c" alt="Image" style="zoom:80%;" /> 

- **WEB-INF 하위 XML(오른쪽-Child)** → 컨트롤러 및 웹 관련 빈 관리
  - 예로 컴포넌트스캔(Controller), mvc:interceptors, mvc:view-controller 등
  - 특히, mvc:view-controller 는 컨트롤러 메소드 없이 **직접 URL을 뷰에 매핑**
- **resources 하위 XML(왼쪽-Root)** → 서비스, 리포지토리 및 공통 빈 관리
  - 예로 컴포넌트스캔(Repository, Service) 등

참고: 순수스프링은 web.xml에 필터, 디스패처 서블릿 다 세팅한 덕분에 main함수직접 작성 안해도 톰캣 위에서 동작  

> **ContextLoaderListener**는 **web.xml** 파일에 설정되어, 웹 애플리케이션이 시작될 때 **Spring** 애플리케이션 **컨텍스트를 초기화**
>
> 이 리스너는 **contextConfigLocation** 파라미터를 통해 **XML** 파일의 위치를 지정받고, 해당 파일을 로드하여 빈을 등록
>
> 이를 담당해주는 web.xml이 없으면 당연히 "자바코드"로 직접 작성해서 main함수로 실행해줘야 할거임.

헷갈리는 스프링의 xml 같은 config 파일들 인식 방법:

- web.xml에서 xml들 다 인식하게 설정하는건 자명.
- web.xml이 없다면?
  - Test코드라면 `@ContextConfiguration(locations = {"classpath:...*.xml"}` 이런식 등록
  - 부트라면 `@ImportResource("classpath...xml")` 이렇게 간단히 가능하다.
  - 추가방법(GPT): Java Config로 등록 or ClassPathXmlApplicationContext 로 등록 법이 있음  
    => 둘다 main함수에서 직접 applicationContext초기화 방식

<br>

void를 반환하면 loginSuccess.jsp를 자동으로 렌더링함 -> 원래 String 반환으로 직접 jsp 이름을 반환 했었음. (위에서 정리했었긴 함!)

```java
@RequestMapping(value="/loginSuccess.do", method=RequestMethod.GET)
public void loginSuccess() {
}
```

@ModelAttribute 좀 더 심화  
(위에서 정리했었긴 함!)

- 안써야 할 때?

  - ```java
    @RequestMapping(value = "/updateEmployee.do", method = RequestMethod.GET)
    public String defaultUpdateEmployee(@RequestParam("employeeid") String employeeid, ModelMap model) {
        model.addAttribute("employee", getEmployeeInfo(employeeid));
        return "modifyemployee";
    }
    
    //@ModelAttribute("employee") //-> 이거 안써야 잘 동작.
    //String employeeid를 스프링이 찾지 못해 에러가 뜬다. 업데이트 URL은 ...?id 로 주겠지만 다른 URL은 아니잖아.
    //그니까 에러 뜸. 안쓰는게 맞음. 어차피 defaultUpdateEmployee 에서만 필요한거라 "공통으로 쓸 이유도 없음". String id 못 찾는것도 여전히 문제고.
    public Employee getEmployeeInfo(String employeeid) {
        return employeeService.getEmployeeInfoById(employeeid);
    }
    ```

- 원래는 이렇게 써야지

  - ```java
    //deptInfoOneDepthCategory 객체를 JS에서 바로 사용하기 위해.
    @ModelAttribute("deptInfoOneDepthCategory")
    private Map<String, String> referenceDataOneDepthDept() {
        return departmentService.getDepartmentIdNameList("1");
    }
    
    //employee 추가하는 form 화면이라면 이걸 사용해줘야 null pointer 에러 방지.
    //물론, form 화면 매핑하는 GET컨트롤러에서 직접 model.addAttribute("employee", new Emplyee()) 해도 됨.
    //더 쉬운건 @ModelAttribute("employee") Employee employee 이렇게 인자로 넣으면 model.addAttribute를 자동으로 해준다는거~!
    @ModelAttribute("employee")
    public Employee defaultEmployee() {
        return new Employee();
    }
    ```

**(학습?) Pagination Tag**

[이거봐-공문](https://www.egovframe.go.kr/wiki/doku.php?id=egovframework%3Arte%3Aptl%3Aview%3Apaginationtag) 로 페이징 페이지 만드는것 좀 따로 정리. 커스텀 당연히 가능. 이거보고 커스텀 해야할듯ㅇㅇ.

**(학습?) 여러가지 Exception**

- **XML 설정 + AOP**: 예외가 발생하면 지정된 **JSP 뷰**로 매핑되어 예외 메시지를 출력

- **REST API 예외 처리**: 예외가 발생하면 **JSON** 형태의 응답을 반환
  
  - 이전에 이미 Boot로 하는거 정리했다. (부트는 뷰로 /error 하위 자동 페이지 반환 되는것도 기억)
  
- **전체적인 흐름 정리: 서비스계층, 컨트롤러 계층 나눠서 보기**

- | 단계 | 위치                                                | 처리 방식                                           | 설명 및 역할                                                 |
  | ---- | --------------------------------------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | ①    | Service Layer (`EgovAbstractServiceImpl`)           | `processException()` 호출하여 EgovBizException 발생 | 중요한 비즈니스 로직 오류 시 메시지를 다국어로 처리하여 명확히 사용자에게 전달 |
  | ②    | Service Layer (`EgovAbstractServiceImpl`)           | `leaveaTrace()` 호출하여 로그만 남김 (예외 미발생)  | 심각하지 않은 상황에서 로그만 남기고 정상 흐름 유지          |
  | ③    | Controller Layer (`SimpleMappingExceptionResolver`) | 서비스에서 던진 Exception을 JSP 뷰로 매핑           | Exception 종류에 따라 적절한 에러 페이지로 안내              |

  - **EgovAbstractServiceImpl** 를 보면 EgovBizException 발생 메소드(**processException**) 와 Exception 발생없이 후처리로직 메소드(**leaveaTrace**)를 제공한다.
    - processException는 어떤 예외든 EgovBizException로 예외처리 하게끔!
    - leaveaTrace는 내부적으로 TraceHandlerService로 로그처리 하는데 TraceHandlerService는 로그를 넘어서 별도의 모니터링 연동 등 다양한 확장성을 제공

  - **의문점: 왜 processException, leaveaTrace를 사용하지?**
    - try-catch로 간단히 logger쓰고 넘어가면 leaveaTrace를 사용할 필요 없을텐데...  
      try-catch에서 throw로 EgovBizException 직접 발생시켜도 될텐데...
    - **다국어 지원 메시지, 확장성, 표준화 로직** 때문에 사용!


**(학습?) 여러가지 Validator**

| 프레임워크                        | 특징                        | 사용 방법                                                    |
| --------------------------------- | --------------------------- | ------------------------------------------------------------ |
| **Valang**                        | XML 기반 유효성 검사        | XML 파일에서 규칙 정의                                       |
| **Jakarta Commons Validator**     | 템플릿 기반 유효성 검사     | validator-rules.xml 파일 사용 (Spring Module에서 Jakatra Commons도 제공 중) |
| **Spring (Java Bean Validation)** | 어노테이션 기반 유효성 검사 | @Valid, @Validated 어노테이션 사용                           |

- Jakarta Commons: eGovframe학습할 때 계속 이것만 사용하더라.

  - **validator-rules.xml** 에 기본제공 룰 말고 **커스텀 룰 추가**하는법: [공문](https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:rte:ptl:validation:add_rules_in_commons_validator)

  - **적용법:**

    1. **validation.xml, validator.jsp, URL매핑** : 공통 규칙 정의 (서버+클라이언트)

       validation.xml로 공통 규칙 정의

       validator.jsp로 \<validator:javascript> 수행 시 자동으로 검증 JS코드 생성하게 선언

       URL 매핑은 "컨트롤러"로 하던지 context-servlet.xml에서 \<mvc:view-controller> 등록하던지!

    2. **JSP validator 태그** : 클라이언트 측 JavaScript 코드 자동 생성 및 적용

       ```jsp
       <script type="text/javascript" src="<c:url value="/validator.do"/>"></script>
       <validator:javascript formName="employee"/>
       <!-- 위 코드 수행시 자동으로 검증하는 JS코드를 생성해 줌. -->  
       <!-- validateEmployee(this.form) 이런식으로 생성된 JS코드를 사용한 후(검증후) 버튼 submit 할 것 -->
       ```

    3. **Spring MVC Controller** : 서버 측에서 동일한 validation.xml 기반으로 다시 한번 검증 수행  
       -> beanValidator + bindingResult 활용

       DefaultBeanValidator는 @Validated 로 사용했던 스프링프레임워크의 검증 지원 빈이다.  
       이를 스프링모듈에서 Jakarta Commons 와 연동할 수 있게 지원해 준다.  
       따라서 DefaultBeanValidator 를 직접 사용하자!   
       **=> 즉, @Validated 는 beanValidator.validate() 를 자동 수행해 bindingResult에 결과를 담았었는데, 우린 이걸 수동으로 해야함!**

- **Spring 어노테이션 기반: 이건 우리가 하던 그거**임 ㅇㅇ. 이미 옛날에 정리해둠.

<br>

<br>

## 참고 지식

**여러가지**

<br><br>

### "로그인 인증 방식"

**참고: DB에 PW를 보통 암호화해서 넣어두는데, 로그인 인증 때 ID만 비교해서 값을 찾아오고 ID 있으면 같이 가져온 PW를 복호화해서 비교하는 순으로 하는게 효율적이고 빠르다는 것.**

<br>

#### (1) Session(서버 메모리) 방식

**AOP챕터의 -ArgumentResolver 예시 코드의 로그인 로직 참고**

동작흐름: HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 인터셉터1 -> 인터셉터2 -> 컨트롤러

interceptor 먼저 수행 후 -> argumentresolver 수행 순서

<br>

#### (2) JWT(토큰) -> Spring Security 사용 (코드포함)

<details><summary><b>Spring Security??</b></summary>
<div markdown="1">
- `Spring` 내에서 `Filter` 를 기반으로 동작한다. 즉, `Http Request` 가 `Dispatcher Servlet` 에 도착하기 이전, `Spring Security` 가 제공하는 기능을 활용하여, 인증 혹은 인가에 대한 사전 검증을 진행한다.
- **참고: HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 인터셉터1 -> 인터셉터2 -> 컨트롤러**
- **주요기능** -> [참고 문서](https://velog.io/@beberiche/Spring-Spring-Security-%EC%A3%BC%EC%9A%94-%EA%B8%B0%EB%8A%A5%EA%B3%BC-%EC%B2%98%EB%A6%AC-%EA%B3%BC%EC%A0%95-%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0)
  1. **UserDetails 인터페이스** -> 인증, 인가(권한부여) 기능 제공
     - 실무 관례: UserDetails 상속받는 CustomUserDetails 클래스 만들어 사용
     - getAuthorities() : 계정의 권한 목록을 리턴한다.
     - getPassword() : 계정의 비밀번호를 리턴한다.
     - getUsername() : 계정의 고유한 값을 리턴한다. (중복 없는 이메일, PK)
     - isAccountNonExpired() : 계정의 만료 여부를 리턴한다.
     - isAccountNonLocked() : 계정의 잠김 여부를 리턴한다.
     - isEnabled() : 계정의 활성화 여부를 리턴한다.
     - 기본 제공 메서드 말고, 커스텀하여 추가 메서드 예시? getEmail() 이런거!
  2. **UserDetailsService 인터페이스** -> 사용자 정보 조회 위한 서비스 단
     - loadUserByUsername(String token) : 사용자의 토큰에서 `claim` 정보를 기반으로, 사용자를 조회하여, 조회된 정보를 기반으로 `UserDetails` 인스턴스를 생성한다.
       - claim 정보란 JWT의 사용자 정보인 키-값 쌍 데이터!
  3. **Authentication 인터페이스** -> 사용자 정보와 권한 담음
     - 동작 과정을 보면 알지만 UsernamePasswordAuthenticationToken, UserDetails를 다 가지게 된다.
  4. **AuthenticationProvider 인터페이스** -> `authenticate()` 와 `supports()` 메서드를 오버라이드
     - authenticate() : 실제 인증 로직 구현이 이루어지는 메서드이고, `UsernamePasswordAuthenticationToken` 로 토큰을 만들며, 이 토큰 클래스는 `UserDetails` 클래스를 기반으로 `Authentication ` 객체로 반환 한다.
     - supports() : 현재 `Provider` 클래스가 특정 타입의 `Authentication` 객체를 지원하는 여부를 알려주는 메서드이다. 예로, 인증객체(=authentication)가 특정 인증 타입(=UsernamePasswordAuthenticationToken)을 지원하는지 판별
       - **예시 코드:** `return UsernamePasswordAuthenticationToken.class.isAssignableFrom(authentication);`
  5. **AuthenticationManager** -> `AuthenticationProvider` 구현체를 관리
     - 여러 Provider 구현체를 AuthenticationManager 에 담아 두면 상황에 따라 필요한 Provider 구현체를 불러오는 것이 가능하다.
  6. **SecurityContext** -> 인증 정보를 서버 내에서 보관 및 `@AuthenticationPrincipal`을 사용하여 인증 정보를 가져올 수 있다
     - 동작 과정을 보면 알지만 Authentication를 포함하는 구조다.
  7. **GrandAuthority** -> 현재 사용자가 가진 권한을 의미 (권한 확인에 사용)
     - 예로 ROLE_USER, ROLE_ADMIN 등을 정의하거나 체크할 수 있다.
</div>
</details>

<details><summary><b>Spring Security 처리과정</b></summary>
<div markdown="1"><br>
<img src="https://github.com/user-attachments/assets/f9f565fd-2380-4e9b-9307-2c5a9baaf1f3" alt="image" style="zoom:80%;" /><br>
1. 사용자가 로그인 정보와 함께 인증 요청을 진행한다.
2. `AuthenticationFilter` 가 해당 요청에 대하여 `UsernamePasswordToken` 객체를 생성한다.
3. `AuthenticationManager` 에, 해당 `UsernamePassowordToken` 객체를 전달한다.
4. `AuthetincationManager` 는 전달된 `UsernamePassowordToken` 객체의 인증을 지원하는 `AuthenticationProvider` 를 찾아, 인증을 요구한다.
5. `AutheticationProvider` 는 `authenticate()` 메서드를 호출하여, 인증 로직이 수행되도록 한다. 인증 로직의 경우 서비스마다 차이는 있겠으나, 공통적인 수행 방식은 다음과 같다.
   - `UserDetailsService` 에서 해당 `token` 값의 `claims` 정보를 기반으로 해당하는 사용자를 찾는다.
   - 인증 과정을 수행한 후, 성공적으로 인증이 되었다면 해당 사용자 객체 정보를 기반으로 `UserDetails` 객체를 생성하여 반환한다.
   - 인증이 완료된 `UserDetails` 객체를 기반으로, 사용자 정보 및 권한 정보가 담긴 `Authentication` 객체를 생성한다.
   - `Authentication` 객체를 `SecurityContext` 에 저장한다. 향후 `Controller` 에서 사용자 정보나 권한 정보가 필요한 경우, `@AuthenticationPrincipal` 등을 사용하여, 필요한 객체 정보를 불러올 수 있다.
</div>
</details>

<details><summary><b>Spring Security 장점</b></summary>
<div markdown="1">
- **간편한 인증 및 인가**: 기본적인 인증 및 인가 기능이 내장
- **자동 세션 관리:** 간단한 코드 설정으로 로그인 시 자동으로 세션을 관리하고, 로그아웃 시 세션을 무효화
- **CSRF 보호:** 기본적으로 CSRF 공격에 대한 보호 기능이 제공
  - CSRF(크로스 사이트 요청 위조, Cross-Site Request Forgery) 공격은 공격자가 사용자의 인증된 세션을 이용해, 사용자가 의도하지 않은 요청을 전송하게 만드는 공격 방식
- **비밀번호 암호화:** PasswordEncoder로 간단히 비밀번호 암호화
</div>
</details>

**예전버전 -> 현재버전 바뀐점들 많다!! [공문 참고](https://spring.io/blog/2022/02/21/spring-security-without-the-websecurityconfigureradapter)**

- 예전 버전은 `WebSecurityConfigurerAdapter`가 `Deprecated` 되었으니 `SecurityFilterChain`를 `Bean`으로 등록해서 사용하는 방식을 써라. 

- mvcMatchers가 없어지고 requestMatchers 로 바뀜 -> 내부에 new MvcRequestMatcher() 또는 new AntPathRequestMatcher() 조합을 사용해야지만 함!

- authorizeHttpRequest가 없어지고 authorizeHttpRequests 로 바뀜

- 예전이랑 코드가 많이 바꼈기 때문에 **공식문서 꼭 잘 참고! (테스트 코드도 제공!)**

<br>

**LEPL플젝에 적용해보기 (위 Spring Security 처리과정 그림 꼭 참고)**<br>**사용파일:** ApiConfigV2.java, MemberDetail.java, MemberDetailService.java, CustomAuthenticationFilter.java, CustomAuthenticationToken.java, CustomAuthenticationProvider.java, CustomAuthenticationSuccessHandler.java, CustomAuthenticationFailureHandler.java

기존 세션방식에서 직접 구현한 `Login`, `LoginMemberArgumentResolver`, `MemberCheckInterceptor` 클래스 같은 것 들이 필요없어 짐! 대신 웹이아닌 API이다 보니 커스텀할 클래스도 좀 더 많긴 하다.

`implementation 'org.springframework.boot:spring-boot-starter-security'` 의존성 추가 하면 바로 인증 필요로 접속 막힘 (기본값 username:user, password:콘솔창에 UUID)

특히 LEPL에 적용하려면... 웹 말고 API 방식 사용! (JWT)

- uid를 username로, password는 `""`로 빈값으로 통일함!<br>참고: 스프링 시큐리티는 기본적으로 Form 인증 방식을 채택 -> 우리 플젝은 API 이므로 json데이터 매핑위해 커스텀 필수! (그래서 추가 커스텀 파일 굉장히 많은 것!)

  - 물론,,,,,,  커스텀Filter를 Bean등록하는데 오류 터지는 중 ㅠㅠ<br>**AOP프록시에서 문제가 있었고, scurity패키지 부분은 제외해서 해결!**

  - 컨틀롤러에 login, logout 부분은 없애야 할걸?! 이미 커스텀 필터로 다 했고, 성공핸들러와 실패핸들러도 직접 커스텀 다 했으니까!! 반환도 직접 다 했고!

    - 실제로, 로그인 부분은 애초에 컨트롤러 있어도 그쪽은 타질 않음. 필터에서 이미 뺏어서 다 처리했기 때문!!

    - 로그아웃도 추가하면 애초에 필터에서 컨트롤러가기전에 먼저 뺏는거라 잘 처리됨!

      - **Logout 기능을 활성화하면 LogoutFilter 가 생김 (코드1줄!)**

      - 해당 필터 내부에는 이미 로그아웃 핸들러도 존재 -> 아래 다 자동!~!!

        1. 세션 무효화 핸들러

        2. 인증토큰, SecurityContext 삭제 핸들러

        3. 쿠키정보 삭제 핸들러

  - 마지막으로 @Login 애노테이션으로 memberId (세션에 저장된) 를 가져오는것처럼<br>**@AuthenticationPrincipal 로 바로 가져와 사용 가능!**

- API 기반의 애플리케이션에서 Spring Security로 로그인과 로그아웃을 처리할 때는 `formLogin`이나 `logout` 설정을 사용하지 않고, 주로 **JWT**나 **OAuth2** 같은 토큰 기반 인증 방식을 사용

  - 웹의 경우: formLogin(로그인페이지), loginProcessingUrl(로그인 처리 페이지), defaultSuccessUrl(성공시 이동페이지) 등등 이런식으로 구성!!! -> [참고 문서](https://velog.io/@wooyong99/Spring-Security)
  - **API는 JWT, OAuth2 같은 토큰을 활용하므로 방식이 다름 -> [참고 문서](https://padosol.tistory.com/69)**
    - API 기반 개발했고, 자세한 내용은 **전체 코드의 주석+참고 문서+Spring Security 처리과정 그림** 을 보면 이해할 수 있을것이다!

  <details><summary><b>전체 코드</b></summary>
  <div markdown="1"><br>
  **build.gradle**
  ```groovy
  //Spring Security
  implementation 'org.springframework.boot:spring-boot-starter-security'
  implementation "org.springframework.security:spring-security-web"
  implementation "org.springframework.security:spring-security-config"
  ```
  **ApiConfigV2.java**
  ```java
  /**
   * Spring Security 쓸거면??
   * 1. build.gradle 에 주석해제해서 라이브러리 다운
   * 2. security 패키지 파일 주석 전부 해제 (개노가다;;)
   */
  @Configuration // 설정 파일임을 알림
  @EnableWebSecurity // Spring Security
  @RequiredArgsConstructor
  @Slf4j
  public class ApiConfigV2 {
    private final CustomAuthenticationSuccessHandler customAuthenticationSuccessHandler;
    private final CustomAuthenticationFailureHandler customAuthenticationFailureHandler;
    private final AuthenticationConfiguration authenticationConfiguration;
    private final HandlerMappingIntrospector introspector;
    private final MemberDetailService detailService;
    /**
     * Spring Security의 앞단 설정을 할수 있다.
     * debug, firewall, ignore등의 설정이 가능
     * 단 여기서 resource에 대한 모든 접근을 허용하는 설정할수도 있는데
     * 그럴경우 SpringSecuity에서 접근을 통제하는 설정은 무시해버린다.
     */
    @Bean
    public WebSecurityCustomizer webSecurityCustomizer() {
      return (web) -> {
        web
            .ignoring()
            .requestMatchers(new AntPathRequestMatcher("/h2-console/**")
            );
      };
    }
  //
    /**
     * Spring Security 기능 설정을 할수 있다.
     * 특정 리소스에 접근하지 못하게 하거나 반대로 로그인, 회원가입 페이지외에 인증정보가 있어야
     * 접근할 수 있도록 설정할 수 있다.
     * 특정 리소스의 접근허용 또는 특정 권한 요구,로그인, 로그아웃, 로그인,로그아웃 성공시 Event
     * 등의 설정이 가능하다.
     */
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
      // csrf는 브라우저 없이 API 개발은 jwt같은 인증방식을 사용해서 굳이 필요가 없다고 한다.
      // 요청에 대한 설정
      // permitAll시 해당 url에 대한 인증정보를 요구하지 않는다.
      // authenticated시 해당 url에는 인증 정보를 요구한다.(로그인 필요)
      // hasAnyRole시 해당 url에는 특정 권한 정보를 요구한다.
      // resources에 대해 접근혀용을 해야지 브라우저에서 로그인없이 js파일이나 css파일에 접근할 수 있다.
      http
          .csrf(csrf -> csrf
              .ignoringRequestMatchers(new AntPathRequestMatcher("/**"))
          ) //전체 경로에 csrf 비활성화
          .authorizeHttpRequests(authorize -> authorize
              .requestMatchers(new MvcRequestMatcher(introspector,"/api/v1/members/**")).permitAll()
              .requestMatchers(PathRequest.toStaticResources().atCommonLocations()).permitAll() // 정적 자원
              .anyRequest().authenticated()
          )
          .addFilterBefore(ajaxAuthenticationFilter(),
              UsernamePasswordAuthenticationFilter.class) // 필터 가로채기!
          .logout(logout -> logout
              .logoutUrl("/api/v1/members/logout")
              .logoutSuccessHandler((request, response, authentication) -> {
                response.setStatus(HttpStatus.OK.value());
                response.setContentType(MediaType.APPLICATION_JSON_VALUE);
                response.getWriter().print("로그아웃 성공");
              }) // 로그아웃 성공 핸들러 (간단히 추가)
          );
      return http.build();
    }
  //
    @Bean
    public CustomAuthenticationFilter ajaxAuthenticationFilter() throws Exception {
      CustomAuthenticationFilter customAuthenticationFilter = new CustomAuthenticationFilter();
      customAuthenticationFilter.setAuthenticationManager(authenticationManager()); //manager 등록
      customAuthenticationFilter.setAuthenticationSuccessHandler(customAuthenticationSuccessHandler);
      customAuthenticationFilter.setAuthenticationFailureHandler(customAuthenticationFailureHandler);
  //
      //Form Login 방식은 기본적으로 DelegatingSecurityContextRepository 가 설정되어 있어서 REST API 방식을 위해 아래 추가
      //RequestAttributeSecurityContextRepository 는 세션을 저장 하지 않기 때문에 로그인 후 API 요청시 Anonymous 토큰이 생성되게 됩니다.
      //HttpSessionSecurityContextRepository 는 빈 세션을 만들 뿐! (어차피 토큰 사용할거라 빈 상태로 놔둘거임)
      customAuthenticationFilter.setSecurityContextRepository(
          new DelegatingSecurityContextRepository(
              new RequestAttributeSecurityContextRepository(),
              new HttpSessionSecurityContextRepository()
          ));
      System.out.println("test5");
      return customAuthenticationFilter;
    }
  //
    @Bean
    public AuthenticationManager authenticationManager() throws Exception {
      return authenticationConfiguration.getAuthenticationManager();
    }
  //
    @Bean
    public PasswordEncoder passwordEncoder(){
      return new BCryptPasswordEncoder(); //provider 에서 사용 위해 빈 등록
    }
  }
  ```
  **MemberDetail.java**
  ```java
  @Data
  public class MemberDetail implements UserDetails {
    private Member member;
    public MemberDetail(Member member) {
      this.member = member;
    }
    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
      // 특별한 권한 시스템을 사용하지 않을경우 -> role
      return Collections.EMPTY_LIST;
    }
    @Override
    public String getPassword() {
      return "";
    }
    @Override
    public String getUsername() {
      return member.getUid();
    }
    // 계정 만료여부 제공
    // 특별히 사용을 안할시 항상 true를 반환하도록 처리
    @Override
    public boolean isAccountNonExpired() {
      return true;
    }
    // 계정 비활성화 여부 제공
    // 특별히 사용 안할시 항상 true를 반환하도록 처리
    @Override
    public boolean isAccountNonLocked() {
      return true;
    }
    // 계정 인증 정보를 항상 저장할지에 대한 여부
    // true 처리할시 모든 인증정보를 만료시키지 않기에 주의해야한다.
    @Override
    public boolean isCredentialsNonExpired() {
      return false;
    }
    // 계정의 활성화 여부
    // 딱히 사용안할시 항상 true를 반환하도록 처리
    @Override
    public boolean isEnabled() {
      return true;
    }
  }
  ```
  **MemberDetailService.java**
  ```java
  @Service
  @RequiredArgsConstructor
  public class MemberDetailService implements UserDetailsService {
    private final MemberRepository repository;
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
      Member member = repository.findByUid(username);
      if(member == null)
        throw new UsernameNotFoundException("계정을 찾을 수 없습니다.");
      return new MemberDetail(member);
    }
    /**
     * 위에서 비밀번호는 사용안하냐고 물을수 있다.
     * 보통 Database에 비밀번호를 암호화 해서 저장하므로 Database에서 해당 정보를 찾을때 ID값과 암호화된 비밀번호를
     * 비교해가면서 찾는것보다 먼저 ID값으로 먼저 찾고 비밀번호를 복호화해서 비교하는게 더 빠르고 정확하다.
     * 때문에 대부분 회원가입할때 ID 중복을 확인하는 이유가 ID값으로 찾았을때 여러개의 계정정보가 검색되면 어떤 계정으로 인증을 해야할지 알수없기 때문.
     */
  }
  ```
  **CustomAuthenticationFilter.java**
  ```java
  public class CustomAuthenticationFilter extends AbstractAuthenticationProcessingFilter {
    private ObjectMapper objectMapper = new ObjectMapper();
    public CustomAuthenticationFilter() {
      // url과 일치하면 해당 필터가 가로채서 동작!
      super(new AntPathRequestMatcher("/api/v1/members/login"));
      System.out.println("test2");
    }
  //
    @Override
    public Authentication attemptAuthentication(HttpServletRequest request,
        HttpServletResponse response) throws AuthenticationException, IOException, ServletException {
      // POST 인지 확인
      System.out.println("test1");
      if (!"POST".equals(request.getMethod())) {
        throw new IllegalStateException("Authentication is not supported, no post");
      }
      // body를 login 정보에 매핑
      LoginDto loginDto = objectMapper.readValue(request.getReader(), LoginDto.class);
      // id, password cehck
      if (loginDto.getUid().isEmpty()) {
        throw new IllegalArgumentException("username or password is empty, no uid");
      }
      // 인증 되지 않은 토큰 생성 (나중에 인증)
      CustomAuthenticationToken token = new CustomAuthenticationToken(
          loginDto.getUid(),
          "" // uid만 사용하므로 password 생략
      );
      // manager 가 인증 처리하게 위임
      Authentication authentication = getAuthenticationManager().authenticate(token);
      return authentication;
    }
  //
    @Data
    public static class LoginDto {
      private String uid;
    }
  }
  ```
  **CustomAuthenticationToken.java**
  ```java
  public class CustomAuthenticationToken extends AbstractAuthenticationToken {
    private static final Long serialVersionUID = SpringSecurityCoreVersion.SERIAL_VERSION_UID;
    private final Object principal; //사용자 식별 정보(ex: username, id)
    private Object credentials; //사용자 인증 정보(ex: password, token)
    //인증 전 생성자
    public CustomAuthenticationToken(Object principal, Object credentials) {
      super(null); //인증전이라 role X
      this.principal = principal;
      this.credentials = credentials;
      setAuthenticated(false); //인증되지 않은 상태로 설정 (오버라이딩한 자식 메소드임)
    }
    //인증 후 생성자
    public CustomAuthenticationToken(Object principal, Object credentials,
        Collection<? extends GrantedAuthority> authorities) {
      super(authorities); //role 등록 -> 이 플젝은 role 미사용이긴 함!
      this.principal = principal;
      this.credentials = credentials;
      super.setAuthenticated(true); //인증된 상태로 설정 (부모)
    }
  //
    @Override
    public void setAuthenticated(boolean isAuthenticated) throws IllegalArgumentException {
      //애초에 true 로 설정할 일이 없어서 조건문 추가 위해 Override
      Assert.isTrue(!isAuthenticated, "Cannot set this token to trusted - use constructor which takes a GrantedAuthority list instead");
      super.setAuthenticated(false);
    }
    @Override
    public void eraseCredentials() {
      super.eraseCredentials(); //인증 정보 지우기
      this.credentials = null;
    }
    @Override
    public Object getCredentials() {
      return this.credentials;
    }
    @Override
    public Object getPrincipal() {
      return this.principal;
    }
  }
  ```
  **CustomAuthenticationProvider.java**
  ```java
  @Component
  @RequiredArgsConstructor
  public class CustomAuthenticationProvider implements AuthenticationProvider {
    private final MemberDetailService memberDetailService;
  //  private final PasswordEncoder passwordEncoder; // 원래 여기서 password 복화해 해서 비교!
  //
    @Override
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
      String username = authentication.getName();
      String password = (String) authentication.getCredentials();
      MemberDetail entity = (MemberDetail) memberDetailService.loadUserByUsername(username);
      return new CustomAuthenticationToken(entity, null, entity.getAuthorities());
    }
    @Override
    public boolean supports(Class<?> authentication) {
      return authentication.equals(CustomAuthenticationToken.class);
    }
  }
  ```
  **CustomAuthenticationSuccessHandler.java**
  ```java
  @Slf4j
  @Component
  public class CustomAuthenticationSuccessHandler implements AuthenticationSuccessHandler {
    private ObjectMapper objectMapper = new ObjectMapper();
    //Rest API 이기 때문에 redirect 처리를 하지 않고 response 에 바로 값
    @Override
    public void onAuthenticationSuccess(HttpServletRequest request,
        HttpServletResponse response,
        Authentication authentication) throws IOException {
      System.out.println("test3");
      MemberDetail member = (MemberDetail) authentication.getPrincipal();
  //
      response.setStatus(HttpStatus.OK.value());
      response.setContentType(MediaType.APPLICATION_JSON_VALUE);
  //    objectMapper.writeValue(response.getWriter(), member);
      objectMapper.writeValue(response.getWriter(), SUCCESS_LOGIN);
    }
  }
  ```
  **CustomAuthenticationFailureHandler.java**
  ```java
  @Slf4j
  @Component
  public class CustomAuthenticationFailureHandler implements AuthenticationFailureHandler {
    private ObjectMapper objectMapper = new ObjectMapper();
    @Override
    public void onAuthenticationFailure(HttpServletRequest request,
        HttpServletResponse response,
        AuthenticationException exception) throws IOException {
      String errMsg = "Invalid Username or Password";
      response.setStatus(HttpStatus.UNAUTHORIZED.value());
      response.setContentType(MediaType.APPLICATION_JSON_VALUE);
  //
      if(exception instanceof BadCredentialsException) {
        errMsg = "Invalid Username or Password";
      } else if(exception instanceof DisabledException) {
        errMsg = "Locked";
      } else if(exception instanceof CredentialsExpiredException) {
        errMsg = "Expired password";
      }
      objectMapper.writeValue(response.getWriter(), errMsg);
    }
  }
  ```
  **Controller.java**
  ```java
  /**
   * 일정 조회(3) - 모든 Lists(=하루단위 일정모음) 조회 -> 해당 회원꺼만
   */
  //아래 코드는 기존 argumentresolver 사용해서 @Login 으로 memberId 겟!
  @GetMapping(value = "/member/all")
  public ResponseEntity<List<ListsResDto>> findAllWithMemberTask(@Login Long memberId) {
    List<Lists> listsList = listsService.findAllWithMemberTask(memberId);
    if (listsList.isEmpty()) {
      return ResponseEntity.status(HttpStatus.NO_CONTENT).body(null);
    }
    List<ListsResDto> result = listsList.stream()
        .map(o -> new ListsResDto(o))
        .collect(Collectors.toList());
    return ResponseEntity.status(HttpStatus.OK).body(result);
  }
  //
  //아래 코드는 Spring Security 사용하여 @Login 대신 memberId를 가져온 코드
  @GetMapping(value = "/member/all")
  public ResponseEntity<List<ListsResDto>> findAllWithMemberTask(@AuthenticationPrincipal
      MemberDetail memberDetail) {
    Long memberId = memberDetail.getMember().getId();
    List<Lists> listsList = listsService.findAllWithMemberTask(memberId);
    if (listsList.isEmpty()) {
      return ResponseEntity.status(HttpStatus.NO_CONTENT).body(null);
    }
    List<ListsResDto> result = listsList.stream()
        .map(o -> new ListsResDto(o))
        .collect(Collectors.toList());
    return ResponseEntity.status(HttpStatus.OK).body(result);
  }
  ```
  </div>
  </details>

<br><br>

### "파일 업로드, 다운로드" 기본 지식

* **파일은 "스토리지"** 저장, 경로와 이름 등 **정보(EX: Item)는 "DB"** 저장
* **Item** - uploadFileName, storeFileName 는 필수 저장
  * uploadFileName(=업로드 파일명), storeFileName(=서버에 저장된 파일명) 둘 다 DB에 기록해놔야 함
  * 업로드 파일명들은 사람마다 중복될 수 있으며, 서버 파일명은 중복되면 안돼서 UUID 같은걸로 지정하기에 "둘 다 기록"
* **ItemForm** - Item의 Dto 용으로 만들어서 Form 데이터를 받는 도메인을 만들어줘야 함
  * 여기선 `MultipartFile` 타입을 사용해 데이터 받을거라 Item 에선 할 수 없기에 만들어줌
* **FileStore.java**
  * "스토리지" 에 저장하는 로직을 작성해서 "컨트롤러" 에서 사용
* **컨트롤러에서..**
  * `@GetMapping("/images/{filename}")` : \<img> 태그로 **이미지를 조회**할 때 사용
    * UrlResource 로 이미지 파일을 읽어서 @ResponseBody 로 이미지 바이너리를 반환
    * **경로에 "file:"** 을 넣어야 **내부저장소 경로**를 접근하는 것 (스토리지에 파일 있으니까!)
      * 이 부분을 통해 **"경로 설정" 을 꼭 해줘야 정상 접근**
  * `@GetMapping("/attach/{itemId}")` : **파일을 다운로드** 할때 실행
    * "/attach/{itemId}" - \<a> 태그 "href" 활용해 "파일명" 을 눌러서 접근하게 한 URL 경로
      * 파일 다운로드시 권한 체크같은 복잡한 상황까지 가정해서 이미지 id 를 요청하도록 함
    * 파일 다운이 되려면 반환할때 **"헤더" 가 필수**
    * 파일 다운로드시에는 고객이 업로드한 파일 이름으로 다운로드 하는게 좋다. 
      * Content-Disposition 헤더에 `attachment; filename="업로드 파일명"`

<br><br>

### TCP, UDP 통신(+멀티스레드) - 소켓,웹소켓,http

참고) Java NIO로 TCP 통신 예제도 있다 [참고문서](https://velog.io/@appti/java.nio-qrmmy51p)  
원래 기존 I/O는 스레드가 Block 되므로 불가능 했지만, New I/O에선 Non-Blocking을 제공해서 가능!

**[bchat 프로젝트](https://github.com/BH946/bchat) 참고 -> 코드와 기능개발.md**

HTTP 통신은 Spring 프로젝트 했던 모든게 HTTP 통신이었고 TCP, UDP를 알아보자

- 참고) 개념적으로 OSI 7계층 생각해보면??
  - HTTP가 응용계층이고 하위가 TCP, UDP전송계층이라서 분류하기 애매하다. 
  - 따라서 HTTP는 단기적인 TCP 커넥션을 하고 종료한다고 생각하자.
  - **(정정) HTTP1.1(우리가 주로 사용하는) 버전과 HTTP2 버전은 TCP 사용하고, HTTP3 버전은 UDP를 사용함.**
- TCP는 1:1 양방향 통신(EX: 실시간 채팅), UDP는 1:N 통신으로 전달순서 보장X (EX: 라디오)
  - 참고 문서: [UDP개념 예제](https://coding-factory.tistory.com/271), **[TCP개념 예제 - 그림 꼭 참고!!](https://coding-factory.tistory.com/270)**
- **소켓이란?** 
  - 계층별 각 프로토콜은 일종의 통신 규약일뿐, **프로토콜 구현을 위해 안에 들어갈 구체적인 구현부**인 함수가 필요할텐데 소켓에서 이러한 함수들의 body를 제공함
  - **TCP/IP 4계층에서 소켓은 전송 계층 위에 놓이며 프로토콜 제어를 위한 코드를 제공**
  - **즉, 소켓은 엔드포인트다. 통신의 양끝단.**   
    통신할 때 인터넷은 소켓을 찾아 **연결**하고 **데이터를 송수신**
  - <img src="https://github.com/user-attachments/assets/995e4a8b-4878-48fb-b8a7-ead00785f059" alt="Image" style="zoom:50%;" /> 
  - 자바 Socket라이브러리는 네트워크 부분의 끝 부분으로써 읽기/쓰기 인터페이스 제공<br>Spring에도 TCP와 UDP 지원 라이브러리가 있다.

- **웹소켓은??** **클라이언트가 웹이면 웹소켓을 사용하자!?** [참고문서](https://velog.io/@rhdmstj17/%EC%86%8C%EC%BC%93%EA%B3%BC-%EC%9B%B9%EC%86%8C%EC%BC%93-%ED%95%9C-%EB%B2%88%EC%97%90-%EC%A0%95%EB%A6%AC-2)
  - WebSocket(웹소켓) 통신은 **http에서 실시간 통신을 할 수 없다는 문제를 해결하기 위해 나온 첨단 기술** (HTTP는 실시간X. 물론 http polling 로 가능은 함)
  - **웹 소켓은 HTTP 레이어에서 작동하는 소켓으로 TCP/IP 소켓의 레이어가 다르다!**  
    앞에서 언급한 소켓과 여기서 언급하는 웹소켓의 차이를 분명히 알고 가자.
  - **웹에서 실시간 통신을 해야하는 상황에서 잘 쓰이는 프로토콜 중 하나이며 HTTP 레이어 위에서 작동!**
  - 웹소켓 이전의 비슷한 기술: http폴링, http스트리밍
- **소켓 vs 웹소켓 vs http**
  - (내생각)**http**는 TCP 위에서 동작하는건 알텐데 왜 얜 단방향 구조인가 생각해보면 7계층에서 그렇게 만드는것 같음. http가 상태없고 비연결성이게 만들려고 일부러 그랬을 듯.
  - **소켓**으로 채팅 프로그램 [bchat 프로젝트](https://github.com/BH946/bchat) 만든거 생각해보면 HTTP 사용한거 아니잖아. **웹소켓** 사용했으면 HTTP사용하니까 Swing(구이)말고 "브라우저에서 TCP 통신"으로 채팅 웹 만들었겠지.
    - 특히나 URL도 다름: `http://www.sample.com/` 과 같은 형식이 아니라 `ws://www.sample.com/` 과 같은 형식
    - 자바의 기본 소켓은 net에 있는 Socket라이브러리고, 웹 소켓은 websocket라이브러리.


<br>

**프로젝트에 TCP, UDP 를 적용하려면 아래를 알아두자. (JAVA 기준)**

- TCP통신의 경우: **클라이언트의 소켓 <-> 서버의 accept() 메서드에 의해 반환 된 소켓** 과 스트림 통신

  - Socket 클래스(=위 두 소켓을 의미), ServerSocket 클래스(=서버 단에서 외롭게 존재하는 1개의 소켓)

- UDP통신의 경우: **하나의 소켓이 송신, 수신에 둘 다 사용 가능**

  - DatagramSocket 클래스(=소켓), DatagramPacket 클래스(=데이터 관련)
  - UDP도 수신이 가능! 연결지향형이 아니라고 해서 송신만 하는건 아니다.

- **TCP, UDP 두 통신의 "서버는 항상 구동상태로써 클라 요청 무한 대기" 가 기본이다.**

  - TCP로 구현한 채팅방 예시

    - (서버) 채팅방 만든 방장은? ServerSocket 이 구동하며 accept()로 클라 소켓(=Socket) 만들려고 무한 대기 -> 클라 마다 새로운 Socket 생성! (멀티스레드 중요!!)

    - (클라) 채팅방 입장 유저는? Socket 를 서버에 요청해서 반환 받은 Socket을 사용
    - Socket 이 만들어진 순간 "클라<->서버" 실시간 연결!<br>이제 송, 수신 자유롭게 할 수 있다.

  - UDP로 구현한 회원가입, 로그인 예시
    - (서버) 회원가입, 로그인 응답은? receive()로 클라 요청을 무한 대기
    - (클라) 회원가입, 로그인 요청은? send()로 서버로 요청
    - 당연히 클라에서 receive()로 성공, 실패 응답을 반환 받을 수 있다.
  - TCP를 보면 여러 Thread가 생성 되는데(채팅 참여자가 N명 이니까) 송, 수신 순서도 중요한 멀티스레드 상황이라 **volatile 키워드로 교통정리 필수!**

<br>

**프로젝트에 왜 멀티스레드를 적용??**

- 애초에 클라 컴퓨터, 서버 컴퓨터 따로 구동해야하는데 그냥 한개의 컴퓨터에서 구현했기에 "서버에서 무한정 대기 Thread" 를 계속 구동하며 **main Thread 와 별개로 처리**할 목적
- TCP 서버에서 반환한 소켓들이 각각 new Thread 들이고 다들 실시간 채팅을 송, 수신 해야하기에 **다들 수신을 무한정 대기하는게 필수**라 멀티스레드는 필수!

<br><br>

### 리버스 프록시와 동시성 제어 기법 with Proxy?

**프록시(Proxy)란 '대리' 를 의미**

POINT: 동일한 요청을 매번 처리하는 것은 곧 리소스 낭비 와 서버의 부하 ->  본 서버에 도달하기 전에 새로운 서버(proxy server)를 미리 배치하여 중복 요청에 대해 (연산이 필요없는) 동일한 응답을 할 수 있다면, 클라이언트에겐 빠른 속도의 서비스를, 서버에게는 불필요한 부하를 줄이는 효과

**Forward Proxy, Reverse Proxy 그림**

<img src="https://github.com/user-attachments/assets/5d31af15-5af2-449e-b51c-6966a1c19ab7" alt="image" style="zoom:80%;" /> 

**프록시 서버 위치**

- Forward Proxy 서버는 클라이언트 앞에 놓여져 있는 반면,
- Reverse Proxy 서버는 웹서버/WAS 앞에 놓여 있다는 차이점이 있다.

**프록시 서버 통신 대상**

- Forward Proxy는 내부망에서 클라이언트와 Proxy 서버가 통신하여 인터넷을 통해 외부에서 데이터를 가져온다.
- Reverse Proxy는 내부망에서 Proxy 서버와 내부망서버가 통신하여 인터넷을 통해 요청이 들어오면 Proxy 서버가 받아 응답해준다.

**감춰지는 대상**

- Forward Proxy는 내부망에서 인터넷 상에 있는 서버에 요청할때 먼저 포워드 프록시 서버를 호출하고 프록시가 서버에게 요청을 보내게 되는데, 이로서 서버에게 클라이언트가 누구인지 감출수 있다. 즉, 서버 입장에서 응답받은 IP는 포워드 Forward Proxy의 IP이기 때문에 클라이언트가 누군지 알 수 없다.
- Forward Proxy는 직접 서버 url로 요청을 보내지만, Reverse Proxy는 프록시 서버 url로만 접근이 가능하다.이로서 Reverse Proxy는 본서버의 IP 정보를 숨길수 있는 효과를 얻게 된다.

**예시**

- 우리가 naver.com 을 요청하면 포워드 프록시 서버가 naver.com 리소스를 대신 구해와 클라이언트에게 내밀어준다(forward)고 생각하면 된다.
- 우리가 구성하는 일반적인 WEB(Apache, nginx) - WAS(Tomcat) 분리 형태를 Reverse 프록시라고 보면 된다.여기서 WEB(Apache, nginx)가 reverse proxy가 된다.물론 아파치 톰캣 같이 물리적인 한서버에 web, was가 존재한다면 reverse proxy라고 볼 수 없다.

[참고 문헌](https://inpa.tistory.com/entry/NETWORK-%F0%9F%93%A1-Reverse-Proxy-Forward-Proxy-%EC%A0%95%EC%9D%98-%EC%B0%A8%EC%9D%B4-%EC%A0%95%EB%A6%AC)

<br>

#### 1. 리버스 프록시 (Reverse Proxy)

리버스 프록시는 클라이언트의 요청을 여러 백엔드 서버로 분배하거나, 로드 밸런싱, 캐싱, 보안 강화 등의 목적으로 서버와 클라이언트 간의 중개 역할을 수행하는 프록시 서버이다. 클라이언트는 리버스 프록시 서버를 통해 백엔드 서버와 통신하며, 이를 통해 서버 부하 분산 및 보안을 강화할 수 있다.

- **주요 목적**: 로드 밸런싱, 보안 강화, 백엔드 서버 보호
- **예시**: Nginx와 Apache HTTP Server는 웹 서버이자 리버스 프록시 서버로 사용할 수 있다.
- **포지션**: 리버스 프록시는 웹서버/WAS 앞에 놓여 서버 요청을 처리하고, 클라이언트에게는 프록시 서버의 IP만 노출되게 하여 보안을 강화한다.

<br>

#### 2. 주로 사용하는 동시성 제어 기법

DB 행에 Lock을 거는 것은 애플리케이션 단이든 데이터베이스(ex:Oracle) 단이든 같음!<br>
-> 다만, 저장된 **데이터**의 안정성(DB) / **비즈니스 로직 레벨**에서의 동시성 문제 해결(앱) 이 목적

동시성 문제를 해결하기 위한 주요 방법들로는 비관적 잠금, 낙관적 잠금, 메시지 큐, 트랜잭션과 인덱스 등이 있다. 이들은 각각 데이터베이스 레벨과 애플리케이션 레벨에서 동시성을 관리하는 데 사용된다.

1. **비관적 잠금 (Pessimistic Locking)**
   - **사용 방법**: `@Lock(LockModeType.PESSIMISTIC_WRITE)` (쿼리에 적용)
   - **특징**: 데이터 충돌 가능성이 높을 때 사용. 트랜잭션을 유지하면서 데이터에 락을 걸어 다른 트랜잭션의 접근을 막음
2. **낙관적 잠금 (Optimistic Locking)**
   - **사용 방법**: `@Version` (엔티티 클래스의 필드에 적용)
   - **특징**: 충돌 가능성이 낮을 때 사용. 버전 번호를 통해 데이터 변경 여부를 검증하여 동시성을 관리
3. **메시지 큐 (Message Queue)**
   - **예시**: RabbitMQ, Kafka 등 사용
   - **특징**: 동시성 문제를 해결하기 어려운 경우 큐를 사용해 처리 요청을 순차적으로 처리. 단일 소비자가 요청을 순차적으로 처리하여 동시성을 해결
4. **트랜잭션과 인덱스**
   - **트랜잭션**: 여러 인스턴스에서 데이터를 변경할 때 ACID 속성을 보장하는 트랜잭션을 사용하여 데이터의 일관성을 유지
   - **인덱스**: 특정 컬럼에 유니크 인덱스를 설정하여 동시에 접근할 때 충돌을 방지. 유니크 인덱스는 데이터베이스가 자동으로 락을 지원

<br>

#### 3. (중요) Synchronized와 DB Lock 언제?

[개념확립 하기 좋은 문서](https://ksh-coding.tistory.com/125#3.%20%EB%8F%99%EC%8B%9C%EC%84%B1%20%EB%AC%B8%EC%A0%9C%EB%A5%BC%20%ED%95%B4%EA%B2%B0%ED%95%98%EB%8A%94%20%EB%B0%A9%EB%B2%95%20(feat.%203%EA%B0%80%EC%A7%80%20Lock)-1)

결론: 실무에선 DB Lock을 좀 더 사용.   
Synchronized(스레드에 락 검)는 하나의 프로세스에서만 동시성 제어하므로 여러 서버에서 하나의 DB에 접속하는건 동시성 제어가 불가함.  
따라서 DB 단에서 해결할 수 있는 DB Lock을 활용! (Spring에서 애노테이션으로 간편히 제공 중)

<br><br>

### "배포"에 필요한 지식 필독

클라우드에 GKE로 배포한건 DevOps 쳌리에서 보고.  
**여기선 cafe24 같이 서버 호스팅에서 하는것 위주로 필수 지식 정리.**

#### WS와 WAS?

**WAS가 WS를 포함하면 이제 WS는 필요 없나? 그건 아님**

WAS = WS + Web Container로 구성!
 클라 요청은 WS를 통해 받고, DB를 조회하는 등 비지니스 로직 수행은 Web Container로 전달

WAS가 WS를 포함하고 있기 때문에 WAS만으로도 웹 서버를 구축할 수는 있습니다. **그러나, 현재 보편적인 웹 서버 아키텍처는 WS와 WAS를 혼용해서 사용**하고 있으며, 많은 개발자들이 이 방식을 권장하고 있습니다. (아래 그림 방식을 권장)

![Image](https://github.com/user-attachments/assets/937eeb5b-0e15-4f05-b2ed-61b1ebb46569) 

**WAS는 WS의 부하 분산을 위해 고안된 서버**입니다. 따라서, WS와 혼용하면서 각각의 용도에 맞게 분배하며 사용해야 합니다. WS는 최전방에 위치하여 클라이언트의 요청을 받고 비즈니스 로직이 포함되지 않는 정적인 파일에 대해서는 WAS 앞에서 즉각적으로 처리해줍니다. 반면에 동적인 페이지에 대한 요청은 WAS로 넘겨 WS는 다음 요청을 처리할 수 있도록 합니다.

**WAS를 조금 더 활용한다면 최전방에 있는 WS에 하나 이상의 WAS를 연결하여 로드 밸런싱할 수도 있습니다. 더불어, WS는 Reverse Proxy 구조를 갖기 때문에 비즈니스 로직이 포함된 WAS를 직접 노출시키지 않음으로써 유연하고 고성능의 서비스를 구축할 수 있습니다.**

이렇게 되면 인터넷에 떠도는 많은 포스팅에서 얘기하는 것처럼 WS는 정적 파일을 서빙하고 WAS는 동적 파일을 서빙하는 형태가 됩니다.

<br>

**참고) WS로 HTTP(80)포트 들어오는것 도메인마다 개별 포트로 연결 가능**  
Apache의 가상호스트(VirtualHost) 기능을 사용하면 도메인별로 다른 포트로 프록시가 가능

```
- [app1.example.com]-> 해당 도메인이면 3000 포트로 프록시  
<VirtualHost *:80>
ServerName [app1.example.com](http://app1.example.com/)
ProxyPass / http://localhost:3000/
ProxyPassReverse / http://localhost:3000/
</VirtualHost>

- [app2.example.com]-> 해당 도메인이면 8080 포트로 프록시  
<VirtualHost *:80>
ServerName [app2.example.com](http://app2.example.com/)
ProxyPass / http://localhost:8080/
ProxyPassReverse / http://localhost:8080/
</VirtualHost>
```

<br>

#### WAS vs Gateway Interface 와 CGI vs WSGI vs ASGI ?

**WAS vs Gateway Interface**

- WAS는 웹 애플리케이션의 실행 환경 및 관련 기능들을 제공하는 반면에, Gateway Interface는 웹 서버와 웹 애플리케이션 사이의 **프로토콜을 정의**한다.
- WAS는 웹 애플리케이션을 직접 실행하는 반면에, Gateway Interface는 애플리케이션과 웹 서버간의 **메시지 전달 역할 만**을 한다.

**Gateway Interface를 쓰게 된 배경**

- 파이썬 웹 개발은 단순함과 유연성을 중요시 하기 때문에 **웹 애플리케이션과 웹 서버를 독립**적이고 **간단**하게 하고자 함
- **사실상 Nginx, Apache와 같은 리벅스 프록시와 같이 사용하면서 사실상 WAS의 역할 들을 수행해줌**

Common **Gateway Interface**(CGI)

- WS로 정적 컨텐츠를 응답해주는데, 동적 컨텐츠인 python의 경우 웹 프레임워크인 장고같은걸 사용하면 WSGI를 통해 응답이 가능하고 웹 프레임워크 없으면 CGI 를 통해 응답이 가능하다.
- (장점)언어에 독립적이고 아파치에 포함되어있고 사용 방식도 간단!
- (단점)단, 호출마다 프로세스 생성하는 자원낭비

Web Server **Gateway Interface**(WSGI)

- python **웹 어플리케이션과 웹 서버 사이의 표준 인터페이스를 정의 (=CGI, WSGI, ASGI 전부 이 개념이 기본)**
- **장고, 플라스크(=웹 프레임워크)가 사용 중. 실행은 django가 생성한 wsgi application을 사용.**

Asynchronous Server **Gateway Interface**(ASGI)

- WSGI와 비슷한 구조를 가지면서 기본적으로 요청을 비동기로 처리하는 방식 (WSGI 상위버전)
- fastapi(=웹 프레임워크)가 사용 중. 실행은 uvicorn을 활용
  - Fast api는 비동기 방식의 web server framework
  - uvicorn은 비동기 방식의 http server → ASGI

<br>

#### 톰캣은 모든 IP 접근 허용 기본. 차단방안은? - feat. putty, filezilla

톰캣은 기본적으로 모든 IP가 접근하게 허용하므로 차단도 고려해야함.
 **⇒ 크게 “로컬에서만 접근가능하게 설정”과 “방화벽 사용 방식” 고려!**

1. Spring Boot가 외부에서 직접 접근되지 않도록 **127.0.0.1에서만 동작하도록 설정** (즉, 로컬호스트에서만 쓰라고! 디스이즈 DB가 그렇게 해놨잖아? 그거처럼 ㅇㅇ.)

   예로 터미널에서 netstat -lntp 로 포트설정 체크해보면 **mysqld이름으로 127.0.0.1:3306 임.
    따라서 mysql은 외부에서 접근을 못하고 로컬에서만 가능!(cafe24 로컬.내부에서만)
    실제로 python코드도** `db = pymysql.connect(host="127.0.0.1", port=3306, user=USER, passwd=PASSWD, db=DB, charset='utf8')` 이런식으로 사용했었음.

2. **UFW 또는 iptables로 8081, 8082 포트를 외부에서 차단** (즉, 방화벽쓰라고)

<br>

**putty와 filezilla는 왜그리 접근들이 외부에서 수월할까?**

**Putty, Filezilla란?**
 putty는 우선 리눅스같은 서버에 SSH 지원해주는 프로그램 ⇒ 리눅스처럼 터미널 다루게
 파일질라는 FTP서비스로 파일전송, 관리에 도움을 주는 프로그램 ⇒ 로컬 디렉토리 처럼

**결론부터 말하면?** mysql은 로컬호스트로만 접근 가능하게 해놨지만, 파일질라, putty 관련은 외부IP에서도 접근 가능하게 해놓은것

**netstat -lntp 로 포트 설정 검색해서 보면?**
 putty, 파일질라를 포트 60022로 접근하는데 실제로도 0.0.0.0:60022 가 있으며 이름도 sshd이다.
 참고) 0.0.0.0 은 모든 IP 접근을 허용

<br>

#### 무중단 배포는? 

웹서버는 apache, nginx를 주로 사용하는데 무중단 배포 nginx로 하는거 살펴보장
 ⇒ GCP GKE로 했던건 쿠버네티스 기능 활용한건데 "DevOps쳌리.md" 참고

**이 홈페이지에 정말 자세히 잘 나와있으니 이걸 보고 따라하기! [문서](https://github.com/jojoldu/springboot-webservice/blob/master/tutorial/7_NGINX_SSL_무중단배포.md)**  
=> 추후에 cafe24에서도 따라할건데 따라하면 그 내용은 여기 쳌리에 자세히 정리할게.

![Image](https://github.com/user-attachments/assets/0614c83e-1f3e-43b1-8571-e4b3f6276bc7) 

TIP1) 운영환경에 yml에서 자동 DB생성 그건 사용안하는게 더 안전(실수로 깃에 올리면 모르고 사용하게되면 초기화.. 머리 개 아프지)

TIP2) 운영환경 yml은 외부에 생성해서 관리하는걸 권장! (DB정보 등 때문에. jar로 패키징 한다해도 뭐.. 소스 프로젝트도 git에 다 올리니까 우린.. 조심해야지?). 내부 resources 에 있는 yml과 외부에 생성한 yml 둘다 스프링 부트에서 사용하게 코드로 설정하길 바람!(코드간단)

TIP3) Nginx가 외부의 요청을 받아 뒷단 서버로 요청을 전달하는 행위를 **리버스 프록시**

**간략히 구현방법 설명하자면?**
 nginx 설치 및 내부 앱 포트 연결 → 안전하게 운영환경.yml을 외부에 생성 및 프로필2개에 포트(8081,8082)이런식 설정 → 배포 [스크립트.sh](http://xn--5y2bv6no1lihc.sh) 작성(현재 구동중 프로필 확인 후 nginx 연결 안된 프로필 찾아 할당해두고 구동중인건 종료하고 새로 할당한 섭 실행) → nginx가 근데 포트 설정엔 하나의 profile을 바라보고 있었을테니 이걸 변경하는 nginx동적 프록시 설정 → 배포시점에 이 바라보는 프로필을 자동으로 변경하는 nginx 스크립트 작성(포트도 자동으로 변경 하려는 거임) → 마지막으로 처음 만든 배포스크립트.sh에 뒤에 만든 자동변경 스크립트를 수행하게 하기 위해 스크립트 실행 코드까지 넣어주면 끝

<br><br>

### 페이징 JS - 직접제작 vs Egov 라이브러리

페이징(limit, offset) 방법은 앞에서 이미 정리했다. [JPQL+페이징](https://bh946.github.io/checklist/CHECK_LIST_SPRING/#jpql--%ED%8E%98%EC%9D%B4%EC%A7%95)  
좀 더 체계적인 방식은 **Egov 파트에서 페이징**을 참고하자.

DAO에서 페이징 쿼리로 데이터를 잘 받아왔다면, **웹에서는 어떻게 JS로 구현 할지가 문제다.**

**결론부터 말하자면, Egov 사용한것처럼 이미 잘 구현되어있는 라이브러리 사용하는걸 추천! (Egov 파트 페이징을 꼭 써라)**  
직접 JS로 작성해보면 페이징에 사용할 로직은 거기서 거기니까.

<br>

**간단비교:**

- 직접 만든ver:   
  jquery로 js코드로 직접 페이지 번호 생성 및 이동 버튼 생성과 실제 URL이동 등을 하나하나 DOM에 붙여서 태그가 생성되게 만들었다.
- Egov라이브러리 사용ver:  
  Egov가 제공하는 페이징 룰을 따라서 직접 js코드 작성이 아닌 제공된 기능을 그대로 사용하며 ajax로 SPA방식으로 구현했다.

<br>

<details><summary><b>직접 만든ver 코드 (html)</b></summary>
<div markdown="1"><br>
```html
<!-- pagination -->
<br><br><br><br>
<nav aria-label="Page navigation">
    <ul id="dyn_ul" class="pagination" style="justify-content: center;">
    </ul>
</nav>
<!-- -->
<!-- 페이징 -->
<!--    var pageCount = /*[[${(totalCount/10)+1}]]*/ null; // 총 페이지 크기 -> 통신으로 받음-->
<script th:inline="javascript">
    var totalCount = /*[[${totalCount}]]*/ null;
    if(totalCount % 10 == 0) {var pageCount = totalCount/10;}
    else {var pageCount = totalCount/10 +1;} // 총 페이지 크기 -> 통신으로 받은값으로 계산
    var pageMax = 10; // 보여줄 페이지 수
    var activePage = /*[[${pageId}]]*/ null; // 현재 페이지 -> 통신으로 받음(active)
    // 보여줄 시작 페이지(계산 여기서 하겠음)
    // 현재페이지/보여줄페이지수 몫 * 보여줄페이지수
    // ex) 23 / 10 * 10 -> 2*10 -> 20
    var startIndex = parseInt((activePage-1)/pageMax)*pageMax + 1;
    var endIndex = startIndex + pageMax -1;
    if(endIndex > pageCount)
        endIndex = startIndex + (pageCount%startIndex);
//
    // 동적 스타일링
    var arrowLeft = "<img class='img-fluid' src='/arrow-left.svg' style='width:1.2vw;' onclick='redirectSavedBgm()'/>"
    var arrowRight = "<img class='img-fluid' src='/arrow-right.svg' style='width:1.2vw;' onclick='redirectSavedBgm()'/>"
//
//
    // [동적 ul 페이징 처리 실시]
    if(pageCount == 1){ //생성해야할 페이지가 1페이지인 경우
        var insertUl = "<li class='page-item'>"; // 변수 선언
        insertUl += insertUl + "<a class='page-link active' href='/gallery/1' onclick = 'redirectSavedBgm()'>";
        insertUl += "1</a></li>";
        $("#dyn_ul").append(insertUl); //jquery append 사용해 동적으로 추가 실시
    }
    else if(pageCount >= 2){ //생성해야할 페이지가 2페이지 이상인 경우
        // (Previous)이전 페이지 추가 실시
        var insertSTR = "<li class='page-item'>"; // 변수 선언
        if(activePage===1) insertSTR = insertSTR + "<a class='page-link disabled' style='background-color: var(--main-1); opacity:0.3;' href='/gallery/"+(activePage)+"' onclick = 'redirectSavedBgm()'>";
        else insertSTR = insertSTR + "<a class='page-link' href='/gallery/"+(activePage-1)+"' onclick = 'redirectSavedBgm()'>";
        // insertSTR = insertSTR + "Previous";
        insertSTR = insertSTR + arrowLeft;
        insertSTR = insertSTR + "</a></li>";
        $("#dyn_ul").append(insertSTR); //jquery append 사용해 동적으로 추가 실시
//
        // ... 페이지 추가 (앞에 페이지 더 있을경우 맨앞 페이지로 이동)
        if((activePage > pageMax)) {
            var insertMID = "<li class='page-item'>"; // 변수 선언
            insertMID = insertMID + "<a class='page-link' href='/gallery/"+(1)+"' onclick = 'redirectSavedBgm()'>";
            insertMID = insertMID + "...";
            insertMID = insertMID + "</a></li>";
            $("#dyn_ul").append(insertMID); //jquery append 사용해 동적으로 추가 실시
        }
//
//
        // 1, 2, 3 .. 페이지 추가 실시
        var count = 1;
        for(var i=startIndex; i<=pageCount; i++){
            if(count > pageMax){ //최대로 생성될 페이지 개수가 된 경우
                page = i - pageMax; //생성된 페이지 초기값 저장 (초기 i값 4 탈출 인경우 >> 1값 저장)
                break; //for 반복문 탈출
            }
            var insertUl = "<li class='page-item'>"; // 변수 선언
            if(i===activePage) insertUl = insertUl + "<a class='page-link active' href='/gallery/"+i+"' onclick = 'redirectSavedBgm()'>";
            else insertUl = insertUl + "<a class='page-link' href='/gallery/"+i+"' onclick = 'redirectSavedBgm()'>";
            insertUl = insertUl + String(i);
            insertUl = insertUl + "</a></li>";
            $("#dyn_ul").append(insertUl); //jquery append 사용해 동적으로 추가 실시
            count ++;
        }
//
        // ... 페이지 추가 (뒤에 페이지 더 있을경우 끝 페이지로 이동)
        if((activePage < pageCount) && (activePage >= pageMax) && (endIndex < pageCount)) {
            var insertMID = "<li class='page-item'>"; // 변수 선언
            insertMID = insertMID + "<a class='page-link' href='/gallery/"+(pageCount)+"' onclick = 'redirectSavedBgm()'>";
            insertMID = insertMID + "...";
            insertMID = insertMID + "</a></li>";
            $("#dyn_ul").append(insertMID); //jquery append 사용해 동적으로 추가 실시
        }
//
        // (Next)다음 페이지 추가 실시
        var insertEND = "<li class='page-item'>"; // 변수 선언
        if(activePage === pageCount)  insertEND = insertEND + "<a class='page-link disabled' style='background-color: var(--main-1); opacity:0.3;' href='/gallery/"+(activePage)+"' onclick = 'redirectSavedBgm()'>";
        else insertEND = insertEND + "<a class='page-link' href='/gallery/"+(activePage+1)+"' onclick = 'redirectSavedBgm()'>";
        // insertEND = insertEND + "Next";
        insertEND = insertEND + arrowRight;
        insertEND = insertEND + "</a></li>";
        $("#dyn_ul").append(insertEND); //jquery append 사용해 동적으로 추가 실시
    }
</script>
```
</div>
</details>

<details><summary><b>Egov 사용 코드 (jsp)</b></summary>
<div markdown="1"><br>
jquery 문법을 활용해서 ajax 적용 했음. (jstl은 jpa 표준 태그 라이브러리 - c, fmt 등)
```jsp
<%@ page contentType="text/html; charset=UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt"%>
<%@ taglib prefix="ui" uri="http://egovframework.gov/ctl/ui"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<link rel="stylesheet" href="/css/basic.css"/>
<link rel="stylesheet" href='/css/gallery.css'/>
<link rel="stylesheet" href='/css/jqueryui.css'/>
<script src="<c:url value='/js/jquery.js'/>"></script>
<script src="<c:url value='/js/jqueryui.js'/>"></script>
</head>
<script type="text/javascript">
	function linkPageAjax(pageNo) {
		//location.href = "/gallery?pageIndex=" + pageNo;
        $.ajax({
            url: '/gallery',
            type: 'post',
            data: {
                pageIndex: pageNo
            }
        }).done(function(fragment) {
            $('#content_area').html(fragment);
        });
	}
</script>
<body>
<div id="content_area" >
${resultList}
    <ui:pagination paginationInfo="${paginationInfo}" type="image"
                   jsFunction="linkPageAjax" />
</div>
</body>
</html>
```
</div>
</details>

