---
title:  "[실습-초급05]Continuous Delivery(CD) with Jenkins in Kubernetes Engine"
categories : [Kubernetes_Bas]
tag : [구글 클라우드, GCP, GCC, 구글 클라우드 콘솔, 클라우드 쉘, 쿠버네티스, Kubernetes, 구글 클라우드 스터디 잼, 스터디 잼 초급]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
typora-root-url: ../../..
---



# Continuous Delivery with Jenkins in Kubernetes Engine

**다섯번째 실습은 `Jenkins(오픈소스)를 통한 지속적 배포(CD) 파이프라인 설정과 코드의 지속적인 통합(CI)` 을 배워 봅니다.**

**핵심 : Kubernetes+Jenkins 조합을 활용할 시 CI/CD와 컨테이너 환경 구축이 편리**

**아래 13개의 Task를 해결하면서 간접적으로 경험해 봅시다.**

<br>

**Objectives(목표)**

* Provision a Jenkins application into a Kubernetes Engine Cluster
* Set up your Jenkins application using Helm Package Manager
* Explore the features of a Jenkins application
* Create and exercise a Jenkins pipeline

<br><br>

## Task 1. Download the source code

**`compute zone` 을 먼저 세팅합니다.**

**그리고 `gstuil` 명령어를 활용해서 예제 코드들을 다운받습니다!**

<br>

### 1. Google Cloud Console -> Cloud Shell 접속

**Google Cloud Console 화면**

![image-20230621003916367](/images/2023-10-18-(실습-초급01)Introduction to Docker/image-20230621003916367.png)

<br>

**Cloud Shell 실행 화면(세팅)**

* `gcloud config set compute/zone Zone`
* **예제 코드들 다운(클론)**
  * `gsutil cp gs://spls/gsp051/continuous-deployment-on-kubernetes.zip .`
  * `unzip continuous-deployment-on-kubernetes.zip`
  * `cd continuous-deployment-on-kubernetes` 

<br><br>

## Task 2. Provisioning Jenkins

### 1. 클러스터 생성

**먼저, 쿠버네티스 클러스터 생성합니다. (5분정도 소요)**

* `gcloud container clusters create jenkins-cd \
  --num-nodes 2 \
  --machine-type e2-standard-2 \
  --scopes "https://www.googleapis.com/auth/source.read_write,cloud-platform"`

<br>

### 2. 클러스터 인증 및 연결

**클러스터 연결 확인합니다.**

* `gcloud container clusters list` : 클러스터 실행중인가 확인
* **`gcloud container clusters get-credentials jenkins-cd` : 클러스터 인증정보를 "jenkins-cd"로 가져오기**
* `kubectl cluster-info` : 클러스터 연결 가능여부 확인

<br><br>

## Task 3. Setup Helm

**Helm(패키지 관리자)을 사용해서 차트저장소에 Jenkins를 설치합니다.**

* 참고) Helm은 pip, apt 같은것!!

* `helm repo add jenkins https://charts.jenkins.io`
* `helm repo update` : 저장소 최신상태인지 확인!

<br><br>

## Task 4. Configure and Install Jenkins

Jenkins 설치할 때 `values.yaml` 을 통해서 연동에 필요한 플러그인들을 자동으로 설치할 수 있습니다.

* `values.yaml` 에 다양한 플러그인들이 추가되어 있어서 굉장히 편리!

<br>

### 1. Install Jenkins

 **Helm CLI를 이용해서 설치합니다.**

* `helm install cd jenkins/jenkins -f jenkins/values.yaml --wait` : 설치
* `kubectl get pods` : 설치 확인
* `kubectl create clusterrolebinding jenkins-deploy --clusterrole=cluster-admin --serviceaccount=default:cd-jenkins`
  * Jenkins 서비스 계정 설정

