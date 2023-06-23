---
title:  "[실습02]Working with Cloud Build"
categories : [Kubernetes]
tag : [구글 클라우드, GCP, GCC, 구글 클라우드 콘솔, 클라우드 쉘, 쿠버네티스, Kubernetes, 구글 클라우드 스터디 잼, 스터디 잼 중급, Docker, 도커, 이미지]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
typora-root-url: ../../..
---



# Working with Cloud Build

**두번째 실습은 `Cloud Build`와 `빌드 구성 파일` 또는 `Dockerfile` 을 사용해서 `Docker 컨테이너 이미지` 를 빌드를 해보고 `Container Registry` 에 업로드 해보겠습니다.**

**아래 4개의 Task를 해결하면서 간접적으로 경험해 봅시다.**

<br>

**Objectives(목표)**

* Use **Cloud Build** to build and push **containers**
* Use **Container Registry** to store and deploy **containers**

<br><br>

## Task 1. 필요한 API 확인하기

이 과제에서는 **Google Cloud 프로젝트에서 필요한 API가 활성화되어 있는지 확인**합니다.

* **Navigation menu > APIs & Services > Enable APIs and Services** 클릭 후 아래 설정을 진행
  * `Cloud Build` 검색 후 `Enable` 설정
  * `Container Registry` 검색 후 `Enable` 설정

<br>

**설정 모습**

![image-20230621214125654](/images/2023-06-20-(실습02)Working with Cloud Build/image-20230621214125654.png) 

<br><br>

## Task 2. DockerFile과 Cloud Build를 이용한 컨테이너 빌드하기

이 태스크에서는 **DockerFile을 생성하고 이를 Cloud Build의 빌드 구성 스크립트로 사용하여 컨테이너를 빌드**합니다. 또한, 간단한 셸 스크립트(`quickstart.sh`)를 생성하여 컨테이너 내의 애플리케이션을 대표합니다.

<br>

### 1. quickstart.sh, dockerfile 생성

* **Cloud Shell**에서 `nano quickstart.sh` 명령어를 실행하여 **nano text editor**로 접근 및  `quickstart.sh` 파일을 생성합니다.

* 이후 **editor**에서 다음의 내용을 추가합니다.

  ```bash
  #!/bin/sh
  echo "Hello, world! The time is $(date)."
  ```

* 다시 **Cloud Shell**로 돌아와서 이번엔 `nano Dockerfile` 로 **nano text editor**로 접근 및 `Dockerfile` 을 생성합니다.

* 마찬가지로 **editor**에서 다음의 내용을 추가합니다.

  * alpine : use Alpine Linux base image.
  * COPY quickstart.sh / : 이미지의 디렉토리 '/' 에 `quickstart.sh` 스크립트 추가
  * CMD ["/quickstart.sh"] : 컨테이너가 생성 및 실행될 때 `quickstart.sh` 실행되게 설정

  ```dockerfile
  FROM alpine
  COPY quickstart.sh /
  CMD ["/quickstart.sh"]
  ```

* `chmod +x quickstart.sh` 명령어를 실행하여 `quickstart.sh` 스크립트를 실행 가능하게 만듭니다.

<br>

### 2. docker 컨테이너 이미지 생성

* `gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/quickstart-image .` 명령어를 실행하여 **Docker 컨테이너 이미지를 Cloud Build에서 빌드**합니다.
  * 뒤에 '.' 의 경우 소스 코드가 빌드 시 필요 파일(dockerfile 포함)들이 현재 작업 디렉토리에 있음을 지정합니다.
  * 즉, 위 명령은 현재 디렉토리에 있는 **Dockerfile**을 사용하여 `quickstart-image` 이름으로 **Docker 이미지를 빌드**하고, 빌드된 이미지를 `gcr.io/${GOOGLE_CLOUD_PROJECT}/quickstart-image` 의 태그로 **Google Cloud Registry**에 업로드하는 작업을 수행합니다.

