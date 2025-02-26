---
title: "[체크리스트] 파이썬"
categories : [CheckList]
tag : [spring, python, 장고, fastapi, 백엔드]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



Spring의 MVC 패턴으로만 쭉 백엔드 개발 연습을 했는데, Python의 장고와 FastApi를 경험해보는것도 좋을 것 같아서 정리하고자 함. 계속 내용 수정해야함. 일단은 대략적인 흐름만 작성해두고 나중에 경험이 쌓이면 틀린내용 수정해 가겠음.

**일단 내용은 Spring의 체크리스트의 흐름으로 작성(Gpt 도움 받으면서)**

<br>

1. **Django의 핵심 특징**

   사용 예시: 콘텐츠 관리 시스템(CMS), 전자상거래 플랫폼, 데이터 기반 앱

   - "batteries-included" 철학을 따르는 풀스택 프레임워크 (즉, 웹 개발 필요 기능 대부분 제공)

   - MVT(Model-Template-View) 아키텍처 패턴 사용

     1. Django는 맨 처음 URL을 보고 알맞은 메인 로직을 처리하는 View을(를) 호출

     2. View에서는 필요하다면 Model을 통해 데이터베이스와 소통하고

     3. 처리한 데이터를 화면을 담당하는 Template과 함께 렌더해서 최종 화면을 만든 후

        > 스프링의 타임리프가 생각나는 뷰 템플릿 부분이다..

     4. View를 통해 클라이언트 에게 응답으로 돌려줌

   - 강력한 ORM 시스템과 자동 관리자 인터페이스 제공

   - 보안 기능이 기본 내장되어 있음 (XSS, CSRF, SQL 인젝션 방지)

2. **FastAPI의 핵심 특징**

   사용 예시: 실시간 앱, 머신러닝 API 서비스, 서버리스 함수

   - Python 3.6+ 기반의 현대적이고 고성능 API 프레임워크

   - 비동기 프로그래밍 지원으로 높은 동시성 처리

   - Python 타입 힌트를 활용한 자동 API 검증 및 문서화

   - Swagger UI와 ReDoc을 통한 자동 API 문서 생성

3. **MVT 보충설명: [참고 링크**](링크가 기억안남)

   | MVC구조    | MVT구조                                   |
   | ---------- | ----------------------------------------- |
   | Model      | 데이터 저장, 보관 => Model                |
   | View       | 사용자에게 보여지는 부분 담당 => Template |
   | Controller | 웹 사이트의 로직 담당 => View             |

   MVT 아키텍처는 MVC 아키텍처 구조를 기반으로 만들어져서, 둘 간의 공통점이 많다.  
   차이점은 MVC구조에서 Controller가 했던 역할을 일부 분리해서, Django프레임워크가 직접 처리한다. 

   Django 개발자는 Model, View, Template에 집중하고, 나머지 모든 부분은 Django 프레임 워크에게 맡겨서 빠르게 개발할 수 있다. 

<br>

<br>

## Django 프레임워크

**기본 설치 법**

```bash
# Django 설치 (콘다 base에 설치했음)
pip install django

# 프로젝트 생성
django-admin startproject projectname

# 앱 생성
cd projectname
python manage.py startapp app1
```

<br>

아래 흐름으로 정리해보겠음.

**프로젝트 권장구조 -> MVT 개발 흐름(Model, Template, View...)** -> 테스트코드(?) -> application.yml같은 환경설정 -> 리팩토링(캐시, 메시지국제화, 외부설정, 검증, 예외처리 정도?) -> 배포(프로필설정 있는강?) -> 참고지식(로그인 인증방식 Spring때랑 같은지보자, 파일업로드 다운로드도 같은지 보자 딱 이정도만?)

<br><br>

### 권장 구조

**기본 프로젝트 구조**

