---
title:  "개발 환경 설정과 IDE 활용 가이드 IntelliJ, Eclipse, STS"
categories : Spring
tag : [개발환경,IDE,IntelliJ,Eclipse,STS,Gradle,Maven,JUnit,단축키]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**개발 환경 설정부터 IDE 선택, 빌드 도구 활용, JUnit 테스트 설정, 그리고 유용한 단축키까지 Java 기반 개발에 필요한 모든 환경 구성 요소를 종합적으로 정리한 가이드다.**

<br>

<br>

## 개발 환경과 빌드 도구 선택

개발 환경과 빌드 도구는 개발 생산성에 직접적인 영향을 미치는 중요한 요소다. 적절한 IDE와 빌드 도구 조합을 선택하는 것이 중요하다.

<br><br>

### IDE와 빌드 도구 조합

현재 Java 개발에서 주로 사용되는 IDE와 빌드 도구 조합은 다음과 같다:

- IntelliJ + Gradle(Groovy)
- Eclipse + Maven(Pom.xml)

```
주로 IntelliJ + Gradle 조합을 본인은 사용
```

<span style="color:#ff9300">Spring Boot 3.2 버전부터는 IntelliJ에서 빌드와 실행을 모두 Gradle로 설정해야 오류 없이 작동한다. 첫 빌드는 느릴 수 있지만 Gradle 캐시 덕분에 두 번째부터는 빠르게 실행된다.</span>

<br><br>

### 빌드 도구별 외부 라이브러리 적용법

빌드 도구에 따라 외부 라이브러리를 추가하는 방법이 다르다:

- Maven: `pom.xml` 파일에 의존성 설정
- Gradle: `build.gradle` 파일에 의존성 설정
- 전통 방식(빌드 도구 미사용): 직접 jar 파일을 프로젝트에 추가

<br><br>

### Gradle 설정 예시

