---
title:  "[실습-중급01]Use Google Cloud Console & Cloud Shell"
categories : [Kubernetes_Mid]
tag : [구글 클라우드, GCP, GCC, 구글 클라우드 콘솔, 클라우드 쉘, 쿠버네티스, Kubernetes, 구글 클라우드 스터디 잼, 스터디 잼 중급]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
typora-root-url: ../../..
---



# Use Google Cloud Console, Cloud Shell

**첫번째 실습은 `Google Cloud Console, Cloud Shell` 을 사용해서 `Bucket, VM, IAM` 을 생성해보고 권한에 따른 접근을 확인해보는 실습입니다.**

**아래 4개의 Task를 해결하면서 간접적으로 경험해 봅시다.**

<br>

**Objectives(목표)**

* Learn how to access the `Cloud Console` and `Cloud Shell`
* Become familiar with the `Cloud Console`
* Become familiar with `Cloud Shell` features, including the `Cloud Shell code editor`
* Use the Cloud Console and Cloud Shell to `create buckets and VMs and service accounts`
* Perform `other commands in Cloud Shell`

<br><br>

## Task 1. Google Cloud Console 탐색하기

**`Google Cloud Console`은 Google Cloud의 모든 리소스와 서비스를 관리하는 데 사용되는 웹 기반 인터페이스입니다. 이 과제에서는 Google Cloud Console로 Bucket, VM, IAM 을 생성해봅니다.**

<br>

### 1. Google Cloud Console 접속

**접속화면**

![image-20230621003916367](/images/2023-06-20-(실습01)Use Google Cloud Console & Cloud Shell/image-20230621003916367.png) 

<br>

### 2. Create A Bucket

* **Navigation menu > Cloud overview > Dashboard** 의 Project ID 기억
  * Projcet ID : qwiklabs-gcp-03-076d173bb0ac
* **Navigation menu > Cloud Storage > Buckets** 에서 CREATE
  * **Name** 을 위에서 구한 **Project ID**로 설정
  * **Choose how to control access to objects** 로 들어가서 **Enforce public access prevention on this bucket** 을 uncheck 하고, **Fine-grained** 를 select
    * 참고 : Public Access를 가능하게 바꾼것이다.

<br>

**설정 모습**

![image-20230621004655112](/images/2023-06-20-(실습01)Use Google Cloud Console & Cloud Shell/image-20230621004655112.png) 

<br>

**버킷 생성 완료**

![image-20230621004938410](/images/2023-06-20-(실습01)Use Google Cloud Console & Cloud Shell/image-20230621004938410.png) 

<br>

### 3. Create A VM

**Google Compute Engine은 Google의 데이터 센터와 네트워크에서 실행되는 가상 머신을 서비스로 제공합니다. `Google Kubernetes Engine(=GKE)`은 아키텍처의 구성요소로 Compute Engine을 사용하므로 Compute Engine에 대해 알아봅니다.**

* **Navigation menu > Compute Engine > VM instances** 에서 Create instance
  * **Name** 은 `first-vm` 로 임의로 설정
  * **Region** 은 **us-central1 선택**
  * **Zone** 은 **us-central1-c 선택**
  * **Machine type**은 **e2-micro (2 shared vCPU) 선택**
    * 저렴한 공유 코어 VM을 사용하기 때문
  * **Firewall** 설정에서 **Allow HTTP traffic** 체크

<br>

**VM 생성 모습**

![image-20230621005744352](/images/2023-06-20-(실습01)Use Google Cloud Console & Cloud Shell/image-20230621005744352.png) 

<br>

**HTTP 트래픽을 허용한 결과**

* 응답 없음이 아닌 연결 거부로 정상임을 알 수 있음
* "연결거부"의 경우 VM에 대한 HTTP 트래픽을 위해 열려 있는 방화벽 포트가 있지만 거기에서 실행 중인 웹 서버가 없기 때문에 발생!!

![image-20230621015712647](/images/2023-06-20-(실습01)Use Google Cloud Console & Cloud Shell/image-20230621015712647.png) 

