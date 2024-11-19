---
title:  "[실습-초급04]Managing Deployments Using Kubernetes Engine"
categories : [Kubernetes_Bas]
tag : [구글 클라우드, GCP, GCC, 구글 클라우드 콘솔, 클라우드 쉘, 쿠버네티스, Kubernetes, 구글 클라우드 스터디 잼, 스터디 잼 초급]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
typora-root-url: ../../..
---



**네 번째 실습은** `yaml로 배포, 배포 확장 및 업데이트 방법(Rolling, Canary, Blue-green)` **을 배워 봅니다.**

**여기서는 다양한 kubectl tool 사용 소개와 업데이트 방법을 중점으로 공부하게 됩니다.**

**아래 5개의 Task를 해결하면서 간접적으로 경험해 봅시다.**

<br>

**Objectives(목표)**

* Practice with kubectl tool
* Create deployment yaml files
* Launch, update, and scale deployments
* Practice with updating deployments and deployment styles

<br>

<br>

## Task 1. Learn about the deployment object

`compute zone` **을 먼저 세팅합니다.**

**그리고** `gstuil` **명령어를 활용해서 예제 코드들을 다운받습니다!**

<br><br>

### 1. Google Cloud Console -> Cloud Shell 접속

**Google Cloud Console 화면**