```
projectname/
├── apps/ -> 모든 장고 앱을 한 곳에 모아 관리
│   ├── app1/ -> 각 앱은 독립적인 기능 단위로 구성
│   ├── app2/
├── projectname/
│   ├── settings/ -> 환경별로 설정 파일 분리
│   │   ├── base.py -> 공통 설정
│   │   ├── development.py -> 개발 환경 설정
│   │   └── production.py -> 운영(배포) 환경 설정
│   ├── urls.py -> 프로젝트 레벨
│   └── wsgi.py -> WS(아파치 등)와 장고 앱 통신 담당
├── static/ -> 전역 static 폴더는 공통 자원
├── media/
├── templates/ -> 기본 템플릿
└── manage.py
```

<br>

**앱 구조(app1, app2...)**

```app1/
app1/
├── migrations/
├── static/ -> 앱별 static 폴더 사용
├── templates/ -> 앱별 템플릿
├── admin.py
├── apps.py
├── models.py
├── views.py
├── urls.py -> 앱 레벨
└── tests/
```

<br><br>

### MVS? (Model, View, Serializer) - API (JSON)

**api위해 rest_framework(viewset,router 쓰려고)설치와 settings.py 등록!**  
`pip install djangorestframework`

```python
# settings.py에 등록:
INSTALLED_APPS = [
    ...
    "rest_framework", # 설치한 rest_framework 추가
    "tarot", # 새로 만든 앱 추가
]
```

<br>

**Model 부분 (엔티티 느낌)**

- `class TarotCard(models.Model):` 란 Django의 Model 클래스를 상속받는다는 의미

- models.Model이 제공하는 기능:

  ```python
  # 자동으로 사용 가능한 메서드들
  card = TarotCard.objects.create(name="The Fool")  # 생성
  card.save()                                       # 저장
  card.delete()                                     # 삭제
  TarotCard.objects.all()                          # 전체 조회
  ```

```python
# tarot/models.py
from django.db import models

class TarotCard(models.Model):
    name = models.CharField(max_length=100)
    number = models.IntegerField(null=True)
    arcana_type = models.CharField(max_length=20, choices=[
        ('MAJOR', 'Major Arcana'),
        ('MINOR', 'Minor Arcana')
    ])
    keywords = models.CharField(max_length=255)
    meaning_upright = models.TextField()
    meaning_reversed = models.TextField()
    description = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.name

```

<br>

**Serializer부분. JSON으로 반환 위함 (API) - 직렬화, 역직렬화**

```python
# tarot/serializers.py
from rest_framework import serializers
from .models import TarotCard

class TarotCardSerializer(serializers.ModelSerializer):
    class Meta:
        model = TarotCard
        fields = '__all__' # 모든 필드 사용한다고.

```

<br>

 **View 부분**

- ModelViewSet은 자동으로 CRUD 생성!! 그래서 rest_framework 이거 설치한 것

```python
# tarot/views.py
from rest_framework import viewsets
from .models import TarotCard
from .serializers import TarotCardSerializer

class TarotCardViewSet(viewsets.ModelViewSet):
    queryset = TarotCard.objects.all() # 전체 조회
    serializer_class = TarotCardSerializer

    def get_queryset(self):
        queryset = TarotCard.objects.all()
        arcana_type = self.request.query_params.get('arcana_type', None)
        if arcana_type:
            queryset = queryset.filter(arcana_type=arcana_type)
        return queryset
```

<br>

**URL 설정 (컨트롤러 느낌-라우터..)**

- Router는 ModelViewSet이 생성한 CRUD를 자동으로 URL 매핑!! 그래서 rest_framework 설치!

```python
# tarot/urls.py
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import TarotCardViewSet

router = DefaultRouter()
router.register(r'cards', TarotCardViewSet)

urlpatterns = [
    path('api/', include(router.urls)),
]

# projectname/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('tarot.urls')),  # tarot 앱의 URLs 포함
]

```

<br><br>

### MVT (Model, View, Template) - 웹

**위에서 만든 api와 파일명 구분하기 위해 디렉토리 살짝 수정**  
자세한 코드는 CODE 폴더에 projectname 폴더보면 됨ㅇㅇ

