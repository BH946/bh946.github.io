---
title:  "[실습-초급02]Kubernetes Engine-Qwik Start"
categories : [Kubernetes]
tag : [구글 클라우드, GCP, GCC, 구글 클라우드 콘솔, 클라우드 쉘, 쿠버네티스, Kubernetes, 구글 클라우드 스터디 잼, 스터디 잼 초급]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
typora-root-url: ../../..
---



# Kubernetes Engine-Qwik Start

**두번째 실습은 `GKE(Google Kubernetes Engine)로 컨테이너 생성 및 앱 배포` 를 배워 봅니다.**

**아래 5개의 Task를 해결하면서 간접적으로 경험해 봅시다.**

<br>

**Objectives(목표)**

* GKE - Kubernetes로 컨테이너 생성 - 클러스터 생성
* 앱 배포 - 도커 이미지 배포

<br><br>

## Task 1. Set a default compute zone

**`compute zone` 을 세팅합니다.**

<br>

### 1. Google Cloud Console -> Cloud Shell 접속

**Google Cloud Console 화면**

![image-20230621003916367](/images/2023-10-18-(실습-초급01)Introduction to Docker/image-20230621003916367.png)

<br>

**Cloud Shell 실행 화면**

* `gcloud config set compute/region us-east4` : us-east4 로 설정
* `gcloud config set compute/zone us-east4-c` : us-east4-c 로 설정
  * 참고 : Region > Zone 로써 Region이 더 큰 개념입니다.


![image](https://github.com/BH946/bh946.github.io/assets/80165014/e007bb18-15e7-408c-8665-7d9c5f516aeb) 

<br><br>

## Task 2. Create a GKE cluster

**GKE 클러스터 생성을 합니다.**

* **클러스터**는 하나 이상의 **"클러스터 마스터 머신"과 "노드"**라고 불리는 여러 작업자 머신으로 구성됩니다.
* **노드**는 노드를 클러스터의 일부로 만드는 데 필요한 Kubernetes 프로세스를 실행하는 Compute Engine **"가상 머신(VM) 인스턴스"**입니다.

<br>

### 1. 클러스터 생성

**`gcloud container clusters create --machine-type=e2-medium --zone=us-east4-c lab-cluster`**

* lab-cluster 이름으로 생성

![image](https://github.com/BH946/bh946.github.io/assets/80165014/ef6d343f-ff73-4853-bffd-26a99daf53d0)  

<br><br>

## Task 3. Get authentication credentials the cluster

**클러스터 사용에는 사용자 인증이 필수입니다.**

<br>

**`gcloud container clusters get-credentials lab-cluster` : 사용자 인증**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/8651fd3c-7286-4bb2-aff7-76c022d23818) 

<br><br>

## Task 4. Deploy an application to the cluster

**생성한 클러스터에 컨테이너한 앱을 배포합니다.**

**GKE는 Kubernetes 객체를 사용하여 클러스터를 관리하며, Kubernetes는 Deployment 객체와 Service 객체를 제공합니다.**

* Service의 경우 앱을 외부 트래픽에 노출해주는 역할을 하므로 중요합니다!

<br>

### 1. Kubernetes의 Deployment 객체 생성

**`kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0`**

* 해당 Kubernetes 명령은 `hello-server` 배포 객체를 생성합니다.
* 이때, 도커 이미지는 Container Registry 버킷에서 예제 이미지를(1.0버전) 가져옵니다. 

<br>

### 2. Kubernetes의 Service 객체 생성

**`kubectl expose deployment hello-server --type=LoadBalancer --port 8080`**

* 포트 8080 매핑합니다.
* 로드밸런싱 타입지정합니다.

<br>

**`kubectl get service`** : 생성한 객체를 확인합니다.

* EXTERNAL-IP(외부연결 IP) 의 경우 생성되는데 시간이 걸린다는점!

![image](https://github.com/BH946/bh946.github.io/assets/80165014/a39bde73-ef10-4441-a7ed-baaa2371afcc) 

<br>

### 3. EXTERNAL-IP로 접속해보기

**`http://[EXTERNAL-IP]:8080`**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/848a486c-0403-474e-8068-bd9a787be456) 

<br><br>

##  Task 5. Deleting the cluster

**생성한 클러스터 환경을 삭제해봅시다!**

<br>

### 1. 클러스터 삭제

**`gcloud container clusters delete lab-cluster`** : lab-cluster 삭제

![image](https://github.com/BH946/bh946.github.io/assets/80165014/c7f8720b-95f1-496b-8426-5bff1c168a44) 

<br><br>

# 마무리

**GKE는 Kubernetes 객체를 사용하여 클러스터를 관리하며, Kubernetes는 Deployment 객체와 Service 객체를 제공합니다.**

**GKE 클러스터를 실행하면 Google Cloud가 제공하는 고급 클러스터 관리 기능(최적화 등등)을 사용할 수 있습니다.**

**실습흐름 : 클러스터를 생성 + 사용자인증 + 클러스터에 도커이미지 배포와 외부 트래픽에 연결**