![image](https://github.com/user-attachments/assets/416e3c00-9c47-43dc-8137-9fbfb04e8811) 

<br>

**Cloud Shell 실행 화면(세팅)**

* `gcloud config set compute/zone Zone`
* **예제 코드들 다운(클론)**
  * `gsutil -m cp -r gs://spls/gsp053/orchestrate-with-kubernetes .`
  * `cd orchestrate-with-kubernetes/kubernetes`

* `gcloud container clusters create bootcamp \
    --machine-type e2-small \
    --num-nodes 3 \
    --scopes "https://www.googleapis.com/auth/projecthosting,storage-rw"`
  * 쿠버네티스 클러스터 생성 -> node 3개 생성  

<br>

**Deployment 객체의 자세한 내용을 볼 수 있습니다.**

* `kubectl explain deployment`
* `kubectl explain deployment --recursive`
* `kubectl explain deployment.metadata.name`

![image](https://github.com/BH946/bh946.github.io/assets/80165014/479c4459-ed59-40b5-8935-18d7b2b47465) 

<br>

<br>

## Task 2. Create a deployment

**yaml파일로 생성해보자!**

<br><br>

### 1. auth.yaml 파일 수정

**`deployments/auth.yaml` 파일에서 auth버전을 2.0.0->1.0.0으로 수정 후 배포를 생성하자!**

* 본인은 vim 에디터로 수정
* `vim deployments/auth.yaml` -> `i 명령어` -> 수정
* `esc` -> `:wq 명령어` -> 탈출(저장하면서 탈출)
* `cat deployments/auth.yaml` : 출력해보기

![image](https://github.com/BH946/bh946.github.io/assets/80165014/9820244b-451b-48c9-8593-2357dd1a2870) 

<br><br>

### 2. 배포 생성!

**Kubernetes를 생성하고 배포해봅시다.**

* `kubectl create -f deployments/auth.yaml` 
  * 위에서 봤듯이 replica가 1이므로 Pod도 1개 자동 생성!
* `kubectl get deployments`
* `kubectl get replicasets`
* `kubectl get pods` 
* `kubectl create -f services/auth.yaml` **: 서비스까지 생성 - 외부노출 위해**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/a6845bba-6e14-4321-b400-4aee1aeb969d) 

<br>

**hello, frontend 도 추가 배포 해보자**

* kubectl create -f deployments/hello.yaml   
  kubectl create -f services/hello.yaml
* kubectl create secret generic tls-certs --from-file tls/  
  kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf  
  kubectl create -f deployments/frontend.yaml  
  kubectl create -f services/frontend.yaml
  * frontend 프로젝트에는 tls, configmap이 추가로 필요!

<br><br>

### 3. TEST

**frontend를 확인해보자!**

* `kubectl get services frontend` : 외부IP 확인
* `curl -ks https://<EXTERNAL-IP>`

![image](https://github.com/BH946/bh946.github.io/assets/80165014/306701ad-c9d9-4f20-b91f-1eebbdccf6b1) 

<br>

**물론 외부IP 없이 한 줄로 해결 가능(자동으로 외부IP 기입)**

* `````
  curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"` 
  `````

<br><br>

### 4. 배포확장

**replicas 를 수정해서 배포 Pod를 늘려보자!**

* `kubectl explain deployment.spec.replicas` : 참고용
* `cat deployments/hello.yaml` : 아까 배포한 hello를 확인해보자.
  * replicas 확인해보면 3임을 알 수 있다.

![image](https://github.com/BH946/bh946.github.io/assets/80165014/5fb92698-0ecf-4ae7-9f29-28b76695ea6b) 

<br>

**replicas 를 3->5->3로 수정해보자!**

* `kubectl scale deployment hello --replicas=5`
* `kubectl get pods | grep hello- | wc -l`
  * Pod가 5개면 성공!
* `kubectl scale deployment hello --replicas=3`
* `kubectl get pods | grep hello- | wc -l`
  * Pod가 3개면 성공!

<br>

<br>

## Task 3. Rolling update

**업데이트 3가지를 소개할 텐데, 첫 번째로 Rolling update를 소개하겠습니다.**

`Rolling update(=순차적 업데이트)` **는 배포가 새 버전으로 업데이트되면 새로운 ReplicaSet이 만들어지고, 이전 ReplicaSet의 복제본이 감소하면서 새 ReplicaSet의 복제본 수가 천천히 증가하는 방식이다.**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/bb77ab8a-233c-430e-b92e-fedcf3c330f4) 

<br><br>

### 1. Trigger a rolling update

**구성 파일의 내용을 변경하면 됩니다.**

* `kubectl edit deployment hello` : 로 1.0.0을 2.0.0으로 수정하자!
* `kubectl get replicaset` : 로 새로 복제되는 모습을 확인!
* `kubectl rollout history deployment/hello` : 기록 확인!

![image](https://github.com/BH946/bh946.github.io/assets/80165014/43b27f3c-8c80-49b2-b64c-636638d3b847) 

**hello replicaset이 2개 생성! 그리고 버전1에서 새로운 버전2도 생성됨을 알 수 있습니다.**

<br><br>

### 2. Pause a rolling update

**새로운 업데이트를 일시중지 가능합니다.**

* `kubectl rollout pause deployment/hello` : 중지합니다.
* `kubectl rollout status deployment/hello` : 출시 상태를 확인합니다.
* `kubectl get pods -o jsonpath --template='{range .items[*]}{.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}` : Pod를 직접 확인합니다.
  * 아까 새롭게 생성된 Pod는 없음을 알 수 있습니다.

![image](https://github.com/BH946/bh946.github.io/assets/80165014/36bb0001-8b86-47ee-bfec-0930f7e7c416) 

<br><br>

### 3. Resume a rolling update

**Rolling Update(=순차적 업데이트) 이다 보니까 순차적으로 업데이트 되므로 위의 Pod에서 새로운 업데이트 Pod로 바뀌었을 가능성도 있습니다.**

* 해결1) 일시중지 한 업데이트를 다시 시작할 수 있습니다.

* 해결2) 이전 버전으로 롤백 가능합니다.

**해결1인 일시중지 한 업데이트를 다시 시작입니다.**

* `kubectl rollout resume deployment/hello`

* `kubectl rollout status deployment/hello`

<br><br>

### 4. Rollback an update

**해결2인 롤백입니다.**

* `kubectl rollout undo deployment/hello`
* `kubectl rollout history deployment/hello`
* `kubectl get pods -o jsonpath --template='{range .items[*]}{.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'`
  * 아래 사진을 보면 history로 새로운 버전인 3버전으로 생성됨을 알 수 있습니다.
  * 또한, Pod를 보면 실시간으로 2.0.0인 수정 버전에서 1.0.0인 이전 버전으로 Pod가 변경되어가는 모습을 볼 수 있습니다.

![image](https://github.com/BH946/bh946.github.io/assets/80165014/72ffb8bf-bbbd-4466-98ee-62fbd2f642e4) 

<br>

<br>

## Task 4. Canary deployments

**2번째 Canary배포는 일부 사용자들에게만 업데이트 배포를 먼저해서 사용하게 하고, 이상이 없으면 전체 적용을 하는 방식입니다.**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/a6a6832e-0cb9-415a-a425-cd494018515e) 

<br><br>

### 1. hello-canary.yaml

`cat deployments/hello-canary.yaml` **: hello-canary로 테스트해보겠습니다.**

**먼저 배포합니다.**

* kubectl create -f deployments/hello-canary.yaml  
  kubectl get deployments    

  ````
  curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
  ````

![image](https://github.com/BH946/bh946.github.io/assets/80165014/60ed13f8-6f29-4544-87cc-5eb76feac6ac) 

<br><br>

### 2. TEST

**hello-canary.yaml**은  ` selector:    matchLabels:      app: hello ` 가 설정되어있습니다.

아까 서비스 등록한 hello 서비스는 app:hello 선택기인 Pod를 전부 사용합니다.

**따라서 hello Pod3개와 hello-canary Pod1개를 배포하는 상태가 되며 사용자들은 25% 확률로 hello-canary 로 배포된 Pod로 접근하게 됩니다!**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/71ab2c3e-c618-4c6c-996a-8f7263990ed3) 

<br><br>

### 3. 세션 어피니티(Sticky session)

**그런데 hello-canary로 접근한 사용자는 다시 접속할 때도 hello가 아닌 hello-canary로 접근해야 사용자의 혼란을 막을 수 있습니다.**

**이때, 사용자에게 한 배포에 "고정" 시켜야 합니다. 이를 IP 고정을 통해서 해결 가능하다고 합니다.(따로 실습은 안합니다!)**

- `sessionAffinity: ClientIP` 를 서비스 단 yaml에 추가하면 됩니다.

<br>

<br>

##  Task 5. Blue-green deployments

**세 번째 방식은 Blue/Green 배포 방식인데, 이전버전과 새로운버전을 둘다 배포하고 서비스로 배포 버전을 선택한다고 생각하면 됩니다.**

**하지만 Blue/Green 배포의 주요 단점은 애플리케이션을 호스팅하려면 클러스터에 최소 2배의 리소스가 필요하다는 점입니다.**

* 아래 그림은 서비스가 버전 1.0.0을 가리키고 있습니다.

![image](https://github.com/BH946/bh946.github.io/assets/80165014/6b29c7c4-9fed-4efc-8fc0-6b99aac7d606) 

<br><br>

### 1. Blue/Green 배포 테스트

**Blue/Green 배포 테스트**

* `kubectl apply -f services/hello-blue.yaml` **: hello의 버전 1.0.0이 배포중인 상태인데 이녀석을 가리키게 하는 서비스 입니다. 이를 적용!**

* `kubectl create -f deployments/hello-green.yaml` : 이후 새로운 버전을 배포합니다.

* ```
  curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
  ```

  * 배포를 하더라도 hello-blue서비스 때문에 1.0.0을 여전히 가리킵니다.

* `kubectl apply -f services/hello-green.yaml` **: 버전 2.0.0을 가리키는 서비스를 적용합니다!**

* ```
  curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
  ```

  * 버전이 2.0.0을 가리키게 수정되었음을 확인할 수 있습니다.

![image](https://github.com/BH946/bh946.github.io/assets/80165014/4c4f7006-6482-402b-acce-f541023ec86d) 

<br><br>

### 2. 롤백 테스트

**롤백은 너무나도 간단합니다.**

**왜냐하면 이전 버전을 배포중이기 때문에 이전버전을 가리키는 서비스를 바로 적용하면 되기 때문입니다.**

`kubectl apply -f services/hello-blue.yaml` : 이전 버전 가리키는 서비스 적용!

```
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
```

* Pod 확인! 1.0.0이면 성공!

![image](https://github.com/BH946/bh946.github.io/assets/80165014/422a8a36-bae3-4f2f-bbf0-cbb22aa74f03) 

<br>

<br>

## 마무리

**참고) 너무 잘 정리한 글이 있어서 이글의 내용도 함께 참고했다!**

* **[CN_City님](https://cn-c.tistory.com/48?category=1105946#%EC%-E%--%EC%--%--%----%--%EC%--%-C%EC%B-%A-%EC%A-%--%--%EC%--%--%EB%-D%B-%EC%-D%B-%ED%-A%B--Rolling%--update-)**

<br>

**3가지 업데이트 방법을 실습을 통해서 배우니 확실히 이해가 잘되었고, 나중에 꼭 개인 프로젝트를 하게 되면 Rolling Update 방식으로 업데이트 진행해봐야겠다!**
