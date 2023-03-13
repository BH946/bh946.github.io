---
title:  "Github Actions(CI/CD) 개념"
categories : Action_Git
tag : [깃헙, 깃허브, git, github, CI/CD, cron, 깃허브 액션, Github action]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



## 깃허브 액션이란?

**GitHub Actions**는 코드 저장소(repository)로 유명한 **GitHub**에서 제공하는 CI(Continuous Integration, 지속 통합)와 CD(Continuous Deployment, 지속 배포)를 위한 소프트웨어 개발 **Workflow**를 자동화 할 수 있는 서비스 이다.

해외 기업이기 때문에 해외 IP를 사용하니 개발하면서 **해외 서버로 접속**된다는것을 참고 해야한다.

<br>

### CI란?

CI 명칭은 `지속적인 통합(Continuous Integration)` 이라는 뜻을 가지며 `빌드/테스트 자동화 과정` 을 의미한다.

여러 사람들이 같은 코드를 작업 할 경우 서로 충돌할 수 있는데,  
**커밋할 때마다 빌드와 일련의 자동 테스트가 이루어져서 문제가 생기는 부분이 없도록 보장해주는 장점이 있다.**

<br>

### CD란?

CD 명칭은 `지속적인 서비스 제공(Continuous Delivery)` 또는 `지속적인 배포(Continuous Deployment)` 라는 뜻을 가지며 `배포 자동화 과정` 을 의미한다.

**코드 변경이 모두 성공적으로 통과하면 수동 개입 없이 자동으로 해당 변경사항이 배포되는 장점이 있다.**

<br><br>

## 깃허브 액션의 주요 개념

**Workflow, Event, Job, Step, Action, Runner**

참고 사이트 : [URL](https://insight-bgh.tistory.com/m/473), [git-acion-smartstore](https://github.com/BH946/git-action-smartstore/blob/main/.github/workflows/python-package.yml)

<br>

### Workflow

* 전체적인 동작을 담당하며, **프로세스와 유사**하다고 생각하면 된다.
* 여러개의 Job으로 구성되어 event기반으로 동작한다.
* `Job`은 최상위 개념이며 **스레드와 유사**하다고 생각하면 된다.
* Workflow file을 만들어 전달하면 깃허브 액션이 이를 실행한다.
* Workflow file의 **확장자는 YAML**로 작성되며, Github Repository의 `.github/workflows` 폴더 아래에 저장.

<br>

### Event

* Workflow를 Trigger(실행)하는 특정 활동이나 규칙

* 특정 브랜치로 Push 일때 Trigger

* 특정 브랜치로 Pull Request 일때 Trigger

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
        - cron: '00 19 * * *' # UST 가 default. UST 19:00는 한국시간 04:00(9H 차이)
    ```

<br>

### Job

* 여러 Step으로 구성되고, 가상 환경의 인스턴스에서 실행

* 여러 Job이 있을 경우, Default로 병렬 실행한다.

  * 예제

  * build라는 job을 생성

  * runs-on은 OS 종류 선택

    ```yaml
    jobs:
      build:
    
        runs-on: ubuntu-latest # 최신 우분투 사용
        strategy:
          fail-fast: false
          matrix:
            python-version: ["3.8"] # 파이썬 3.8 버전
    ```

<br>

### Step

* 순차적으로 명령어를 수행한다.

* Task들의 집합이고, 커맨드를 날리거나 action을 실행

  * 예제
  * build라는 job 아래에 3개의 step 존재하는 구조

  * steps의 uses는 이미 만들어진 액션을 사용하고 싶을때 지정

    ```yaml
    jobs:
      build:
    
        runs-on: ubuntu-latest # 최신 우분투 사용
        strategy:
          fail-fast: false
          matrix:
            python-version: ["3.8"] # 파이썬 3.8 버전
            
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

<br>

### Action

* Workflow의 가장 작은 블럭
* Job을 만들기 위해 Step들을 연결할 수 있다.
* 재사용이 가능한 컴포넌트
* 개인적으로 만든 Action을 사용할 수도 있고, Marketplace에 있는 공용 Action을 사용할 수도 있음
  - [Github Marketplace](https://github.com/marketplace?type=actions)와 [Github Actions Repository](https://github.com/actions/)에서 확인 가능하다.

<br>

### Runner

* Gitbub Action Runner 어플리케이션이 설치된 머신으로, Workflow가 실행될 인스턴스
* Github에서 호스팅해주는 Github-hosted runner와 직접 호스팅하는 Self-hosted runner로 나뉨
* Github-hosted runner는 Azure의 Standard_DS2_v2로 vCPU 2, 메모리 7GB, 임시 스토리지 14GB

<br><br>

## Workflow file

Github Repository 에서 Actions를 클릭하여, 제공되는 workflows를 이용해 간편히 만들수 있다.

다른 방법은 자신의 레퍼지토리에 `.github/workflows` 경로로 `원하는파일명.yml` 을 직접 생성하는 방법이다.

