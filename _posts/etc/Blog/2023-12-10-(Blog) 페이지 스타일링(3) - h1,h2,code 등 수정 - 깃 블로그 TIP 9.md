---
title:  "[Blog] 페이지 스타일링(3) - h1,h2,code 등 수정 - 깃 블로그 TIP 9"
categories : Blog
tag : [블로그, Git, git blog, 깃허브 블로그, Minimal-mistakes, 지킬, jekyll]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"

typora-root-url: ../../..
---



# Intro

**사진을 보면 "페이지 스타일링(2)" 는 h1이고, "카테고리 스타일링" 은 h2 이다.**

**그리고 "inline code블럭" 이 크기가 작아서 이를 수정한다.**

**이처럼 "소제목"을 스타일링 해보고, code 블럭의 폰트를 키우자!**

![image](https://github.com/BH946/bh946.github.io/assets/80165014/3937a0b6-e232-4a5d-8d13-6faf9dd4f593) 

<br>

**code 블럭 크기 작을 때**

![prev](https://github.com/BH946/bh946.github.io/assets/80165014/89bcaa71-8dab-4b9b-baf1-81931a20cdaf)

<br>

**code 블럭 크기 키울 때**

![nxt](https://github.com/BH946/bh946.github.io/assets/80165014/71e57c4d-0a51-4ab8-9004-b611b8335ea3)  

<br><br>

# 페이지 스타일링(3)

## 소제목

* `_page.scss` 에서 `page__content` 를 수정 해 볼것
  * **아래 코드 나타냈고, h2와 h3을 자주 사용해서 이것만 스타일링 하였다.**

```scss
// _page.scss
.page__content {
  margin-top: 20px; // 게시글 제목과 본문 거리를 벌리기 위함

  h2 {
    margin: 29px 0 22px;
    color : #000;
    border-bottom:2px solid #333333;
	  border-left:10px solid #333333;
	  padding: 5px 15px;
    line-height: 1.5;
  }
  h3 {
    margin: 29px 0 22px;
    color : #000;
    border-bottom:2px solid #333333;
	  border-left:5px solid #333333;
	  padding: 5px 15px;
    line-height: 1.5;
  }
}
```

<br>

## code 블럭

`code태그` 사용의 크기를 키우자 ! !

* `_base.scss` 에 `td > code` 부분에서 크기를 키우면 끝!
