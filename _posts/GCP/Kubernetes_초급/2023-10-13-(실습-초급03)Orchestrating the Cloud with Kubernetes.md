---
title:  "[실습-초급03]Orchestrating the Cloud with Kubernetes"
categories : [Kubernetes_Bas]
tag : [구글 클라우드, GCP, GCC, 구글 클라우드 콘솔, 클라우드 쉘, 쿠버네티스, Kubernetes, 구글 클라우드 스터디 잼, 스터디 잼 초급]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
typora-root-url: ../../..
---



**세번째 실습은** `GKE로 쿠버네티스 클러스터 생성, 클러스터 상에서 kubectl 명령어로 도커 컨테이너 배포 및 관리, 쿠버네티스의 배포 및 서비스를 사용하여 앱을 마이크로서비스로 분할하는법` **을 배워 봅니다.**

**배포, 서비스 방법 2가지 (앞의 실습에서는 yaml로 생성 안 했었음!)**

1. Kubernetes의 Deployment or Service 를 명령어로 생성
2. **configuration files(구성 파일).yaml로 생성이 가능 - Replica의 숫자만큼 Pod 자동생성**
   - **배후에서 배포는 복제본 세트를 사용하여 포드 시작 및 중지를 관리! (훨씬 안전)**

**추가로** `https 트래픽`**을 처리할 수 있는** `보안 포드(secure-monolith Pod)`**생성도 소개!**

<br>

**아래 10개의 Task를 해결하면서 간접적으로 경험해 봅시다.**

<br>

**Objectives(목표)**