```python
#앱 구조(app1, app2...) 부분임.
projectname/
├── tarot/
│   ├── templates/
│   │   └── tarot/
│   │       ├── base.html
│   │       ├── card_list.html
│   │       ├── card_detail.html
│   │       └── card_form.html
│   ├── api/
│   │   ├── __init__.py
│   │   ├── views.py        # API 뷰
│   │   ├── urls.py         # API URL 패턴
│   │   └── serializers.py
│   ├── web/
│   │   ├── __init__.py
│   │   ├── views.py        # 웹 뷰
│   │   └── urls.py         # 웹 URL 패턴
│   ├── models.py
│   └── forms.py
```

<br>

**Model 위에꺼 그대로 사용ㅇㅇ**

- `class TarotCard(models.Model):` 란 Django의 Model 클래스를 상속받는다는 의미

- models.Model이 제공하는 기능:

  ```python
  # 자동으로 사용 가능한 메서드들
  card = TarotCard.objects.create(name="The Fool")  # 생성
  card.save()                                       # 저장
  card.delete()                                     # 삭제
  TarotCard.objects.all()                          # 전체 조회
  ```

```python
# tarot/models.py
from django.db import models

class TarotCard(models.Model):
    name = models.CharField(max_length=100)
    number = models.IntegerField(null=True)
    arcana_type = models.CharField(max_length=20, choices=[
        ('MAJOR', 'Major Arcana'),
        ('MINOR', 'Minor Arcana')
    ])
    keywords = models.CharField(max_length=255)
    meaning_upright = models.TextField()
    meaning_reversed = models.TextField()
    description = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.name
```

<br>

**Template이 필요한 경우임. (웹 페이지)**

- 다른 템플릿들이 base.html을 상속받아 content 블록을 채우는 방식

- 템플릿 태그 설명:

  ```xml
  {% extends %} - 템플릿 상속
  {% block %} - 자식 템플릿이 채울 수 있는 영역 정의
  {% csrf_token %} - CSRF 보안 토큰
  {{ form.as_p }} - 폼 필드를 p 태그로 감싸서 렌더링
  ```

```python
# tarot/web/views.py
from django.shortcuts import render, get_object_or_404, redirect
from ..models import TarotCard
from .forms import TarotCardForm

def card_list(request):
    cards = TarotCard.objects.all()
    return render(request, 'tarot/card_list.html', {'cards': cards})

def card_detail(request, pk):
    card = get_object_or_404(TarotCard, pk=pk)
    return render(request, 'tarot/card_detail.html', {'card': card})

def card_create(request):
    if request.method == "POST":
        form = TarotCardForm(request.POST)
        if form.is_valid():
            card = form.save()
            return redirect('tarot:card_detail', pk=card.pk)
    else:
        form = TarotCardForm()
    return render(request, 'tarot/card_form.html', {'form': form})
```

```html
<!-- templates/tarot/base.html -->
<!DOCTYPE html>
<html>
<head>
    <title>타로 카드 앱</title>
    <style>
        .card-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
            gap: 20px;
            padding: 20px;
        }
        .card {
            border: 1px solid #ddd;
            padding: 15px;
            border-radius: 8px;
        }
        .btn {
            display: inline-block;
            padding: 10px 20px;
            background-color: #007bff;
            color: white;
            text-decoration: none;
            border-radius: 5px;
        }
    </style>
</head>
<body>
    <nav>
        <!-- views.py의 메소드 -->
        <a href="{% url 'tarot:card_list' %}">홈</a>
    </nav>
    <main>
        <!-- nav태그의 홈은 모든 페이지에서 유지, 이 main태그 부분만 변동 -->
        {% block content %}
        {% endblock %}
    </main>
</body>
</html>
```

```html
<!-- templates/tarot/card_list.html -->
{% extends 'tarot/base.html' %}

{% block content %}
    <h1>타로 카드 목록</h1>
    <div class="card-grid">
        {% for card in cards %}
            <div class="card">
                <h2>{{ card.name }}</h2>
                <p>{{ card.description|truncatewords:30 }}</p>
		        <!-- views.py의 메소드 -->
                <a href="{% url 'tarot:card_detail' pk=card.pk %}">자세히 보기</a>
            </div>
        {% endfor %}
    </div>
	<!-- views.py의 메소드 -->
    <a href="{% url 'tarot:card_create' %}" class="btn">새 카드 추가</a>
{% endblock %}
```

