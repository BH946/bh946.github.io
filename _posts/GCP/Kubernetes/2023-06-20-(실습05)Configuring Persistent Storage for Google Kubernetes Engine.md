---
title:  "[실습05]Configuring Persistent Storage for Google Kubernetes Engine"
categories : [Kubernetes]
tag : [구글 클라우드, GCP, GCC, 구글 클라우드 콘솔, 클라우드 쉘, 쿠버네티스, Kubernetes, 구글 클라우드 스터디 잼, 스터디 잼 중급, Docker, 도커, 이미지, GKE, Clusters]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
typora-root-url: ../../..
---



# Configuring Persistent Storage for Google Kubernetes Engine

**다섯번째 실습은 `PersistentVolume(=PV)` 및 `PersistentVolumeClaim(=PVC)`을 설정합니다. 해당 내용들은 스토리지 관련 내용입니다.**

**Persistent Storage 는 영구 스토리지를 의미합니다.**

**아래 4개의 Task를 해결하면서 간접적으로 경험해 봅시다.**

<br>

**Objectives(목표)**

* Create **manifests for PersistentVolumes (PVs) and PersistentVolumeClaims (PVCs)** for `Google Cloud persistent disks` (dynamically created or existing)
* Mount `Google Cloud persistent disk PVCs` as **volumes in Pods**
* Use manifests to create **StatefulSets**
* Mount `Google Cloud persistent disk PVCs` as **volumes in StatefulSets**
* Verify the connection of Pods in StatefulSets to particular PVs as the Pods are stopped and restarted

<br><br>

## Task 1. Create PVs and PVCs

**우리는 대부분 직접 PV객체를 구성하거나, Compute Engine persistent disks 를 만들 필요가 없습니다.  
PVC를 만들면, Kubernetes가 자동으로 PV를 생성하도록 트리거하기 때문입니다.**

**따라서 이 과제에서는 PVC를 생성하는 실습을 진행합니다.**

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

    ![image-20230623201405477](/images/2023-06-20-(실습05)Configuring Persistent Storage for Google Kubernetes Engine/image-20230623201405477.png) 

<br>

### 2. PVC 매니페스트 생성 및 적용

- 제공하는 실습 파일을 다운받기 위해 git clone을 진행합니다.

  ```bash
  git clone https://github.com/GoogleCloudPlatform/training-data-analyst
  ```

- 작업 디렉토리에 대한 바로 가기를 생성합니다.

  ```bash
  ln -s ~/training-data-analyst/courses/ak8s/v1.1 ~/ak8s
  cd ~/ak8s/Storage/
  ```

- 현재 PVC가 설정되어있나 `kubectl get persistentvolumeclaim` 로 확인해보면, 당연히 없다고 할 것입니다.

- 따라서 실습 파일에서 이미 제공하는 'PVC 매니페스트' 를 사용합니다.

  ```bash
  kubectl apply -f pvc-demo.yaml
  ```

  * 다시 `kubectl get persistentvolumeclaim` 를 통해서 생성된 스토리지를 확인합니다.

  - PVC는 hello-web-disk라는 이름의 30GB PVC를 생성하며, 이는 한 번에 하나의 노드에서 읽기-쓰기 볼륨으로 마운트할 수 있습니다.

  - 아래는 적용된 yaml 파일 내용입니다.

    ```yaml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: hello-web-disk
    spec:
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 30Gi
    ```

<br><br>

## Task 2. Mount and verify Google Cloud persistent disk PVCs in Pods

**이 과제에서는 PVC를 Pod에 마운트합니다. 자세한 내용은 아래 설정된 yaml 파일을 참고합니다.**

**또한, 마운트 이후에는 영구적인지 테스트를 진행합니다.**

<br>

### 1. Mount the PVC to a Pod

