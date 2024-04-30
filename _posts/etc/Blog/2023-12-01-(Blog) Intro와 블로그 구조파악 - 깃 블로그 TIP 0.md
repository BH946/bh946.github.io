---
title:  "[Blog] Intro & 블로그 구조파악 - 깃 블로그 TIP 0"
categories : Blog
tag : [블로그, Git, git blog, 깃허브 블로그, Minimal-mistakes, 지킬, jekyll]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



# Intro.. 

**필자의 깃 블로그 커스텀 방식들을 정리하려고 한다!**

> 정리 안하니까 매번 똑같은걸로 시간낭비..

<br>

**아래는 [지킬 홈페이지](https://jekyllrb-ko.github.io/docs/structure/) 에서 가져온 `구조도`**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/58397b44-0674-4305-8180-ca88a5067d79) 

<br>

이렇게만 보면 사실 뭐가뭔지 보기 어렵다. 

**알아두면 좋은 부분들만 추적해서 `구조를 파악`해보자**

<br><br>

## _config.yml

`yml` 파일은 보통 setting들을 할 수 있는 파일이며 **필자의 테마 버전**도 확인할 수 있다! 

* `remote_theme : "mmistakes/minimal-mistakes@4.24.0"`
* 해당 버전을 기준으로 설명합니다

<br>

**핵심 : `# Site Settings, # Site Author, # Site Footer`** 

**(1) Site Settings**

* `locale` : 언어
* `title` : 사이트 명과 대표 이름
* `logo` : 대표 이름 옆에 로고 -> 아래 사진의 BH_Notes 왼쪽 로고

![image](https://github.com/BH946/bh946.github.io/assets/80165014/7a60fc68-0211-42c0-9b04-11164430f886) 

<img src="https://github.com/BH946/bh946.github.io/assets/80165014/f22d9edf-b51f-44a6-8f64-c16daa8f270a" alt="image" style="zoom:50%;" />  <img src="https://github.com/BH946/bh946.github.io/assets/80165014/05920f1f-e579-430c-bf96-3073bd592b66" alt="image" style="zoom:80%;" /> 

<br>

**(2) Site Author**

* `name` : 프로필 이름
* `avatar` : 프로필 사진 -> 아래 사진속 BH Note 그림
* `bio` : 프로필 소개
* `location` : 지역명
* `links` : 하위에 Email, Github 등등 본인꺼 추가 가능

![image](https://github.com/BH946/bh946.github.io/assets/80165014/0b4d15a9-99e7-460e-8125-b2e263051a7c) 

<img src="https://github.com/BH946/bh946.github.io/assets/80165014/ac14f276-da81-4ac3-aeff-fc137e8e356f" alt="image" style="zoom: 80%;" /> 

<br>

**(3) Site Footer**

* 사이트 아래에 별도로 표시하는 Footer 공간

![image](https://github.com/BH946/bh946.github.io/assets/80165014/075ed43b-8fad-4e14-9cfb-7d0762f711cf) 

<img src="https://github.com/BH946/bh946.github.io/assets/80165014/643d1bee-a998-407f-905c-c218a6316ff7" alt="image" style="zoom:80%;" /> 

<br><br>

## _data 폴더

**네비게이션 바** 부분을 커스텀 할 수 있고, **네이밍**을 위한 변수를 등록한다.

폴더 하위 : `navigation.yml, ui-text.yml`

<br>

### navigation.yml

**(1) main : 웹사이트 상단 네비게이션 바**

* `title` : 버튼 이름
* `url` : 보여줄 html -> 예로 아래 `/search/` 는 `_layouts/search.html` 으로 이동

![image](https://github.com/BH946/bh946.github.io/assets/80165014/3b0a6f71-9a97-4509-87d5-9627be780ea1)   

![image](https://github.com/BH946/bh946.github.io/assets/80165014/bd222b9e-2582-4f38-a79e-77ee5ec72cd4) 

<br>

**(2) docs : 게시물의 왼쪽 편 프로필 자리에 위치**

* `title` : 이름
* `childeren` : 원하는 링크 등록

![image](https://github.com/BH946/bh946.github.io/assets/80165014/31fa1725-dc78-495a-a668-124671f51e9d) 

![image](https://github.com/BH946/bh946.github.io/assets/80165014/fef92a4a-1372-47ba-9a2a-de465fb9b730) 

<br>

### ui-text.yml

* 버튼 이름 같은것들을 따로 변수 등록한 파일

![image](https://github.com/BH946/bh946.github.io/assets/80165014/dd03ad46-3608-4d7e-b1c7-4faa5bc21bb0) 

<br>

**예시로 "이전", "다음" 버튼 명 적용 모습을 보자**

* 아래 코드는 `_includes/post_pagination.html` 이며,
* 게시물 속 하위에 있는 **"이전, 다음" 게시물로 이동하는 버튼**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/83826d6c-c3c5-4e23-86ea-b5aefc492aba) 

![image](https://github.com/BH946/bh946.github.io/assets/80165014/cd1dd215-1947-4a1d-b904-4243880bdd68) 

<br><br>

## _includes 폴더

**반복**하여 삽입하기 좋은 파일들의 모음집으로 볼 수 있다. 

개인적으로 spring-thymeleaf 의 **fragment** 같은 느낌이였다.

**폴더 하위 : `archive-single.html, breadcrumbs.html, footer.html, head.html, page__meta.html, post__taxonomy.html, post_pagination.html, scripts.html, nav_list_main`**

<br>

### archive-single.html

네비게이션 바의 **"Recent Posts"** 와 **"Category"** 페이지 및 게시물 하단의 **"참고"** 게시물 각각에 사용된다.

특히, 필자는 **사이트 왼쪽에 정리한 카테고리들**에 접근 했을때는 `archive-single2.html` 로 따로 만들어 사용한다.

**(1) Recent Posts, Category 페이지, 참고**

**(1-1) Recent Posts**

<img src="https://github.com/BH946/bh946.github.io/assets/80165014/dfddc246-cdfb-4409-973a-de8bbca0bd2a" alt="image" style="zoom: 67%;" />  

<br>

**(1-2) Category**

<img src="https://github.com/BH946/bh946.github.io/assets/80165014/e116780c-592c-4a98-a6ba-77735873b62d" alt="image" style="zoom: 67%;" />  

<br>

**(1-3) 참고**

<img src="https://github.com/BH946/bh946.github.io/assets/80165014/0b2c5ed5-7e76-4d1e-84c9-d1e2db638cad" alt="image" style="zoom:67%;" /> 

**(1-4) archive-single.html -> archive__item 확인!**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/3516892e-13a3-441b-acb1-20535fac2148) 

<br>

**(2) 카테고리 중 Knowledge(지식) 페이지**

* 이것은 따로 만든 `archive-single2.html` 를 적용했기 때문에 UI 가 다른것이다.

<img src="https://github.com/BH946/bh946.github.io/assets/80165014/de3e8621-40d9-495e-8ecf-64673b494c59" alt="image" style="zoom: 80%;" /> 

<br>

### breadcrumbs.html

페이지의 네비게이션 부분으로 상단 부분에 **"경로"** 를 나타낸 부분이며, 아래 사진을 보고 이해하자

* `Home/Categories/지식/정보` 를 의미

![image](https://github.com/BH946/bh946.github.io/assets/80165014/20b401fa-6b38-4245-a546-1937afa6eb80) 

<br>

### footer.html, head.html

footer.html 은 **footer 태그** 부분이라고 생각하고 사용하자

head.html 은 **head 태그** 부분이라고 생각하고 사용하자

* 참고로 본인은 폰트를 그냥 `_layouts/default.html` 에서 등록하였다.

<br>

### page__meta.html

archive-single 부분에 있던 **날짜 부분을 커스텀** 할 수 있다.

* 아래 그림의 `2023-12-02` 처럼 나오게끔 날짜 부분을 커스텀 진행한 모습이다.

<img src="https://github.com/BH946/bh946.github.io/assets/80165014/dfddc246-cdfb-4409-973a-de8bbca0bd2a" alt="image" style="zoom: 67%;" /> 

<br>

### post__taxonomy.html

archive-single 부분에 있던 **"카테고리, 태그" 단어들을 커스텀** 할 수 있다.

참고로 필자는 "태그" 안보이게 수정했다.

* `Knowledge` 부분을 의미하며 실제로 디자인을 커스텀 진행한 모습이다.

<img src="https://github.com/BH946/bh946.github.io/assets/80165014/dfddc246-cdfb-4409-973a-de8bbca0bd2a" alt="image" style="zoom: 67%;" /> 

<br>

### post_pagination.html

게시물 내부 하위에 **"이전, 다음" 버튼을 커스텀** 할 수 있다.

* 참고로 최신글(Recent Posts) 기준으로 "이전, 다음" 게시물이 책정 되는것을 확인했고,
* 필자는 이를 카테고리 별로 "이전, 다음" 게시물이 책정 되게끔 수정했다.
* "참고" 부분은 `_layouts/single.html` 에서 커스텀! (뒤에서 정리한다)

<img src="https://github.com/BH946/bh946.github.io/assets/80165014/0b2c5ed5-7e76-4d1e-84c9-d1e2db638cad" alt="image" style="zoom:67%;" /> 

<br>

### scripts.html

자바 스크립트 사용하는 부분들은 여기서 커스텀 하자

* 참고로 필자는 mathjax 문법을 사용 가능하게 하기위해 이곳을 수정했다.

<br>

### nav_list_main

웹 사이트의 왼쪽 부분 카테고리 목록은 이 파일로 구성한다고 볼 수 있다.

**특히, 카테고리 추가할 때 `_pages/categories/` 하위에 md 파일로 카테고리 생성을 같이 해야한다.**

* 아래 그림 참고
* 자세한 설명은 따로..

<img src="https://github.com/BH946/bh946.github.io/assets/80165014/ee98d1cd-f7b8-44e9-89ed-485aade31b70" alt="image" style="zoom:67%;" /> 

<br><br>

## _layouts

tag, category, archive 및 다양한 페이지 **layout 정보들**이 담겨 있다.

* 여러개가 있어서 조금만 소개해보겠다

<br>

**(1) single.html -> 게시물 하위에 "참고" 부분 커스텀**

<img src="https://github.com/BH946/bh946.github.io/assets/80165014/0b2c5ed5-7e76-4d1e-84c9-d1e2db638cad" alt="image" style="zoom:67%;" /> 

<br>

**(2) home.html -> "Recent Posts" 부분 커스텀**

* 단, 필자는 `archive.html` 에서 수정하긴 했다.

<img src="https://github.com/BH946/bh946.github.io/assets/80165014/5b51b165-208f-4790-ac07-d27e0eb8832d" alt="image" style="zoom:67%;" /> 

<br>

**(3) archive.html**

* 이곳에서 "Recent Posts"를 추가하여 **archive__subtitle** 을 관리하였다.

![image](https://github.com/BH946/bh946.github.io/assets/80165014/fbce5672-671b-4599-948b-32717468d58c) 

<br>

**(4) default.html -> 필자는 그냥 이곳에 "폰트" 적용**

* **html 태그** 가 들어가므로 가장 **첫 페이지**로 보면 된다.
* 물론 index.html 의 구조는 아래에 따로 정리했으니 참고하자!

<br><br>

## _pages

네비게이션 바 **(navigation)에서 로드 가능한 페이지가 저장**된다.

* 웹 사이트 상단바 네비게이션 부분
* 카테고리들 생성했던 네비게이션 부분
* 등등..

<br>

이부분을 이해하기 위해 한가지 예를 보자!

**예시 : 필자가 생성한 `_pages/category-archive.md`**

```markdown
---
title : "Category"
layout : categories
permalink : /categories/
author_profile : true
sidebar_main : true
---
```

* layout이 categories로 설정함으로써 실제 `_layouts/categories.html` 이 실행!
  * 따라서 웹 상단 네비게이션 부분의 **Category 가 정상 작동**
* **categories.html**에서는 `layout: archive` 로 설정되어 있어서 `_layouts/archive.html` 도 실행!
* **archive.html**에서는 `layout: default` 로 설정되어 있어서 `_layouts/default.html` 도 실행! 끝

<br><br>

## _posts

이곳이 바로 **"게시물 작성"** 하는 공간이다!

<br><br>

## _sass

폰트, 사이즈, 색상 등의 정보가 해당 폴더에 담겨 있다 -> **중요!**

필자의 블로그를 보면 "폰트, 사이즈, 색상, 마진" 등이 기존 지킬과 다르기 때문에 이부분이 **중요!**

<br><br>

## assets

font, css, images **(ex:사이트 로고)** 등이 담겨 있다.

* `asset/css/_main.scss` 에서 구글 폰트 **@import** 할 수 있으므로 참고
* 실제로, 필자의 프로필 로고도 이곳에 저장

<br><br>

## index.html

index.html 로 시작되는것은 어느 웹사이트든 동일하다고 볼 수 있다.

단, 어떤 구조인지 추적 해보면 아래와 같은 구조임을 알 수 있다.

**`index.html -> home.html -> archive.html -> default.html`**

**좀 더 알기쉽게 그림도 참고하자! -> index, home, archive, default 글자를 참고**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/823f89cf-26ef-464a-b6d4-62c8c86a8fe3) 