<br>

### 4. Create an IAM service account

**IAM 서비스 계정은 개별 사용자가 아닌 애플리케이션이나 가상 머신에 속하는 특별한 유형의 Google 계정을 의미하며, 관리자가 사용자에게 특정 리소스에 조치를 취할 수 있는 권한을 부여할 수 있어 Google Cloud 클라우드 리소스를 중앙에서 관리하는 데 필요한 완전한 제어와 가시성을 얻을 수 있습니다.**

* **Navigation menu > IAM & admin > Service accounts** 에서 **Create service account**
  * Name : `test-service-account` 로 설정
* **Grant this service account access to project** 설정에서 **Basic > Editor** 권한을 적용 후 **Create**
* 생성 후에 우리가 만든 `test-service-account` 의 오른쪽에 **Dot(...) 클릭 >  Manage keys**
  * **ADD Key > CREATE > JSON > CREATE** 실행시 자동으로 해당 JSON 다운

<br>

**키 생성 모습**

![image-20230621010623462](/images/2023-06-20-(실습01)Use Google Cloud Console & Cloud Shell/image-20230621010623462.png) 

<br><br>

## Task 2. Cloud Shell 탐색하기

**`Cloud Shell` 은 Google Cloud Console 내에 내장된 Interactive Shell 환경입니다. 이 과제에서는 Cloud Shell로 Bucket, VM, IMA을 생성해봅니다. 아래는 Cloud Shell의 제공 기능입니다.**

- Temporary Compute Engine VM
- Command-line access to the instance through a browser
- 5 GB of persistent disk storage (`$HOME dir`)
- Preinstalled Cloud SDK and other tools
- `gcloud`: for working with Compute Engine, Google Kubernetes Engine (GKE) and many Google Cloud services
- `gsutil`: for working with Cloud Storage
- `kubectl`: for working with GKE and Kubernetes
- `bq`: for working with BigQuery
- Language support for Java, Go, Python, Node.js, PHP, and Ruby
- Web preview functionality
- Built-in authorization for access to resources and instances

<br>

### 1. Open Cloud Shell

**아래 그림의 오른쪽 상단에 아이콘이 존재**

![image-20230621011045090](/images/2023-06-20-(실습01)Use Google Cloud Console & Cloud Shell/image-20230621011045090.png) 

<br>

### 2. 환경설정 및 credentials file 등록

**아래 환경변수 설정을 먼저 진행**

```bash
MY_BUCKET_NAME_1="qwiklabs-gcp-03-076d173bb0ac" // Task1 에서 만든 버킷이름
MY_BUCKET_NAME_2="qwiklabs-gcp-test123" // 새로 만들 버킷이름(유니크하게)
MY_REGION=us-central1
```

<br>

**Task1에서 만든 IAM 서비스 계정의 작업 증명 파일을 등록**

* Cloud Shell 의 **Dot(...) > Upload** 를 통해서 JSON 파일을 업로드
  * 이때, JSON파일 구분쉽게 `credentials.json` 파일명으로 바꿔서 업로드!
* **만약 업로드를 안한다면??**
  * 아래에서 Cloud Shell로 "버킷"을 만들때 no configured credentials(자격증명없음) 이라는 오류를 발생한다.
  * 사실 여기서 현재 account는 OWNER 일텐데 왜 이렇게 뜨는지는 잘 모르겠다.

<br>

**여기까지 모습**

![image-20230621011843671](/images/2023-06-20-(실습01)Use Google Cloud Console & Cloud Shell/image-20230621011843671.png) 

<br>

### 3. gsutil-Create A Second Cloud Storage Bucket In Cloud Shell

**`Cloud Shell` 에 세팅을 끝내놨으니 이번엔 Google Console이 아니고 여기에서 바로 "버킷, 가상머신" 을 만들어보겠습니다.**

* `gsutil mb gs://$MY_BUCKET_NAME_2` 명령어 사용 및 **Authorize**
  * mb : make bucket
  * gs : google storage

