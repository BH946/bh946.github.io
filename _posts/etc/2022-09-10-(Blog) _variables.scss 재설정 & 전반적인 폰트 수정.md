---
title:  "[Blog] _variables.scss 재설정 & 전반적인 폰트 수정"
categories : Blog
tag : [블로그, Git, git blog, 깃허브 블로그, Minimal-mistakes, 지킬, jekyll]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



## _variables.scss 재설정 & 전반적인 폰트 수정



### 적용 방법 추가

**이전에 css방식으로 @import로 폰트 추가했는데, 이를 html의 head에서 불러오는 방식으로 변경**

* `default.html` 에서 `<head>`에 추가

```html
<head>
	<!--폰트 : "Nanum Gothic Coding", "Coming Soon"-->
    <link rel="preload" as="style" href="https://fonts.googleapis.com/css2?family=Coming+Soon&family=Nanum+Gothic+Coding&display=swap">
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Coming+Soon&family=Nanum+Gothic+Coding&display=swap">
    
    <!--폰트 : "Iropke Batang"-->
    <link rel="preload" as="style" href="https://cdn.jsdelivr.net/font-iropke-batang/1.2/font-iropke-batang.css">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/font-iropke-batang/1.2/font-iropke-batang.css">

    <!--폰트 : "Cafe24Oneprettynight"-->
    
    <!--main.scss 빨리 로드되게끔.. preload-->
    <link rel="preload" as="style" href="{{ '/assets/css/main.css' | relative_url }}">
```



**다만, 새로운 폰트 하나는 @import 방식으로 여전히 추가할것임**

* `assets/css/main.scss` 에서 추가

```scss
// "Cafe24Oneprettynight"
@font-face {
  font-family: 'Cafe24Oneprettynight';
  src: local('Cafe24Oneprettynight'), url('https://cdn.jsdelivr.net/gh/projectnoonnu/noonfonts_twelve@1.1/Cafe24Oneprettynight.woff') format('woff');
  font-weight: normal;
  font-style: normal;
}
```





### 전역변수 이름 추가 및 수정

* `Iropke Batang`, `Cafe24Oneprettynight` 폰트가 추가되면서 폰트들을 갈아 엎었다.

```scss
$serif: "Coming Soon", Georgia, Times, serif !default;
$sans-serif: "Iropke Batang", -apple-system, BlinkMacSystemFont, "Roboto", "Segoe UI",
  "Helvetica Neue", "Lucida Grande", Arial, sans-serif !default;
$monospace: "Nanum Gothic Coding", Monaco, Consolas, "Lucida Console", monospace !default;
$cute: "Cafe24Oneprettynight" !default;

/* sans serif typefaces */
$sans-serif-narrow: $sans-serif !default;
$helvetica: "Cafe24Oneprettynight", Helvetica, "Helvetica Neue", Arial, sans-serif !default;
```





### 폰트 재설정



#### 왼쪽 사이드바 부분

**왼쪽 사이드바 부분의 아래쪽을 의미**

* `_navigation.scss` 에서 폰트들 변경(아래와 동일하게 설정하면 됨)
* 참고로 toc(목차)에선 폰트수정하면 글자가 좀 더 작아서 한단계씩 키웠다.

```scss
.breadcrumbs{ // 파일 경로 나타내는 부분
	font-family: $sans-serif;
}

.nav__list .nav__items {
  margin: 0;
  font-size: 1.25rem;
  font-family: $sans-serif;

  a { // color: inherit만 있었음
    color: inherit;
    font-size: $type-size-4;
    font-family: $cute;
    // color: mix(#fff,$primary-color,80%);

    &:hover {
      text-decoration: underline;
    }
  }
}

.nav__sub-title {
  font-family: $serif;
}

.toc {
  // font-family: $sans-serif-narrow; // 수정전
  font-family: $cute; // 수정후
  
  .nav__title {
    font-size: $type-size-5; // 수정(6에서 5로)
  }
}

.toc__menu {
  font-size: $type-size-5; // 수정(6에서 5로)
  @include breakpoint($large) {
    font-size: $type-size-6; // 수정(7에서 6로)
  }
}
```



**왼쪽 사이드바 부분의 위쪽을 의미**

* `_sidebar.scss` 에서 폰트 설정

```scss
.sidebar{
	li {
    	font-family: $serif; // 수정
    }
}

.author__content {
  font-family: $cute; // 추가
}

.sidebar .author__name {
  font-family: $cute; // 수정
}

.author__bio {
  font-family: $cute; // 추가
    
  p { // p 추가
    font-family: $cute;
    margin-top: 1.6px;
    margin-bottom: 3px;
  }
}
```





#### breadcrumbs - 경로 보이는곳

페이지 상단에 경로보이는곳을 의미하는데 이곳도 폰트를 재설정 한다.

* 위에서 사실 이미 이부분 언급했으나, 보기 쉽게 따로 또 정리하는 의도이다.
* `_navigation.scss` 에서 수정했던 사안이다.

```scss
.breadcrumbs{ 
	font-family: $sans-serif;
}
```





#### 제일 상위바 부분

* 제일 상위의 **Home, Category, Search** 가 있는곳을 의미한다.

* `_masthead.scss` 에서 수정한다.

```scss
.masthead {
  &__inner-wrap {
    font-family: $serif; // 수정
  }
}
```





#### 게시글 제목들 모음 페이지

* `.archive__subtitle`  : 게시물 제목들 나열된 페이지의 상단에 페이지 제목부분

* `.archive__item-title` : 이 페이지의 게시물 제목들
  * 그런데 계속 이부분이 적용이 안됨. **최근 게시물 페이지**에서만 적용이됨.
  * 따라서 `.list__item` 에 추가로 폰트 적용해서 적용하였음.

```scss
.archive__subtitle {
  font-family: $serif; // 수정후
  + .list__item .archive__item-title {
    margin-top: 0.5em;
  }
}

.archive__item-title {
  font-family: $cute;
}

.list__item {
  margin-bottom: 0.3em; // 추가
  font-family: $cute; // 추가
  font-size: $type-size-4; // 추가
}
```





#### 게시글 본문 설정

게시글 본문의 게시글 제목부분을 스타일링 한다. 그리고 h1~h6을 스타일링 한다.

* `_page.scss` 에서 설정한다.
* h1~h6 스타일링은 스타일링만 하고, 폰트는 따로 설정하지 말것.
  * `_base.scss` 에서 설정된 폰트로 사용할거기 때문.

```scss
.page__title {
  font-family: $cute; // 수정후
}

.page__content{
    h1~h6를 꾸밀수있다.
}
```



본문 폰트를 안건들였으면 문제가없지만, 건들였다면 수정을 할 필요가 있다.

* `_base.scss` 에서 폰트를 수정하면되고, 되도록이면 이 파일은 안 건드리게끔 사용하려고 한다.
* `$global-font-family`, `$header-font-family` 전부 `$sans-serif` 로서 설정되어 있다.
  * 따라서 수정하고 싶으면 `_variables.scss` 에서 `$sans-serif` 를 차라리 수정하라.

```scss
body {
  font-family: $global-font-family;
}

h1,
h2,
h3,
h4,
h5,
h6 {
  font-family: $header-font-family;
}
```
