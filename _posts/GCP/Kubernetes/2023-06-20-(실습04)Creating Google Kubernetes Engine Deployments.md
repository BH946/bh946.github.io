---
title:  "[실습04]Creating Google Kubernetes Engine Deployments"
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

**네번째 실습은 `Cloud Shell` 을 많이 활용하며, 직접 `Deployment Manifests` 를 작성해서 `클러스터` 배포를 합니다. 또한, `Pod 수동 스케일링`이나 `롤아웃, 롤백`을 경험해봅니다.**   

**그리고 `서비스 유형`을 정의해보는 경험을 하고, `Canary`를 배포해봅니다.**

**아래 5개의 Task를 해결하면서 간접적으로 경험해 봅시다.**

<br>

**Objectives(목표)**

* Create **deployment manifests**, deploy to **cluster**, and verify **Pod** rescheduling as nodes are disabled
* Trigger manual **scaling up and down** of Pods in deployments
* Trigger deployment **rollout** (rolling update to new version) and **rollbacks**
* Perform a **Canary deployment**

<br><br>

## Task 1. 배포 매니페스트 생성 및 클러스터에 배포

이 과제에서는 클러스터 내의 **Pod**에 대한 **배포 매니페스트**를 생성합니다.

<br>

### 1. GKE 클러스터 연결

- 환경 변수를 설정하여 Zone과 클러스터 이름을 설정합니다.

  ```bash
  export my_zone=us-central1-a
  export my_cluster=standard-cluster-1
  ```

- `kubectl`의 Bash 자동 완성 스크립트를 현재 실행 중인 셸 세션에 로드하여 자동 완성 기능을 사용할 수 있게 합니다.

  ```bash
  source <(kubectl completion bash)
  ```

- Cloud Shell에서 클러스터에 대한 액세스를 구성합니다.

  ```bash
  gcloud container clusters get-credentials $my_cluster --zone $my_zone
  ```

  - `$my_cluster`라는 이름의 Kubernetes Engine 클러스터의 자격 증명을 가져와서 해당 클러스터와 상호작용할 수 있는 권한을 부여합니다.

  - 이를 통해 로컬 환경에서 Kubernetes 클러스터와 상호작용하고 클러스터 내의 리소스에 접근할 수 있습니다.

    ![image-20230623201405477](/images/2023-06-20-(실습04)Creating Google Kubernetes Engine Deployments/image-20230623201405477.png) 


<br>

### 2. 배포 매니페스트 생성 및 배포

* 실습 파일을 클론하여 랩 Cloud Shell에 가져옵니다.

  ```bash
  git clone https://github.com/GoogleCloudPlatform/training-data-analyst
  ```

* 작업 디렉토리에 대한 바로 가기를 생성합니다.

  ```bash
  ln -s ~/training-data-analyst/courses/ak8s/v1.1 ~/ak8s
  cd ~/ak8s/Deployments/
  ```

* 실습 파일에서 이미 제공하는 '배포 매니페스트' 를 사용합니다.

  ```bash
  kubectl apply -f ./nginx-deployment.yaml
  ```

  * `kubectl get deployments` 를 통해서 배포 목록을 확인합니다.

<br>

**nginx 배포 모습**

* Workloads 를 보면 Pod 3개 생성되는 `nginx-deployment.yaml` 이 잘 동작했음을 알 수 있습니다.
* 또한, 아래 `kubectl get deployments` 를 통해서 1/3 -> 3/3 처럼 실시간 생성 상태도 확인할 수 있었습니다.

![image-20230623201642016](/images/2023-06-20-(실습04)Creating Google Kubernetes Engine Deployments/image-20230623201642016.png) 

<br><br>

## Task 2. 배포에서 Pod 수동 스케일링

Kubernetes에서는 특정 Pod를 원하는 수의 인스턴스로 스케일링할 수 있습니다.

<br>

### 1. Google Cloud Console 에서 Pod 스케일링

* **Navigation menu  > Kubernetes Engine > Workloads** 에 `nginx-deployment` 를 선택합니다.
* **ACTIONS > Scale > Edit Replicas** 에서 1개로 스케일링 합니다.

![image-20230623202209646](/images/2023-06-20-(실습04)Creating Google Kubernetes Engine Deployments/image-20230623202209646.png) 

<br>

### 2. Cloud Shell에서 Pod 스케일링

