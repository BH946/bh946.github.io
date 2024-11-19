---
title:  "[실습-초급05]Continuous Delivery(CD) with Jenkins in Kubernetes Engine (2024ver)"
categories : [Kubernetes_Bas]
tag : [구글 클라우드, GCP, GCC, 구글 클라우드 콘솔, 클라우드 쉘, 쿠버네티스, Kubernetes, 구글 클라우드 스터디 잼, 스터디 잼 초급]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
typora-root-url: ../../..
---

**다섯 번째 실습은** `Jenkins(오픈소스)를 통한 지속적 배포(CD) 파이프라인 설정과 코드의 지속적인 통합(CI)` **을 배워 봅니다.**

**핵심 : Kubernetes+Jenkins 조합을 활용할 시 CI/CD와 컨테이너 환경 구축이 편리**

**아래 13개의 Task를 해결하면서 간접적으로 경험해 봅시다.**

**+) yaml 분석을 통해 어떤 흐름인지 따로 정리합니다.**

<br>

**Objectives(목표)**

* Provision a Jenkins application into a Kubernetes Engine Cluster
* Set up your Jenkins application using Helm Package Manager
* Explore the features of a Jenkins application
* Create and exercise a Jenkins pipeline

<br>

<br>

## Task 1. Download the source code

`compute zone` **을 먼저 세팅합니다.**

**그리고** `gstuil` **명령어를 활용해서 예제 코드들을 다운로드합니다!**

<br><br>

### 1. Google Cloud Console -> Cloud Shell 접속

**Google Cloud Console 화면**

![image](https://github.com/user-attachments/assets/416e3c00-9c47-43dc-8137-9fbfb04e8811) 

<br>

**Cloud Shell 실행 화면(세팅)**

* `gcloud config set compute/zone Zone`
* **예제 코드들다운(클론) -> Jenkin의 values.yaml 을 위해서라도 꼭 설치**
  * `gsutil cp gs://spls/gsp051/continuous-deployment-on-kubernetes.zip .`
  * `unzip continuous-deployment-on-kubernetes.zip`
  * `cd continuous-deployment-on-kubernetes`

<br>

<br>

## Task 2. Provisioning Jenkins

### 1. 클러스터 생성

**먼저, 쿠버네티스 클러스터 생성합니다. (5분 정도 소요)**

* `gcloud container clusters create jenkins-cd \
  --num-nodes 2 \
  --machine-type e2-standard-2 \
  --scopes "https://www.googleapis.com/auth/source.read_write,cloud-platform"`
* 이미 클러스터가 있다면 굳이 새로 설치할 필요는 없고 기존꺼 사용해도 좋다.

<br><br>

### 2. 클러스터 인증 및 연결

**클러스터 연결 확인합니다.**

* `gcloud container clusters list` : 클러스터 실행중인가 확인
* `gcloud container clusters get-credentials jenkins-cd` **: 클러스터 인증정보를 "jenkins-cd"로 가져오기**
* `kubectl cluster-info` : 클러스터 연결 가능여부 확인

<br>

<br>

## Task 3. Setup Helm

**Helm(패키지 관리자)을 사용해서 차트저장소에 Jenkins를 설치합니다.**

* 참고) Helm은 **pip, apt 같은 것!!**
* `helm repo add jenkins https://charts.jenkins.io`
  * Jenkins 차트가 저장되어 있는 저장소를 Helm에 추가하여 나중에 해당 차트를 검색하고 설치할 수 있게 한다.
  * `helm search repo jenkins` : Helm에 추가된 Jenkins 차트 저장소 설치 확인!

* `helm repo update` : 저장소 최신 상태인지 확인!

<br>

<br>

## Task 4. Configure and Install Jenkins

Jenkins 설치할 때 `values.yaml` 을 통해서 연동에 필요한 플러그인들을 자동으로 설치할 수 있습니다.

* `values.yaml` 에 다양한 플러그인들이 추가되어 있어서 굉장히 편리!

<br><br>

### 1. Install Jenkins

 **Helm CLI를 이용해서 설치합니다.**

* `helm install cd jenkins/jenkins -f jenkins/values.yaml --wait` : 설치
  * Jenkins 설치할 때 `values.yaml` 을 통해서 연동에 필요한 플러그인들을 자동으로 설치
  * `helm status cd` : cd라는 이름으로 설정했으므로 cd 상태 체크로 설치 상태 확인 가능

* `kubectl get pods` : 설치 확인
* `kubectl create clusterrolebinding jenkins-deploy --clusterrole=cluster-admin --serviceaccount=default:cd-jenkins`
  * Jenkins 서비스 계정 설정

