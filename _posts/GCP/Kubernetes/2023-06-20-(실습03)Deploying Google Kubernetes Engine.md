---
title:  "[실습03]Deploying Google Kubernetes Engine"
categories : [Kubernetes]
tag : [구글 클라우드, GCP, GCC, 구글 클라우드 콘솔, 클라우드 쉘, 쿠버네티스, Kubernetes, 구글 클라우드 스터디 잼, 스터디 잼 중급, Docker, 도커, 이미지, GKE, Clusters]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
typora-root-url: ../../..
---



# Deploying Google Kubernets Engine

**세번째 실습은 `Google Cloud Console` 을 통해 `Cluster` 를 빌드를 해보고 `Pod` 를 배포한 후 `Workload, Pod` 를 자세히 살펴보겠습니다.**

**아래 4개의 Task를 해결하면서 간접적으로 경험해 봅시다.**

<br>

**Objectives(목표)**

* Use the **Google Cloud Console** to **build and manipulate** GKE `clusters`
* Use the **Google Cloud Console** to **deploy** a `Pod`
* Use the **Google Cloud Console** to **examine** the `cluster and Pods`

<br><br>

## Task 1. GKE 클러스터 배포

이 과제에서는 **Google Cloud Console을 사용하여 GKE 클러스터를 배포**합니다.

*  **Navigation menu > Kubernetes Engine > Clusters** 로 접근합니다.

* `Create`를 클릭하여 GKE 클러스터 생성을 시작합니다. `Standard: You manage your cluster`에 대해 `configure`를 클릭합니다.

  * 해당 버튼은 현시점 기준 위쪽 바**(=SWITCH TO STANDARD CLUSTER)**에 존재합니다.

    ![image-20230623174127949](/images/2023-06-20-(실습03)Deploying Google Kubernetes Engine/image-20230623174127949.png) 

  * 이후 클러스터 이름, 클러스터 위치, Kubernetes 버전, 노드 수, 기본 노드 풀의 머신 유형 등 노드 리소스를 변경하는 UI들을 확인합니다.

* 클러스터 이름을 `standard-cluster-1`로 변경하고, 존을 `us-central1-a`로 설정합니다. 나머지는 기본값으로 두고 `Create`를 클릭합니다.

<br>

**클러스터 배포 완료 모습**

* 기본값으로 Node 3개가 생성됩니다!

![image-20230623174730458](/images/2023-06-20-(실습03)Deploying Google Kubernetes Engine/image-20230623174730458.png) 

<br><br>

## Task 2. GKE 클러스터 수정

이 과제에서는 **Google Cloud Console을 사용하여 GKE 클러스터의 크기를 수정**합니다.

* `Kubernetes Engine > Clusters > standard-cluster-1`을 클릭하고, 상세 페이지 상단에서 `NODES`를 클릭합니다.

* 노드 풀 섹션에서 `default-pool`을 클릭합니다.

* Google Cloud Console에서 노드 풀 상세 페이지 상단의 `RESIZE`를 클릭합니다.
  * `EDIT` 에서도 수정 가능합니다.

* 노드 수를 3에서 4로 변경하고 `RESIZE`를 클릭합니다.

<br>

**노드 수가 변경된 모습**

![image-20230623175151254](/images/2023-06-20-(실습03)Deploying Google Kubernetes Engine/image-20230623175151254.png) 

<br><br>

## Task 3. 샘플 워크로드 배포

이 과제에서는 **Google Cloud Console을 사용하여 nginx 웹 서버를 실행하는 Pod를 샘플 워크로드로 배포**합니다.

* `Kubernetes Engine > Workloads`를 클릭합니다.

* `Deploy`를 클릭하여 배포 생성 마법사를 표시합니다.

* `Continue`를 클릭하여 기본 컨테이너 이미지 `nginx:latest`를 수락합니다. 이 작업은 최신 버전의 nginx를 실행하는 단일 컨테이너를 갖는 **3개의 Pod를 배포**합니다.

* 창 하단으로 스크롤하고, 구성 세부사항을 기본값으로 두고 `Deploy` 버튼을 클릭합니다.

<br>

**기본 워크로드 배포 결과**

* 앞전에 생성한 클러스터에 워크로드 배포를 통해서 Pod 3개가 잘 배포되었음을 알 수 있습니다.

![image-20230623175941422](/images/2023-06-20-(실습03)Deploying Google Kubernetes Engine/image-20230623175941422.png) 

<br><br>