* Provision a complete [Kubernetes](http://kubernetes.io/) cluster using [Kubernetes Engine](https://cloud.google.com/container-engine).
* Deploy and manage Docker containers using `kubectl`.
* Break an application into microservices using Kubernetes' Deployments and Services.

<br>

<br>

## Task 1. Get the sample code

`compute zone` **을 먼저 세팅합니다.**

**그리고** `gstuil` **명령어를 활용해서 예제 코드들을 다운받습니다!**

<br><br>

### 1. Google Cloud Console -> Cloud Shell 접속

**Google Cloud Console 화면**

![image](https://github.com/user-attachments/assets/416e3c00-9c47-43dc-8137-9fbfb04e8811) 

<br>

**Cloud Shell 실행 화면**

* `gcloud config set compute/zone Zone`
* `gcloud container clusters create io` : 클러스터 생성
* `gsutil cp -r gs://spls/gsp021/* .` : 예제 코드들 다운(클론)
* `cd orchestrate-with-kubernetes/kubernetes`
* `ls`
  * deployments : 배포를 위한 파일들
  * nginx : nginx 구성 파일들
  * pods : 쿠버네티스 pod를 위한 파일들
  * services : 쿠버네티스 서비스를 위한 파일들
  * tls : TLS 인증서
  * cleanup.sh : 정리 스크립트


![image](https://github.com/BH946/bh946.github.io/assets/80165014/e50fd378-28e5-42b6-8b91-1c871035dae0)  

<br>

<br>

## Task 2. Quick Kubernetes Demo

**Kubernetes에서는 모든 컨테이너가 Pod에서 실행됩니다.**

* **애플리케이션 이미지 < 컨테이너 < Pod < 워커노드**
* 참고) 일반적인 배포와 쿠버네티스에서 배포는 조금 의미가 다르다.
  * **Pod를 생성하는 과정을 배포**라 한다.

<br>

**Kubernetes를 생성하고 배포해봅시다.**

<br><br>

### 1. kubectl create - Deployment

`kubectl create deployment nginx --image=nginx:1.10.0` : nginx 이미지 사용!

`kubectl get pods`

* deployment 를 통해서 pod 생성 -> 즉, **"배포상태!"**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/0d4cfc1e-59fc-45e8-b8d5-5d10aa10a5a1) 

<br><br>

### 2. kubectl expose - Service

`kubectl expose deployment nginx --port 80 --type LoadBalancer` : 외부 노출!

`kubectl get services` 

* 배포상태여도 외부에서 접근하려면 **"서비스"가 필수!**
* 서비스 생성 3가지 방법 중 **LoadBalancer 사용**
  * Kubernetes가 외부IP가 첨부된 로드 밸런서를 만듦
  * 외부IP접속 -> nginx Pod로 라우팅

* **EXTERNAL-IP** 생성까지 조금 기다려줘야함.
  * 외부 IP덕분에 브라우저로 바로 테스트 가능


![image](https://github.com/BH946/bh946.github.io/assets/80165014/b476dd28-48c8-4895-86ec-7e26148130d4) 

<br><br>

### 3. Test

`curl http://<External IP>:80` : 배포 테스트!

![image](https://github.com/BH946/bh946.github.io/assets/80165014/92d0d3c8-0a8c-46d6-a8d3-a1d8031ee69f) 

<br>

<br>

## Task 3. Pods

**Kubernetes의 핵심은 [Pod](https://kubernetes.io/docs/concepts/workloads/pods/) 입니다.**

* 아래 그림은 nginx컨테이너와 monolith컨테이너를 포함하는 Pod
* volume도 존재하며 이는 데이터 디스크
* 또한, Pod는 네트워크IP를 가짐
* 물론, 그림처럼 포함구조이므로 Pod 내에서 공유가능함

![image](https://github.com/BH946/bh946.github.io/assets/80165014/fc683829-bad6-4bb3-ae86-feeae27767f2) 

<br>

<br>

## Task 4. Creating pods

**pod configuration files(포드 구성 파일)로도 Pod 생성가능하며 예제파일로 살펴보겠습니다.**

<br><br>

### 1. 살펴보기

`cd ~/orchestrate-with-kubernetes/kubernetes`

`cat pods/monolith.yaml`

* 이름지정, 컨테이너 - 이미지/포트 등등 "포드 구성 파일"내용
* 주목할 점은 컨테이너 1개! - monolith

![image](https://github.com/BH946/bh946.github.io/assets/80165014/6e2c3425-3df3-4384-bc1d-084b28265a1d) 

<br><br>

### 2. 생성하기

`kubectl create -f pods/monolith.yaml` : 위에서 확인한 설정으로 "생성"

`kubectl get pods` : 처음에 만든 nginx와 지금만든 monolith 존재하면 성공!

* `kubectl describe pods monolith` : 자세히 해당 포드 확인가능!

![image](https://github.com/BH946/bh946.github.io/assets/80165014/0b939609-5b89-4d47-b025-d8acea6a83cb) 

<br>

<br>

##  Task 5. Interacting with pods

**기본적으로 포드에는 개인 IP 주소가 할당되며 클러스터 외부에 도달할 수 없습니다. (이 때문에 초반 nginx 포드는 로드밸런서 서비스를 만들어서 접근했었음!)** 

`kubectl port-forward` **명령을 사용하여 로컬 포트를 모놀리스(monolith) 포드 내부 포트에 매핑합니다. (포트포워드)**

* 터미널 창을 1개 더 켜서 진행하겠습니다.

<br><br>

`kubectl port-forward monolith 10080:80` **: 새로운 터미널에서 실행 ㄱ**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/0d7373b7-1f8b-4b00-9520-53fc2c73f004) 

<br>

`curl http://127.0.0.1:10080` **: 1번째 터미널에서 실행**

* 포트포워드를 10080:80으로 했기때문에 10080포트(로컬)로 접속 -> 80포트(monolith) 포워드
* hello가 나오면 정상!

<br>

**이번엔 보안이 설정된 엔드포인트를 조회하자**

`curl http://127.0.0.1:10080/secure` **: 에 접속해보면?? -> "authorization faile"**

`curl -u user http://127.0.0.1:10080/login` **: 따라서 이곳에 접속하여 "password" 입력후 토큰인증!**

`TOKEN=$(curl http://127.0.0.1:10080/login -u user|jq -r '.token')` **: 얻은 토큰을 TOKEN변수에 저장!**

`curl -H "Authorization: Bearer $TOKEN" http://127.0.0.1:10080/secure` **: 다시 secure에 접속!!**

* hello가 나오면 정상!

![image](https://github.com/BH946/bh946.github.io/assets/80165014/b4b02fa5-fa72-4f93-b51f-69ae9086a725) 

<br>

**로그확인!!**

* `kubectl logs monolith` : 로그를 확인할 수 있다.
* `kubectl logs -f monolith` : 세번째 터미널에 실행시 실시간 로그를 확인 가능하다!
* `curl http://127.0.0.1:10080` : 첫번째 터미널에서 접속 테스트를 해보면 "세번째 터미널 로그가 실시간 변경!"

![image](https://github.com/BH946/bh946.github.io/assets/80165014/712bb91c-ddca-4b22-9ecb-9564493e6676) 

 <br>

`kubectl exec monolith --stdin --tty -c monolith -- /bin/sh` **: 내부 컨테이너 셀에 접속도 가능**

* 컨테이너 내부에서 터미널 명령어로 문제원인 파악하고 싶을때 유용!
* `ping -c 3 google.com` : 예로 외부 접속 테스트를 할 수 있음
* `exit` 로 탈출

![image](https://github.com/BH946/bh946.github.io/assets/80165014/d0be5b8d-6d35-4542-826d-12c37bd8c332) 

<br>

<br>

## Task 6. Services

**Pod는 여러가지 이유로 중지되거나 실행될 수 있으므로 영구적이지 않습니다.**

**Pod는 Kubernetes가 랜덤하게 내부IP를 생성하므로 Pod 재시작마다 IP주소가 바뀝니다.**

**이를위해 Service는 Pod에 대한 안정적인 엔드포인트를 제공합니다.**

* **Service는 Pod의 라벨을 통해 선택이 가능하기 때문!**
* **서비스의 3가지 타입 : ClusterIP, NodePort, LoadBalancer**
  * `ClusterIP`: 사용자에게 서비스를 제공하기 위한 용도가 아니며, 클러스터 안에서만 확인 가능
  * `NodePort`: 클러스터의 각 노드에 외부에서 액세스 가능한 IP 주소를 제공
  * `LoadBalancer`: 클라우드 제공업체로부터 로드 밸런서를 추가하여 서비스에서 유입되는 트래픽을 내부에 있는 노드로 전달하는 역할


![image](https://github.com/BH946/bh946.github.io/assets/80165014/92a16e4e-f6d5-4e98-8143-1085aac0b81b) 

<br>

<br>

## Task 7. Creating a service

**서비스를 생성해볼건데, 그전에 먼저** `https 트래픽`**을 처리할 수 있는** `보안 포드(secure-monolith Pod)`**를 생성합니다.**

<br><br>

### 1. 보안 포드 생성

`cd ~/orchestrate-with-kubernetes/kubernetes`

`cat pods/secure-monolith.yaml` : 해당파일 열어보면 `tls-certs, nginx-proxy-conf` 파일 이 필요

* 따라서 필요한 파일 먼저 생성해주고 Pod를 생성하자

* `kubectl create secret generic tls-certs --from-file tls/`
* `kubectl create configmap nginx-proxy-conf --from-file nginx/proxy.conf`
* `kubectl create -f pods/secure-monolith.yaml`
* **보안포드 생성 완료!**

<br><br>

### 2. 서비스 생성

**보안 모놀리식(secure-monolith) Pod를 외부에 노출하기 위해 서비스를 생성합니다.**

`cat services/monolith.yaml` **: 서비스 생성위해 구성파일 내용 확인**

`kubectl create -f services/monolith.yaml` **: 서비스 생성**

* 서비스 type은 NodePort
* 'app: monolith' 와 'secure:enabled' 라벨이 있는 모든 Pod를 자동으로 찾아서 노출 설정
* 포트 31000에서 nginx(포트 443)로 외부 트래픽을 전달하는 방법 설정
  * **이 때문에 여기에 노드포트를 노출해야 한다.**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/92daf537-e006-409a-9662-e42e38a9efdb)  

<br>

`gcloud compute firewall-rules create allow-monolith-nodeport \
  --allow=tcp:31000`

* 노출된 노드 포트에서 monolith Service에 대한 트래픽을 허용

![image](https://github.com/BH946/bh946.github.io/assets/80165014/cbaeb868-733a-428c-8e98-87e19841e301) 

<br><br>

### 3. Test

**이제 모든 것이 설정되었으므로 포트 전달을 사용하지 않고도 클러스터 외부에서 보안 단일체 서비스를 사용할 수 있습니다.**

`gcloud compute instances list` **: 사용해서 노드들 외부 IP 참고**

`curl -k https://<EXTERNAL_IP>:31000`

* **그런데... 에러발생!!**

<br><br>

### 4. Questions

* Why are you unable to get a response from the monolith service?
  * 바로 Pod 라벨이 올바르게 설정되어 있지 않아서!

* How many endpoints does the monolith service have?
* What labels must a Pod have to be picked up by the monolith service?

<br>

<br>

## Task 8. Adding labels to pods

**현재 모놀리식 서비스에는 엔드포인트가 없습니다.** 

**이와 같은 문제를 해결하는 한 가지 방법은 레이블 쿼리와 함께** `kubectl get pods` **명령을 사용하는 것입니다.**

* **앞서 서비스를 만들 때 두 라벨을 모두 가지고 있는 파드를 인식하도록 만들었기 때문에 이를 보완해야합니다.**

<br><br>

### 1. Pod 네임

**포드 이름들을 확인합니다.**

* `kubectl get pods -l "app=monolith"` : 2개의 Pod가 나타납니다.

* `kubectl get pods -l "app=monolith,secure=enabled"` : 이 이름인 Pod가 있어야하지만, 나타나지 않습니다.

![image](https://github.com/BH946/bh946.github.io/assets/80165014/e93d9c00-4dcd-4338-a203-dcc765e3b1e9) 

<br>

**포드 이름을 수정합니다.**

* `kubectl label pods secure-monolith 'secure=enabled'` : 포드 라벨을 지정합니다.
* `kubectl get pods secure-monolith --show-labels` : 지정한 라벨이름으로 포드를 불러옵니다.
  * 아래 사진을보면 정상적으로 불러왔으며, Pod 라벨도 "app=monolith, secure=enabled"


![image](https://github.com/BH946/bh946.github.io/assets/80165014/705fd9c5-f243-4d1c-ac68-3a85eb505ba7) 

<br><br>

### 2. TEST

**포드에 올바르게 레이블이 지정되었으므로 모놀리식 서비스의 엔드포인트 목록을 확인합니다.**

`kubectl describe services monolith | grep Endpoints`

`gcloud compute instances list` **: 노드 외부 IP들 확인**

`curl -k https://<EXTERNAL_IP>:31000` **: 하나 선정해서 테스트("Hello")**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/e630c149-addc-40a9-ba8c-42729aa4c57e) 

<br>

<br>

## Task 9. Deploying applications with Kubernetes

**이 실습의 목표는 프로덕션 환경에서 컨테이너를 확장하고 관리할 수 있도록 준비하는 것입니다.** 

**이때 배포가 필요합니다.** 

**배포는 실행 중인 포드 수가 사용자가 지정한 원하는 포드 수와 동일하도록 보장하는 선언적 방법입니다.**

* **배후에서 배포는 복제본 세트를 사용하여 포드 시작 및 중지를 관리합니다.** 
* **Pod를 업데이트하거나 확장해야 하는 경우 배포가 이를 처리합니다.** 
* **어떤 이유로든 Pod가 작동 중지되는 경우 배포는 Pod 다시 시작도 처리합니다.**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/aa0a6e89-8329-4abd-98ec-8ef233ab040d) 

<br>

**포드는 생성된 노드의 수명과 연결됩니다.** 

**아래 예시에서는 Node3가 다운되었습니다(Pod도 함께 가져옴).** 

**새 포드를 수동으로 생성하고 이에 대한 노드를 찾는 대신 배포에서 새 포드를 생성하고 Node2에서 시작했습니다.**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/ecc45a0d-eb2b-4493-8488-f42b0b24ee24) 

<br>

<br>

## Task 10. Creating deployments

모놀리식 앱을 3가지 부분으로 나누겠습니다.

- **auth** - Generates JWT tokens for authenticated users.
- **hello** - Greet authenticated users.
- **frontend** - Routes traffic to the auth and hello services.

<br>

`cat deployments/auth.yaml` **: auth를 확인합니다.**

`kubectl create -f deployments/auth.yaml` **: 배포생성**

`kubectl create -f services/auth.yaml` **: 서비스생성**

**hello, frontend도 마찬가지입니다.**

* kubectl create -f deployments/hello.yaml  
  kubectl create -f services/hello.yaml
* kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf  
  kubectl create -f deployments/frontend.yaml  
  kubectl create -f services/frontend.yaml
* frontend의 경우 필요한 파일을 추가로 생성했습니다.

![image](https://github.com/BH946/bh946.github.io/assets/80165014/baab5039-ce30-4ae3-9df4-b868b24e88a5) 

<br>

<br>

## 마무리

**프로젝트에 생성된 Service, Pod**

1. nginx는 처음에 생성했던 내용입니다.

2. 다음으로 모놀리식을 생성했었습니다.

3. hello, frontend, auth는 마지막 예제를 생성한겁니다.

![image](https://github.com/BH946/bh946.github.io/assets/80165014/2588f5e5-8112-4121-855f-4b4264be7ff9) 

<br>

**배포, 서비스 방법 2가지**

1. Kubernetes의 Deployment or Service 를 명령어로 생성

2. configuration files(구성 파일).yaml로 생성이 가능

<br>

**배포 때 Pod 생성 2가지**

1. Pod만 따로 생성(이 자체가 "배포"를 의미) 하고, 서비스 생성하는 방법

2. Kubernetes의 Deployment를 생성할 때 yaml파일에 Replica의 숫자만큼 Pod 자동생성

<br>

**배포를 한 상태는 내부IP만 가지며, 접속을 위해선 외부IP가 필요 -> 서비스**

* 따라서 서비스도 생성을 하는것!
* 서비스 생성 방법 3가지 타입 기억!

<br>

**kubectl get 명령어 출력 내용 비교**

- kubectl get deployments
  - 출력 내용: 각 Deployment의 이름, 준비 상태(Ready), 생성된 Pod 수, 원하는 Pod 수, 최신 업데이트 시간 등을 보여줍니다.
  - 물론, Jenkins 처럼 deployments에는 안 보일 수도 있는데 문제가 있는건 아닙니다.<br>Helm Chart에서는 Deployment 대신 다른 리소스를 사용하여 Jenkins를 관리할 수 있습니다. 예를 들어, Jenkins는 StatefulSet으로 배포될 수 있습니다. StatefulSet은 상태가 있는 애플리케이션을 배포하는 데 사용되는 Kubernetes 리소스 -> `kubectl get statefulsets` 로 확인 가능!
- kubectl get pods
  - 출력 내용: 각 Pod의 이름, 상태(예: Running, Pending, CrashLoopBackOff 등), 준비 상태(Ready), 재시작 횟수, 생성된 시간 등을 보여줍니다.
- kubectl get svc
  - 출력 내용: 각 Service의 이름, 클러스터 IP, 외부 IP(있는 경우), 포트 정보, 타입(예: ClusterIP, NodePort, LoadBalancer) 등을 보여줍니다.