```html
<!-- templates/tarot/card_detail.html -->
{% extends 'tarot/base.html' %}

{% block content %}
    <div class="card-detail">
        <h1>{{ card.name }}</h1>
        <div class="card-info">
            <p><strong>번호:</strong> {{ card.number }}</p>
            <p><strong>종류:</strong> {{ card.get_arcana_type_display }}</p>
            <p><strong>키워드:</strong> {{ card.keywords }}</p>
        </div>
        <div class="card-meanings">
            <h2>정방향 의미</h2>
            <p>{{ card.meaning_upright }}</p>
            <h2>역방향 의미</h2>
            <p>{{ card.meaning_reversed }}</p>
        </div>
        <div class="card-description">
            <h2>설명</h2>
            <p>{{ card.description }}</p>
        </div>
    </div>
{% endblock %}

```

```xml
<!-- templates/tarot/card_form.html -->
{% extends 'tarot/base.html' %}

{% block content %}
    <h1>새 타로 카드 추가</h1>
    <form method="post">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit" class="btn">저장</button>
    </form>
{% endblock %}
```

<br>

**form.py 가 필요 (위에 시리얼라이즈 처럼 DTO)**

```python
# tarot/web/form.py
from django import forms
from ..models import TarotCard

class TarotCardForm(forms.ModelForm):
    class Meta:
        model = TarotCard
        fields = ['name', 'number', 'arcana_type', 'keywords', 
                 'meaning_upright', 'meaning_reversed', 'description']
        widgets = {
            'description': forms.Textarea(attrs={'rows': 4}),
            'meaning_upright': forms.Textarea(attrs={'rows': 3}),
            'meaning_reversed': forms.Textarea(attrs={'rows': 3}),
        }
        labels = {
            'name': '카드 이름',
            'number': '번호',
            'arcana_type': '아르카나 종류',
            'keywords': '키워드',
            'meaning_upright': '정방향 의미',
            'meaning_reversed': '역방향 의미',
            'description': '설명',
        }
```

<br>

**URL 설정 (컨트롤러 느낌-라우터)**

```python
# tarot/web/urls.py
from django.urls import path
from . import views

app_name = 'tarot'

urlpatterns = [
    path('', views.card_list, name='card_list'),
    path('card/<int:pk>/', views.card_detail, name='card_detail'),
    path('card/new/', views.card_create, name='card_create'),
]


# projectname/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('tarot.api.urls')),    # API 경로 (tarot/api/urls.py)
    path('', include('tarot.web.urls')),        # 웹 경로 (이걸로 변경)
]

# tarot/api/urls.py
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import TarotCardViewSet

router = DefaultRouter()
router.register(r'cards', TarotCardViewSet)

urlpatterns = [
    path('', include(router.urls)), # http://localhost:8000/api/ 로 접근 위해
    # 만약 이부분 수정 안하면 http://localhost:8000/api/api/ 로 접근해야 함.
]
```

<br><br>

### 테스트

**SQLite 메모리 DB 설정 가능**  
**=> 근데 비추!** 서버 시작마다 마이그레이션이 초기화! 그래서 서버시작시 자동 마이그레이션도 추가해야함.

**따라서 SQLite DB 사용! 개발에서!**

```python
# settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        "NAME": BASE_DIR / "db.sqlite3",
        # 'NAME': ':memory:',
    }
}
```

```bash
# db 마이그레이션
python manage.py makemigrations tarot
python manage.py migrate
# db 테이블 재생성도 해주는..
python manage.py migrate --run-syncdb

# 서버(앱) 실행
python manage.py runserver
```

<br>

테스트코드.. 스프링처럼.. 작성하는거 있나... 체크.....ㄱ...ㄱ...ㄹ...  
from django.test import TestCase 이런게 또 보이더라구 ㅇㅅㅇ







<br>

<br>

## Fastapi 프레임워크

**기본 설치법**

```bash
# Fastapi 설치 (콘다 base에 설치했음)
pip install fastapi
pip install uvicorn

# 기본 앱 구조 생성은 수동
```

<br>

아래 흐름으로 정리해보겠음.