* 실습 파일에 있는 `pod-volume-demo.yaml` 을 적용할건데, 그전에 어떤 내용인지 보겠습니다.

  * 매니페스트 파일 `pod-volume-demo.yaml` 은 nginx 컨테이너를 배포하고, pvc-demo-volume(=PV)을 Pod에 연결하고 해당 볼륨을 nginx 컨테이너 내부의 /var/www/html 경로에 마운트합니다. 

  * 컨테이너 내부의 이 디렉터리에 저장된 파일은 영구 볼륨에 저장되며 Pod와 컨테이너가 종료되고 다시 생성되더라도 지속됩니다.

    ```yaml
    kind: Pod
    apiVersion: v1
    metadata:
      name: pvc-demo-pod
    spec:
      containers:
        - name: frontend
          image: nginx
          volumeMounts:
          - mountPath: "/var/www/html"
            name: pvc-demo-volume
      volumes:
        - name: pvc-demo-volume
          persistentVolumeClaim:
            claimName: hello-web-disk
    ```

* 바로 적용 해보겠습니다. `kubectl apply -f pod-volume-demo.yaml`

  * 이후 `kubectl get pods` 로 Pod 생성을 확인해보면 정상 동작함을 알 수 있습니다.

  * 먼저 Pod가 생성되고, Container가 생성됨을 알 수 있습니다.

    ![image-20230623220938520](/images/2023-06-20-(실습05)Configuring Persistent Storage for Google Kubernetes Engine/image-20230623220938520.png)  

* 해당 `pvc-demo-pod` 쉘로 접근해서 임시로 `index.html` 을 만든 후 해당 파일이 잘 출력되나 확인합니다.

  ```bash
  kubectl exec -it pvc-demo-pod -- sh
  echo Test webpage in a persistent volume!>/var/www/html/index.html
  chmod +x /var/www/html/index.html
  cat /var/www/html/index.html
  exit
  ```

<br>

### 2. Test the persistence of the PV

- Pod 를 제거합니다. `kubectl delete pod pvc-demo-pod`

  - `kubectl get pods` 로 결과를 확인합니다.

- `kubectl get persistentvolumeclaim` 를 입력하면, 여전히 PVC 존재함을 알 수있고 Pod 삭제해도 제거되지 않았음을 알 수 있습니다.

- 다시 Pod 생성을 위해 `kubectl apply -f pod-volume-demo.yaml` 를 적용하면, 좀 더 빨리 "상태" 가 Running으로 바뀜을 알 수 있습니다.

  - 이는 이미 PVC가 존재하기 때문입니다.

- 마찬가지로 다시 해당 쉘로 접근해서 출력해보면 잘 됨을 알 수 있습니다.

  ```bash
  kubectl exec -it pvc-demo-pod -- sh
  cat /var/www/html/index.html
  exit
  ```

<br><br>

## Task 3. Create StatefulSets with PVCs

**이 과제에서는 StatefulSet에서 PVC를 사용합니다. StatefulSet은 Pod에 고유한 식별자를 부여하는점을 제외하면, 기존 배포와 유사합니다.**

<br>

### 1. Create a StatefulSet

- StatefulSet과 함께 PVC를 사용하려면 기존 PVC와 사용중인 Pod를 삭제해야 하므로 `kubectl delete pod pvc-demo-pod` 를 진행합니다.

* 제공된 파일인 `statefulset-demo.yaml` 를 바로 apply 할건데, 그전에 무슨 내용인지만 간략히 소개하겠습니다.

  * 매니페스트 파일 `statefulset-demo.yaml` 은 **LoadBalancer 서비스와 StatefulSet을 생성**합니다.

  * **StatefulSet**은 이름이 hello-web-disk인 30GB PVC용 volumeClaimTemplate 및 nginx 컨테이너가 포함된 포드의 3개 복제본을 포함해서 생성합니다. 

  * nginx 컨테이너는 이전 작업에서와 같이 /var/www/html에 hello-web-disk라는 PVC를 마운트합니다.

    ```yaml
    kind: Service
    apiVersion: v1
    metadata:
      name: statefulset-demo-service
    spec:
      ports:
      - protocol: TCP
        port: 80
        targetPort: 9376
      type: LoadBalancer
    ---
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: statefulset-demo
    spec:
      selector:
        matchLabels:
          app: MyApp
      serviceName: statefulset-demo-service
      replicas: 3
      updateStrategy:
        type: RollingUpdate
      template:
        metadata:
          labels:
            app: MyApp
        spec:
          containers:
          - name: stateful-set-container
            image: nginx
            ports:
            - containerPort: 80
              name: http
            volumeMounts:
            - name: hello-web-disk
              mountPath: "/var/www/html"
      volumeClaimTemplates:
      - metadata:
          name: hello-web-disk
        spec:
          accessModes: [ "ReadWriteOnce" ]
          resources:
            requests:
              storage: 30Gi
    ```

