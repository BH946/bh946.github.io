---
title:  "[실습-초급01]Introduction to Docker"
categories : [Kubernetes_Bas]
tag : [구글 클라우드, GCP, GCC, 구글 클라우드 콘솔, 클라우드 쉘, 쿠버네티스, Kubernetes, 구글 클라우드 스터디 잼, 스터디 잼 초급]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
typora-root-url: ../../..
---



**첫번째 실습은** `도커의 기본 사용법` **을 배워 봅니다**

**이번** `전체 초급 과정` **에서는 도커 컨테이너에서의 작업, 해당 작업을 Google Kubernetes Engine에서 제공하는 쿠버네티스 클러스터로 배포하는 방법 및 증가한 트래픽에 대처할 수 있게 스케일링 하는 방법에 대해 배웁니다.   
또한, 업데이트에 따라 지속적으로 쿠버네티스 클러스터로 새로운 코드를 배포하는 방법에 대해서 배웁니다.**

**아래 5개의 Task를 해결하면서 도커 사용법을 간접적으로 경험해 봅시다.**

<br>

**Objectives(목표)**

* How to `build, run`, and `debug` Docker containers.
* How to `pull` Docker images from Docker Hub and Google Artifact Registry.
* How to `push` Docker images to Google Artifact Registry.

<br>

**용어 참고**

- GCP 환경에서 CI/CD를 구축하기 위해 도커 컨테이너 이미지가 저장될 저장소가 필요
- Docker Hub와 Google Artifact Registry는 도커 이미지를 관리하는 저장소!
- 만약 유료를 사용할 거라면 Google Artifact Registry 추천!
  - Google Cloud의 도구 및 런타임과 통합되어 있어서 CI/CD 도구(Google Cloud Build, Google Cloud Run 등)를 파이프라인 구축 가능!
  - **CI 자동화 예로 Github에 Trigger 발생 시 Google Cloud Build가 감지하여 Artifact Registry에 Docker Image를 빌드**
- **도커 컨테이너, 도커 이미지 용어 잘 구분**
- Google Cloud Platform (GCP) : 클라우드 서비스 관리 제공
- Compute Engine : VM 생성 및 관리 제공
- Google Kubernetes Engine (GKE) : Google Cloud가 제공하는 **Kubernetes 관리형 서비스**
- Kubernetes : 오픈 소스 **컨테이너 오케스트레이션 플랫폼**
- Cloud Functions & Cloud Run :  **Cloud Functions는 이벤트 기반 함수 실행을 위한 서버리스 컴퓨팅**이고, **Cloud Run은 컨테이너를 서버리스 방식으로 실행하는 것**
- Cloud Storage : 클라우드가 제공하는 저장소 (public, private 다 가능) - Buckets
- 등등..

<br>

<br>

## Task 1. Hello world

**"도커 허브"에서 제공하는 기본 도커 이미지를 실행해보겠습니다.**

<br><br>

### 1. Google Cloud Console -> Cloud Shell 접속

**Google Cloud Console 화면**

