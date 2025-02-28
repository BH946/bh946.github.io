---
title: "Liquid Syntax Error - jekyll blog"
categories : [Err]
tag : [md, 깃블로그, 특수문자]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**지킬 블로그로 깃 블로그 사용중인 상태라면 md파일에 Ruby언어가 자동 사용이 된다.**

{%raw%} {{ }} {%endraw%} 를 사용하면 리퀴드에서는 탈출문으로 알고 있다.   
따라서 자꾸 문법 에러가 발생하게 된다.  

어차피 md파일에서 Ruby문법 사용할 일은 없을거다.   
따라서 **글 처음부터 끝까지 범위로 아래 문법을 적용**해주면 해결할 수 있다. (raw ~ endraw 범위내에 적용이 됨)

![Ruby문법 무시](https://github.com/BH946/bh946.github.io/assets/80165014/6a83e2f5-0ad5-4871-8096-a624a50e56f1)  

<br>

참고: 아래는 Ruby 코드를 사용한 예시이며 자동으로 문법이 적용 되는것을 알 수 있다.

**코드**

![ruby문법](https://github.com/BH946/bh946.github.io/assets/80165014/375e414e-8044-4c58-ba3c-849ab3156a40)   

<br>

**결과**

* `{% comment %} 안녕하세요 {% endcomment %}` : 주석
* `{% assign check = true %}` : 변수 선언
  * 변수 변경도 다시 선언하는식으로 하면 되겠다
* `{% if check %}` : check 변수가 있거나 true, false를 판단 `{% endif %}` 로 마무리 **필수**
  * and, or 사용가능 : check and check2 == false 라던지 check or check2 등등
* `{% for post in posts %}` : for문이며 `{% endfor %}` 로 마무리 **필수**
* `{{check}}` : check 출력값