<br>

**두번째 버킷의 생성 모습**

![image-20230621012357942](/images/2023-06-20-(실습01)Use Google Cloud Console & Cloud Shell/image-20230621012357942.png) 

<br>

### 4. gcloud-Create A Second VM In Cloud Shell

**아래 명령어 진행**

```bash
// 환경 설정
MY_ZONE="us-central1-c"
gcloud config set compute/zone $MY_ZONE // 기본 zone으로 등록
MY_VMNAME=second-vm
```

```bash
// VM 생성
gcloud compute instances create $MY_VMNAME \
--machine-type "e2-standard-2" \
--image-project "debian-cloud" \
--image-family "debian-11" \
--subnet "default"
```

<br>

**두번째 VM 생성된 모습**

![image-20230621013336530](/images/2023-06-20-(실습01)Use Google Cloud Console & Cloud Shell/image-20230621013336530.png) 

<br>

**첫번째 VM과는 다르게 따로 HTTP 트래픽 허용을 안한 두번째 VM**

* "연결거부"가 아닌 "응답없음" 이 발생하는게 정상

![image-20230621015925419](/images/2023-06-20-(실습01)Use Google Cloud Console & Cloud Shell/image-20230621015925419.png) 

<br>

### 5. gcloud-Create A Second IAM service account

**아래 명령어 사용!!**

```bash
gcloud iam service-accounts create test-service-account2 --display-name "test-service-account2"
```

<br>

**Project Viewer 권한을 부여**

```bash
gcloud projects add-iam-policy-binding $GOOGLE_CLOUD_PROJECT --member serviceAccount:test-service-account2@${GOOGLE_CLOUD_PROJECT}.iam.gserviceaccount.com --role roles/viewer
```

<br>

**우리가 이때까지 만든 IAM 2개의 역할 확인**

![image-20230621020402942](/images/2023-06-20-(실습01)Use Google Cloud Console & Cloud Shell/image-20230621020402942.png) 

<br><br>

## Task 3. Cloud Shell에서 Cloud Storage 작업하기

**`Cloud Storage` 는 Google Cloud의 객체 스토리지 서비스입니다. 이 과제에서는 Google이 제공해주는 이미지를 Cloud Shell과 Bucket에 저장 및 사용법을 배워봅니다.**

<br>

### 1. 구글에서 제공하는 "고양이" 이미지 저장

**Cloud Shell, Bucket1, Bucket2에 각각 저장해보겠습니다.**

```bash
// 구글제공 이미지 -> Cloud Shell에 복제
gsutil cp gs://cloud-training/ak8s/cat.jpg cat.jpg
```

```bash
// Cloud Shell -> Bucket1에 복제
gsutil cp cat.jpg gs://$MY_BUCKET_NAME_1
```

```bash
// Bucket1 -> Bucket2에 복제
gsutil cp gs://$MY_BUCKET_NAME_1/cat.jpg gs://$MY_BUCKET_NAME_2/cat.jpg
```

<br>

**생성된 "이미지"**

![image-20230621022547543](/images/2023-06-20-(실습01)Use Google Cloud Console & Cloud Shell/image-20230621022547543.png) 

<br>

### 2. Cloud Storage 접근 권한 확인

**먼저, Bucket1의 접근 권한 확인 명령어**

* 접근 권한이 여러개가 있음을 알 수 있다.

```bash
gsutil acl get gs://$MY_BUCKET_NAME_1/cat.jpg  > acl.txt
cat acl.txt
```

```bash
// 결과
[
  {
    "entity": "project-owners-560255523887",
    "projectTeam": {
      "projectNumber": "560255523887",
      "team": "owners"
    },
    "role": "OWNER"
  },
  {
    "entity": "project-editors-560255523887",
    "projectTeam": {
      "projectNumber": "560255523887",
      "team": "editors"
    },
    "role": "OWNER"
  },
  {
    "entity": "project-viewers-560255523887",
    "projectTeam": {
      "projectNumber": "560255523887",
      "team": "viewers"
    },
    "role": "READER"
  },
  {
    "email": "google12345678_student@qwiklabs.net",
    "entity": "user-google12345678_student@qwiklabs.net",
    "role": "OWNER"
  }
]
```