## Task 4. Google Cloud Console에서 워크로드 세부사항 확인

이 과제에서는 **Google Cloud Console에서 GKE 워크로드의 세부사항을 직접 확인**합니다.

<br>

### 1. 워크로드 세부사항

* `Kubernetes Engine > Workloads` 에서 앞전에 생성한 `nginx-1`을 클릭합니다.
  * 이 화면은 리소스 사용량 차트, 로그 링크, 이 워크로드와 연관된 Pod의 세부사항 등 워크로드에 대한 개요 정보를 표시합니다.

* `nginx-1` 워크로드의 `Details` 탭을 클릭합니다. 
  * 이 화면은 Pod 사양, Pod 복제본의 수와 상태, 수평 Pod 자동 조정기에 대한 세부사항 등 워크로드에 대한 더 많은 세부사항이 표시됩니다.

* `Revision History` 탭을 클릭합니다. 
  * 이 화면은 이 워크로드에 대한 수정 내역이 표시됩니다.

* `Events` 탭을 클릭합니다.
  * 이 화면은 이 워크로드와 관련된 이벤트 목록이 표시됩니다.

* `YAML` 탭을 클릭합니다. 
  * 이 화면은 이 샘플 워크로드를 정의하고 완전히 구성하는 데 필요한 모든 구성 요소와 YAML 파일이 제공됩니다.

<br>

### 2. Pod 세부사항

* `Overview` 탭을 클릭하고, `Managed Pods` 섹션으로 스크롤하여 Pod의 이름을 클릭하고 해당 Pod의 세부사항 페이지를 확인합니다.
  * Pod 세부사항 페이지는 Pod 구성 및 리소스 사용량, Pod가 실행 중인 노드에 대한 정보를 제공합니다.

* Pod 세부사항 페이지에서 `Events`와 `Logs` 탭을 클릭하여 이벤트 세부사항과 Cloud Operations의 컨테이너 로그에 대한 링크를 확인할 수 있습니다.

* `YAML`탭을 클릭하여 Pod 구성에 대한 자세한 YAML 파일을 확인합니다.

<br><br>

## 마무리

**Cluster, Pod, Workload, Node, Cluster Standard 모드 이렇게 5가지가 궁금했고 이것들의 개념만 간략히 정리하겠습니다.**

**참고로 쿠버네티스는 컨테이너 단위가 아닌 포드 단위로 클러스터를 관리하며, 포드는 한개 이상의 컨테이너 그룹을 의미합니다.  **

**따라서 가장 작은 단위이므로 하나의 포드는 온전한 하나의 애플리케이션이 됩니다.**

* 포함관계는 `Region > Zone > Cluster > Node > Pod` 형태가 기본적인 형태입니다.
* Cluster는 한개 이상의 `Control Plane`과 `워커 노드`들을 가집니다.
  * Control Plane은 `워커 노드`를 제어하는 프로세스 묶음으로 `마스터 노드` 집합입니다.
  * 이 노드들은 쿠버네티스의 워커 머신입니다. (가상, 물리 모두 가능)
* Node는 여러개의 Pod를 가질 수 있습니다.
* Pod는 한개 이상의 컨테이너 그룹을 의미합니다.
  * 대부분의 포드는 1개의 컨테이너로 온전한 애플리케이션 역할을 수행합니다.

* Workload는 쿠버네티스에서 구동되는 애플리케이션(pod 집합)으로 정의합니다.
  * k8s은 Pod를 일일이 관리하는 노동을 보완하기위해서 Pod 집합을 관리하기 위한 **Workload Resources**를 사용합니다. 
  * 각 Resource는 Pod가 지정한 상태와 일치하는지 확인합니다.
  * Workload resources
    * ReplicaSet의 상위개념인 Deployment를 사용하는것이 권장되고 있고,
    * Cron과 같은 작업도 이곳에서 진행

* 마지막 Cluster Standard는 GKE 모드 유형(Autopilot, Standard) 중 1가지입니다.
  * Standard 모드는 Node pool로 다양한 종류의 노드 관리 (쿠버네티스가 아닌 GKE의 기능)
    - Node pool : 메모리양이나 CPU 세대와 같은 구성을 고유하는 클러스터 내 노드의 하위 집합입니다.
    - 워크로드를 위해 사용자는 원하는 노드풀로 라벨을 지정하기만 하면 된다.
  * Autopilot 은 대부분을 자동으로 설정 해주기 때문에, 특별한 이유가 없는한 이 유형을 권장하고 있습니다.
