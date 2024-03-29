---
title:  "[Python]Colab 정리"
categories : PY
tag : [코랩, Colab, ipynb, python가상환경]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
---



## Colab(코랩)

* 홈페이지에서 파이썬을 실행할 수 있게 지원해주는 사이트(주피터와 같은 맥락)

<br>

#### 한글이 제대로 나오지 않는 경우?

**주석 해제후 실행**

```python
plt.rc('font', family='NanumBarunGothic')
!sudo apt-get install -y fonts-nanum
!sudo fc-cache -fv
!rm ~/.cache/matplotlib -rf
```

<br>

#### 구글 드라이브 연동

* Colab은 기본적으로 Colab 라이브러리를 포함하고 있다.

<br>

**Colab이 아닌 IDE인 경우 pip 필요**

```python
!python -m pip install -U google-colab
```

<br>

**1. 사용법(자신의 구글 드라이브에 직접 csv 파일 업로드 한 경우**)

```python
from google.colab import drive
drive.mount('/content/gdrive/')
```

```python
path_dataset = "/content/gdrive/MyDrive/....." # 구글 드라이브 경로이다
filename= "부산교통공사_시간대별 승하차인원_20210801.csv"

my_df = pd.read_csv(path_dataset + filename)
print(my_df.head(10))
```

<br>

**2. 사용법(구글 드라이브로 공유된 파일을 접근하기)**

* drive 객체 만들기

  ```python
  # 아래 코드는 공유 파일을 사용하기 위한 코드 이니 그대로 사용하시면 됩니다.
  
  # pyDrive 설치
  # !pip install -U -q PyDrive
  from pydrive.auth import GoogleAuth
  from pydrive.drive import GoogleDrive
  from google.colab import auth
  from oauth2client.client import GoogleCredentials
  
  # Authenticate and create the PyDrive client
  auth.authenticate_user()
  gauth = GoogleAuth()
  gauth.credentials = GoogleCredentials.get_application_default()
  drive = GoogleDrive(gauth)
  ```

<br>

* 구글 드라이브로 공유된 파일 공유해서 가져오기

  ```python
  # 구글드라이브 id를 찾아서 아래 코드에 대입하여 실행시키시기 바랍니다
  # 예) 공유된 폴더 링크가 다음과 같다면 https://drive.google.com/drive/folders/1-EJr-anFISoVb96QYedLx5PklEtm9JSS?usp=sharing
  # id는 1-EJr-anFISoVb96QYedLx5PklEtm9JSS 가 됩니다
  
  # 공유된 파일의 키를 아래에 대체하여 사용하시기 바랍니다
  id= '.......'
  file_list = drive.ListFile({'q': f"'{id}' in parents and trashed=false"}).GetList()
  
  # 공유된 파일리스트 확인하고 데이터를 다운로드 받습니다.
  for file in file_list:
      downloaded = drive.CreateFile({'id': file['id']})
      downloaded.GetContentFile(file['title'])
      print(file['title'])
  
  # 정상적으로 출력되었다면, 왼쪽에 폴더탭에서 정상적으로 파일이 다운로드된 것을 확인할 수있습니다.
  ```



