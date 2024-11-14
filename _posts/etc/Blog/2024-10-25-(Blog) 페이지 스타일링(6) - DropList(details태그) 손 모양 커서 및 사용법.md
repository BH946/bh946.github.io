---
title:  "[Blog] 페이지 스타일링(6) - DropList(details태그) 손 모양 커서 및 사용법"
categories : Blog
tag : [블로그, Git, git blog, 깃허브 블로그, Minimal-mistakes, 지킬, jekyll, details, 드롭리스트, 드롭다운, 마우스]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"

typora-root-url: ../../..
---



**MarkDown의 드롭리스트(details)를 사용하는 법 + 마우스 커서 커스텀을 알아보자**

**변경전**

![image](https://github.com/user-attachments/assets/3455c316-b3a3-4432-a536-8b971882d0fd) 

<br>

**변경후** 


![image](https://github.com/user-attachments/assets/9a15c2f7-5d8b-4216-913e-dad730a1e387) 

<br>

**참고: details 태그는 아래 사진처럼 드롭리스트**

![image](https://github.com/user-attachments/assets/176b58f5-d03c-4a3e-b326-b90d90141c57) 

<br>

<br>

## detalis 사용법은?

**[blog 주의사항](https://bh946.github.io/blog/(Blog)-%EC%A3%BC%EC%9D%98%EC%A0%90(TIP)-%EB%B0%8F-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%84%9C%EB%B2%84-%EA%B5%AC%EB%8F%99%EB%B2%95/) 참고** 

<br>

<br>

## 커서 수정법은?

`_sass/minimal-mistakes/_reset.scss` 를 찾아가서 아래처럼 수정하면 된다.

```scss
/* droplist custom!! */
details {
  summary {
    cursor: pointer; // summary에만 손 모양 커서
  }
}
```

