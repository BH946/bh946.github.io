---
title: "[체크리스트] DevOps"
categories : [Knowledge]
tag : [spring, 스프링, 스프링부트, DevOps]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



# Intro

**DevOps 경험을 정리**

<br><br>

# CHECK LIST



## DevOps - 모니터링

**모니터링 3단계 : 대시보드, 앱 추적, 로그**

* **대시보드 - 마이크로미터, 프로메테우스, 그라파나 등등**
  * 추천) 액츄에이터(Log) + 마이크로미터(Data) + 프로메테우스(DB) + 그라파나(View) 순으로 적용!
    * `그라파나` "기본세팅(사람들꺼)" + "점진적으로 비지니스 추가세팅" 방식 권장
    * `마이크로미터` 핵심 - @Counted, @Timed, 게이지(MeterBinder)
    * `액츄에이터` 는 포트변경 제공하므로 "내부망" 에서만!!
  * 모니터링 대상
    * 시스템 메트릭(CPU, 메모리)
    * 애플리케이션 메트릭(톰캣 쓰레드 풀, DB 커넥션 풀, 애플리케이션 호출수) 
    * 비즈니스 메트릭(주문수, 취소수)
* **애플리케이션 추적 - 핀포인트(오픈소스) 등등**
  * 추천) 핀포인트(주로 HTTP 요청 추적)
* **로그 - 파일, 클라우드**
* 알람은 2가지로 사용 - 경고, 심각
  * 예시
    * 디스크사용량 70% 경고 
    * 디스크사용량 80% 심각 
    * CPU 사용량 40% 경고 
    * CPU 사용량 50% 심각

<br>

**대시보드 세팅 예시**

* **액츄에이터, 프로메테우스, 그라파나 순으로 적용**

* **(1)액츄에이터**

  * actuator 의존성 추가 + yml 설정

    * `implementation 'org.springframework.boot:spring-boot-starter-actuator' `
    * endpoints 기본 비활성화 되어있는것들을 전부 활성화로 세팅

    * health, loggers, metric
      * health 에서 상태 OK이면 서버정상
      * 서버실행중에 loggers 레벨 변경가능
      * 자바 메모리 사용량 : `http://localhost:8080/actuator/metrics/jvm.memory.used`
      * HTTP 요청수 : `http://localhost:8080/actuator/metrics/http.server.requests`


  * 보안을 위해서 내부망 포트로 수정하기도 한다는점 참고!

  * **이러한 metric을 어딘가 지속 보관 및 대시보드로 확인을 위해 프로메테우스, 그라파나를 추가 적용**


* **(2)프로메테우스**

  * 프로메테우스 설치 : `https://github.com/prometheus/prometheus/releases/download/v2.42.0/prometheus-2.42.0.windows-amd64.zip`

  * 폴더에서 바로 실행 -> `http://localhost:9090`

  * 각각의 metric들은 마이크로미터 표준 방식을 따르고 있기에 어떤 구현체인지만 지정해주면 되므로, 이를 위해 의존성으로 지정한다.

    * 의존성 추가 : `implementation 'io.micrometer:micrometer-registry-prometheus`
      * 구현체 확인 : `http://localhost:8080/actuator/prometheus`
      * `http_server_requests_seconds_count` 의 숫자가 새로고침마다 변경됨을 확인할 수 있다.

    * 수집 설정 - prometheus.yml(프로메테우스 폴더에 존재)
      * 타겟 주소, 프로메테우스 실행 주소 등등 설정
      * 특히, interval의 경우 임의로 1s로 하였고 실무에선 1m을 많이 한다고 함


  * 대시보드는 그라파나로 보고싶기 때문에 그라파타 연동으로 넘어가자


* **(3)그라파나**

  * 그라파나 설치 : `https://dl.grafana.com/enterprise/release/grafana-enterprise-9.3.6.windows-amd64.zip`

  * bin폴더의 grafana-server 파일 실행해서 구동 -> `http://localhost:3000/login` 실행
    * 프로메테우스와 연동
      * 설정에 Data sources 에 프로메테우스 `http://localhost:9090` 를 등록


  * 다른 사람의 그라파나 사용
    * ID 11378 그라파나 사용
      * 추가설정 : Jetty 통계 -> Tomcat 통계
        * 이 대시보드는 톰캣이 아니라 Jetty라는 웹 서버를 기준으로 통계를 수집하기 때문
        * Jetty Statistics 부분 Title을 Tomcat Statistics로 변경
        * jetty_threads_config_max -> tomcat_threads_config_max_threads 로변경 
        * jetty_threads_current -> tomcat_threads_current_threads
        * jetty_threads_busy -> tomcat_threads_busy_threads
        * jetty_threads_idle 제거
        * jetty_threads_jobs 제거


* **다음 3가지 반드시 구동 : 앱, 프로메테우스, 그라파나**

<br>

**대시보드에서 확인 가능한 주로 발생 4가지**

* CPU 사용량 초과 
* JVM 메모리 사용량 초과 
* 커넥션 풀 고갈 
* 에러 로그 급증
* +) 비지니스..

<br>

**대시보드의 비지니스 예시와 모니터링 알림**