![image](https://github.com/BH946/bh946.github.io/assets/80165014/211f1e16-59e5-4d61-84d1-7a334e02420f) 

<br><br>

### 2. Jenkins 서비스 확인

**Cloud Shell에서 Jenkins로의 포트전달 설정 후 서비스 생성을 확인합니다.**

* `export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")`

  * POD_NAME에 결국 cd-jenkins-0 라는 문자열이 저장 (pod 이름)

* `kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &`

  * 로컬(웹 미리보기) 8080 포트 접근 시 -> pod 8080 포트로 연결 (=포트포워딩)

  * `>> /dev/null` 이거는 로그 출력 무시, `&`는 백그라운드 실행

  * 만약 포트포워딩 안하면?? 아래 에러 뜸

    <img src="https://github.com/user-attachments/assets/b67b5c54-f91f-4ac5-ac17-d93b2b598cd0" alt="image" style="zoom:80%;" /> 

* `kubectl get svc` : service랑 svc는 같다.

![image](https://github.com/BH946/bh946.github.io/assets/80165014/4e7d97cd-7ec1-424a-a215-a29f7ff3b6a2) 

<br>

<br>

##  Task 5. Connect to Jenkins

**쿠버네티스 클러스터에 Jenkins를 설치했으니 연결까지 해봅니다.**

<br>

**Jenkins는 처음 생성할 때 자동으로 관리자 비밀번호를 설정합니다.**

* `printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo` : 비밀번호 확인 명령어
* 따라서 관리자 ID : admin
* 비밀번호 : 위에서 확인한 번호

<br>

**따로 외부IP는 없으니까(ClusterIP는 클러스터 내에서만 접근 가능한 기본 서비스 유형) 위 정보를 사용해서 Cloud Shell의 웹 미리보기 포트 8080 클릭해서 접속합니다.**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/4de1d24a-3fd7-4557-b329-921c359ce245) 

<br>

<br>

## Task 6. Understanding the Application

**자동화된 CI/CD 파이프라인을 만들기 위해 사용될 애플리케이션을 이해해보자.**

**gceme란 애플리케이션이며 저장소의 sample-app 디렉터리에 존재합니다.**

**인스턴스의 메타데이터를 보여주는 일을 진행합니다.**

* **백엔드 모드와 프론트엔드 모드가 존재합니다.**

<br>

**아래그림은 실행 예시와 아키텍처입니다.**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/a6de0828-3fe0-4017-abaf-fecd97b09539) 

<br>