* `kubectl scale --replicas=3 deployment nginx-deployment` 로 3개로 다시 스케일링 할 수 있습니다.
* `kubectl get deployments` 로 확인합니다.

![image-20230623202310572](/images/2023-06-20-(실습04)Creating Google Kubernetes Engine Deployments/image-20230623202310572.png) 

<br><br>

## Task 3. 배포 롤아웃 및 롤백 트리거

배포의 롤아웃은 배포의 Pod 템플릿이 변경될 때만 트리거됩니다. 이 작업에서는 배포 롤아웃을 트리거하고 롤백을 트리거합니다.

<br>

### 1. Trigger a deployment rollout

* nginx의 버전을 1.9.1로 업데이트하여 롤아웃을 트리거합니다.

  ```bash
  kubectl set image deployment.v1.apps/nginx-deployment nginx=nginx:1.9.1 --record
  ```

* 롤아웃 history를 봅니다.

  ```bash
  kubectl rollout history deployment nginx-deployment
  ```

<br>

**History 모습**

![image-20230623202631803](/images/2023-06-20-(실습04)Creating Google Kubernetes Engine Deployments/image-20230623202631803.png) 

![image-20230623202900984](/images/2023-06-20-(실습04)Creating Google Kubernetes Engine Deployments/image-20230623202900984.png) 

<br>

### 2. Trigger a deployment rollback

* `kubectl rollout undo deployments nginx-deployment` 로 롤아웃을 진행합니다.

* 참고로 아래 명령어를 통해서 원하는 history 내용을 자세히 볼 수도 있습니다.

  ```bash
  kubectl rollout history deployment/nginx-deployment --revision=3
  ```

<br>

**History 모습 입니다.**

![image-20230623203044423](/images/2023-06-20-(실습04)Creating Google Kubernetes Engine Deployments/image-20230623203044423.png) 

<br>

**활성화 된것도 1.7.9 로 돌아왔음을 알 수 있습니다.**

![image-20230623203133504](/images/2023-06-20-(실습04)Creating Google Kubernetes Engine Deployments/image-20230623203133504.png) 

<br><br>

## Task 4. 매니페스트에서 서비스 유형 정의

**이 과제에서는 애플리케이션으로의 인바운드 트래픽(=외부에서 가상서버 내부로 데이터 접근)을 제어하는 `서비스` 를 생성하고 확인합니다. 서비스는 `ClusterIP, NodePort, LoadBalancer 유형` 으로 구성할 수 있습니다.**

**실습으로는 `LoadBalancer` 를 알아보겠습니다.**

<br>

- 실습 파일에서 이미 제공하는 '서비스 타입 매니페스트' 를 사용합니다. (LoadBalancer)

  ```bash
  kubectl apply -f ./service-nginx.yaml
  ```

- `kubectl get service nginx` 를 통해서 LoadBalancer 생성을 확인합니다.

<br>

**생성 전**

![image-20230623203642454](/images/2023-06-20-(실습04)Creating Google Kubernetes Engine Deployments/image-20230623203642454.png) 

<br>

**생성 후**

![image-20230623203852934](/images/2023-06-20-(실습04)Creating Google Kubernetes Engine Deployments/image-20230623203852934.png)

<br>

![image-20230623203742792](/images/2023-06-20-(실습04)Creating Google Kubernetes Engine Deployments/image-20230623203742792.png)

<br>

**서비스 설정을 통해서 외부에서 접근가능한 IP 경로가 제공**

* 이때 로드밸런싱을 통해서 부하를 나누는 효과

![image-20230623203816221](/images/2023-06-20-(실습04)Creating Google Kubernetes Engine Deployments/image-20230623203816221.png) 

<br><br>

## Task 5. Canary 배포

**Canary 배포는 애플리케이션의 새 버전을 테스트하는 데 사용되는 별도의 배포입니다.** 

**단일 서비스는 카나리아와 일반 배포를 모두 대상으로 합니다. 이때, 일부 사용자를 Canary 버전으로 안내하여 새 릴리스의 위험을 완화할 수 있습니다.** 

**제공되는 매니페스트 파일 nginx-canary.yaml은 기본 배포보다 최신 버전의 nginx를 실행하는 단일 포드를 배포합니다. 이 작업에서는 이 새 배포 파일을 사용하여 카나리아 배포를 생성합니다.**

