---
title: "[체크리스트] DevOps와 서버최적화"
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

**DevOps와 서버 최적화의 경험을 정리**

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

## 서버 최적화 - SQL 쿼리튜닝

**DevOps는 아닐지라도 "SQL 쿼리튜닝" 은 서버 최적화의 중요한 개념**

* 쿼리튜닝은 서버 최적화처럼 **DB 성능개선을 위해 SQL을 최적화** 하는것이라 볼 수 있다.

* **`페이징, N+1 해결(=쿼리수 줄이기=적절한 JOIN사용=ORM의 문제 해결), Index 사용(단일 or 복합), db캐시(물론 db캐시는 안쓰고 서버,웹 캐시만 사용할 예정)` 전부 해당**

  * 쿼리튜닝 방법은 정말 많다. - 자료첨부
    * **[더 빠른 SQL 쿼리를 위한 21가지 데이터베이스 튜닝 규칙](https://www.itworld.co.kr/tags/2665/SQL/105792?page=0,0)**
    * **[추가적인 튜닝 규칙](https://velog.io/@gillog/SQL-%ED%8A%9C%EB%8B%9D#%EC%B6%94%EA%B0%80%EC%A0%81%EC%9D%B8-%ED%8A%9C%EB%8B%9D-%EA%B7%9C%EC%B9%99)**
    * **[DB전문가의 TIP - Youtube](https://youtu.be/qY-nOOX_Smc?si=28sjP31btwGLlcDM)**

* **`페이징, N+1해결, Index` 외에 추가로 적용해볼 쿼리튜닝 방식들은??**

  * **"전체범위 처리", "부분범위 처리"**

    * **좀 포괄적인 개념이다. Index도 포함 할 수 있고,, 기본적으로 인지해야할 부분이다.**

    * 전체범위 처리 예시 : `select * from 테이블 order by asc`

      * order by, count함수 등

      - Full Range Scan과 다를바 없다.

    * 부분범위 처리 예시 : `select * from 테이블`

      - Index를 타서 그부분만 읽던지, order by가 없던지 등
      - `select * from 테이블`은 데이터 처리하는 입장에서보면 테이블을 한바퀴 스캔할 필요없이 그냥 바로 테이블에 있는 데이터를 보여주면 되기 때문에 "부분범위 처리"

    * 참고 : **[전체vs부분범위 처리](https://kjw1313.tistory.com/m/96)**

  * Not 조건의 중요성 - “아니다”란 조건이 있으면 맞다는 조건을 찾을것

  * 페이징, N+1 해결(ORM의 문제로 발생)

  * UPDATE 대신 CASE 문 사용, 배치 모드로 Delete, Update 작업

  * OR 보다는 AND 사용, distinct 는 가급적 사용X (내부적으로 정렬연산 발생하기 때문)

  * 가급적 where 조건에는 Index 컬럼 모두 사용 -> 가급적 "=" 연산 사용(LIKE 같은것은 효율X)

    * `CHECKPOINT;` : db 버퍼 캐시 비우기 -> 테스트시 반드시 필수로 사용
    * `SET CACHE_SIZE 0;` : checkpoint 보다 이게 확실한 방법 (캐시 지우기)
      * 공식문서 한참 뒤지니까 나옴
    * `explain` : 반드시 사용해서 index 적용여부 확인
    * `use index` : index가 원하는대로 적용되지 않으면 직접 설정하라(방법은 다양함)
    * 참고로 예상이 안가면 할 수 있는 Index 를 다 적용해서 성능비교를 해보자
      * **필수 세팅 : "데이터 세팅(실제처럼)" + "SET CACHE_SIZE 0;(캐시비우기)"**
      * **예시로 `단일index, 복합index(컬럼순서중요), index사용X` 를 성능비교 해볼 수 있겠다.**

  * 다양한 예시들

    * [**DB의 페이지네이션을 어떻게 최적화-이해하기 좋음**](https://taegyunwoo.github.io/tech/Tech_DBPagination#limit-offset-%EB%B0%A9%EC%8B%9D%EC%9D%98-%EB%AC%B8%EC%A0%9C%EC%A0%90) : **Index의 개념도 포함**되어 있다.
    * **[SQL 튜닝 여러가지 방식](https://chung-develop.tistory.com/145)**

* **추가정보1) 발견한 특징??**

  * h2는 `explain analyze` 를 사용해야 rows 까지 확인 가능
  * Index에 사용한 컬럼 정보는 h2웹에서 직접 확인 가능
  * mysql의 `show index` 대신할 명령어 -> index 정보확인
    * `SELECT * FROM INFORMATION_SCHEMA.INDEXES WHERE TABLE_NAME = 'LISTS';`
  * 그냥 컬럼 정보는?
    * `SELECT * FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'LISTS';`
  * 특히 "외래키 매핑" 하면 "**자동**으로 해당 외래키와 현재키 매핑한 Index 생성"
  * h2 db테스트 할때 처음 db connection 에 제일 많은 시간이 소요
    * 이는 db connection pool 기법을 활용해서 개선가능 (나중에 다룰 문제)
  * db connection 이후엔 캐시를 고려해야 함
    * **캐시를 제거안해주면, 정확한 속도 측정이 어려우므로 꼭 캐시 제거하면서 테스트하자**
  * h2에 "최대 행 수" 부분을 전체로 해줘야 limit 설정을 적용하기 쉬우니 이부분도 체크

* **추가정보2) Index 사용 주의점** - 참고 문헌:[인덱스 사용](https://jie0025.tistory.com/509)

  1. 인덱스는 **열 단위에 생성**
  2. 인덱스는 **WHERE 절에서 사용되는 열에 생성**
  3. WHERE 절에 사용되는 열이라도 자주 사용해야 가치가 있음
  4. 데이터 중복도가 높은 열에는 인덱스를 만들어도 효과가 없음 (선택도가 높은 경우는 효과 없음)
  5. **외래키를 설정한 열**에는 **자동으로 외래키 인덱스가 생성**됨
  6. **조인에 자주 사용되는 열**에는 인덱스를 생성하는 것이 좋음
  7. **데이터 변경(삽입, 수정, 삭제) 작업이 얼마나 자주 일어나는지 고려**해야 함
  8. 단일 테이블에 인덱스가 많으면 속도가 느려질 수있다. (**테이블당 4~5개 권장**)
  9. **클러스터형 인덱스는 테이블당 하나만** 생성할 수 있음
  10. 테이블에 클러스터형 인덱스가 아예 없는 것이 좋은 경우도 있음
  11. 사용하지 않는 인덱스는 제거
  12. 복합 인덱스는 **WHERE절에서 OR조건이 아니라 AND 조건일 때** 사용하는 것이 좋음
  13. [복합 인덱스의 컬럼 순서 결정법](https://khdscor.tistory.com/51)

* **추가정보3) Index로 개선한 예시**

  * **상황**

    * H2 디비 사용
    * 테스트 쿼리 : `select * from member m inner join character c on m.character_id=c.character_id order by m.member_id desc limit 10000,5;`
    * 데이터 약 5만개 존재
    * **테스트 실행코드**
      * `explain analyze select * from member m inner join character c on m.character_id=c.character_id order by m.member_id desc limit 10000,5;`
      * **`SET CACHE_SIZE 0;` => (중요) 캐시 꼭 초기화해줘야 정확한 성능 측정이 가능**
      * `select * from member m inner join character c on m.character_id=c.character_id order by m.member_id desc limit 10000,5;`

    * 문제점 : order by 정렬 때문에 "전체범위 처리"이므로 기존 offset 1만을 읽는것도 문제지만 전체 5만을 다 읽는 더 큰 문제가 발생 중!
      * 성능 : 50ms / scanCount: 98146
      * scanCount가 약 10만개인건 join 때문인지 member 5만개, character 5만개 데이터 읽은거라 예상

  * **Index 적용**

    * 필요없는 Index는 `drop index 인덱스이름 on member;` 삭제

    * **`create index idx_member_id on member(member_id desc);` 로 인덱스 생성**

      * 해당 Index를 사용하기 위해서는 JPQL을 수정해야 한다!! - 서브쿼리 사용

      * JPQL로 서브쿼리는 from절에 사용불가하므로 이땐 네이티브쿼리 쓸수밖에

      * **핵심은 Index 사용하는 부분을 "서브쿼리"로 가져와 나머지 JOIN**

        * **`order by member_id desc` 가 있으니 자동으로 index 사용!!**

      * **이때, JOIN들도 외래키로 등록된 Index 를 통해 빠르게 조회**

      * ```java
        // 자바에서 "서브쿼리"
        return em.createNativeQuery("select m.member_id, m.nickname, e.level " +
                                    "from (select * from member order by member_id desc limit "+offset+","+limit+") m " +
                                    "inner join character c on m.character_id=c.character_id " +
                                    "inner join exp e on c.exp_id=e.exp_id;")
            .getResultList();
        ```

        ```
        // h2 db에서 "서브쿼리"
        select m.member_id, m.nickname, e.level from (select * from member order by member_id desc limit 10000,10) m inner join character c on m.character_id=c.character_id inner join exp e on c.exp_id=e.exp_id;
        ```

    * 성능 : 12ms / scanCount: 20009 / index sorted

      * 신기하게 index sorted 라고 정렬된 index 사용한것을 알려준다.

    * scanCount가 약 2만개 인건 order by가 이미 적용된 index를 사용하게 되기 때문!

    * 따라서 기존 OFFSET의 문제인 해당 자리까지 데이터 읽는것 때문에 2만개일 뿐!

      * sql문에 offset 1만개로 설정되어 있기에 member 1만개, character 1만개 읽은 걸로 예상

  * **정리**

    * order by 한 index를 사용한 경우 거의 5배 가까이 성능이 개선되었다.
      * 물론 OFFSET의 문제는 가져가긴 하지만, 
      * 제일 큰 문제인 매번 조회마다 "order by desc" 실행을 해결한것이 큰 이득
      * OFFSET의 문제를 가져간 이유가 궁금하다면 [페이지네이션 최적화](https://taegyunwoo.github.io/tech/Tech_DBPagination) 참고!


<br><br>

## 서버 최적화 - connection pool

**+다중요청) DB Connection Pool, Thread Connection Pool**

**DB Connection Pool**

* pool size 도출 예시 : `((core_count * 2) + effective_spindle_count) = 4*2+1=9`
  * 기본값은 10
  * 참고로 **스프링부트 yml 파일 HikariCP 설정** 로 간단히 바꿀수 있다.

* **[시리즈3 db 커넥션풀](https://hyuntaeknote.tistory.com/m/12)**

<br>

**Thread Connection Pool**

* **[Thread Pool 간단 적용법](https://velog.io/@rara_kim/Spring-Thread-Pool-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0feat.-Scheduler)**