<br>

**도커 이미지 생성후 모습(Navigation menu > Container Registry > Images)**

![image-20230621220104210](/images/2023-06-20-(실습02)Working with Cloud Build/image-20230621220104210.png) 

![image-20230621220114338](/images/2023-06-20-(실습02)Working with Cloud Build/image-20230621220114338.png) 

<br><br>

## Task 3. 빌드 구성 파일과 Cloud Build를 이용한 컨테이너 빌드하기

**Cloud Build**는 **커스텀 빌드 구성 파일**도 지원합니다. 이 과제에서는 커스텀 YAML 형식의 빌드 파일을 Cloud Build와 함께 사용하여 기존 Docker 컨테이너를 통합합니다.

<br>

### 1. 실습 파일 다운로드

* Cloud Shell에서 `git clone https://github.com/GoogleCloudPlatform/training-data-analyst` 명령어를 실행하여 레포지토리를 복제합니다.
  * 해당 실습 파일에는 우리가 Task1,2에서 만든 dockerfile과 quickstart.sh 복제본이 존재하며,
  * 추가로 실습할 cloudbuild.yaml(커스텀 빌드 구성 파일)이 있습니다.

* `ln -s ~/training-data-analyst/courses/ak8s/v1.1 ~/ak8s` 명령어를 실행하여 작업 디렉토리에 대한 소프트 링크를 생성합니다.

* `cd ~/ak8s/Cloud_Build/a` 명령어를 실행하여 이번 실습에 필요한 샘플 파일이 있는 디렉토리로 이동합니다.

* `cat cloudbuild.yaml` 로 내용을 보면 아래와 같습니다.

  ```bash
  steps:
  - name: 'gcr.io/cloud-builders/docker'
    args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/quickstart-image', '.' ]
  images:
  - 'gcr.io/$PROJECT_ID/quickstart-image'
  ```

  * 이 파일은 **Cloud Build**가 **Docker**를 사용하여 **현재 로컬 디렉토리의 Dockerfile** 사양을 사용하여 이미지를 빌드하고 gcr.io/$PROJECT_ID/quickstart-image로 태그를 지정하도록 지시합니다.
  * ($PROJECT_ID)는 Cloud Build에서 프로젝트로 자동으로 채워지는 대체 변수입니다. 연결된 프로젝트의 ID 이미지를 **Container Registry**로 푸시합니다.

<br>

### 빌드

* `gcloud builds submit --config cloudbuild.yaml .` 명령어를 실행하여 `cloudbuild.yaml` 을 빌드 구성 파일로 사용하여 Cloud Build를 시작합니다.

<br>

**생성된 도커 이미지 모습**

![image-20230621221433468](/images/2023-06-20-(실습02)Working with Cloud Build/image-20230621221433468.png) 

<br>

**Cloud Build 모습**

![image-20230621221551515](/images/2023-06-20-(실습02)Working with Cloud Build/image-20230621221551515.png) 

<br><br>

## Task 4. 빌드 구성 파일과 Cloud Build를 이용한 컨테이너 빌드 및 테스트하기

사용자 지정 빌드 구성 파일의 진정한 힘은 단순히 컨테이너를 빌드하는 것 외에도 다른 작업을 병렬 또는 순서대로 수행할 수 있는 기능입니다. 새로 빌드된 컨테이너에서 테스트를 실행하고 다양한 대상으로 푸시하고 심지어 Kubernetes Engine에 배포할 수도 있습니다. 

이 실습에서는 빌드한 컨테이너를 테스트하고 그 결과를 호출 환경에 보고하는 빌드 구성 파일과 같은 간단한 예를 살펴보겠습니다.

<br>

* Cloud Shell에서 `cd ~/ak8s/Cloud_Build/b` 명령어를 실행하여 이번 실습에 필요한 샘플 파일이 있는 디렉토리로 이동합니다.
  * Task 3과 달리 이번엔 /b 경로이며 cloudbuild.yaml 파일이 서로 다름을 알고 넘어가자.

