---
title: "[체크리스트] DevOps"
categories : [CheckList]
tag : [spring, 스프링, 스프링부트, DevOps]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**DevOps 경험을 정리**

<br>

<br>

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

**액츄에이터, 프로메테우스, 그라파나 순으로 적용**

**(1) 액츄에이터**

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

**(2) 프로메테우스**

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

**(3) 그라파나**

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

<br>

<br>

## DevOps - 테스트(+부하테스트)

**API를 일반적으로 테스트할때? -> 포스트맨 추천!** <br>포스트맨의 장점은 **API 명세서**도 제공!! -> **[API 명세서](https://documenter.getpostman.com/view/21970313/2s93mBwec5)**

GET,POST 방법은 정말 많이 했으니까 생략하고 **명세서 부분만 설명! (사진 참고)**

주로 **add request와 add example** 을 많이 사용할거고, 프로젝트 클릭하여 View complete documentation 으로 이동하면 **“명세서 부분 확인 및 수정 가능”**

<details><summary><b>사진 참고</b></summary>
<div markdown="1"><br>
<img src="https://github.com/user-attachments/assets/9735c105-2ae4-4e58-b1ff-dbaaafd0e93a" alt="image" style="zoom:80%;" /><br>
<img src="https://github.com/user-attachments/assets/4b9bc945-2981-4406-89bd-35ce244ef605" alt="image" style="zoom:80%;" /><br><br>
<img src="https://github.com/user-attachments/assets/4979d249-cdde-4663-9f7a-98d9ffd5a9aa" alt="image" style="zoom:80%;" /><br>
<img src="https://github.com/user-attachments/assets/d1158a48-aae1-4d47-906c-ae31ed9b1eaf" alt="image" style="zoom:80%;" />
</div>
</details>

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

* 부하테스트를 통해서 **api 장애 개선을 경험**
  * 취약 부분 찾아서 **"쿼리튜닝"** 을 진행했음!

* **부하테스트 간단 사용법**

  1. **세팅**

     * java11 버전(1.8과 11만 지원!) + cmd 사용 + windows 기준

     * nGrinder 실행 : `java -jar ngrinder-controller-{version}.war --port=8300`
       * agent 실행 시 설치한 agent경로 들어가서 `run_agent.bat` 실행
       * 포트 열려있나 확인(예:16001) : `telnet 127.0.0.1 16001`

  2. **스크립트 작성**

     * 스크립트는 대부분 groovy 언어 - JVM에서 실행되는 동적 프로그래밍 언어

     * 동작순서 : @BeforeProcess -> @BeforeThread -> HTTP 요청(@Test)
       * @Before : @Test 하나하나 실행 전에 반복 실행
       * @BeforeThread : Thread가 설정된 수 만큼 동작
       * @BeforeProccess : Proccess가 설정된 수 만큼 동작

     * **위 동작순서의 특징을 이용한 예시!!**
       * 일반적인 기능(메서드) 테스트의 경우 : @Test에 기능 테스트 작성!
       * "로그인"인증이 필요한 기능 테스트의 경우 : 로그인이 목적이 아니므로 @BeforeProcess에서 로그인 추천!
         * @BeforeProcess는 프로세스 실행 전 1번 실행되는데, 프로세스는 여러 스레드를 실행할 수 있기 때문

  3. **테스트 세팅**

     * **사용할 스크립트 설정 : 자신이 작성한 스크립트(ex: xxx.groovy) 사용**

     * **에이전트(부하 테스트용 컴퓨터) : 실행한 agent 수 작성**
       * agent는 서버에 기능 요청을 할 일반 사용자 컴퓨터로 봐도 된다!
       * 부연설명: 보통 하나의 프로세스는 하나의 앱이다.<br>고성능 앱의 경우 앱 관점에서 병렬 프로세스를 처리하기도 한다. 이는 여러 사용자들의 접속을 처리하기 위함이다.<br>에이전트 1개로 여러 프로세스와 여러 스레드를 설정하면 테스트에 충분하다.<br>**다만, 좀 더 크고 빠른 부하테스트를 위해 에이전트도 여러 개 설정이 가능할 뿐이다.**

     * **vuser : 에이전트당 가상사용자 X명으로 설정**
       * vuser를 사용하면 proccess, thread 을 자동 세팅 해준다! (간편) -> 이걸 쓰자!<br>예로 agent 1, vuser 1000 이면 proccess 10, thread 100 이런식이다.(=사용자1000명)
       * 물론, vuser가 아닌 직접 proccess, thread 세팅을 해도 된다.<br>예로 agent 1, proccess 5, thread 200 세팅하면 사용자 1000명과 같다.
         * 참고로 agent 1이면 하나의 IP로 부하테스트 하는 것이고, agent 10이면 10개의 IP로 부하테스트 하는 것이므로 **가상으로 흉내 내는 것이지 실제라고 착각하지는 말자.**

     * **Duration vs Run Count**
       * Duration은 설정한 시간만큼 테스트를 진행하며 "요청->응답->요청->응답..."로 바로바로 진행
         * **@Test가 설정한 시간동안 무수히 많이 동작**
       * Run Count는 설정한 횟수만큼 테스트를 진행 - 속도 조절 가능
         * **@Test가 설정한 횟수만큼 동작**

     * **Enable Ramp-up** 
       * thread설정을 주로 사용
       * 적절한 증가단위, 주기를 설정

  4. **결과 분석**
     * 다음의 용어들을 참고하여 결과를 분석해보자 -> 결과를 이해할 줄 알아야 최적화를 할 수 있다.
     * TPS(=Test Per Seconds) : 초당 테스트 개수
     * MTT: 평균 테스트 타임(vuser와 비례관계)
     * MTFB: 평균 첫 번째 바이트 도달 시간(=**Response Time**으로 볼 수도 있다.)
     * 테스트 기간 : 요청 후 응답 받으면 바로 다시 요청보내고, 설정한 시간만큼 진행한다.
       * @Test(+@Before) 이부분을 의미!
       * 따라서 설정한 시간만큼 엄청 많이 테스트 함
     * 실행 횟수 : 스레드당 몇번 테스트할지 직접 설정할 수 있다.
       * @Test(+@Before) 이부분을 의미!
       * 따라서 설정한 횟수 만큼 테스트 함
     * GTest 객체 : 각각 테스트 시나리오 (@Test에 작성하는것 - 스크립트)

<br>

<br>

## DevOps - CI/CD (지속적 통합과 배포) + Docker, GKE

**도커(가상화 플랫폼. 컨테이너 기반), 쿠버네티스(오케스트라 툴)**

- **[쿠버네티스 초급](https://bh946.github.io/categories/gcp_kuber_bas)** 을 참고 -> 도커 내용도 존재

<br>

**CI/CD (지속적 통합과 배포) - Github Action, Jenkins**

이걸 **구현하는건 기본**이고 **"CI/CD 속도를 개선**" 하는게 중요!

<br><br>

### Github Action + Docker

**CI - Github Action 적용해보기**

* **보통 gradle 빌드가 느리므로 이를 캐시를 적용하여 CI 속도를 향상 시킨다고도 한다.**
  
  * 참고 문헌 : [CI 속도향상](https://hellorennon.tistory.com/22)
  
* **CI 구축 이유**
  
  * 매번 main에 앱 업데이트 후 실행하여 정상 동작하는지 보는게 너무 귀찮
  * 제일 중요한 점은 열심히 작성한 **단위테스트 코드들을 실행할 수 있다는것**
  * **따라서 테스트 시간을 대폭 단축**
    * **맨 처음 Unit Test Code** 작성으로 테스트시간 단축 후
    * **CI** 까지 구축하여 테스트시간을 더욱 단축
  
* **워크플로우 계획**

  * main에 push 할 때 실행
  * "정상 빌드" 확인
  * "정상 테스트" 확인
    * **test쪽 yml파일을 주석처리 함으로써 메모리DB 사용으로 h2 DB 연결 문제 방지!**
  
  <details><summary><b>워크플로우 소스</b></summary>
  <div markdown="1">
  ```groovy
  name: CI Test
  #
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
  #
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
  </div>
  </details>

<br>

**CD - Github Action 적용해보기**

**도커 빌드까지는 CI로 볼 수 있고, CD는 Cafe24 서버에서 빌드한 도커 이미지를 배포하는 부분!**

1. **GitHub에 소스코드 수정 후 push**:
   - Main 브랜치에 수정된 소스를 push하면 **GitHub Actions**가 트리거됩니다.
2. **GitHub Actions의 CI/CD 파이프라인 실행**:
   - **테스트 코드 실행**: 먼저 테스트 코드를 실행하여 코드의 안정성을 검증합니다.
   - **Gradle 빌드**: Gradle을 이용해 애플리케이션을 빌드합니다.
   - **Docker 이미지 생성**: Docker를 사용해 프로젝트를 기반으로 이미지를 빌드합니다.
   - **Docker 이미지를 저장소에 푸시**: 빌드된 Docker 이미지를 **Docker Hub** 또는 **Google Container Registry(-> 요즘은 Artifact Registry 사용)**와 같은 외부 Docker 이미지 레지스트리에 저장합니다.
     - **Docker Hub의 Public은 무료**이지만, Private은 유료입니다.
     - 만약 유료를 사용할거면 Artifact Registry가 보안이 좋아서 더 좋습니다.
3. **Cafe24 서버에서 Docker 이미지 가져와 실행 (SSH로 접속)**:
   - **Cafe24 서버에서** GitHub Actions에서 빌드한 **Docker 이미지를 저장소(ex: Docker Hub)에서 가져와 실행**합니다.
   - **Cafe24 서버에 미리 Docker를 설치**해야 정상 동작합니다.
     - 만약, 공유 호스팅 서버라면 루트권한이 제한되므로 Docker 설치가 제한될 수 있습니다.
     - 따라서 FTP로 서버에 jar파일을 보내고, java -jar 로 배포하는 전통방법이 해법이 될 수 있습니다.
4. **워크플로우 소스는 생략**

<br><br>

### Jenkins + Docker + GCP(GKE)

Jenkins로 GCP 연동해서 Google Cloud Build 를 사용해 도커 이미지로 빌드 및 Artifact Registry에 Push 를 하게 되므로 "직접 도커 이미지 빌드" 를 할 필요는 없지만,<br>**Jenkins 사용 전이라 가정하고 GCP로 도커 이미지 빌드 및 푸시하는 과정을 시작으로 설명하겠음.** (뒤에 Jenkins 연동도 설명할거임!)

**jar파일(앱)만 수정해서 깃 push하면 GKE+Jenkins로 CI/CD**

- CI: 바뀐 내용을 Gradle로 빌드하고(이건 본인은 생략하고 바로 jar를 올림), Docker Image로 빌드한 후, GCP의 Artifact Registry에 Push (본인은 Docker Hub말고 이걸 사용)
- CD: Artifact Registry에 Push한 Docker Image를 GKE로 배포(Deploy)

<br>

#### 1. 도커 빌드와 푸시

* Docker 이미지를 빌드하려면 애플리케이션(=jar)과 Dockerfile이 필요
* 따라서 jar 파일을 GCP에 **"업로드"** 후 **Dockerfile 작성**해서 **"도커 이미지 빌드" 해보자.**
  * 단, db 서버를 따로 안 열어서 메모리db 사용하게 앱을 수정한 점 참고

**Dockerfile**

```dockerfile
FROM openjdk:17-jdk
LABEL maintainer="test"

# JAR 파일을 Docker Image로 복사 -> 도커 이미지로 jar파일 복사안하면 unable jar 오류
COPY secret-art-typing-gallery-0.0.1-SNAPSHOT.jar /app/secret-art-typing-gallery-0.0.1-SNAPSHOT.jar
# 작업 디렉토리 설정 -> 도커 작업 상황은 /app으로 가야 jar파일 바로 사용 
WORKDIR /app # COPY에서 app/경로에 jar를 복제했기 때문
# 실행 명령어 설정
ENTRYPOINT ["java","-Dspring.profiles.active=default","-jar","secret-art-typing-gallery-0.0.1-SNAPSHOT.jar"] 
```

- **에디터 사용 TIP : nano Dockerfile**
  - Ctrl+X -> Y : 저장하고 에디터 종료하는것
- docker build -t docker-springboot:0.1 .
  - 도커 이미지 이름은 docker-springboot 이고 태그는 0.1로 지정 (태그로 버전구분 용이)
  - **도커 이미지**는 실행 가능한 **컨테이너의 원본이 되는 패키지**
- docker images : 빌드한 도커 이미지 확인 방법 (아래 사진)
- docker run -p 5000:8080 --name my-app docker-springboot:0.1 : **앱 실행(docker run)**
  * `-p` 로 5000포트 출입을 8080포트에 매핑, `--name` 으로 my-app로 실행 컨테이너 이름 지정
- docker run -p 5000:8080 --name my-app -d docker-springboot:0.1 : **백그라운드 실행**
  - `-d` 로 백그라운드 실행
- docker ps -a : 실행 컨테이너 확인 방법
- `curl http://localhost:5000` 으로 테스트 (아래 사진) 정상임을 확인

<br>

**Google Container Registry API** 사용하여 이곳에 **도커 이미지 저장** (GKE에서 바로 사용하게)

- 단, 클러스터 생성을 먼저 해야하므로 **2. GKE 클러스터 환경 설치 및 사용자 인증**를 먼저 수행하고 올 것!!

- gcloud auth configure-docker asia-northeast3-docker.pkg.dev : Container Registry 저장소 사용 위해 인증!
- docker build -t asia-northeast3-docker.pkg.dev/vital-wavelet-381119/my-repository/docker-springboot:0.1 . : 도커 빌드!
  - 본인의 project_id 가 vital-wavelet-381119
  - Container Registry에 my-repository를 미리 만들었었음
  - docker images 명령어 사용해보면 이미 있으므로 바로 docker-springboot:0.1 빌드가 가능!
- docker push asia-northeast3-docker.pkg.dev/vital-wavelet-381119/my-repository/docker-springboot:0.1 : **Registry에 push** (결과는 아래 사진 참고)

<details><summary><b>아래 사진</b></summary>
<div markdown="1"><br>
<img src="https://github.com/BH946/bh946.github.io/assets/80165014/8f278179-7480-4f1d-b71f-8347dd4a544d" alt="image"  /><br><br>
<img src="https://github.com/user-attachments/assets/8608c71f-ccde-4c48-bc75-ab62ccb243c4" alt="image"  /><br><br>
<img src="https://github.com/user-attachments/assets/f9341fc9-0b17-4fc0-a8bd-642fa2bc49f1" alt="image"  />
</div>
</details>

<br>

#### 2. GKE 클러스터 환경 설치 및 사용자 인증

<details><summary><b>클러스터 개념 참고</b></summary>
<div markdown="1"><br>
**Node(컴퓨팅리소스-VM) -> Pod(컨테이너 실행시킴), Container(어플리케이션)**<br>
`Region > Zone > Cluster > Node > Pod` **형태의 포함 관계를 가짐.**<br>
**Build 한 Docker Image를 실행 가능한 Container의 원본 패키지**<br>
**쿠버네티스 클러스터(=아래그림 전체)*<br>
<img src="https://github.com/BH946/bh946.github.io/assets/80165014/c68de552-2acf-429e-a82a-22f8c56fdff1" alt="image" style="zoom:80%;" />
</div>
</details>
- gcloud config set project 본인프로젝트ID

* gcloud config set compute/region asia-northeast3
* gcloud config set compute/zone asia-northeast3-a
  * Region > Zone 로써 Region이 더 큰 개념입니다.
* gcloud container clusters create --machine-type=e2-medium --zone=asia-northeast3-a lab-cluster
  * 혹시나 **Kubernetes Engine API has not been used ... 에러** 발생시 **GCP에서 API 사용 설정**
  * **lab-cluster 이름**으로 GKE 클러스터 생성 -> 5분정도 소요
    * **클러스터**는 하나 이상의 **"클러스터 Master 머신"과 "Worker 머신"**으로 구성
      * "머신 = Node"
    * **노드**를 만들기 위해서는 컴퓨팅 리소스가 필요한데, GCP에서는 가상머신(VM)으로 컴퓨팅리소스를 만듬 -> **GCP의 Compute Engine 에서 확인 가능**
      * GKE로 클러스터 생성시 자동으로 만들어주기 때문에 **직접 VM을 구축할 필요없다!!** (간편함)
* gcloud container clusters get-credentials lab-cluster --zone asia-northeast3-a
  * 클러스터 사용에는 **사용자 인증이 필수**
    * 어떤 **서비스 계정을 사용했는지 확인**은 **Compute Engine 에서 확인 가능**
  * 뒤의 zone은 클러스터 생성한 위치 의미!! (쉽게 경로로 생각해도 좋다)
  * **쿠버네티스 클러스터와 Cloud Shell은 다른 환경이므로 인증이 필수**
    * 단, Cloud Shell에서 쿠버네티스 클러스터 생성했을 경우 해당 Shell에서는 자동으로 인증을 이미 했기 때문에 바로 클러스터 정보를 조회하거나 사용이 가능

<br>

#### 3. GKE로 Deployment, Service

<details><summary><b>배포 관리를 위한 참고 명령어</b></summary>
<div markdown="1">
- `kubectl rollout pause deployment/hello` : 배포 중지!
- `kubectl rollout resume deployment/hello` : 일시 중지를 다시 시작!
- `kubectl rollout undo deployment/hello` : 이전 버전으로 롤백!
- `kubectl rollout history deployment/hello` : 배포 기록 확인!
- `kubectl rollout status deployment/hello` : 출시 상태를 확인!
- `kubectl get pods` : 생성된 Pod 확인 (Pod는 컨테이너 실행하니까 앱으로 보면 됨)
- `kubectl get svc (or service)` : 생성된 Service 확인 (접근을 Pod로 연결 담당)
- `kubectl get deployment` : 생성된 Deployment 확인
- `kubectl delete deployment hello` : 배포 삭제 완료! 포드도 다 삭제 됨!
- `kubectl delete svc hello` : 서비스 삭제 완료!
- `kubectl get pods -L role` : 역할 확인 가능(=full)
- `kubectl get pods -o=jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[*].image}{"\n"}{end}'` : **JSONPath**를 사용해 모든 파드의 도커 이미지 path를 확인 가능
- **JSONPath 활용한 도커이미지 path (아래 그림)**
<img src="https://github.com/user-attachments/assets/cd283862-0c0e-47d6-9580-c3b1ec925354" alt="image"  />
</div>
</details>
<details><summary><b>Pod, Deployment, StatefulSet, Service, PersistentVolumeClaim 객체 특징</b></summary>
<div markdown="1"><br>
**yaml작성 때 kind: Pod, Deployment, StatefulSet, Service, PersistentVolumeClaim 정도는 기억하자!**
1. **Pod**: 컨테이너의 가장 작은 배포 단위, 단일 컨테이너 또는 다중 컨테이너.
   - 명령어: `kubectl get pods`
2. **Deployment**: Pod의 배포, 스케일링, 업데이트 관리.
   - 명령어: `kubectl get deployments`
3. **StatefulSet**: 상태를 가진 애플리케이션 관리, 고정된 네트워크 ID 제공.
   - 명령어: `kubectl get statefulsets`
4. **Service**: Pod 간의 통신을 위한 네트워크 엔드포인트 제공, 로드 밸런싱.
   - 명령어: `kubectl get services`
5. **PersistentVolumeClaim**: PV자동 생성 및 저장소 제공, 데이터 영속성 제공.
   - 명령어: `kubectl get pvc`
</div>
</details>

**Kubernetes의 대표적인 2가지 객체 : Deployment, Service**

**Kubernetes의 Deployment, Service하는 방법은 2가지가 있다.** <br>참고로 배포 할 **도커 이미지 경로**는 저장했던 **Container Registry 저장소**

1. create(Deployment), expose(Service) 사용

   - kubectl create deployment hello-server --image=asia-northeast3-docker.pkg.dev/vital-wavelet-381119/my-repository/docker-springboot:0.1
   - kubectl expose deployment hello-server --type=LoadBalancer --port 8080

2. apply(Deployment, Service) 사용 -> 작성한 yaml 파일이 필수로 필요

   - kubectl apply -f k8s/production -> production 디렉토리에 production.yaml 존재 필수

   - kubectl apply -f k8s/service -> production 디렉토리에 service.yaml 존재 필수

<br>

**본인은 2번 yaml로 생성 방식 택!**

yaml 작성 전 경로정리 -> my-app 폴더 만들어서 거기에 필요 파일들 관리!

- mkdir my-app : 폴더 생성 예시
- mv Dockerfile my-app/ : 파일 이동(jar 파일도) 예시
- cd my-app : 경로 이동 예시

<details><summary><b>yaml 소스 모음 - production, canary, dev, service</b></summary>
<div markdown="1"><br>
`nano k8s/production/production.yaml`
```yaml
kind: Deployment
apiVersion: apps/v1
metadata:
  name: secret-full-production
spec:
  replicas: 3
  selector:
      matchLabels:
          app: secret
          role: full
          env: production
  template:
    metadata:
      name: full
      labels:
        app: secret
        role: full
        env: production
    spec:
      containers:
      - name: full
        image: asia-northeast3-docker.pkg.dev/vital-wavelet-381119/my-repository/docker-springboot:0.1
        resources:
          limits:
            memory: "500Mi"
            cpu: "100m"
        imagePullPolicy: Always
        ports:
        - name: full
          containerPort: 8080
```
<br>
`nano k8s/canary/canary.yaml`
```yaml
kind: Deployment
apiVersion: apps/v1
metadata:
  name: secret-full-canary
spec:
  replicas:
  selector:
      matchLabels:
          app: secret
          role: full
          env: canary
  template:
    metadata:
      name: full
      labels:
        app: secret
        role: full
        env: canary
    spec:
      containers:
      - name: full
        image: asia-northeast3-docker.pkg.dev/vital-wavelet-381119/my-repository/docker-springboot:0.1
        resources:
          limits:
            memory: "500Mi"
            cpu: "100m"
        imagePullPolicy: Always
        ports:
        - name: full
          containerPort: 8080
```
<br>
`nano k8s/dev/dev.yaml`
```yaml
kind: Deployment
apiVersion: apps/v1
metadata:
  name: secret-full-dev
spec:
  replicas:
  selector:
      matchLabels:
          app: secret
          role: full
          env: dev
  template:
    metadata:
      name: full
      labels:
        app: secret
        role: full
        env: dev
    spec:
      containers:
      - name: full
        image: asia-northeast3-docker.pkg.dev/vital-wavelet-381119/my-repository/docker-springboot:0.1
        resources:
          limits:
            memory: "500Mi"
            cpu: "100m"
        imagePullPolicy: Always
        ports:
        - name: full
          containerPort: 8080
```
<br>
`nano k8s/service/service.yaml`
```yaml
kind: Service
apiVersion: v1
metadata:
  name: secret-full-svc
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 80
    targetPort: 8080
    protocol: TCP
  selector:
    app: secret
    role: full
```
</div>
</details>

**배포 해보자 -> 복제본 3개와 카나리아 1개로 하자 (dev는 Jenkins 설명에서 언급)**

- kubectl apply -f k8s/production
- kubectl apply -f k8s/canary
- kubectl apply -f k8s/service

- 배포 테스트 -> 실제 웹 `http://34.22.73.59` 접속도 정상 확인

<img src="https://github.com/user-attachments/assets/21688378-7fcb-4895-bd8a-b0a6cbfa1659" alt="image" style="zoom:80%;" /> 

<br>

#### 4. Jenkins 로 CI/CD 자동화

<details><summary><b>GCP IAM 서비스 계정vs 쿠버네티스 서비스 계정</b></summary>
<div markdown="1"><br>
**GCP IAM 서비스 계정**은 GCP의 **리소스(GCE 인스턴스, GKE 클러스터 등)에 접근할 권한**<br>
**쿠버네티스 서비스 계정**은 **쿠버네티스 클러스터 내부에서의 권한을 관리**하는 역할<br>
- `kubectl get serviceaccounts -n default` : `default` 네임스페이스에 존재하는 **서비스 계정 목록**을 확인
- `kubectl create serviceaccount cd-jenkins -n default` : `default` 네임스페이스에 **`cd-jenkins`**라는 이름의 **서비스 계정**을 생성. 그런 다음, `clusterrolebinding`을 통해 클러스터 관리 권한을 부여하는 과정을 수행할 수 있다.
</div>
</details>

**Helm(패키지 관리자)을 사용해서 차트저장소에 Jenkins를 설치**

- helm repo add jenkins https://charts.jenkins.io : Jenkins 차트가 저장되어 있는 저장소를 Helm에 추가하여 나중에 해당 차트를 검색하고 설치할 수 있게 한다.

  * helm search repo jenkins : Helm에 추가된 Jenkins 차트 저장소 설치 확인!

  * helm repo update : 저장소 최신 상태인지 확인!

- values.yaml 파일 하나를 생성하자.

  <details><summary>values.yaml 코드</summary>
  <div markdown="1">
  ```
  controller:
    installPlugins:
      - kubernetes:latest
      - workflow-job:latest
      - workflow-aggregator:latest
      - credentials-binding:latest
      - git:latest
      - google-oauth-plugin:latest
      - google-source-plugin:latest
      - google-kubernetes-engine:latest
      - google-storage-plugin:latest
    resources:
      requests:
        cpu: "50m"
        memory: "1024Mi"
      limits:
        cpu: "1"
        memory: "3500Mi"
    javaOpts: "-Xms3500m -Xmx3500m"
    serviceType: ClusterIP
    service:
      port: 8081
  agent:
    resources:
      requests:
        cpu: "500m"
        memory: "256Mi"
      limits:
        cpu: "1"
        memory: "512Mi"
  persistence:
    size: 100Gi
  serviceAccount:
    name: cd-jenkins
  ```
  </div>
  </details>

- helm install cd jenkins/jenkins -f jenkins/values.yaml --wait : **진짜 Jenkins 설치**
  - Jenkins 설치할 때 `values.yaml` 을 통해서 연동에 필요한 플러그인들을 자동으로 설치
  - helm status cd : cd라는 이름으로 설정했으므로 cd 상태 체크로 설치 상태 확인 가능
  - kubectl get pods 와 svc 로 정상 배포까지 확인!
- kubectl create clusterrolebinding jenkins-deploy --clusterrole=cluster-admin --serviceaccount=default:cd-jenkins : 쿠버네티스 서비스 계정 설정 (jenkins 서비스 계정)
- kubectl port-forward cd-jenkins-0 8080:8080 >> /dev/null & : 로컬(웹 미리보기) 8080 포트 접근 시 -> pod 8080 포트로 연결 (=포트포워딩)
  - **jenkins 실행에 포트포워딩 필수(웹 미리보기) -> 새로 Cloud Shell 사용할 때 마다 필수**
  - service type이 ClusterIP 이므로 외부IP는 없으니까 Cloud Shell의 웹 미리보기 포트 8080 클릭해서 접속합니다.
- printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo : 비밀번호 확인 명령어 (Jenkins 처음 생성 시 자동으로 관리자 비밀번호 설정)
  - 관리자 ID : admin
  - 비밀번호 : 위에서 확인한 번호 -> 본인의 경우: dMordzg7DFfNWdXX2QWO12

<br>

**Jenkins 대시보드 설정 -> 깃허브에 레포 만들어서 우리 플젝 미리 올려두기!**

1. 서비스 계정 사용자 인증
   - Jenkins에서 GitHub 저장소에 접근하기 위해서는 GitHub 플러그인을 설치<br>`Manage Jenkins -> Manage Plugins -> Available 탭에서 "GitHub"를 검색하여 설치`
   -  `Jenkins 관리 > Manage Credentials > System > Global credentials > Add Credentials`
     * `Kind는 Username with password 선택 -> Username: GitHub 사용자 이름 -> Password: Personal Access Token (PAT) 사용 -> Create`
       * Git 관련 사용 위한 인증 계정!
     * `Kind는 Secret file 선택 -> GCP IAM에 서비스계정 등록(JSON 파일) -> Create`
       * GCP 관련 사용 위한 인증 계정!
       * Compute Engine 사용 계정을 확인하여 얘를 등록하는 것!
2. Kubernetes용 Jenkins Cloud 구성
   - 구성방법 : `Jenkins 관리 > Manage Cloud > Clouds Add a new cloud > Kubernetes > Kubernetes Cloud Details`

     * Jenkins URL : `http://cd-jenkins:8080`
     * Jenkins tunnel : `cd-jenkins-agent:50000`
3. Jenkins 작업 생성
   1. Dashboard(대시보드) > New Item(새 항목) 클릭
   2. 프로젝트 이름은 my-app으로 지정, Multibranch Pipeline(다중 브랜치 파이프라인) 옵션 선택 후 OK 클릭
   3. Branch Sources(브랜치 소스) 섹션에 있는 Add Source(소스 추가) 드롭다운에서 Git 선택
   4. Project Repository(프로젝트 저장소) 필드에 깃허브에 만든 레포지토리 주소 입력(http) : `https://github.com/BH946/my-app.git`
   5. 이전 단계에서 서비스 계정을 추가할 때 만든 사용자 인증 정보의 이름을 사용자 인증 정보드롭다운에서 선택
   6. Scan Multibranch Pipeline Triggers(다중 브랜치 파이프라인 트리거 검색)섹션에서 Periodically if not otherwise run(별도로 실행하지 않는 경우 주기적으로 실행)상자를 선택하고 Interval(간격) 값을 1분으로 설정
   7. `Branch Indexing`이라는 작업이 자동으로 실행되므로 `Finished: SUCCESS` 메시지 뜨면 성공!

<br>

**Jenkins 파이프라인을 만들어보자! (Jenkinsfile 스크립트 작성이 매우 중요!)**

**파이프라인 흐름**<br>수정된 앱이 Github에 push가 되면 1분마다 동작되는 Jenkins에 트리거 되어 Jenkinsfile을 수행<br>Docker Image로 빌드 후 Artifact Registry에 푸시<br>Artifact Registry에 Push한 Docker Image를 GKE로 배포(Deploy)

<details><summary><b>Jenkinsfile 문법 참고</b></summary>
<div markdown="1">
- env.BRANCH_NAME과 env.BUILD_NUMBER를 사용하여 Docker 이미지 태그를 동적으로 생성
  - 만약 깃허브라면,
  - env.BRANCH_NAME: 현재 빌드가 실행 중인 Git 브랜치의 이름을 나타냅니다.
  - env.BUILD_NUMBER: 현재 빌드의 번호를 나타냅니다. 각 빌드는 고유한 번호를 가집니다.
- Jenkinsfile에 더 다양한 소스들이 존재
  - Build and Push Docker Image: 도커 이미지 빌드 및 푸시
  - Deploy Canary: Canary 환경 배포
  - Deploy Production: 프로덕션 환경 배포
  - Deploy Dev: 개발 환경 배포
  - 배포 단계별 주요 특징
    - `when` 조건: 브랜치에 따라 다른 배포 수행 (다양한 배포 환경!)
    - `container()`: 특정 컨테이너에서 명령 실행
    - `sed` 명령: yaml 파일의 이미지 경로 동적 대체
    - `kubectl` 명령: Kubernetes 클러스터에 리소스 배포
  - sh 명령어 관려 코드 자세히
    - gcloud auth configure-docker: Google Container Registry에 Docker 인증 정보를 설정합니다. 이 명령어를 실행하면 Docker가 Google Container Registry에 이미지를 푸시할 수 있도록 인증됩니다.
    - docker build -t ${IMAGE_TAG} .: 현재 디렉토리(.)의 Dockerfile을 사용하여 Docker 이미지를 빌드합니다. 이미지 태그는 ${IMAGE_TAG}로 설정됩니다.
    - docker push ${IMAGE_TAG}: 빌드된 이미지를 Google Container Registry에 푸시합니다.
    - gcloud container clusters get-credentials: 지정된 클러스터에 대한 인증 정보를 가져옵니다. 이를 통해 kubectl 명령어를 사용할 수 있게 됩니다.
    - sed -i.bak 's#DOCKER_IMAGE_PLACEHOLDER#${IMAGE_TAG}#' ./k8s/canary/*.yaml: canary 디렉토리의 YAML 파일에서 DOCKER_IMAGE_PLACEHOLDER를 ${IMAGE_TAG}로 교체합니다. .bak 확장자의 백업 파일이 생성됩니다.
    - kubectl apply -f ./k8s/services: Kubernetes 서비스 리소스를 적용합니다.
    - kubectl apply -f ./k8s/canary: Canary 배포를 위한 리소스를 적용합니다.
    - kubectl get ns ${env.BRANCH_NAME} || kubectl create ns ${env.BRANCH_NAME}: 현재 브랜치 이름에 해당하는 네임스페이스가 존재하지 않으면 생성합니다.
    - sed -i.bak 's#LoadBalancer#ClusterIP#' ./k8s/services/frontend.yaml: 프론트엔드 YAML 파일에서 서비스 타입을 LoadBalancer에서 ClusterIP로 변경합니다.
    - kubectl apply -f ./k8s/services -n ${env.BRANCH_NAME}: 지정된 네임스페이스에 서비스를 적용합니다.
    - kubectl apply -f ./k8s/dev -n ${env.BRANCH_NAME}: 개발 환경 리소스를 적용합니다.
      마지막 두 줄은 로컬에서 서비스에 접근하는 방법을 안내합니다.
    - 등등.. 원하는 입맛대로 수정 가능
</div>
</details>

<details><summary><b>Jenkinsfile 소스</b></summary>
<div markdown="1"><br>
`nano Jenkinsfile`
```groovy
pipeline {
    environment {
        PROJECT = "vital-wavelet-381119"
        APP_NAME = "docker-springboot"
        FULL_SVC_NAME = "secret-full-svc"
        CLUSTER = "lab-cluster"
        CLUSTER_ZONE = "asia-northeast3-a"
        IMAGE_TAG = "asia-northeast3-docker.pkg.dev/${PROJECT}/my-repository/${APP_NAME}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"
        JENKINS_CRED = "${PROJECT}"
        GCP_CRED = "99e295f9-753c-459d-9b77-1c814a4f83c3" // Jenkins에 등록한 GCP 자격 증명 ID
    }
    agent {
        kubernetes {
            label 'my-app'
            defaultContainer 'jnlp'
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    component: ci
spec:
  serviceAccountName: cd-jenkins
  containers:
  - name: gcloud
    image: gcr.io/cloud-builders/gcloud
    command:
    - cat
    tty: true
  - name: kubectl
    image: gcr.io/cloud-builders/kubectl
    command:
    - cat
    tty: true
"""
        }
    }
    stages {
        stage('Build and Push Docker Image') {
            steps {
                container('gcloud') {
                    withCredentials([file(credentialsId: env.GCP_CRED, variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                        sh """
                            # JSON 확인 위한 LOG
                            echo "Using credentials from: \$GOOGLE_APPLICATION_CREDENTIALS"
                            cat \$GOOGLE_APPLICATION_CREDENTIALS
                            gcloud config set project ${PROJECT}
                            # GCP 자격 증명을 활성화한 후 Docker 이미지 빌드 및 푸시
                            gcloud auth activate-service-account --key-file=\$GOOGLE_APPLICATION_CREDENTIALS
                            PYTHONUNBUFFERED=1 gcloud builds submit -t ${IMAGE_TAG} .
                        """
                    }
                }
            }
        }
        stage('Deploy Canary') {
            when { branch 'canary' }
            steps {
                container('kubectl') {
                    withCredentials([file(credentialsId: env.GCP_CRED, variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                        sh """
                            gcloud auth activate-service-account --key-file=\$GOOGLE_APPLICATION_CREDENTIALS
                            gcloud config set project ${PROJECT}
                            gcloud container clusters get-credentials ${CLUSTER} --zone ${CLUSTER_ZONE} --project ${PROJECT}
                            sed -i.bak 's#asia-northeast3-docker.pkg.dev/vital-wavelet-381119/my-repository/docker-springboot:0.1#${IMAGE_TAG}#' ./k8s/canary/*.yaml
                            kubectl apply -f ./k8s/service
                            kubectl apply -f ./k8s/canary
                        """
                    }
                }
            }
        }
        stage('Deploy Production') {
            when { branch 'main' }
            steps {
                container('kubectl') {
                    withCredentials([file(credentialsId: env.GCP_CRED, variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                        sh """
                            gcloud auth activate-service-account --key-file=\$GOOGLE_APPLICATION_CREDENTIALS
                            gcloud config set project ${PROJECT}
                            gcloud container clusters get-credentials ${CLUSTER} --zone ${CLUSTER_ZONE} --project ${PROJECT}
                            sed -i.bak 's#asia-northeast3-docker.pkg.dev/vital-wavelet-381119/my-repository/docker-springboot:0.1#${IMAGE_TAG}#' ./k8s/production/*.yaml
                            kubectl apply -f ./k8s/service
                            kubectl apply -f ./k8s/production
                        """
                    }
                }
            }
        }
        stage('Deploy Dev') {
            when {
                not { branch 'main' }
                not { branch 'canary' }
            }
            steps {
                container('kubectl') {
                    withCredentials([file(credentialsId: env.GCP_CRED, variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                        sh """
                            gcloud auth activate-service-account --key-file=\$GOOGLE_APPLICATION_CREDENTIALS
                            gcloud config set project ${PROJECT}
                            gcloud container clusters get-credentials ${CLUSTER} --zone ${CLUSTER_ZONE} --project ${PROJECT}
                            kubectl get ns ${env.BRANCH_NAME} || kubectl create ns ${env.BRANCH_NAME}
                            sed -i.bak 's#asia-northeast3-docker.pkg.dev/vital-wavelet-381119/my-repository/docker-springboot:0.1#${IMAGE_TAG}#' ./k8s/dev/*.yaml
                            kubectl apply -f ./k8s/service -n ${env.BRANCH_NAME}
                            kubectl apply -f ./k8s/dev -n ${env.BRANCH_NAME}
                            echo 'To access your environment run `kubectl proxy`'
                            echo "Then access your service via http://localhost:8001/api/v1/proxy/namespaces/${env.BRANCH_NAME}/services/${FULL_SVC_NAME}:80/"
                        """
                    }
                }
            }
        }
    }
    post {
        success {
            echo '배포 성공!'
        }
        failure {
            echo '배포 실패!'
        }
    }
}
```
</div>
</details>

1. Github 접근은 문제 없음 -> Jenkins 대시보드에서 서비스 계정 (깃허브 id,pw) 등록 덕분

2. Docker를 위해 Google Cloud Build 와 배포를 위해 kubectl 사용도 문제 없음 -> Jenkins 대시보드에서 서비스 계정 (GCP IAM JSON) 등록 덕분!! 단, Jenkinsfile 에 소스를 잘 작성해야 한다.

   Jenkins가 도커를 자동으로 제공하는건 아니므로 도커 플러그인을 설치하거나, Docker-In-Docker (DinD) 방식을 사용하는 등 다소 복잡하다. 하지만, **Google Cloud Build를 사용**하면 훨씬 간단하다. 얘는 내부적으로 도커 빌드 환경을 제공!! 대신 GCP 쪽 IAM 계정 인증이 필수~!

   **핵심 코드는?**

   - GCP IAM 자격 증명!
     - `GCP_CRED = "99e295f9-753c-459d-9b77-1c814a4f83c3" // Jenkins에 등록한 GCP 자격 증명 ID`
     - ``withCredentials([file(credentialsId: env.GCP_CRED, variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) { ... }``
     - `gcloud auth activate-service-account --key-file=\$GOOGLE_APPLICATION_CREDENTIALS`

   - 도커 빌드, 푸시 - Google Cloud Build 사용!
     - `gcloud builds submit -t ${IMAGE_TAG} .`

   - 배포를 위해 kubectl  사용!
     - `sed -i.bak 's#asia-northeast3-docker.pkg.dev/vital-wavelet-381119/my-repository/docker-springboot:0.1#${IMAGE_TAG}#' ./k8s/canary/*.yaml`
     - 이 부분이 중요한 이유는 yaml 에 있는 도커 이미지 경로를 수정 부분이라서!

<br>

#### 5. 배포 테스트 + 카나리아 업뎃 방식 사용

1. **그외 branch -> dev (개발서버)**

   - git checkout -b new-feat<br>git add .<br>git commit -m "test : dev deployment"<br>git push origin new-feat

   - dev의 경우 "네임스페이스"를 생성하게 작성해서 독립적으로 pod, svc가 관리 가능하다.

   - 실제로 똑같은 service.yaml 파일을 사용했는데 1개 더 생성되었다! (외부IP도 그럼 2개!)

   - **네임스페이스: new-feat 으로 생성했음 (=깃헙 브랜치명)** 

2. **main branch -> production (운영서버)** 
   - 만약 카나리아를 운영서버로 업뎃하게 된다면 merge로 바로 CI/CD 간편!
   - git checkout main<br>
     git merge canary<br>
     git push origin main

3. **canary branch -> 카나리아 업뎃**
   - IP고정하고 싶으면? `sessionAffinity: ClientIP` 를 서비스 단 yaml에 추가

<details><summary><b>테스트 사진</b></summary>
<div markdown="1"><br>
<img src="https://github.com/user-attachments/assets/7d4e8423-1df2-41b5-b1a6-f700977acebc" alt="image" style="zoom:80%;" /><br>
<img src="https://github.com/user-attachments/assets/e362f2b8-bb35-4df5-87cc-aa85c5debd57" alt="image" style="zoom:80%;" /><br>
**위 사진은 카나리아 적용되어 25%(4개 Pod중 1개) 확률로 업뎃한 배포가 출력!**<br>
<img src="https://github.com/user-attachments/assets/d2f7d0ee-1db3-4f37-b91e-59520d6be2fc" alt="image" style="zoom:80%;" /><br>
**위 사진은 canary 를 main과 merge하여 최종 업뎃한 배포 출력! 100%(4개 전부 최신 Pod)**<br>
**배포기록 - Artifact Registry, Jenkins**<br>
<img src="https://github.com/user-attachments/assets/3d83b772-4a4e-4b4c-a579-052ae13a4f7b" alt="image" style="zoom:80%;" /><br>
<img src="https://github.com/user-attachments/assets/c0b1eef1-92b3-46fa-82ee-4390f1dd9d2c" alt="image" style="zoom:80%;" /> 
</div>
</details>