* `kubectl apply -f statefulset-demo.yaml` 로 바로 적용합니다.

  ```bash
  // 아래 2개가 생성 됩니다.
  service "statefulset-demo-service" created  
  statefulset.apps "statefulset-demo" created
  ```

<br>

### 2. StatefulSets 에서 Pod를 확인

* `kubectl describe statefulset statefulset-demo` 로 StatefulSet 세부사항을 볼 수 있습니다.
* `kubectl get pods` 과 `kubectl get pvc` 를 통해서 Pod, PVC 를 전부 볼 수 있습니다.
  * `kubectl describe pvc hello-web-disk-statefulset-demo-0` 로 특정 PVC를 선택해서 세부사항을 더 자세히 볼 수 있습니다.
  * Pod가 3개 존재해야하며, PVC는 기존꺼와 합해서 4개가 존재해야 합니다.
    * 참고로 StatefulSet이므로 Pod 3개 복제가 각각 따로 구분되게 존재함을 알 수 있습니다.
    * 아래 결과를 확인하세요.

* **결과모습**

  ```bash
  // Pod
  NAME                 READY     STATUS    RESTARTS   AGE
  statefulset-demo-0   1/1       Running   0          6m
  statefulset-demo-1   1/1       Running   0          3m
  statefulset-demo-2   1/1       Running   0          2m
  
  // PVC
  NAME                          STATUS    VOLUME           CAPACITY ACCESS
  hello-web-disk                Bound     pvc-86117ea6-...34   30Gi    RWO
  hello-web-disk-st...-demo-0   Bound     pvc-92d21d0f-...34   30Gi    RWO
  hello-web-disk-st...-demo-1   Bound     pvc-9bc84d92-...34   30Gi    RWO
  hello-web-disk-st...-demo-2   Bound     pvc-a526ecdf-...34   30Gi    RWO
  ```

<br><br>

## Task 4. Verify the persistence of Persistent Volume connections to Pods managed by StatefulSets

**이 과제에서는 StatefulSet에 의해 관리되는 Pod가 중지되고 다시 시작될 때 여전히 특정 PV에 연결이 되는지(지속성, 영구적) 확인합니다.**

- `statefulset-demo-0` 쉘로 접근해서 임시로 `index.html` 을 만든 후 해당 파일이 잘 출력되나 확인합니다.

  ```bash
  kubectl exec -it statefulset-demo-0 -- sh
  echo Test webpage in a persistent volume!>/var/www/html/index.html
  chmod +x /var/www/html/index.html
  cat /var/www/html/index.html
  exit
  ```

* 지속성 테스트를 위해 Pod를 바로 삭제합니다. 단, `statefulset-demo-0` Pod를 삭제해야 합니다!

  ```bash
  kubectl delete pod statefulset-demo-0
  ```

  * `kubectl get pods` 로 삭제를 확인합니다.
  * 그러나 StatefulSet 특성상 다시 Pod가 자동 생성되었음을 확인해야 합니다.

* 이후 다시 해당 쉘에 접근해서 출력을 확인합니다.

  ```bash
  kubectl exec -it statefulset-demo-0 -- sh
  cat /var/www/html/index.html
  exit
  ```

<br>

**실제로 Pod 삭제 후 자동 생성모습, PVC 또한 지속 및 특정 PV에도 여전히 연결해주는 모습**

![image-20230623224230462](/images/2023-06-20-(실습05)Configuring Persistent Storage for Google Kubernetes Engine/image-20230623224230462.png) 

<br><br>

# 마무리

**StatefulSet은 Pod의 그룹을 관리하면서 안정적인 식별자와 네트워크 도메인 이름을 제공하여 상태를 유지해야 하는 애플리케이션을 운영하는 데 사용됩니다.**