* `cat cloudbuild.yaml` 명령어를 실행하여 `cloudbuild.yaml` 파일의 내용을 확인합니다.

  ```bash 
  steps:
  - name: 'gcr.io/cloud-builders/docker'
    args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/quickstart-image', '.' ]
  - name: 'gcr.io/$PROJECT_ID/quickstart-image'
    args: ['fail']
  images:
  - 'gcr.io/$PROJECT_ID/quickstart-image
  ```

  * 두번째 name 부분은 이전 단계(첫번째 name)에서 빌드한 Docker 이미지를 실행하는 단계입니다.
  * 물론, ['fail'] 인수가 전달될 때 테스트 실패를 시뮬레이트하도록 quickstart.sh 스크립트가 수정되었습니다.

* `gcloud builds submit --config cloudbuild.yaml .` 명령어를 실행하여 `cloudbuild.yaml`을 빌드 구성 파일로 사용하여 **Cloud Build를 시작**합니다.

  ```bash
  Finished Step #1
  ERROR
  ERROR: build step 1 "gcr.io/ivil-charmer-227922klabs-gcp-49ab2930eea05/quickstart-image" failed: exit status 127
  ---------------------------------------------------------------------------------------------------------
  ERROR: (gcloud.builds.submit) build f3e94c28-fba4-4012-a419-48e90fca7491 completed with status "FAILURE"
  ```

  * 일부러 발생시킨 'fail' 에러를 볼 수 있습니다.

<br>

**아래는 에러의 모습입니다.**

![image-20230621222610775](/images/2023-06-20-(실습02)Working with Cloud Build/image-20230621222610775.png) 

<br>

**에러 History 확인을 해보면, 에러가 어느 액션에서 발생했는지도 찾을수 있습니다.**

![image-20230621223216249](/images/2023-06-20-(실습02)Working with Cloud Build/image-20230621223216249.png) 

<br><br>

## 마무리

아래는 마지막 Task 4 실습하면서 궁금점을 chatgpt 한테 물어본 내용입니다.

<br>

두 번째 액션에서 'fail'을 넣어 테스트 실패를 모방했지만, Cloud Storage의 Bucket이 생성된 것을 확인했다는 점이 이해하기 어려울 수 있습니다. 이에 대해 설명드리겠습니다.



구글 클라우드(Google Cloud)의 서비스는 각각 독립적으로 동작합니다. 즉, 하나의 액션이 실패하거나 문제가 발생하더라도 다른 액션이나 서비스는 정상적으로 동작할 수 있습니다. 따라서, 빌드 프로세스에서 두 번째 액션이 실패해도 Cloud Storage의 Bucket이 생성되는 것은 의도된 동작입니다.



'gcloud builds submit' 명령이나 ''빌드 구성 파일''을 사용하여 Google Cloud Build를 실행하면, 각 액션은 독립적인 단계로 실행됩니다. 첫 번째 액션은 Docker 이미지를 빌드하고 레지스트리에 업로드하며, 두 번째 액션은 해당 이미지를 실행하여 'quickstart.sh' 스크립트를 실행합니다.



첫 번째 액션에서는 Docker 이미지를 빌드하고 레지스트리에 업로드하지만, 빌드가 실패하거나 에러가 발생한다면 해당 이미지는 생성되지 않습니다. 따라서, 이미지는 확인할 수 없을 것입니다.



하지만 두 번째 액션은 이미지 실행 단계이기 때문에, 이미지의 존재 여부와는 관계없이 해당 단계의 스크립트가 실행됩니다. 그래서 Cloud Storage의 Bucket이 생성된 것을 확인할 수 있는 것입니다.



따라서, Cloud Storage의 Bucket은 빌드 프로세스 중간에 생성되기 때문에 첫 번째 액션의 이미지 생성 여부와는 독립적입니다.