**프로젝트 권장구조 -> 얜 어떤 개발 흐름이냐 ㅠㅠ(프로젝트 구조대로... GPT부탁ㄱ)+타로ai 바로 만들면서ㄱㄱㄹ ->** 테스트코드(?) -> application.yml같은 환경설정 -> 리팩토링(캐시, 메시지국제화, 외부설정, 검증, 예외처리 정도?) -> 배포(프로필설정 있는강?) -> 참고지식(로그인 인증방식 Spring때랑 같은지보자, 파일업로드 다운로드도 같은지 보자 딱 이정도만?)

<br><br>

### 권장 구조

**기본 프로젝트 구조**

```
fastapi-project/
├── main.py -> 앱 진입 점
├── requirements.txt -> 프로젝트에서 사용하는 패키지 목록
└── app/ -> 앱 코드
    ├── __init__.py
    ├── models.py
    ├── schemas.py
    └── routers/
        └── __init__.py
```

**앱 구조**

```
app/
├── main.py -> 애플리케이션의 진입점으로 FastAPI 인스턴스 생성
├── routers/ -> 리소스별로 구분된 라우트 정의 파일들 포함
│   ├── __init__.py
│   ├── users.py
│   └── items.py
├── models/ -> 데이터베이스 모델 정의
│   ├── __init__.py
│   └── user.py
├── schemas/ -> Pydantic 모델을 사용한 데이터 검증 및 직렬화 스키마 (DTO)
│   ├── __init__.py
│   └── user.py
└── services/ -> 비즈니스 로직 처리 계층
    ├── __init__.py
    └── user_service.py
```

**장고와 주요 차이점**

- Django와 달리 FastAPI는 프로젝트/앱 생성 명령어가 없음
- 프로젝트 구조를 수동으로 생성해야 함
- uvicorn이라는 별도의 서버가 필요함
- Docs 기능을 지원하여 등록된 API를 문서화된 UI로 확인이 가능하다.
  - **접속 방법: URL/docs 를 추가하면 접근 가능**

<br><br>

### 기본 개발 흐름

**main.py -> 시작 점**

```python
from fastapi import FastAPI
from app.routers import tarot

app = FastAPI(title="Tarot AI Reading API")

# 라우터 등록
app.include_router(tarot.router, prefix="/api/v1")
```

**app/models/database.py -> db 설정**

```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

SQLALCHEMY_DATABASE_URL = "sqlite:///./tarot.db"

engine = create_engine(
    SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False}
)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

**app/models/tarot.py -> 엔티티 느낌**

```python
from sqlalchemy import Column, Integer, String, DateTime, Text
from sqlalchemy.sql import func
from .database import Base

class ReadingHistory(Base):
    __tablename__ = "reading_history"

    id = Column(Integer, primary_key=True, index=True)
    ip_address = Column(String(50))
    situation = Column(Text)
    question = Column(Text, nullable=True)
    reading = Column(Text)
    created_at = Column(DateTime(timezone=True), server_default=func.now())
```

**app/schemas/tarot.py -> DTO 사용과 유사**

```python
from pydantic import BaseModel
from typing import Optional

class TarotRequest(BaseModel):
    situation: str
    question: Optional[str] = None

class TarotResponse(BaseModel):
    reading: str
```

**app/services/tarot_service.py -> 레포지토리+서비스 느낌**

```python
from sqlalchemy.orm import Session
from ..models.tarot import ReadingHistory

class TarotService:
    def __init__(self, db: Session):
        self.db = db

    async def record_reading(self, ip_address: str, situation: str, question: str, reading: str):
        reading_record = ReadingHistory(
            ip_address=ip_address,
            situation=situation,
            question=question,
            reading=reading
        )
        self.db.add(reading_record)
        self.db.commit()

```

**app/services/perplexity_service.py**

- **비지니스 로직**으로써 perplexity api 연동
- https://docs.perplexity.ai/guides/getting-started 보고 적절한 모델, 프롬프트 세팅

```python
import os
import httpx
from dotenv import load_dotenv

load_dotenv()

