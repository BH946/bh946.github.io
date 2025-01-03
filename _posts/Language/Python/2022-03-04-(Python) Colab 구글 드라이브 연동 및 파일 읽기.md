---
title:  "[Python] Colab 구글 드라이브 연동 및 파일 읽기"
categories : PY
tag : [코랩, Colab, ipynb, python가상환경]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
---



**Google Colab은 웹 브라우저에서 Python을 실행할 수 있게 해주는 클라우드 기반 Jupyter Notebook 환경이다**

<br>

**참고: 한글이 제대로 나오지 않는 경우?**

**주석 해제후 실행**

```python
plt.rc('font', family='NanumBarunGothic')
!sudo apt-get install -y fonts-nanum
!sudo fc-cache -fv
!rm ~/.cache/matplotlib -rf
```

<br>

<br>

## Google Drive 연동

**Colab은 기본적으로 Colab 라이브러리를 포함하고 있다.**

**IDE에서 사용시 설치 필요**

```python
!python -m pip install -U google-colab
```

<br>

**연동 방법 1: 직접 코랩에 업로드한 파일 접근**

```python
# 드라이브 마운트
from google.colab import drive
drive.mount('/content/gdrive/')
```

```python
# 파일 읽기
path_dataset = "/content/gdrive/MyDrive/....."
filename = "example.csv"
my_df = pd.read_csv(path_dataset + filename)
```

<br>

**연동 방법 2: 구글 드라이브의 공유 파일을 코랩에 업로드**

* **drive 객체 만들기**

  ```python
  # 아래 코드는 공유 파일을 사용하기 위한 코드 이니 그대로 사용하시면 됩니다.
  
  # !pip install -U -q PyDrive
  from pydrive.auth import GoogleAuth
  from pydrive.drive import GoogleDrive
  from google.colab import auth
  from oauth2client.client import GoogleCredentials
  
  # 인증
  auth.authenticate_user()
  gauth = GoogleAuth()
  gauth.credentials = GoogleCredentials.get_application_default()
  drive = GoogleDrive(gauth)
  ```

* **구글 드라이브로 공유된 파일 가져오기**

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