Gradle은 Spring Boot 프로젝트에서 많이 사용되는 빌드 도구로, 다음과 같이 설정할 수 있다:

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
  // 기본 의존성
  implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
  implementation 'org.springframework.boot:spring-boot-starter-web'
  compileOnly 'org.projectlombok:lombok'
  runtimeOnly 'com.h2database:h2'
  implementation group: 'com.oracle.database.jdbc', name: 'ojdbc10', version: '19.21.0.0'
  annotationProcessor 'org.projectlombok:lombok'
  
  // 테스트 의존성
  testImplementation 'org.springframework.boot:spring-boot-starter-test'
  testCompileOnly 'org.projectlombok:lombok'
  testAnnotationProcessor 'org.projectlombok:lombok'
  
  // 추가 기능
  implementation 'org.springframework.boot:spring-boot-starter-validation'
  implementation 'org.springframework.boot:spring-boot-starter'
  implementation 'org.springframework.boot:spring-boot-devtools'
  
  // 캐시
  implementation 'org.springframework.boot:spring-boot-starter-cache'
  implementation 'com.github.ben-manes.caffeine:caffeine:3.1.1' 
  
  // 모니터링
  implementation 'org.springframework.boot:spring-boot-starter-actuator'
  implementation 'io.micrometer:micrometer-registry-prometheus'
}
tasks.named('test') {
  useJUnitPlatform()
}
```

💡 **Spring Boot 플러그인을 사용하면 대부분의 라이브러리 버전 관리가 자동화되지만, 지원되지 않는 라이브러리는 직접 버전을 명시해야 한다.**

<br>

<details><summary><b>스프링과 부트에서 JUnit 버전별로 주의점(JUnit4, JUnit5)</b></summary>
<div markdown="1"><br>
순수 스프링은 @RunWith(SpringJUnit4ClassRunner.class)와 @ContextConfiguration(빈 사용 xml)를 사용,<br>
부트는 @ContextConfiguration 대신 편리한 @SpringBootTest를 사용!<br>
아래는 Boot에서 JUnit4, JUnit5 사용 주의점
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

⚡ **Spring Boot 3.xx에서는 JUnit5가 기본으로 사용되므로 @RunWith 어노테이션이 필요 없다. 또한 Java 17 이상을 사용해야 한다.**

<br>

<br>

## Eclipse와 STS 환경 설정

Eclipse는 오랫동안 Java 개발에 사용된 IDE이며, STS(Spring Tool Suite)는 Spring 개발에 최적화된 Eclipse 기반 IDE다.

**Eclipse**에서 소스 폴더가 두 개(예: src/main/java, src/main/resources와 src/main/webapp)로 나뉘는 이유는 다음과 같다:

- 물리적 위치는 동일하지만 **폴더별 역할이 다르다**
- 패키지 모양 폴더(src/main/java, src/main/resources)는 **컴파일 대상** 폴더
- 일반 폴더(src/main/webapp)는 **컴파일하지 않는** 정적 리소스 폴더

<br>

**STS 선택 가이드**

Spring 개발을 위한 IDE 선택 시 고려사항:

1. **STS3**: Legacy Spring 프로젝트와 Spring Boot 모두 지원
2. **STS4**: 최신 Spring Boot 지원에 최적화, Legacy 프로젝트는 STS3 플러그인 추가 필요
3. **Eclipse + Spring Tools 플러그인**: 기존 Eclipse에 Spring 개발 기능 추가

<span style="color:#ff9300">Spring Legacy와 Spring Boot 프로젝트를 모두 사용하려면 STS3을 사용하거나, STS4에 STS3 플러그인을 추가하는 것이 좋다. 특히 STS4.14.1과 STS3.9.22 Add-On For STS4 조합이 권장된다.</span>

<br><br>

### Eclipse/STS 주요 환경 설정

Eclipse나 STS를 효율적으로 사용하기 위한 주요 환경 설정:

1. **IDE가 사용할 JVM 설정**:
   - eclipse.ini 파일에 `-vm` 옵션 추가
   - 예: `-vm C:\Program Files\Java\jdk-17\bin`
2. **워크스페이스 지정**:
   - File > Switch Workspace > Other
3. **인코딩 설정**:
   - Window > Preferences > General > Workspace > Text file encoding을 UTF-8로 설정
   - Web 파일(CSS, HTML, JSP 등)도 UTF-8로 설정
4. **폰트 설정**:
   - Window > Preferences > General > Appearance > Colors and Fonts > Basic > Text Font
5. **자동 완성 설정**:
   - Window > Preferences > Java > Editor > Content Assist
   - Auto triggers java에 `<=$:{.@qwertyuioplkjhgfdsazxcvbnm_QWERTYUIOPLKJHGFDSAZXCVBNM` 추가
   - "Disable insertion triggers except 'Enter'" 체크

```
자주 사용하는 Eclipse 설정:
- Project -> Build Auto: 저장마다 .class 생성
- Open Perspective: Debug, Git, Java 등 화면 구성
- 자동 정렬: Preferences > Web > Html > Editor에서 커스텀
- 힙 메모리 보기: Preferences > General > Show heap status 체크
```

💡 **한글이 깨지는 경우 Window > Preferences > General > Content Types > Java Class File, Test를 UTF-8로 설정하면 해결된다.**

<br><br>

### JDK 버전 우선순위 이해

Eclipse에서 JDK 버전은 다음 우선순위로 결정된다:

1. **프로젝트별 설정**: Java Build Path에 설정된 JRE/JDK
   - 프로젝트 우클릭 > Properties > Java Build Path > Libraries
   - Gradle/Maven 빌드 도구에 명시된 JDK 버전
2. **이클립스 기본 설정**:
   - Window > Preferences > Java > Installed JREs
   - Window > Preferences > Java > Installed JREs > Execution Environments
3. **시스템 환경 변수**: PATH에 등록된 Java 버전

<span style="color:#ff9300">이클립스가 구동되기 위한 JDK와 프로젝트에서 사용 중인 JDK는 서로 독립적이다. 프로젝트 설정이 가장 우선시되므로 프로젝트 Properties에서 Java Compiler 설정을 확인하는 것이 중요하다.</span>

<br>

<br>

## 유용한 IDE 단축키

효율적인 개발을 위한 IDE별 주요 단축키를 알아보자.

<br><br>

### IntelliJ 주요 단축키

```
- Alt + Insert: getter, setter, constructor 등 자동 생성
- Ctrl + Alt + O: 사용하지 않는 import 자동 정리
- Ctrl + Alt + L: 자동 정렬
- Ctrl + Alt + V: 변수 선언부 자동 작성
- Ctrl + Alt + M: 코드 리팩토링을 위한 함수 자동 생성
- Alt + Shift + Down/Up: 코드 한 줄 위/아래 이동
- Ctrl + D: 코드 한 줄 복제
- Shift + F6: 변수명 한 번에 변경
- Alt + 1: 왼쪽 프로젝트 구조 열기
- Alt + F12: 터미널 창 열기
```

<br><br>

### Eclipse 주요 단축키

```
- 코드 실행: Ctrl + F11
- 패키지 임포트/정리: Ctrl + Shift + O
- 자동 정렬: Ctrl + Shift + F
- 주석 처리/해제: Ctrl + / (Java) 또는 Ctrl + Shift + / (XML, JSP)
- 코드 한 줄 이동: Alt + ↑/↓
- 세터 생성: Alt + Shift + S, R
- 생성자 생성: Alt + Shift + S, O
- 파일 찾기: Ctrl + Shift + R
- 전체 검색: Ctrl + H
```

⚡ **단축키를 효율적으로 사용하면 개발 생산성을 크게 향상시킬 수 있다. 자주 사용하는 단축키는 반복적으로 연습하여 손에 익히는 것이 좋다.**

<br>

<span style="color:#777777">개발 환경 설정은 개발자의 생산성과 직결되는 중요한 요소다. IDE, 빌드 도구, JDK 버전, 테스트 프레임워크 등을 프로젝트 요구사항에 맞게 적절히 구성하고, 유용한 단축키와 설정을 활용하면 더 효율적인 개발이 가능하다.</span>