<br>

**접근권한을 "개인"으로 줄여보는 명령어**

```bash
gsutil acl set private gs://$MY_BUCKET_NAME_1/cat.jpg
```

```bash
// 새로운 ACL 확인
gsutil acl get gs://$MY_BUCKET_NAME_1/cat.jpg  > acl-2.txt
cat acl-2.txt
```

```bash
// 결과
[
  {
    "email": "google12345678_student@qwiklabs.net",
    "entity": "user-google12345678_student@qwiklabs.net",
    "role": "OWNER"
  }
]
```

<br>

### 3. Service Account 변경

**Cloud Shell에서 계정을 바꾼후 권한에 맞게 "이미지" 접근의 가능여부를 확인하는게 목적이다.**

`gcloud config list` 를 통해서 현재 account 를 확인

```bash
// 결과
[component_manager]
disable_update_check = True
[compute]
gce_metadata_read_timeout_sec = 30
zone = us-central1-a
[core]
account = google12345678_student@qwiklabs.net
disable_usage_reporting = False
project = qwiklabs-Google Cloud-1aeffbc5d0acb416
[metrics]
environment = devshell
Your active configuration is: [cloudshell-16441]
```

<br>

`gcloud auth activate-service-account --key-file credentials.json` 로 아까 `credentials.json` 으로 파일 업로드한것으로 변경

다시 `gcloud config list` 를 통해서 현재 account 를 확인   
=> **처음에 Task1 에서 만든 계정으로 변경됨**

```bash
[component_manager]
disable_update_check = True
[compute]
gce_metadata_read_timeout_sec = 30
zone = us-central1-a
[core]
account = test-service-account@qwiklabs-Google Cloud-1aeffbc5d0acb416.iam.gserviceaccount.com
disable_usage_reporting = False
project = qwiklabs-Google Cloud-1aeffbc5d0acb416
[metrics]
environment = devshell
Your active configuration is: [cloudshell-16441]
```

<br>

**이번에는 `gcloud auth list` 를 통해서 권한 계정 리스트를 확인**

* 아래는 예시일 뿐이고, 권한 계정들을 확인 가능

```bash
Credentialed Accounts
ACTIVE:
ACCOUNT: student-03-5165fd82c14b@qwiklabs.net
To set the active account, run:
    $ gcloud config set account `ACCOUNT`
```

<br>

### 4. 권한 여부에 따른 "이미지" 접근 가능성

**현재 `test-service-account` 계정으로 활성화 된 상태에서 아래 명령어를 수행해보면??**

**`gsutil cp gs://$MY_BUCKET_NAME_1/cat.jpg ./cat-copy.jpg`**

* 아래처럼 에러가 발생해야합니다.
* 왜냐하면 아까전에 "버킷1" 의 이미지의 접근 권한을 "개인"인 owner 로 두었기 때문입니다.

```bash
Copying gs://test-bucket-123/cat.jpg...
AccessDeniedException: 403  KiB]
```

<br>

**그럼 "버킷2" 의 이미지의 경우에는??**

**`gsutil cp gs://$MY_BUCKET_NAME_2/cat.jpg ./cat-copy.jpg`**

* 아래처럼 정상 접근 가능합니다.
* 왜냐하면 "버킷2" 에는 따로 이미지 접근 권한을 건드리지 않았기 때문입니다.

```bash
Copying gs://test-bucket-123/cat.jpg...
- [1 files][ 81.7 KiB/ 81.7 KiB]
Operation completed over 1 objects/81.7 KiB.
```

<br>

**"버킷1" 의 이미지를 접근하기 위해 "개인"인 owner 로 변경하려면 아래 명령어를 수행**

* **`gcloud config set account [USERNAME]` 단, USERNAME은 사용자의 NAME을 의미**
* 이후 똑같이 "버킷1" 의 이미지를 접근시 정상 구동

