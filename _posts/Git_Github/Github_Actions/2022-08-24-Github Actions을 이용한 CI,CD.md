---
title:  "Github Actions을 이용한 CI/CD"
categories : [Action_Git, Knowledge]
tag : [깃헙, 깃허브, git, github, CI/CD, cron, 깃허브 액션, Github action, 지속적인 통합, 지속적인 배포]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**Github Actionis 을 이용해 CI(Continuous Integration, 지속 통합)와 CD(Continuous Deployment, 지속 배포)를 적용해보자.**

<br>

<br>

## 깃허브 액션이란?

**GitHub Actions**는 코드 저장소(repository)로 유명한 **GitHub**에서 제공하는 CI(Continuous Integration, 지속 통합)와 CD(Continuous Deployment, 지속 배포)를 위한 소프트웨어 개발 **Workflow**를 자동화할 수 있는 서비스이다.

해외 기업이기 때문에 해외 IP를 사용하니 개발하면서 **해외 서버로 접속**된다는 것을 참고해야 한다.

<br><br>

### CI(=지속적인 통합) 란?

CI 명칭은 `지속적인 통합(Continuous Integration)`이라는 뜻을 가지며 `빌드/테스트 자동화 과정`을 의미한다.

여러 사람이 같은 코드를 작업할 경우 서로 충돌할 수 있는데,  
**커밋할 때마다 빌드와 일련의 자동 테스트가 이루어져서 문제가 생기는 부분이 없도록 보장해주는 장점이 있다.**

<br><br>

### CD(=지속적인 배포) 란?

CD 명칭은 `지속적인 서비스 제공(Continuous Delivery)` 또는 `지속적인 배포(Continuous Deployment)`라는 뜻을 가지며 `배포 자동화 과정`을 의미한다.

**코드 변경이 모두 성공적으로 통과하면 수동 개입 없이 자동으로 해당 변경사항이 배포되는 장점이 있다.**

<br>

<br>

## 깃허브 액션의 주요 개념

**Workflow, Event, Job, Step, Action, Runner**

<br><br>

### 1. Workflow

- **워크플로우 구성이 가장 중요하다!! 해당 코드대로 동작하니까 당연한 말이다. 어떻게 만드나??**

  - Github Repository에서 Actions를 클릭하여, 제공되는 workflows를 이용해 간편히 만들 수 있다.

  - 다른 방법은 자신의 레퍼지토리에 `.github/workflows` 경로로 `원하는파일명.yml`을 직접 생성하는 방법이다.

* 전체적인 동작을 담당하며, **프로세스와 유사**하다고 생각하면 된다.
* 여러 개의 Job으로 구성되어 event 기반으로 동작한다.
* `Job`은 최상위 개념이며 **스레드와 유사**하다고 생각하면 된다.
* Workflow file을 만들어 전달하면 깃허브 액션이 이를 실행한다.
* Workflow file의 **확장자는 YAML**로 작성되며, Github Repository의 `.github/workflows` 폴더 아래에 저장.

<br><br>

### 2. Event

* Workflow를 Trigger(실행)하는 특정 활동이나 규칙

* 특정 브랜치로 Push일 때 Trigger

* 특정 브랜치로 Pull Request일 때 Trigger

* 특정 시간대에 반복(cron)

* Webhook을 사용해 외부 이벤트를 통해 실행

  * 예제

    ```yaml
    on: # 이벤트 정의하는 부분
      push:
        branches: [ "main" ]
      pull-request:
      	branches: [ "main" ]
      schedule:
        - cron: '00 19 * * *' # UST가 default. UST 19:00는 한국시간 04:00(9H 차이)
    ```

<br><br>

### 3. Job

* 여러 Step으로 구성되고, 가상 환경의 인스턴스에서 실행

* 여러 Job이 있을 경우, Default로 병렬 실행한다.

  * 예제

    ```yaml
    jobs: # build라는 job을 생성
      build: 
        runs-on: ubuntu-latest # 최신 우분투 사용 (OS 종류 선택)
        strategy:
          fail-fast: false
          matrix:
            python-version: ["3.8"] # 파이썬 3.8 버전
    ```

<br><br>

### 4. Step

* 순차적으로 명령어를 수행한다.

* Task들의 집합이고, 커맨드를 날리거나 action을 실행

* 이미 만들어진 액션도 사용 가능

  * 예제
  
    ```yaml
    jobs: # build라는 job 아래에 3개의 step 존재
      build:
        runs-on: ubuntu-latest # 최신 우분투 사용
        strategy:
          fail-fast: false
          matrix:
            python-version: ["3.8"] # 파이썬 3.8 버전
    # steps의 uses는 이미 만들어진 액션을 사용하고 싶을 때 지정
        steps:
        - uses: actions/checkout@v3
        - name: Set up Python ${{ matrix.python-version }}
          uses: actions/setup-python@v3
          with:
            python-version: ${{ matrix.python-version }}
    
        - name: Install dependencies # 파이썬 관련 패키지 설치
          run: python -m pip install --upgrade pip
          
        - name: Run!! # main.py 실행
    	  run: python src/main.py
    ```

<br><br>

### 5. Action

* Workflow의 가장 작은 블록
* Job을 만들기 위해 Step들을 연결할 수 있다.
* 재사용이 가능한 컴포넌트
* 개인적으로 만든 Action을 사용할 수도 있고, Marketplace에 있는 공용 Action을 사용할 수도 있음
  - [Github Marketplace](https://github.com/marketplace?type=actions)와 [Github Actions Repository](https://github.com/actions/)에서 확인 가능하다.

<br><br>

### 6. Runner

* Gitbub Action Runner 어플리케이션이 설치된 머신으로, **Workflow가 실행될 인스턴스**
* Github에서 호스팅해주는 Github-hosted runner와 직접 호스팅하는 Self-hosted runner로 나뉨
* Github-hosted runner는 Azure의 Standard_DS2_v2로 vCPU 2, 메모리 7GB, 임시 스토리지 14GB

<br>

<br>

## CI/CD 예시: Github Action + Docker

**CD 쪽 워크플로우 소스는 생략!**

**워크플로우 충분히 이해했으면 설명한 로직 순서대로 구현할 수 있을 것.**

<br><br>

### CI - Github Action 적용해보기

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

<br><br>

### CD - Github Action 적용해보기

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
4. **워크플로우 소스는 생략** -> 참고: [Jenkins와 GCP로 구현했던 내용](https://bh946.github.io/knowledge/CHECK_LIST_DEVOPS/#4-jenkins-%EB%A1%9C-cicd-%EC%9E%90%EB%8F%99%ED%99%94)