![image](https://github.com/BH946/bh946.github.io/assets/80165014/211f1e16-59e5-4d61-84d1-7a334e02420f) 

<br>

### 2. Jenkins 서비스 확인

**Cloud Shell에서 Jenkins로의 포트전달 설정 후 서비스 생성을 확인합니다.**

* `export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")`
* `kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &`
* `kubectl get svc` : service랑 svc는 같다.

![image](https://github.com/BH946/bh946.github.io/assets/80165014/4e7d97cd-7ec1-424a-a215-a29f7ff3b6a2) 

<br><br>

##  Task 5. Connect to Jenkins

**쿠버네티스 클러스터에 Jenkins를 설치했으니 연결까지 해봅니다.**

<br>

**Jenkins는 처음 생성할 때 자동으로 관리자 비밀번호를 설정합니다.**

* `printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo` : 비밀번호 확인 명령어
* 따라서 관리자 ID : admin
* 비밀번호 : 위에서 확인한 번호

<br>

**위 정보를 사용해서 Cloud Shell의 웹 미리보기 포트 8080 클릭해서 접속합니다.**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/4de1d24a-3fd7-4557-b329-921c359ce245) 

<br><br>

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

<br><br>

## Task 7. Deploying the Application

**Jenkins 실행은 끝났으니.. 앱을 이제 배포해보겠습니다.**

**앱을 프로덕션(사용자가 엑세스하는 라이브사이트), 카나리아(사용자 일부 수용 사이트. 이전에 배운 업데이트 방식!) 로써 2개의 환경으로 배포해봅시다.**

* `cd sample-app kubectl create ns production` : 네임스페이스 생성
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

<br><br>

## Task 8. Creating the Jenkins Pipeline

**Jenkins 파이프라인을 만들어 보겠습니다.**

<br>

### 1. 소스코드 호스팅위해 저장소 생성

**sampe-app 의 소스코드를 호스팅하는 저장소를 만듭니다.**

* **gceme 샘플 앱의 복사본을 만들고 이를 Cloud Source Repository로 푸시**
* `gcloud source repos create default` : GCP의 Cloud Source Repository 생성(default)
* `git init`
* `git config credential.helper gcloud.sh` : 인증
* `git remote add origin https://source.developers.google.com/p/$DEVSHELL_PROJECT_ID/r/default` : 생성한 저장소를(default) Git 프로젝트에 추가
* `git config --global user.email "[EMAIL_ADDRESS]" `
* `git config --global user.name "[USERNAME]"`
* `git add .`
* `git commit -m "Initial commit" `
* `git push origin master`

<br>

### 2. 서비스 계정 사용자 인증

**Jenkins에서 코드 저장소에 액세스할 수 있도록 허용이 필요합니다.**

* 인증정보 만들기 : `Jenkins 관리 > Manage Credentials > System > Global credentials > Add Credentials`
  * Kind는 Google Service Account from metadata 선택 후 Create

<br>

### 3. Kubernetes용 Jenkins Cloud 구성

구성방법 : `Jenkins 관리 > Manage Cloud > Clouds Add a new cloud > Kubernetes > Kubernetes Cloud Details`

* Jenkins URL : `http://cd-jenkins:8080`
* Jenkins tunnel : `cd-jenkins-agent:50000`

<br>

### 4. Jenkins 작업 생성

**테스트, 배포, 환경설정, 소스 코드 체크아웃 등등을 진행하는 "작업"을 생성합니다.**

1. Dashboard(대시보드) > New Item(새 항목) 클릭
2. 프로젝트 이름은 sample-app으로 지정, Multibranch Pipeline(다중 브랜치 파이프라인) 옵션 선택 후 OK 클릭
3. Branch Sources(브랜치 소스) 섹션에 있는 Add Source(소스 추가) 드롭다운에서 Git 선택
4. Cloud Source Repositories에 있는 sample-app 저장소에 다음 HTTPS clone URL(HTTPS 클론 URL)을 Project Repository(프로젝트 저장소) 필드에 붙여 넣기, [PROJECT_ID]를 현재 프로젝트 ID로 변경 `https://source.developers.google.com/p/[PROJECT_ID]/r/default`
5. 이전 단계에서 서비스 계정을 추가할 때 만든 사용자 인증 정보의 이름을 사용자 인증 정보드롭다운에서 선택
6. Scan Multibranch Pipeline Triggers(다중 브랜치 파이프라인 트리거 검색)섹션에서 Periodically if not otherwise run(별도로 실행하지 않는 경우 주기적으로 실행)상자를 선택하고 Interval(간격) 값을 1분으로 설정
7. `Branch Indexing`이라는 작업이 자동으로 실행되므로 `Finished: SUCCESS` 메시지 뜨면 성공!

![image](https://github.com/BH946/bh946.github.io/assets/80165014/07d134af-621f-449c-a634-6007ea54835b) 

<br><br>

## Task 9. Creating the development environment

**개발 브랜치를 만들고, 파이프라인 정의(Jenkinsfile)를 수정 후 테스트 하겠습니다.**

* `git checkout -b new-feature`

* ```
  vi Jenkinsfile
  i # 편집기 실행
  
  PROJECT = "REPLACE_WITH_YOUR_PROJECT_ID" # 실습에서 할당받은 프로젝트 ID로 수정
  APP_NAME = "gceme"
  FE_SVC_NAME = "${APP_NAME}-frontend"
  CLUSTER = "jenkins-cd"
  CLUSTER_ZONE = "" # 실습에서 할당받은 컴퓨팅 영역으로 수정, gcloud config get compute/zone로 확인 가능
  IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"
  JENKINS_CRED = "${PROJECT}"
  ```

<br>

**테스트를 위해 사이트를 수정합니다.**

* `html.go` 파일에 `<div class="card orange">` 로 수정 (파랑->주황)
* `main.go` 파일에 `const version string = "2.0.0"` 로 수정 (1.0.0->2.0.0)

<br><br>

## Task 10. Kick off Deployment

수정한 파일들을 푸시해서 수정된 배포를 시작합니다.

* git add Jenkinsfile html.go main.go   
  git commit -m "Version 2.0.0"   
  git push origin new-feature
  * 깃 푸시만으로 자동 배포가 진행됨을 알 수 있습니다!
  * 시간이 걸리므로 Jenkins에서 해당 브랜치 들어가서 Console Output을 확인합니다.

![image](https://github.com/BH946/bh946.github.io/assets/80165014/e1758eda-a52d-4ea5-ad34-fd3b4eee0c40) 

<br>

**테스트해봅니다.**

* `kubectl proxy &  `
* `curl \ http://localhost:8001/api/v1/namespaces/new-feature/services/gceme-frontend:80/proxy/version`

![image](https://github.com/BH946/bh946.github.io/assets/80165014/3fe28871-7178-47a5-adbf-1995dd39039d) 

<br><br>

## Task 11. Deploying a canary release

이번엔 개발 브랜치 코드를 카나리아 환경에 배포하겠습니다.

* git checkout -b canary   
  git push origin canary
  * 바로 개발 코드를 푸시합니다.
  * Jenkins에서 카나리아 파이프라인이 자동으로 실행합니다. (개발에서 한것처럼 자동 배포)

<br>

**카나리아 정상 동작하는지 테스트합니다.**

* `export FRONTEND_SERVICE_IP=$(kubectl get -o \ jsonpath="{.status.loadBalancer.ingress[0].ip}" --namespace=production services gceme-frontend)`
* `curl http://$FRONTEND_SERVICE_IP/version` 를 연속으로 확인해서 약 5개중 1개비율로 2.0.0이 반환되는지 확인합니다.

![image](https://github.com/BH946/bh946.github.io/assets/80165014/bb30c44b-a5bf-442f-9dca-6c784358e1ad) 

<br><br>

## Task 12. Deploying to production

**카나리아에서 테스트를 진행했고, 사용자들은 버그를 못느끼고 정상 동작한다고 결론이 나면 "프로덕션(배포)" 환경에 배포하면 끝입니다.**

* 프로덕션 환경은 실제로 사용자들에게 배포되어 사용하고있는 환경입니다.
* **참고로 `개발환경(실제로 개발한것처럼 코드 약간 수정) -> 카나리아(업뎃전에 일부사용자에게 테스트!!) -> 프로덕션` 순서로 최종 배포를 진행하고 있습니다!**

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
  * 전부 2.0.0 만 출력된다면 성공입니다!
* `kubectl get service gceme-frontend -n production`
  * 추가로 아까 수정한 "파랑->주황" 을 확인해봅니다!

![image](https://github.com/BH946/bh946.github.io/assets/80165014/385ea455-365e-47c3-bba9-88be0f78170a) 

<br><br>

## Task 13. Test your Understanding

**간단한 문제 2개 있습니다.**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/a3df5524-5dd2-46ac-9473-4a89aec738e7) 

<br>

![image](https://github.com/BH946/bh946.github.io/assets/80165014/04c78abf-3843-44a3-922f-e563e33ff1a7) 

<br><br>

# 마무리

**참고) 너무 잘 정리한 글이 있어서 이글의 내용도 함께 참고했다!**

* **[CN_City님](https://cn-c.tistory.com/48?category=1105946#%EC%-E%--%EC%--%--%----%--%EC%--%-C%EC%B-%A-%EC%A-%--%--%EC%--%--%EB%-D%B-%EC%-D%B-%ED%-A%B--Rolling%--update-)**

<br>

**이전시간에 배운 3가지 업데이트 방법 중에서 Canary 방식을 사용!**

**Jenkins로 자동 배포를 설정하여 코드 수정 후 깃 푸시하면 적용된다.**

**참고로 테스트과정은 `개발환경(실제로 개발한것처럼 코드 약간 수정) -> 카나리아(업뎃전에 일부사용자에게 테스트!!) -> 프로덕션` 순서로 자동배포 테스트를 진행했다.**