<br>

**"버킷1" 의 이미지를 인증되지 않은 모든 사람들이 접근가능하게 하는것은??**

**`gsutil iam ch allUsers:objectViewer gs://$MY_BUCKET_NAME_1`** 

* 참고: Cloud Storage에서 공개 웹사이트 콘텐츠를 호스팅하기 위한 적절한 설정입니다.
* 따라서 해당 "버킷1" 스토리지에 "이미지" URL을 웹에 접근하면 접속이 가능합니다.

<br><br>

## Task 4. Cloud Shell 코드 에디터 탐색하기

**`Cloud Shell에는 코드 에디터 기능` 이 내장되어 있습니다. 이 과제에서는 Cloud Shell의 코드 에디터를 사용하여 코드를 작성하고 실행하는 방법을 배웁니다.**

<br>

### 1. Open Code Editor

**Cloud Shell 에서 Open Editor 아이콘을 클릭해서 간단히 켤 수 있다.**

* Cloud Shell 에서 `git clone https://github.com/googlecodelabs/orchestrate-with-kubernetes.git` 을 실행
* 다운 받은 파일을 **Code Editor**의 왼쪽 면에서 볼 수 있다.
* 파일 중에서 `cleanup.sh` 파일을 `echo Finished cleanup!` 로 수정하고, 
* 다시 **Cloud Shell** 에서 `cd orchestrate-with-kubernetes` 로 경로 접근 및 `cat cleanup.sh` 로 파일을 열어서 `Finished cleanup!` 가 정상 출력되는지 확인할 수 있다.

<br>

### 2. index.html 생성

**아래 그림처럼 index.html을 생성합니다.**

![image-20230621030200322](/images/2023-06-20-(실습01)Use Google Cloud Console & Cloud Shell/image-20230621030200322.png) 

<br>

**index.html 코드**

* 단, 아래 src 소스 부분의 값은 `https://storage.googleapis.com/qwiklabs-Google Cloud-1aeffbc5d0acb416/cat.jpg` 로 이전에 "고양이" 이미지 링크로 수정 

```html
<html><head><title>Cat</title></head>
<body>
<h1>Cat</h1>
<img src="REPLACE_WITH_CAT_URL">
</body></html>
```

<br>

### 3. SSH - nginx 설치

**이후 웹으로 "고양이" 이미지를 띄우기 위해서 이전에 http 트래픽 허용했던 VM 1을 접근합니다.**

**first-vm > SSH** 로 접근해서 VM에서 열리는 SSH 로그인 창에 **nginx** 웹 서버를 설치합니다.

```bash
sudo apt-get remove -y --purge man-db
sudo touch /var/lib/man-db/auto-update
sudo apt-get update
sudo apt-get install nginx
```

<br>

**이후 Cloud Shell에서 아까 만들었던 index.html을 first-vm 가상머신으로 복제합니다.**

```bash
gcloud compute scp index.html first-vm:index.nginx-debian.html --zone=us-central1-c
```

<br>

**마지막으로 생성한 nginx-debian.html을 `nginx` 웹 서버의 문서 루트인 /var/www/html로 파일을 복제합니다.**

**이후 "이미지" 링크가 아닌, first-vm 의 External IP 로 접근하면 "고양이" 이미지가 존재하는 웹을 볼 수 있습니다.**

<br><br>

## 마무리

**실습을 해보니 `Google Cloud Storage Bucket, Google Cloud VM (가상 머신), Cloud Shell` 각각은 저장소들이 존재했고, 특히 Cloud Shell은 임시 저장소 및 영구 저장소가 있는데 임시 저장소 부분은 일정시간이 지나면 사라진다는 것을 기억하자.**

**모두 저장 기능을 제공하지만, 그 용도와 저장 위치, 데이터 접근 방식 등에 있어서 각기 달랐다. 사용자가 필요로 하는 기능과 목적에 따라 적절한 서비스를 선택하여 사용하자.**