![image](https://github.com/user-attachments/assets/416e3c00-9c47-43dc-8137-9fbfb04e8811) 

<br>

**Cloud Shell에** `docekr run hello-world` **실행 화면**

* 1~4 가지 스텝을 읽어보면 "로컬"에 hello-world 도커 이미지를 찾지 못해서 "도커 허브" 에서 가져옴을 알 수 있습니다.

![image](https://github.com/BH946/bh946.github.io/assets/80165014/78d4648e-30de-4299-a3dd-9728c83ff3ef) 

<br><br>

### 2. Docker

`docker images` **명령어로 방금 가져온 도커 이미지 살펴보기**

* 알 수 있는점 : Docker daemon이 로컬에서 이미지를 찾을 수 없으면 기본적으로 공개 레지스트리에서 이미지를 검색
* `docekr run hello-world` 를 다시 실행하면?? 이번엔 로컬에서 바로 찾았다는 메시지가 출력!

![image](https://github.com/BH946/bh946.github.io/assets/80165014/be17ef39-2bbf-435c-860f-68966085a1af) 

<br>

`docker ps` **명령어로 도커 실행상태 살펴보기**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/22312ca7-0dd0-47a0-9582-389a6a414784) 

<br>

`docker ps -a` **로 실행 중 및 완료된 도커 살펴보기**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/20be387e-a2ac-46f2-8c85-c90dc73dd0a8) 

<br>

<br>

## Task 2. Build

**Node 앱을 도커로 빌드하는 실습입니다.**

**과정: 도커 파일 세팅 -> 빌드 명령(build) -> 실행 명령(run)**

<br><br>

### 1. 세팅

`mkdir test && cd test` **: test 폴더 생성(mkdir) 및 경로 이동(cd) 명령어**

Create a `Dockerfile` : **cat 명령어**로 도커파일 생성

```bash
cat > Dockerfile <<EOF
# Use an official Node runtime as the parent image -> 기본 상위 이미지 지정
FROM node:lts

# Set the working directory in the container to /app -> 작업환경 경로
WORKDIR /app

# Copy the current directory contents into the container at /app -> 컨테이너에 추가
ADD . /app

# Make the container's port 80 available to the outside world -> 외부 포트 80
EXPOSE 80

# Run app.js using node when the container launches -> node 명령어로 앱 실행
CMD ["node", "app.js"]
EOF
```

<br>

Create a `aap.js` : **cat 명령어**로 app.js 생성

```bash
cat > app.js <<EOF
const http = require('http');

const hostname = '0.0.0.0'; //localhost
const port = 80;

const server = http.createServer((req, res) => {
    res.statusCode = 200;
    res.setHeader('Content-Type', 'text/plain');
    res.end('Hello World\n');
});

server.listen(port, hostname, () => {
    console.log('Server running at http://%s:%s/', hostname, port);
});

process.on('SIGINT', function() {
    console.log('Caught interrupt signal and will exit');
    process.exit();
});
EOF
```

<br>

**파일생성 확인**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/a33fc676-39ab-4427-93af-602879464f9d) 

<br><br>

### 2. 도커 빌드

`docker build -t node-app:0.1 .` **: "."은 현재 디렉토리를 의미하므로 도커파일 있는곳에서 명령어 실행!**

* `-t` 는 `name:tag` 구문을 사용하여 이미지의 이름을 지정하고 태그를 지정하는 것입니다. 
  * 이미지 이름은 node-app이고 태그는 0.1입니다. 
  * Docker 이미지를 빌드할 때 태그를 적극 권장하며 태그를 지정하지 않으면 태그가 기본적으로 최신으로 설정되어 최신 이미지와 이전 이미지를 구별하기가 더 어려워집니다. 

<br>

`docker images` **: 실행확인**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/57244dec-efe4-43d7-9e33-fcfd87cf573f) 

<br>

<br>

## Task 3. Run

**앞에서 Build를 성공적으로 마쳤으면 Run을 해보겠습니다.**

* 실행은 `docker ps` or `docker ps -a` 로 확인

<br><br>

### 1. 실행

`docker run -p 4000:80 --name my-app node-app:0.1` **: 실행(docker run)**

* `-p` 로 4000포트 출입을 80포트에 매핑, `--name` 으로 원하는 컨테이너 이름지정 가능

![image](https://github.com/BH946/bh946.github.io/assets/80165014/cd9c727c-0415-4268-ae1b-1135210c1410) 

<br>

`curl http://localhost:4000` **: 테스트**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/9446dc92-10d7-4edf-bf96-75828cbb2213) 

<br><br>

### 2. 중지 -> 백그라운드 실행

`docker stop my-app && docker rm my-app` **: 중지!! 및 실행 컨테이너(my-app) 삭제!!**

`docker run -p 4000:80 --name my-app -d node-app:0.1` **: 백그라운드 실행!!**

`docker ps` **: 실행 확인!**

* `docker logs 컨테이너ID` 명령어도 가능