![image](https://github.com/BH946/bh946.github.io/assets/80165014/755801c9-b35e-4daa-8f71-e6f51b1f3f5c) 

<br>

<br>

## Task 7. Deploying the Application

**Jenkins 실행은 끝났으니.. 앱을 이제 배포해보겠습니다.**

**앱을 프로덕션(사용자가 엑세스하는 라이브사이트), 카나리아(사용자 일부 수용 사이트. 이전에 배운 업데이트 방식!) 로써 2개의 환경으로 배포해 봅시다.**

* `cd sample-app kubectl create ns production` : 네임스페이스 생성 -> Kubernetes 클러스터 내에서 리소스를 논리적으로 분리하기 위한 방법 (예로 개발, 테스트, 운영 으로 분리 하던지)
  * `ls sample-app/k8s/production` 로 확인해 보면 backend, frontend 관련 yaml이 있다.

* kubectl apply -f k8s/production -n production   
  kubectl apply -f k8s/canary -n production   
  kubectl apply -f k8s/services -n production
  * 외부 접속을 위해 서비스는 항상 필수!

![image](https://github.com/BH946/bh946.github.io/assets/80165014/1537be83-d011-4515-821a-c6909840eb3c) 

<br>

**복제본이 1개라 replicas를 수정해서 프론트엔드는 최소 4개의 복제본이 항상 실행되도록 합니다.**

* `kubectl scale deployment gceme-frontend-production -n production --replicas 4`
* `kubectl get pods -n production -l app=gceme -l role=frontend`
* `kubectl get pods -n production -l app=gceme -l role=backend`
  * 프론트엔드와 백엔드 포드 확인합니다.
  * 프론트엔드 프로덕션 4개, 카나리아 1개 / 백엔드 프로덕션 1개, 카나리아 1개 -> 성공!

![image](https://github.com/BH946/bh946.github.io/assets/80165014/2904048e-5897-495b-802c-69cdfe4dd89d) 

<br>

**프로덕션 서비스의 외부IP 테스트를 합니다.**

* `kubectl get service gceme-frontend -n production` : 외부IP 확인
* 터미널에서 확인
  * `export FRONTEND_SERVICE_IP=$(kubectl get -o jsonpath="{.status.loadBalancer.ingress[0].ip}" --namespace=production services gceme-frontend)`
  * `curl http://$FRONTEND_SERVICE_IP/version`

* 브라우저에서 확인

![image](https://github.com/BH946/bh946.github.io/assets/80165014/2aa80530-93ba-48ef-bb1e-24aab6c34beb) 

<br>

![image](https://github.com/BH946/bh946.github.io/assets/80165014/b095384d-515f-4a5d-840d-7e8fe7861199) 

<br>

<br>

## Task 8. Creating the Jenkins Pipeline

**Jenkins 파이프라인을 만들어 보겠습니다.**

<br><br>

### 1. 소스코드 호스팅위해 저장소 생성

**sample-app 의 소스코드를 호스팅하는 저장소를 만듭니다.**

* **gceme 샘플 앱의 복사본을 만들고 이를 Cloud Source Repository로 푸시**

  * **(중요)2024년 기준 Cloud Source Repository를 이제 제공 안한다ㅜ**

  * **아래 gcloud 명령어 대신 직접 Git 에 레포지토리 직접 만들고 아래처럼 연결하자!**

  * ```bash
    git init
    git config --global user.email "[EMAIL_ADDRESS]"
    git config --global user.name "[USERNAME]"
    git add .
    git commit -m "Initial commit"
    git remote add origin https://github.com/BH946/my-app.git # 본인 레포(HTTP)
    git branch -M main
    git push -u origin main
    ```

* `gcloud source repos create default` : GCP의 Cloud Source Repository 생성(default)

* `git init`

* `git config credential.helper gcloud.sh` : 인증

* `git remote add origin https://source.developers.google.com/p/$DEVSHELL_PROJECT_ID/r/default` : 생성한 저장소를(default) Git 프로젝트에 추가

* `git config --global user.email "[EMAIL_ADDRESS]" `

* `git config --global user.name "[USERNAME]"`

* `git add .`

* `git commit -m "Initial commit" `

* `git push origin master`

<br><br>

### 2. 서비스 계정 사용자 인증

**Jenkins에서 코드 저장소에 액세스할 수 있도록 허용이 필요합니다.**

* 인증정보 만들기 : `Jenkins 관리 > Manage Credentials > System > Global credentials > Add Credentials`
  * Kind는 Google Service Account from metadata 선택 후 Create
* **(중요)이것도 지금 Cloud Source Repository 지원 중단 문제로 Github로 하셔야 합니다.**
  * Jenkins에서 GitHub 저장소에 접근하기 위해서는 GitHub 플러그인을 설치<br>`Manage Jenkins -> Manage Plugins -> Available 탭에서 "GitHub"를 검색하여 설치`
  * 서비스 계정 사용자 인증<br> `Jenkins 관리 > Manage Credentials > System > Global credentials > Add Credentials`
    * `Kind는 Username with password 선택 -> Username: GitHub 사용자 이름 -> Password: Personal Access Token (PAT) 사용 -> Create`

<br><br>

### 3. Kubernetes용 Jenkins Cloud 구성

구성방법 : `Jenkins 관리 > Manage Cloud > Clouds Add a new cloud > Kubernetes > Kubernetes Cloud Details`

* Jenkins URL : `http://cd-jenkins:8080`
* Jenkins tunnel : `cd-jenkins-agent:50000`

<br><br>

### 4. Jenkins 작업 생성

**테스트, 배포, 환경설정, 소스 코드 체크아웃 등등을 진행하는 "작업"을 생성합니다.**

1. Dashboard(대시보드) > New Item(새 항목) 클릭
2. 프로젝트 이름은 sample-app으로 지정, Multibranch Pipeline(다중 브랜치 파이프라인) 옵션 선택 후 OK 클릭
3. Branch Sources(브랜치 소스) 섹션에 있는 Add Source(소스 추가) 드롭다운에서 Git 선택
4. Cloud Source Repositories에 있는 sample-app 저장소에 다음 HTTPS clone URL(HTTPS 클론 URL)을 Project Repository(프로젝트 저장소) 필드에 붙여 넣기, [PROJECT_ID]를 현재 프로젝트 ID로 변경 `https://source.developers.google.com/p/[PROJECT_ID]/r/default`
   - **(중요)이것도 본인 깃허브 레포지토리 주소(HTTP)를 입력해야 합니다!**<br>`https://github.com/BH946/my-app.git`
5. 이전 단계에서 서비스 계정을 추가할 때 만든 사용자 인증 정보의 이름을 사용자 인증 정보드롭다운에서 선택
6. Scan Multibranch Pipeline Triggers(다중 브랜치 파이프라인 트리거 검색)섹션에서 Periodically if not otherwise run(별도로 실행하지 않는 경우 주기적으로 실행)상자를 선택하고 Interval(간격) 값을 1분으로 설정
   - **1분마다 실행되므로 git push 하면 약 1분뒤 시작한다고 생각하면 됩니다.**
7. `Branch Indexing`이라는 작업이 자동으로 실행되므로 `Finished: SUCCESS` 메시지 뜨면 성공!

![image](https://github.com/BH946/bh946.github.io/assets/80165014/07d134af-621f-449c-a634-6007ea54835b) 

<br>

<br>

## Task 9. Creating the development environment

**개발 브랜치를 만들고, 파이프라인 정의(Jenkinsfile)를 수정 후 테스트 하겠습니다.**

* `git checkout -b new-feature`

* `nano Jenkinsfile`

  ```groovy
  PROJECT = "REPLACE_WITH_YOUR_PROJECT_ID" # 실습에서 할당받은 프로젝트 ID로 수정
  APP_NAME = "gceme"
  FE_SVC_NAME = "${APP_NAME}-frontend"
  CLUSTER = "jenkins-cd"
  CLUSTER_ZONE = "" # 실습에서 할당받은 컴퓨팅 영역으로 수정, gcloud config get compute/zone로 확인 가능
  IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"
  JENKINS_CRED = "${PROJECT}"
  ```

* **(중요) Jenkinsfile 소스에서 "인증" 부분의 수정이 필요합니다.**

  * 기존에는 Jenkins 인증에서 Kind를 Google Service Account from metadata 사용한 덕분에 GCP쪽 인증을 Google Cloud Build 가 자동으로 진행했습니다. 

  * 그러나, 현재(2024ver)는 Kind를 Username with password 사용하여 인증을 하고 있습니다. Github는 여기서(Jenkins) 바로 인증이 되므로 문제없지만, GCP쪽 인증의 오류를 직면하게 됩니다.

  * **해결방안 : Jenkins 에 추가 인증을 등록(Add Credentials)하자! -> GCP용**

    1. Compute Engine 사용 계정을 먼저 확인하여 해당 계정을 Jenkins에 추가하는 겁니다.

    2. 계정마다 키를 생성할 수 있으므로 키를 생성하여 JSON을 자동 다운 받습니다.

    3. 여기서 중요한 점은 Kind를 반드시 Secret file로 지정하여 해당 JSON을 등록하면 됩니다.

       - 이때 생성된 Credentials의 ID를 기억해야 합니다. ex: 99e295f...

       <img src="https://github.com/user-attachments/assets/77dd5875-5af5-4e37-9f55-82f822a46f88" alt="image" style="zoom:80%;" /> 

    4. `withCredentials` 문법을 사용해서 Jenkinsfile에 추가하면 됩니다. **(코드에 "자격 증명" 부분 중요)**

      추가정보: `gcloud builds submit -t ${IMAGE_TAG} .` 이 명령어는 **Google Cloud Build**를 사용하여 Docker 이미지를 빌드하고 GCP의 **Artifact Registry**나 **Google Container Registry**에 저장하는 역할을 합니다. 즉, 실제로 Jenkins 자체에서 Docker 빌드를 수행하는 것이 아니라 **Cloud Build 서비스**에 해당 작업을 위임하고 있습니다.

      ```groovy
      pipeline {
          environment {
      		...
      		// Jenkins에 등록한 GCP 자격 증명 ID
              GCP_CRED = "99e295f9-753c-459d-9b77-1c814a4f83c3" 
          }
          	...
              stage('Build and Push Docker Image') {
                  steps {
                      container('gcloud') {
                      // 자격 증명 문법(json file) : withCredentials([file...])
                          withCredentials([file(credentialsId: env.GCP_CRED, variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                              sh """
                                  # JSON 확인 위한 LOG
                                  echo "Using credentials from: \$GOOGLE_APPLICATION_CREDENTIALS"
                                  cat \$GOOGLE_APPLICATION_CREDENTIALS
                                  gcloud config set project ${PROJECT}
                                  # 자격 증명
                                  gcloud auth activate-service-account --key-file=\$GOOGLE_APPLICATION_CREDENTIALS
                                  PYTHONUNBUFFERED=1 gcloud builds submit -t ${IMAGE_TAG} .
                              """
                          }
                      }
                  }
              }
          ... 생략
      ```


<br>

**테스트를 위해 사이트를 수정합니다.**

* `html.go` 파일에 `<div class="card orange">` 로 수정 (파랑->주황)
* `main.go` 파일에 `const version string = "2.0.0"` 로 수정 (1.0.0->2.0.0)

<br>

<br>

## Task 10. Kick off Deployment

수정한 파일들을 푸시해서 수정된 배포를 시작합니다.

* git add Jenkinsfile html.go main.go   
  git commit -m "Version 2.0.0"   
  git push origin new-feature
  * **깃 푸시만으로 자동 배포**가 진행됨을 알 수 있습니다!
  * 시간이 걸리므로 Jenkins에서 해당 브랜치 들어가서 Console Output을 확인합니다.

![image](https://github.com/BH946/bh946.github.io/assets/80165014/e1758eda-a52d-4ea5-ad34-fd3b4eee0c40) 

<br>

**테스트해봅니다.**

* `kubectl proxy &  `
* `curl \ http://localhost:8001/api/v1/namespaces/new-feature/services/gceme-frontend:80/proxy/version`

![image](https://github.com/BH946/bh946.github.io/assets/80165014/3fe28871-7178-47a5-adbf-1995dd39039d) 

<br>

<br>

## Task 11. Deploying a canary release

이번엔 개발 브랜치 코드를 **카나리아 환경에 배포**하겠습니다.

* git checkout -b canary   
  git push origin canary
  * 바로 개발 코드를 푸시합니다.
  * **Jenkins에서 카나리아 파이프라인이 자동으로 실행**합니다. (개발에서 한 것처럼 자동 배포)

<br>

**카나리아 정상 동작하는지 테스트합니다.**

* `export FRONTEND_SERVICE_IP=$(kubectl get -o \ jsonpath="{.status.loadBalancer.ingress[0].ip}" --namespace=production services gceme-frontend)`
* `curl http://$FRONTEND_SERVICE_IP/version` 를 연속으로 확인해서 약 5개중 1개 비율로 2.0.0이 반환되는지 확인합니다.

![image](https://github.com/BH946/bh946.github.io/assets/80165014/bb30c44b-a5bf-442f-9dca-6c784358e1ad) 

<br>

<br>

## Task 12. Deploying to production

**카나리아에서 테스트를 진행했고, 사용자들은 버그를 못느끼고 정상 동작한다고 결론이 나면 "프로덕션(배포)" 환경에 배포하면 끝입니다.**

* 프로덕션 환경은 실제로 사용자들에게 배포되어 사용하고있는 환경입니다.
* **참고로** `개발환경(실제로 개발한것처럼 코드 약간 수정) -> 카나리아(업뎃전에 일부사용자에게 테스트!!) -> 프로덕션` **순서로 최종 배포를 진행하고 있습니다!**

<br>

**master 브랜치로 이동해서 canary 브랜치를 통합합니다.**

* git checkout master   
  git merge canary   
  git push origin master
  * 푸시하는순간 앞에서처럼 Jenkins가 자동 배포!

<br>

**정상 동작을 테스트합니다.**

* `export FRONTEND_SERVICE_IP=$(kubectl get -o \ jsonpath="{.status.loadBalancer.ingress[0].ip}" --namespace=production services gceme-frontend) `
* `curl http://$FRONTEND_SERVICE_IP/version ` : 여러번 출력해봅니다.
  * **전부 2.0.0 만 출력된다면 성공**입니다!
* `kubectl get service gceme-frontend -n production`
  * 추가로 아까 수정한 "파랑->주황" 을 확인해봅니다!

![image](https://github.com/BH946/bh946.github.io/assets/80165014/385ea455-365e-47c3-bba9-88be0f78170a) 

<br>

<br>

## Task 13. Test your Understanding

**간단한 문제 2개 있습니다.**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/a3df5524-5dd2-46ac-9473-4a89aec738e7) 

<br>

![image](https://github.com/BH946/bh946.github.io/assets/80165014/04c78abf-3843-44a3-922f-e563e33ff1a7) 

<br>

<br>

## 마무리

**참고) 너무 잘 정리한 글이 있어서 이글의 내용도 함께 참고했다!**

* **[CN_City님](https://cn-c.tistory.com/48?category=1105946#%EC%-E%--%EC%--%--%----%--%EC%--%-C%EC%B-%A-%EC%A-%--%--%EC%--%--%EB%-D%B-%EC%-D%B-%ED%-A%B--Rolling%--update-)**

<br>

**이전시간에 배운 3가지 업데이트 방법 중에서 Canary 방식을 사용!**

**Jenkins로 자동 배포를 설정하여 코드 수정 후 깃 푸시하면 적용된다.**

**참고로 테스트과정은** `개발환경(실제로 개발한것처럼 코드 약간 수정) -> 카나리아(업뎃전에 일부사용자에게 테스트!!) -> 프로덕션` **순서로 자동배포 테스트를 진행했다.**

<br>

<br>

## 번외) yaml 실행 흐름 (Dockerfile 포함)

**GKE에서 크게 2종류 객체가 Deployment, Service**

- Pod단의 frontend, backend, canary 전부 kind:Deployment 이다.
- 서비스단의 frontend, backend 는 kind:Service 이다.

<br><br>

### Dockerfile, Jenkinsfile

설치한 **gsutil cp gs://spls/gsp051/continuous-deployment-on-kubernetes.zip .** 를 보면 젤 상위에 Dockerfile과 main.go 등 소스 코드들이 존재한다.

**Dockerfile**

```dockerfile
FROM golang:1.10
WORKDIR /go/src/app
COPY . . # 메인 소스(프로덕션)들을 도커 이미지로 복사
RUN go install -v # go 언어에서 패키지 컴파일하고 설치 동작
```

<br>

**Jenkinsfile**

```groovy
pipeline {

  environment {
    PROJECT = "REPLACE_WITH_YOUR_PROJECT_ID"
    APP_NAME = "gceme"
    FE_SVC_NAME = "${APP_NAME}-frontend"
    CLUSTER = "jenkins-cd"
    CLUSTER_ZONE = "us-east1-d"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"
    JENKINS_CRED = "${PROJECT}"
  }

  agent {
    kubernetes {
      label 'sample-app'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  serviceAccountName: cd-jenkins
  containers:
  - name: golang
    image: golang:1.10
    command:
    - cat
    tty: true
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
    stage('Test') {
      steps {
        container('golang') {
          sh """
            ln -s `pwd` /go/src/sample-app
            cd /go/src/sample-app
            go test
          """
        }
      }
    }
    stage('Build and push image with Container Builder') {
      steps {
        container('gcloud') {
          sh "PYTHONUNBUFFERED=1 gcloud builds submit -t ${IMAGE_TAG} ."
        }
      }
    }
    stage('Deploy Canary') {
      // Canary branch
      when { branch 'canary' }
      steps {
        container('kubectl') {
          // Change deployed image in canary to the one we just built
          sh("sed -i.bak 's#corelab/gceme:1.0.0#${IMAGE_TAG}#' ./k8s/canary/*.yaml")
          step([$class: 'KubernetesEngineBuilder', namespace:'production', projectId: env.PROJECT, clusterName: env.CLUSTER, zone: env.CLUSTER_ZONE, manifestPattern: 'k8s/services', credentialsId: env.JENKINS_CRED, verifyDeployments: false])
          step([$class: 'KubernetesEngineBuilder', namespace:'production', projectId: env.PROJECT, clusterName: env.CLUSTER, zone: env.CLUSTER_ZONE, manifestPattern: 'k8s/canary', credentialsId: env.JENKINS_CRED, verifyDeployments: true])
          sh("echo http://`kubectl --namespace=production get service/${FE_SVC_NAME} -o jsonpath='{.status.loadBalancer.ingress[0].ip}'` > ${FE_SVC_NAME}")
        }
      }
    }
    stage('Deploy Production') {
      // Production branch
      when { branch 'master' }
      steps{
        container('kubectl') {
        // Change deployed image in canary to the one we just built
          sh("sed -i.bak 's#corelab/gceme:1.0.0#${IMAGE_TAG}#' ./k8s/production/*.yaml")
          step([$class: 'KubernetesEngineBuilder', namespace:'production', projectId: env.PROJECT, clusterName: env.CLUSTER, zone: env.CLUSTER_ZONE, manifestPattern: 'k8s/services', credentialsId: env.JENKINS_CRED, verifyDeployments: false])
          step([$class: 'KubernetesEngineBuilder', namespace:'production', projectId: env.PROJECT, clusterName: env.CLUSTER, zone: env.CLUSTER_ZONE, manifestPattern: 'k8s/production', credentialsId: env.JENKINS_CRED, verifyDeployments: true])
          sh("echo http://`kubectl --namespace=production get service/${FE_SVC_NAME} -o jsonpath='{.status.loadBalancer.ingress[0].ip}'` > ${FE_SVC_NAME}")
        }
      }
    }
    stage('Deploy Dev') {
      // Developer Branches
      when {
        not { branch 'master' }
        not { branch 'canary' }
      }
      steps {
        container('kubectl') {
          // Create namespace if it doesn't exist
          sh("kubectl get ns ${env.BRANCH_NAME} || kubectl create ns ${env.BRANCH_NAME}")
          // Don't use public load balancing for development branches
          sh("sed -i.bak 's#LoadBalancer#ClusterIP#' ./k8s/services/frontend.yaml")
          sh("sed -i.bak 's#corelab/gceme:1.0.0#${IMAGE_TAG}#' ./k8s/dev/*.yaml")
          step([$class: 'KubernetesEngineBuilder', namespace: "${env.BRANCH_NAME}", projectId: env.PROJECT, clusterName: env.CLUSTER, zone: env.CLUSTER_ZONE, manifestPattern: 'k8s/services', credentialsId: env.JENKINS_CRED, verifyDeployments: false])
          step([$class: 'KubernetesEngineBuilder', namespace: "${env.BRANCH_NAME}", projectId: env.PROJECT, clusterName: env.CLUSTER, zone: env.CLUSTER_ZONE, manifestPattern: 'k8s/dev', credentialsId: env.JENKINS_CRED, verifyDeployments: true])
          echo 'To access your environment run `kubectl proxy`'
          echo "Then access your service via http://localhost:8001/api/v1/proxy/namespaces/${env.BRANCH_NAME}/services/${FE_SVC_NAME}:80/"
        }
      }
    }
  }
}
```

<details><summary><b>Jenkinsfile 문법 참고</b></summary>
<div markdown="1">
- env.BRANCH_NAME과 env.BUILD_NUMBER를 사용하여 Docker 이미지 태그를 동적으로 생성
  - 만약 깃허브라면,
  - env.BRANCH_NAME: 현재 빌드가 실행 중인 Git 브랜치의 이름을 나타냅니다.
  - env.BUILD_NUMBER: 현재 빌드의 번호를 나타냅니다. 각 빌드는 고유한 번호를 가집니다.
- 배포 단계별 주요 특징
    - `when` 조건: 브랜치에 따라 다른 배포 수행 (다양한 배포 환경!)
    - `container()`: 특정 컨테이너에서 명령 실행
    - `sed` 명령: yaml 파일의 이미지 경로 동적 대체
    - `kubectl` 명령: Kubernetes 클러스터에 리소스 배포
- **`environment` 블록**: 전역 환경 변수를 설정합니다.
  - **`PROJECT`**: GCP 프로젝트 ID.
  - **`APP_NAME`**: 애플리케이션 이름.
  - **`FE_SVC_NAME`**: 프론트엔드 서비스 이름. `${APP_NAME}-frontend` 형태로 이름을 지정.
  - **`CLUSTER`**: 배포할 GKE 클러스터 이름.
  - **`CLUSTER_ZONE`**: 클러스터가 위치한 GCP 존.
  - **`IMAGE_TAG`**: 빌드된 Docker 이미지의 태그. 여기서는 GCP의 Container Registry에 저장될 경로와 브랜치 이름, 빌드 번호를 사용하여 유일한 이미지 태그를 생성합니다.
  - **`JENKINS_CRED`**: Jenkins에서 사용하는 자격 증명 ID를 저장합니다.
- **`agent` 블록**: 이 파이프라인이 실행될 **Jenkins 에이전트**를 정의합니다. 여기서는 **쿠버네티스 파드**를 사용하여 에이전트를 생성합니다.
  - **`kubernetes`**: Jenkins가 쿠버네티스 클러스터에서 실행될 **에이전트 파드**를 생성합니다.
  - **`label`**: 이 에이전트를 식별하는 **레이블 이름**입니다. 해당 이름으로 파드를 찾습니다.
  - **`defaultContainer 'jnlp'`**: **기본 컨테이너**를 설정합니다. Jenkins에서 사용할 기본 작업 컨테이너로서 `jnlp` 컨테이너가 설정됩니다.
  - `yaml`: 생성할 파드의 정의를 YAML 형식으로 직접 작성합니다.
    - **`serviceAccountName`**: **`cd-jenkins`** 서비스 계정을 사용하여 쿠버네티스 리소스를 배포할 수 있게 합니다.
    - `containers`: 파드에 포함된 컨테이너 목록을 정의합니다.
      - **`golang` 컨테이너**: **Golang 이미지**(`golang:1.10`)를 사용하는 컨테이너입니다. `cat` 명령을 실행하여 컨테이너가 계속 실행 상태에 있도록 합니다.
      - **`gcloud` 컨테이너**: **`gcr.io/cloud-builders/gcloud`** 이미지를 사용하여 Google Cloud 명령어를 수행합니다.
      - **`kubectl` 컨테이너**: **`gcr.io/cloud-builders/kubectl`** 이미지를 사용하여 쿠버네티스 명령어를 실행할 수 있게 합니다.
- **`stages` 블록**: 여러 **단계**(`stage`)로 구성된 Jenkins 파이프라인을 정의합니다.
- **`stage('Test')`**: 테스트 단계를 정의합니다.
  - **`container('golang')`**: `golang` 컨테이너 내에서 명령어를 실행합니다.
  - `sh`: 셸 스크립트를 실행합니다.
    - **`ln -s`**: 현재 디렉터리를 `/go/src/sample-app`에 심볼릭 링크로 연결합니다. 이는 Go의 표준 디렉터리 구조에 맞추기 위함입니다.
    - **`go test`**: Go 테스트를 실행합니다.
- **`stage('Build and push image with Container Builder')`**: 도커 이미지를 빌드하고 푸시하는 단계입니다.
  - **`container('gcloud')`**: `gcloud` 컨테이너에서 명령어를 실행합니다.
  - `sh`: 셸 스크립트를 실행합니다.
    - **`gcloud builds submit -t ${IMAGE_TAG} .`**: 현재 디렉토리(`.`)를 Google Cloud Build를 사용하여 빌드하고, **이미지 태그**(`IMAGE_TAG`)로 **Google Container Registry**에 푸시합니다.
- **`stage('Deploy Canary')`**: **Canary 배포**를 수행하는 단계입니다.
  - **`when { branch 'canary' }`**: 이 단계는 **canary 브랜치**에만 실행됩니다.
  - **`container('kubectl')`**: `kubectl` 컨테이너에서 명령어를 실행합니다.
  - `sh`: 셸 스크립트를 실행합니다.
    - **`sed -i.bak 's#corelab/gceme:1.0.0#${IMAGE_TAG}#' ./k8s/canary/\*.yaml`**: `canary` 배포 YAML 파일에서 이미지 이름을 **새로운 이미지 태그**로 변경합니다.
  - **`step([$class: 'KubernetesEngineBuilder', ...])`**: Jenkins의 **Google Kubernetes Engine Plugin**을 사용하여 GKE에 배포합니다. **매니페스트**를 적용하고 배포를 검증하는 역할을 합니다.
</div>
</details>

<br><br>

### Pods

**해당 폴더의** `ls sample-app/k8s/production` **로 확인해 보면 backend, frontend 관련 yaml이 있다.**

해당 yaml을 kubectl을 통해 실행하게 되면 pod, svc가 생성된다. 정상 생성을 위해서는 도커 이미지가 필요 했을텐데 도커 빌드와 Registry에 푸시는 Jenkins가 구동하게 파이프라인 작성을 했었다!

- Jenkins 사용전에는 우리가 도커를 빌드해서 도커이미지를 Registry에 푸시하여 해당 도커이미지 경로를 구해 사용했던것을 주의하자.

<br>

**cat sample-app/k8s/production/backend-production.yaml** -> frontend와 유사

**cat sample-app/k8s/production/frontend-production.yaml**

```yaml
kind: Deployment
apiVersion: apps/v1
metadata:
  name: gceme-frontend-production
spec:
  replicas:
  selector:
      matchLabels:
          app: gceme
          role: frontend
          env: production
  template:
    metadata:
      name: frontend
      labels:
        app: gceme
        role: frontend
        env: production
    spec:
      containers:
      - name: frontend
        image: corelab/gceme:1.0.0
        resources:
          limits:
            memory: "500Mi"
            cpu: "100m"
        imagePullPolicy: Always
        readinessProbe:
          httpGet:
            path: /healthz
            port: 80
        command: ["sh", "-c", "app -frontend=true -backend-service=http://gceme-backend:8080 -port=80"]
        ports:
        - name: frontend
          containerPort: 80
```

<details><summary><b>코드 관련 자세히</b></summary>
<div markdown="1">
- kind: 리소스의 종류를 지정합니다. 여기서는 Deployment입니다.
- apiVersion: 사용할 API 버전을 지정합니다. apps/v1는 Deployment 리소스의 최신 안정적인 버전입니다.
- metadata: 리소스에 대한 메타데이터를 정의합니다. 여기서는 Deployment의 이름을 gceme-frontend-production으로 설정합니다.
- spec: Deployment의 사양을 정의합니다.
  - replicas: 이 필드는 설정되어 있지 않지만, 원하는 Pod의 수를 지정합니다. 예를 들어, replicas: 3으로 설정하면 3개의 Pod가 생성됩니다.
  - selector: 이 Deployment가 관리할 Pod를 선택하는 기준입니다. matchLabels를 사용하여 레이블이 app: gceme, role: frontend, env: production인 Pod를 선택합니다.
- template: 이 **Deployment에 의해** **생성될 Pod의 템플릿을 정의**합니다.
  - metadata: 생성될 Pod의 메타데이터를 정의합니다. 여기서는 Pod의 이름을 frontend로 설정하고, 동일한 레이블을 부여합니다.
  - spec: **Pod 내의 컨테이너 사양을 정의**합니다.
    - containers: 이 Deployment에서 사용할 컨테이너를 정의합니다.
      - name: 컨테이너의 이름을 frontend로 지정합니다.
      - image: 사용할 Docker 이미지를 지정합니다. 여기서는 corelab/gceme:1.0.0을 사용합니다.
      - resources: 리소스 요청 및 제한을 설정합니다. 여기서는 메모리 500Mi(메가바이트)와 CPU 100m(0.1 CPU)을 설정합니다.
      - imagePullPolicy: 컨테이너 이미지를 어떻게 가져올지 설정합니다. Always로 설정하면 항상 최신 이미지를 가져옵니다.
      - readinessProbe: 이 설정은 컨테이너가 준비 상태인지 확인하는 방법을 정의합니다. HTTP GET 요청을 통해 /healthz 경로에 접근하여 응답을 확인합니다.
        - /healthz 경로는 애플리케이션 코드에서 직접 구현해야 합니다. (main.go에 있음!)
        - 없으면 이 설정은 없애는게 나을듯.
      - command: **컨테이너가 시작될 때 실행할 명령어**를 정의합니다. 여기서는 app이라는 실행 파일을 프론트엔드 모드로 실행하고, 백엔드 서비스 URL을 지정합니다.
        - Docker 이미지에는 Dockerfile에서 정의된 기본 명령어가 있습니다. CMD 또는 ENTRYPOINT 지침을 사용하여 설정됩니다. command를 명시적으로 지정하면 이 기본 명령어를 덮어씁니다. -> app 실행이 이부분
      - ports: **컨테이너가 노출할 포트**를 정의합니다. 여기서는 containerPort 80을 사용하여 HTTP 서비스를 제공합니다.
- **참고 정보**
  - 클라이언트는 `command: ["sh", "-c", "app -frontend=true -backend-service=http://gceme-backend:8080 -port=80"]`
    - app: 실행할 프로그램 이름입니다.
    - -frontend=true: 이 플래그는 app이 클라이언트 모드로 실행되도록 지정합니다. 즉, 이 애플리케이션이 클라이언트 역할을 한다는 것을 나타냅니다.
    - -backend-service=http://gceme-backend:8080: 클라이언트가 요청을 보낼 백엔드 서비스의 URL을 지정합니다. gceme-backend라는 이름의 서비스가 8080 포트에서 실행되고 있다는 것을 나타냅니다.
    - -port=80: 클라이언트가 요청을 수신할 포트를 80으로 설정합니다
  - 백엔드는 `command: ["sh", "-c", "app -port=8080"]`
    - app: 실행할 프로그램 이름입니다.
    - -port=8080: 백엔드 서비스가 요청을 수신할 포트를 8080으로 설정합니다.
</div>
</details>

<br>

**해당 폴더의** `ls sample-app/k8s/canary` **를 확인해 보면 backend-canary, frontend-canary 인 yaml이 있다.**

이를 실행하여 카나리 pod 를 추가로 구동하는 것이다. 설정 내용을 보면 canary 역할이란 점을 제외하고는 앞의 일반적인 pod랑 동일하다.

**cat sample-app/k8s/canary/frontend-canary.yaml**

**cat sample-app/k8s/canary/backend-canary.yaml**

- name과 env가 canary로 다르고 나머지는 동일하다.

<br>

`dev` **폴더도 확인해보면 관련 yaml이 있다.**

이는 운영 서버가 아닌 개발 서버로 생각하면 된다. 따로 배포하여 Pod가 추가 생성 되어 테스트 해볼 수 있다.

문제 없으면 운영 서버로 배포하거나 카나리아(업뎃)로 배포하게 된다!

<br><br>

### Service(svc)

**해당 폴더의** `ls sample-app/k8s/services` **로 확인해 보면 backend, frontend 인 yaml이 있다.**

이를 실행하여 service 도 생성하는 것이다. frontend의 type은 LoadBalancer 사용!

**프로젝트 구조가 아래 형태였던걸 상기하자. frontend만 외부IP 제공을 위해 LoadBalancer 타입을 사용!**

**app:gceme 선택기인 Pod를 전부 사용하므로 카나리어 업뎃 방식을 지원합니다.**

<img src="https://github.com/BH946/bh946.github.io/assets/80165014/755801c9-b35e-4daa-8f71-e6f51b1f3f5c" alt="image" style="zoom:50%;" /> 

**cat sample-app/k8s/services/frontend.yaml**

```yaml
kind: Service
apiVersion: v1
metadata:
  name: gceme-frontend
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 80
    targetPort: 80
    protocol: TCP
  selector:
    app: gceme
    role: frontend
```

**cat sample-app/k8s/services/backend.yaml**

```yaml
kind: Service
apiVersion: v1
metadata:
  name: gceme-backend
spec:
  ports:
  - name: http
    port: 8080
    targetPort: 8080
    protocol: TCP
  selector:
    role: backend
    app: gceme
```