* **비지니스 예**로 주문수 같은 메트릭의 경우들은 **직접 만들어서 등록**해줘야 한다.
  * **기존 Service는 남기기 위해 Service단을 "인터페이스"로 구현해서 테스트 추천!!**
    * `http://localhost:8080/actuator/metrics/my.task` 에서 등록한 메트릭 확인
      * 등록한 name, method 등 참고하게 됨
    * `http://localhost:8080/actuator/prometheus` 에서 프로메테우스 포멧 메크릭 확인
      * 그라파나에 쿼리문 작성시 참고하게 됨
  * **마이크로미터를 사용해 메트릭을 직접 등록**
    * **카운터와 게이지 중**에 우리는 **값이 증가만** 하기때문에 **카운터를 사용**
    * **Timer도 사용**하여 실행시간 함께 보자
    * 이 모든걸 간단히 만들어주는것이 **`@Timed`**
      * Counter, Timer 둘다 간단히 제공
      * 원래는 Counter.builder... 또는 Timer.builder... 형태로 구현해줘야 함
* **모니터링 알림 참고 : [다른분의 모니터링 Alert](https://cleaning-toolbox.tistory.com/94)**
  * 어떠한 값을 기준으로 Alert 발생시킬지 쿼리문 작성
  * 위험, 심각... 이런식으로 구별하여 알람을 받자.
    * **모니터링을 넣은 가장 큰 이유이다.**

![image](https://github.com/BH946/spring-second-roadmap/assets/80165014/1d7271c1-8daa-492c-a8c8-323406c6921e)   

![image](https://github.com/BH946/spring-second-roadmap/assets/80165014/94791ab9-4bab-43ff-b080-779f855f6907) 

<br><br>

## DevOps - 테스트(+부하테스트)

**API를 일반적으로 테스트할때? -> 포스트맨 추천!**

* 포스트맨의 장점은 **API 명세서**도 제공!! -> 생각보다 괜찮음!

<br>

**부하 테스트 툴 - nGrinder(네이버 오픈소스)**

* **부하 테스트 : "임계치 이전" 까지 테스트**
  * **과부하 데이터를 처리**하는 방법을 확인 목적
  * 부하 테스트와 유사한 "성능 테스트, 스트레스 테스트"가 존재하며 차이점을 알아두자
    * **참고문헌 : [주요 차이점 - 성능, 스트레스, 부하 테스트](https://seongwon.dev/ETC/20220919-%EC%84%B1%EB%8A%A5%ED%85%8C%EC%8A%A4%ED%8A%B8-%EB%B6%80%ED%95%98%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%8A%A4%ED%8A%B8%EB%A0%88%EC%8A%A4%ED%85%8C%EC%8A%A4%ED%8A%B8%EB%9E%80/)**
  * 부하 테스트를 좀 더 이해하기 위해 Throughput과 Latency를 알아두자
    * 너무 잘 정리하신 분 발견!
    * **[시리즈1 Throughput, Latency 중요개념](https://hyuntaeknote.tistory.com/m/10)**
    * [시리즈2 Throughput, Latency 테스트적용 분석 + 동시사용자 일반사용자](https://hyuntaeknote.tistory.com/m/11)
    * [시리즈3 db 커넥션풀](https://hyuntaeknote.tistory.com/m/12)
* 부하테스트를 통해서 api 장애 개선을 경험
  * 취약 부분 찾아서 **"쿼리튜닝"** 을 진행했음!
* **부하테스트 간단 사용법**
  * **(1)세팅**
    * java11 버전(1.8과 11만 지원!) + cmd 사용 + windows 기준
    * nGrinder 실행 : `java -jar ngrinder-controller-{version}.war --port=8300`
    * agent 실행시 설치한 agent경로 들어가서 `run_agent.bat`
      * 포트 열려있나 확인(예:16001) : `telnet 127.0.0.1 16001`
  * **(2)스크립트 작성**
    * 스크립트는 groovy 언어 - JVM에서 실행되는 동적 프로그래밍 언어
    * 동작순서 : @BeforeProcess -> @BeforeThread -> HTTP 요청(@Test)
      * @Before 의 경우 @Test 하나하나 실행전에 반복 실행
    * **위 동작순서의 특징을 이용한 예시!!**
      * 일반적인 기능 테스트의 경우 : @Test에 기능 테스트 작성!
      * "로그인"이 필요한 기능 테스트의 경우 : 로그인이 목적이 아니므로 @BeforeProcess에서 로그인 추천!
  * **(3)테스트 세팅**
    * 사용할 스크립트 설정 : xxx.groovy
    * 에이전트 : 실행한 agent수 작성
      * agent는 서버에 기능요청을 할 일반 사용자 컴퓨터로 보면된다! (본인생각임)
    * **vuser : 에이전트당 가상사용자 X명으로 설정**
      * proccess, thread 설정은 자동 세팅 될것이다.
      * 또한 proccess보다 thread를 크게 잡는걸 권장한다고 한다.
        * **참고문헌 : [프로세스, 스레드 차이](http://ngrinder.373.s1.nabble.com/process-thread-td2636.html)**
      * **중요 : @BeforeThread가 설정한 thread만큼 동작 + 프로세스도 마찬가지**
    * **Duration vs Run Count**
      * Duration은 설정한 시간만큼 테스트를 진행하며 "요청->응답->요청->응답..."로 바로바로 진행
        * **중요 : @Test가 설정한 시간동안 무수히 많이 동작**
      * Run Count는 설정한 횟수만큼 테스트를 진행 - 속도 조절 가능
        * **중요 : @Test가 설정한 횟수만큼 동작**
    * **Enable Ramp-up** 
      * thread설정을 주로 사용
      * 적절한 증가단위, 주기를 설정
  * **(4)결과 분석**
    * 다음의 용어들을 참고하여 결과를 분석해보자 -> 결과를 이해할 줄 알아야 최적화를 할 수 있다.
      * TPS(=Test Per Seconds) : 초당 테스트 개수
      * MTT: 평균 테스트 타임(vuser와 비례관계)
      * MTFB: 평균 첫 번째 바이트 도달 시간(=Response Time으로 볼 수도 있다.)
      * 테스트 기간 : 요청후 응답받으면 바로 다시 요청보내고, 설정한 시간만큼 진행한다.
        * @Test(+@Before) 이부분을 의미!
        * 따라서 설정한 시간만큼 엄청많이 테스트 함
      * 실행 횟수 : 스레드당 몇번 테스트할지 직접 설정할 수 있다.
        * @Test(+@Before) 이부분을 의미!
        * 따라서 설정한 횟수 만큼 테스트 함
      * GTest 객체 : 각각 테스트 시나리오 (@Test에 작성하는것 - 스크립트)

<br><br>

## DevOps - CI/CD (지속적 통합과 배포)

**CI/CD (지속적 통합과 배포) - Github Action, Jenkins**

* Github Action 을 CI / Jenkins 를 CD 에 사용하고자 함!
  * **참고 문헌 : [Github Action을 CI, Jenkins를 CD로 사용한 이유](https://velog.io/@kimseungki94/Jenkins-vs-Github-Action-%EC%96%B4%EB%96%A4%EA%B1%B8-%EC%93%B0%EB%8A%94%EA%B2%8C-%EC%A2%8B%EC%9D%84%EA%B9%8C)**
* 이걸 **구현하는건 기본**이고 **"CI/CD 속도를 개선**" 하는게 중요!

<br>

**CI - Github Action 적용해보기**

* **보통 gradle 빌드가 느리므로 이를 캐시를 적용하여 CI 속도를 향상 시킨다고도 한다.**
  * 참고 문헌 : [CI 속도향상](https://hellorennon.tistory.com/22)
* **CI 구축 이유**
  * 매번 main에 앱 업데이트 후 실행하여 정상 동작하는지 보는게 너무 귀찮
  * 제일 중요한 점은 열심히 작성한 단위테스트 코드들을 실행할 수 있다는것
  * **따라서 테스트 시간을 대폭 단축**
    * **맨 처음 Unit Test Code** 작성으로 테스트시간 단축 후
    * **현재 CI** 까지 구축하여 테스트시간을 더욱 단축

* **워크플로우 계획**

  * main에 push 할 때 실행

  * "정상 빌드" 확인

  * "정상 테스트" 확인
    * test쪽 yml파일을 주석처리 함으로써 메모리DB 사용으로 h2 연결문제 해결


```yaml
name: CI Test

on:
  push:
#    branches: [ "main" ]
    branches: [ "kbh" ]
#  schedule:
  # - cron: '00 12 * * *' # UST 가 default. UST 10:00는 한국시간 21:00(9H 차이)
  # - cron: '00 19 * * *' # UST 가 default. UST 19:00는 한국시간 04:00(9H 차이)

jobs:
  build:
    runs-on: ubuntu-latest # 최신 우분투 사용

    # 루트 경로 지정(작업경로 지정) - 전역으로 지정한것
    defaults:
      run:
        working-directory: ./lepl

    steps:
      - uses: actions/checkout@v3

      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          java-version: '17'
          distribution: 'corretto' # JDK 17에 필수 temurin or adopt or corretto
      
      # 권한먼저!!
      - name: Grant execute permission for gradlew
        run: chmod +x ./gradlew

      - name: Setup Gradle
        uses: gradle/gradle-build-action@v2 # JDK 17지원
        with:
          arguments: build
          build-root-directory: ./lepl
          # cache-read-only: ${{ github.ref != 'refs/heads/main' && github.ref != 'refs/heads/develop' }}

      - name: New Gradle
        run: ./gradlew init

      # 빌드 -> 이때는 test 코드 실행 제외
      - name: Build with Gradle
        run: ./gradlew build -x test

      # 테스트 코드 (프로필로 실행)
      - name: Test with Gradle
        run: SPRING_PROFILES_ACTIVE=[test] ./gradlew test
```

<br>

**쿠버네티스 - 오케스트라 툴**

* "4-2학기 도전학기제-연구노트" 참고

<br>

**도커 - 가상화 플랫폼(컨테이너 기반)**

* "4-2학기 도전학기제-연구노트" 참고

<br><br>