![image](https://github.com/BH946/bh946.github.io/assets/80165014/56ea810a-4f14-4d23-9192-7a1943b62abb) 

<br><br>

### 3. 두번째 도커 생성 -> 백그라운드 실행

`app.js` **의 반환 문자열을 "Welcome to Cloud" 로 바꿉니다.**

* 필자는 vim 에디터 사용하였고, esc로 일반모드 진입후 ":" 입력하여 "w" 엔터로 저장
* 이후 ":q!" 로 간단히 탈출

![image](https://github.com/BH946/bh946.github.io/assets/80165014/937cc523-aa1c-4c51-a167-a50f199eeeb5) 

<br>

**아래 순서로 테스트**

* `docker build -t node-app:0.2 .` : build

* `docker run -p 8080:80 --name my-app-2 -d node-app:0.2`

* `docker ps` : 실행 확인

* `curl http://localhost:8080`

*  `curl http://localhost:4000`

<br>

<br>

## Task 4. Debug

**디버그!**

<br><br>

### 1. Open Code Editor

`docker logs -f [container_id]` **: 로그 확인**

`docker exec -it [container_id] bash` **: bash 사용이가능! 도커파일에 지정한 작업경로로 설정**

* ls 로 보면 도커파일과 app.js 가 존재
* exit 로 탈출

`docker inspect [container_id]` **: 메타데이터 확인 가능**

* 사용 예시 : `docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [container_id]`
* 특정 IP 주소 출력

- [Docker inspect reference](https://docs.docker.com/engine/reference/commandline/inspect/#examples)
- [Docker exec reference](https://docs.docker.com/engine/reference/commandline/exec/)

<br>

<br>

##  Task 5. Publish

**생성한 이미지를 [Google Artifact Registry](https://cloud.google.com/artifact-registry) 로 push 후 이곳에서 pull 하여 실행해보려고 합니다.**

<br><br>

### 1. 도커 저장소 만들기

1. **탐색 메뉴**의 CI/CD에서 **Artifact Registry** > **저장소**로 이동합니다.
2. **저장소 만들기**를 클릭합니다.
3. 저장소 이름으로 `my-repository`를 지정합니다.
4. 형식으로 **Docker**를 선택합니다.
5. 위치 유형에서 **리전**을 선택한 후 `us-west1` 위치를 선택합니다.
6. **만들기**를 클릭합니다.

<br>

`gcloud auth configure-docker us-west1-docker.pkg.dev` **: 로 인증!! (저장소 사용 위해)**

`export PROJECT_ID=$(gcloud config get-value project)` **: 프로젝트ID 등록!!**

* `cd ~/test` 경로 이동 (빌드하기 위해)

`docker build -t us-west1-docker.pkg.dev/$PROJECT_ID/my-repository/node-app:0.2 .`

* `docker images` 로 빌드 성공 확인

`docker push us-west1-docker.pkg.dev/$PROJECT_ID/my-repository/node-app:0.2`

* Artifact Registry로 push!!
* 콘솔에 **my-repository** 들어가면 push된 도커 이미지가 존재!

<br><br>

### 2. 테스트 이미지

**모든 도커 컨테이너 중지 및 제거**

* `docker stop $(docker ps -q)`

* `docker rm $(docker ps -aq)`
* `docker ps -a` : 실행 or 실행완료 도커 컨테이너 확인 -> 아무것도 없어야 정상

<br>

**모든 도커 이미지 삭제**

* `docker rmi us-west1-docker.pkg.dev/$PROJECT_ID/my-repository/node-app:0.2`
* `docker rmi node:lts` : 노드 하위녀석들을 먼저 삭제 후 이 노드를 삭제
* `docker rmi -f $(docker images -aq)` : 남은 이미지들 전부 삭제
* `docker images` : 도커 이미지 확인 -> 아무것도 없어야 정상

<br>

**아까 Artifact Registry로 push한 도커이미지 pull 및 실행!!**

* `docker pull us-west1-docker.pkg.dev/$PROJECT_ID/my-repository/node-app:0.2`
* `docker run -p 4000:80 -d us-west1-docker.pkg.dev/$PROJECT_ID/my-repository/node-app:0.2`
* `curl http://localhost:4000` : "Welcome to Cloud" 출력 시 성공!!

<br>

<br>

## 마무리

**도커를 한번도 사용해 보지 않았었는데, GCP에서 도커를 실습해 볼 수 있었습니다.**

**다음부터는 Docker Desktop 을 설치해서 자주 사용해보려고 합니다.**