* `nginx-canary.yaml` 이 실습 파일로 제공되며, `apply` 를 하기전에 해당 파일을 분석하겠습니다.

  * 이전 작업에서 배포한 `service-nginx.yaml` 는 **레이블 선택기**를 사용하여 **app: nginx** 레이블이 있는 **Pod를 대상**으로 지정합니다. 

  * 실제로, **일반 배포**에도 **app: nginx** 가 있었고 이번에 배포하는 **Canary 배포**에도 **app: nginx** 레이블이 설정되어 있습니다.

  * 따라서 인바운드 연결은 서비스에 의해 일반 배포 Pod와 Canary 배포 Pod 모두에 배포됩니다. 

  * 참고로 **Canary 배포**에는 **replicas: 1** 로 설정되어 있어서 일반 배포보다 복제본(Pod)이 적기 때문에 일반 배포보다 적은 수의 사용자만 사용할 수 있습니다.

  * 또한, **container**는 **image: nginx:1.9.1** 로 설정하므로 Canary 배포 Pod에 이 컨테이너가 생성될것임을 알 수 있습니다.

  * 아래는 해당 yaml 파일의 내용을 보여줍니다.

    ```bash
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-canary
      labels:
        app: nginx
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
            track: canary
            Version: 1.9.1
        spec:
          containers:
          - name: nginx
            image: nginx:1.9.1
            ports:
            - containerPort: 80
    ```

* 실습 파일을 적용해보겠습니다.

  ```bash
  kubectl apply -f nginx-canary.yaml
  ```

* 그리고 기본 배포(=nginx-deployment) Pod를 0으로 만들어 봅시다.

  ```bash
  kubectl scale --replicas=0 deployment nginx-deployment
  ```

* 이후 `kubectl get deployments` 를 통해서 현재 상황을 볼 수 있습니다.

<br>

**기존 Pod 를 없앤 후 Canary 배포의 Pod 만 있는 상황의 결과**

![image-20230623205829288](/images/2023-06-20-(실습04)Creating Google Kubernetes Engine Deployments/image-20230623205829288.png) 

<br>

**이 상황에서도 로드밸런싱을 통해서 기존 External IP 는 정상 동작 확인**

![image-20230623205936518](/images/2023-06-20-(실습04)Creating Google Kubernetes Engine Deployments/image-20230623205936518.png) 

<br><br>

# 마무리

**이번 실습에서는 "Service, Container의 Image" 와 "Session Affinity" 개념을 정리해보겠습니다.**

* Container는 Pod 내에 포함되며 Image(예로 도커 이미지같은) 를 가지고 있습니다.

  * 이번 실습에서는 배포한 Pod 내에 컨테이너속에 nginx 이미지를 가지고 있었습니다.

* **Service는 일반적으로 외부에서 접근을 위해서 사용됩니다.**

  * Pod의 IP 주소에 외부에서 직접 접근하려면 서비스를 생성하여 Pod의 IP를 노출시키는 편입니다.

* **마지막 Session Affinity 개념이 여기서 제일 중요한 개념입니다.**

  * "세션 어피니티(Session Affinity)"는 특정 클라이언트의 모든 요청이 항상 동일한 Pod에 연결되도록 하는 설정입니다. 이 설정이 없으면, 각 요청은 개별적으로 처리되어 일반 nginx 배포 또는 nginx-canary 배포 중 어느 것에든 연결될 수 있습니다.
  * 이렇게 다른 버전 간에 전환될 가능성이 있는 것은, canary 릴리스에서 기능적인 변화가 큰 경우 문제를 일으킬 수 있습니다. 이를 방지하기 위해, 클라이언트의 첫 요청이 모든 후속 연결에 사용될 Pod를 결정해야 하는 경우, 서비스의 사양에서 sessionAffinity 필드를 **ClientIP**로 설정할 수 있습니다.

  * 즉, "서비스의 사양에서 sessionAffinity 필드를 ClientIP로 설정할 수 있습니다."는 클라이언트의 IP를 기반으로 세션 어피니티를 설정하여, 특정 클라이언트의 모든 요청이 동일한 Pod에 연결되도록 할 수 있다는 것을 의미합니다. 이렇게 하면 클라이언트의 첫 번째 요청이 결정한 Pod가 모든 후속 연결에도 사용됩니다.