class PerplexityService:
    def __init__(self):
        self.api_key = os.getenv("PERPLEXITY_API_KEY")
        self.base_url = "https://api.perplexity.ai"
    
    async def generate_reading(self, situation: str, question: str = None) -> str:
        prompt = self._create_prompt(situation, question)
        
        async with httpx.AsyncClient() as client:
            response = await client.post(
                f"{self.base_url}/chat/completions",
                headers={"Authorization": f"Bearer {self.api_key}"},
                json={
                    "model": "sonar-medium-chat",
                    "messages": [
                        {"role": "system", "content": "You are an experienced tarot reader."},
                        {"role": "user", "content": prompt}
                    ]
                }
            )
            
            if response.status_code == 200:
                return response.json()["choices"][0]["message"]["content"]
            else:
                raise Exception("Failed to generate reading")

    def _create_prompt(self, situation: str, question: str = None) -> str:
        base_prompt = f"Based on this situation: {situation}"
        if question:
            base_prompt += f"\nSpecific question: {question}"
        return base_prompt + "\nPlease provide a detailed tarot reading with insights and guidance."
```

**app/routers/tarot.py** -> 라우터 설정 (애노테이션 사용하는게 Spring과 유사하네)  
**=> 컨트롤러 느낌**

```python
from fastapi import APIRouter, Depends, Request
from slowapi import Limiter
from slowapi.util import get_remote_address
from sqlalchemy.orm import Session
from ..models.database import get_db
from ..schemas.tarot import TarotRequest, TarotResponse
from ..services.perplexity_service import PerplexityService
from ..services.tarot_service import TarotService

router = APIRouter()
limiter = Limiter(key_func=get_remote_address)
perplexity_service = PerplexityService()

@router.post("/reading", response_model=TarotResponse)
@limiter.limit("5/hour")  # IP당 시간당 5회로 제한
async def get_tarot_reading(
    request: Request,
    tarot_request: TarotRequest,
    db: Session = Depends(get_db)
):
    reading = await perplexity_service.generate_reading(
        tarot_request.situation,
        tarot_request.question
    )
    
    # 리딩 기록 저장
    tarot_service = TarotService(db)
    await tarot_service.record_reading(
        request.client.host,
        tarot_request.situation,
        tarot_request.question,
        reading
    )
    
    return TarotResponse(reading=reading)

```

**환경 변수 설정 (.env):**

```env
PERPLEXITY_API_KEY=your_api_key_here
```

<br><br>

### 테스트

**장고처럼 FastAPI도 DB 마이그레이션 필요 -> SQLAlchemy + Alembic**

```bash
pip install alembic sqlalchemy
# alembic 초기화
alembic init alembic
# alembic.ini 설정 -> 직접 해당파일에 들어가서 수정하면 됨ㅇㅇ.
sqlalchemy.url = sqlite:///./tarot.db

# 마이그레이션 파일 생성 단, alembic/env.py 파일에 target_metadata 설정 필수!
alembic revision --autogenerate -m "Initial migration"

# 마이그레이션 적용 
alembic upgrade head

# 마이그레이션 롤백
alembic downgrade -1
```

```python
#alembic/env.py파일에 target_metadata 설정법은? 그래야 Alembic이 메타데이터 인식하고 자동으로 마이그레이션 파일 생성
from app.models.database import Base
target_metadata = Base.metadata

#main.py 에 테이블 생성 코드도 있어야 함.
from app.models.database import Base, engine
Base.metadata.create_all(bind=engine) # 애플리케이션 시작 시 테이블 생성
```

```bash
# 서버(앱) 실행
uvicorn main:app --reload --host 0.0.0.0 --port 8000

curl localhost:8000 # index 페이지 접근
{"Python":"Framework"}
```

- uvicorn : python 서버 실행을 위해 필요한 기본 명령어
- main : 실행할 초기 python 파일 이름
- app : FastApi() 모듈을 항당한 객체명
- reload : 소스 코드가 변경되었을때 자동 재시작 옵션
- host : 모든 접근이 가능하게 하기 위해 0.0.0.0
- port : 웹서버 포트 할당

<br>

스프링 테스트코드처럼.. 있나 쳌쳌

