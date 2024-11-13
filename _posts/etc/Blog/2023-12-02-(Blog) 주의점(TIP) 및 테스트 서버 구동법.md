---
title:  "[Blog] 주의점(TIP) 및 테스트 서버 구동법"
categories : Blog
tag : [블로그, Git, git blog, 깃허브 블로그, Minimal-mistakes, 지킬, jekyll, xmlParseEntityRef, Liquid Syntax Error, Ruby, YFM]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**깃 블로그 지킬 테스트 서버 구동 및 문법과 여러 주의할 점(TIP)들 위주로 정리 한다.**  
**Ruby 언어 주의점과 특수 문자들로 인한 에러나 이미지 경로 문제나 깃 블로그 잔디와 드롭리스트 문제 등...**

<br>

<br>

## 테스트 서버

**실시간 블로그 업데이트 내역을 보면서 하기 위해 로컬로 서버 여는 방법을 알아보자**

<br><br>

### 구동방법

* git 블로그를 만든 폴더에서 "Shift + 마우스 우클릭" 해서 `powerShell` 터미널로 접속

* `bundle exec jekyll serve` 입력

  > 만약 파일 없다고 에러 뜨면 "bundle add 파일이름" 으로 설치

* `localhost:4000` 페이지 접속

  >  포트번호 다를 수도 있으니 본인에게 맞는 포트번호 적용

<br><br>

### 테스트 서버에서 주의점(TIP)

* 테스트 서버에서 `config.yml` 내용 변경 적용법은 **"무조건 서버 재실행"**
  * 서버 구동할 때 사용하기 때문
* **이미지**가 로딩 안된다면 -> **경로의 문제** 의심
  * Github의 경로와 로컬의 경로는 다르다
  * 따라서 절대경로 보다는 **상대경로 권장 or 아래 TIP 확인**
* jekyll serve 구동에 옵션을 설정 -> **"테스트 속도 상승법"**
  * 컨텐츠가 많을수록 속도가 느릴 수 밖에 없어서 이때 추천!
  * 10개 정도로 줄여서 빌드 테스트 (2~3분이 8초로 단축)
    * `bundle exec jekyll serve --watch --limit_posts 10` : 최신글 10개
    * **폰트 같은 "설정 변경" 적용여부 확인할 때 좋다**고 생각
  * 변경된 컨텐츠만 재빌드 -> 아마도 "캐시 사용" (2~3분이 17초로 단축)
    * `bundle exec jekyll serve --incremental`
    * **전체 게시물들 수정여부 확인할 때 좋다**고 생각
    * 단, 게시물 **실제 파일명**도 바꾸게 되면 처음부터 **다시 서버 실행** 필요 -> "캐시 사용 때문이라 추측"
      * 실제 파일명 변경은 테스트 할 필요가 없긴 해서 이 부분은 알고만 있자!
* 뭔가 잘 동작 안할때는 `powershell`에 엔터키 적용
* 서버 빌드할 때 -> 로컬 테스트 서버에서만 **"비공개 포스트" 확인** 방법은
  - 빌드에 `--unpublished` 추가
  - ex : `bundler exec jekyll serve --unpublished`

<br>

<br>

## 필독 - 주의할 점(TIP)

**경험했던 여러 가지 주의할 점과 TIP들을 정리해보자**

<br><br>

### 깃 잔디 왜 안 채워져?

clone으로 가져왔다면 상관없지만, 

**fork한 git 레퍼지토리는 초록잎이 안 채워진다고 한다!**

* 해결법은 **branch를 병합!**
  * 테디노트님 영상 따라하기 : https://www.youtube.com/watch?v=Z053Qn8LJyk

* 참고
  * git계정 한개당 github.io블로그 한개 생성 가능(레퍼지토리)
  * 여러개 사이트는 블로그가 아닌 프로젝트로 생성해서 블로그처럼 보는 방법이 있다고 한다.

<br><br>

### 본섭 깃 블로그에 폰트 적용이 안 된다?

본섭에 scss 것들이 적용 안될때가 있다! 원인은?? => **"캐시"**

* git cache 에 **몇몇 파일들**은 자동으로 기록되기 때문에 변경사항이 **적용이 안 되기 때문**
* **캐시 초기화 방법**
  * `git rm -r --cached .` : 캐시 모두 제거
  * `git add .` : 새로운 커밋 작성 위함
  * `git commit -m "fixed untracked files"` : 커밋 작성완료
  * `git push` : Github에 적용

<br><br>

### 주석 남발 절대 금지!

수많은 에러 중에 **"주석"을 남발**하여 **에러가 발생**할 가능성이 있다!

 **Jekyll 은 Ruby 언어**로 만들어졌기 때문에 Ruby 언어를 HTML 주석으로 처리하면서 에러를 발생시킬 수 있다.

**결론 : Ruby 언어에는 "주석"을 사용하지 않기를 권장!!**

* `<!-- {% if page.next %} -->` : 이런 거 금지!! `{% endif %}` 가 없으면 문법상 에러발생 하기 때문
* 심지어 md파일에 남발해도 문제니까 조심해서 사용
  * 위 코드도 에러 때문에 일부러 endif 를 추가 설명한 것

<br><br>

### 이미지 로딩 왜 안돼?

md파일에서 이미지 불러오는 문법은 딱히 문제가 없고 **"무조건 경로 문제"** 가 대다수이다.

* <img src... 또는 ![image ... 둘 다 문법 사용가능

* **경로의 문제 -> 상대경로 or Issue사용 권장**

  * Github 레퍼지토리에 올리면 `bh946.github.io/ ` 가 기본경로

    * 로컬의 경로와 다르단 점을 인지하자!

  * **방법1) 로컬 - imgaes폴더로 이미지들을 편리하게 관리하기 위해**

    * 게시물 설정에 `typora-root-url: ../../..` 로 root를 본인에게 맞는 상대경로로 지정!

    * 게시물 설정인 "---내부"(YFM(YAML Front Matter) 에서 Ctrl+S 한번 해줘야 root가 적용되어,

    * 이미지를 해당 게시물(md)에 붙여 넣으면 자동으로 `root경로/images/폴더` 에 이미지 복제!

      * 단, 아래 그림처럼 **md파일 환경설정 세팅**을 해주고 해야합니다!

        <img src="/images/2023-12-02-(Blog) 주의점(TIP) 및 테스트 서버 구동법 - 깃 블로그 TIP 1/image-20231218154421377.png" alt="마크다운 환경설정" style="zoom:67%;" /> 

  * **방법2) git의 Issue 활용 - 온라인 이미지 사용 -> 제일 자주 사용!**

    * 아래 그림처럼 Issue에서 이미지를 붙여 넣으면 자동으로 **온라인 링크**를 줍니다.

    * 장점은 외부 온라인에서 가져오기 때문에 "경로문제가 없다"라는 점입니다.

      * 위 로컬 이미지 방식의 경우 "md파일명"에 따라 폴더도 따로 생성하기 때문에,
      * "md파일명" 수정 시 경로 문제가 발생합니다.

      <img src="/images/2023-12-02-(Blog) 주의점(TIP) 및 테스트 서버 구동법 - 깃 블로그 TIP 1/image-20231218160519967.png" alt="issue의 온라인 이미지" style="zoom:80%;" /> 

<br><br>

### 게시물 작성의 주의점은?

md(마크다운)의 경우 `shift+enter`를 입력해도 개행문자가 입력X -> 내용이 옆으로 이어서 출력

<br>

#### 1. 줄바꿈을 소개

1. 문장 끝에 `<br>`와 `공백 2칸 + shift+enter` 는 아래와 같은 효과 - 글자 바로 밑으로 이동 효과

   * 안녕하세요<br>안녕하세요
   * 안녕하세요  
     안녕하세요

2. `enter` 는 아래와 같은 효과 - 빈 한 줄을 공백으로 가짐

   안녕하세요

   안녕하세요

3. **본인이 자주 사용하는 방식 - `<br>` 은 최대 2번까지 적용이 되는듯하다**

   * 문장 입력하는 중 : `공백 2칸 + shift+enter`
   * 다음 문장 입력할 때 : `enter`
   * 같은 주제이지만 조금 문단을 나누고 싶을 때 : `enter+<br>+enter`
   * 완전히 다른 주제로 문단을 나누고 싶을 때 : `enter+<br><br>+enter`
   * 제일 크게 나눌 때 : `enter+<br>+enter+<br>+enter`

**사용 예시**

안녕하세요  
반갑습니다.

다음 문장입니다.

<br>

다른 내용을 말해보겠습니다.

<br><br>

다른 주장입니다.

<br>

<br>

제일 크게 나눴을 경우입니다. 

<br>

#### 2. 자주 사용 문법

**toc(목차) 잘 적용하려면 이 부분 신경 쓰자!**

* '#' 젤 큰 주제 => 잘 사용 안 함..

* '##' 젤 큰 주제의 아래단계
  * 본인은 이 부분 커스텀을 통해 아래 밑줄 생기게 했음

* '###' 부가 정보
* '####' 소 주제 
* 주의 : # -> ## 변환은 자유롭지만, ## -> #### 갔다가 #### -> ## 처럼 #2개 이상 이동 시 toc(목차)에 문제
  * 따라서 **#1개씩 이동을 권장**

<br>

**개요식 *, -**

* '*'이나 '-'를 통해 개요식 표현 (현재 왼쪽에 있는 점 모양을 의미)
* '**' 처럼 2번 사용 시 더 세부 개요식 (TAB사용시 바로 진입)

<br>

**\<code>\</code> 와 YFM**

* code로 감싸게 되면 `hello` 로 나타나며 \`hello`를 통해 간단히 사용 가능

* `'---'` : YFM(YAML Front Matter) 포맷을 의미
* :smile -> 이모티콘 매우 다양 :smile:

<br>

**인라인 자세한 설명 - 앞서 보여준 \<code> 부분**

* **인라인 코드**

  * \` : code

    `inline code`

  * \``` : block code

    ```python
    print("inline code")
    ```

* **인라인 주소삽입**

  * 링크만 있는 inline : \<https://www.google.com>
    * <https://www.google.com>
  * 설명 있는 inline : \[구글 홈페이지](https://www.google.com)
    * [구글 홈페이지](https://www.google.com)

* **인라인 주소삽입 - 목차에 사용법**

  * 목차처럼 이동하는 것 - toc가 있어서 안 쓰긴 하지만 알아두자
  * 문단 주소 따는 규칙
    * 특수문자 제거(.,다 포함)
    * 공백을 -로 표시
    * 대문자는 소문자로
    * 예 : 3. 파일명 조심!(name) => #3-파일명-조심name
  * [3. 파일명 조심!(NAME)으로 이동해보기](#3-파일명-조심name)

* **마크다운 문법 무시 방법 : `\` 사용**

<br>

#### 3. 파일명 조심!(NAME)

md파일 및 폴더이름에 [] 는 넣지 말자 -> 대신 () 를 사용 권장

md파일 및 폴더이름에 &,<,> 는 넣지 말자 -> 검색엔진에 사용하는 XML에 문제발생 가능성

* 물론 md파일 안에 title 에는 상관없다

<br>

md파일 이름에 `2023-04-01-제목` 이런 형태로 날짜 뒤에 `-` 까지 작성해줘야 한다.

md파일 이름에 날짜가 **오늘 날짜 이후라면 해당 파일은 블로그에 게시되지 않는다.**

<br>

#### 4. 인라인과 진하게 표시주의!

진하게 표시와 인라인을 함께 사용하면 인라인이 먹히지 않는다.

**인라인과 진하게는 구분하여 사용하자!**

* 인라인만 사용 : 안녕`하`세요
* 인라인 포함하여 진하게 표시 : **안녕`하`세요**
* 인라인 제외하고 진하게 표시 : **안녕**`하`**세요**

<br><br>

### 필자의 게시물 작성 세팅방식 - YFM

**본인이 세팅한 "검색" 부분은 TAG 부분도 읽기 때문에 TAG를 잘 적어두면 "검색"할 때 유용!**

```markdown
---
title: "Web_Programming(html, jsp, sql)" // 게시글 제목
categories : test // 카테고리 분류
tag : [android, github] // 태그
author_profile: false // 프로필 보이게, 안보이게 설정
sidebar: // 프로필 대신 -> 사이드바 보려는 목적(왼쪽에 Home, Category, Search 부분)
   nav: "docs" // navigation.yml 에 docs로 등록한 설정 사용
toc: true // 목차 뜨게하기
toc_sticky: true // 목차 고정시키기
typora-root-url: ..\..\.. // 이미지 경로를 위해 root 경로를 직접 설정
---
```

<br><br>

### Ruby 언어 문법 조금은 알고가자

**실제로 마크다운에서도 자동으로 적용되어서 코드는 사진으로 대체..**

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

<br><br>

### 지킬 블로그 포스팅 비공개로 하는 법은?

게시물 yaml 작성란에 

- `published: true`: 포스트 공개
- `published: false`: 포스트 비공개

<br>

서버 빌드할 때 -> 로컬 테스트 서버에서만 포스트 확인 방법은

- 빌드에 `--unpublished` 추가
- ex : `bundler exec jekyll serve --unpublished`

<br><br>

### Liquid Syntax Error?? 그게 뭔데??

md 파일 열심히 작성하고 포스팅을 했는데 이런 에러가 발생한다. 리퀴드 문법에러??

앞서 언급한 Ruby언어 문법 얘기한 거랑 정말 같은 맥락이다. 

**예를들어** {%raw%} {{ }} {%endraw%} 를 사용하면 리퀴드에서는 탈출문으로 알고 있다.

**따라서** 이를 방지하기 위해서는 아래 문법을 사용해줘야 한다.

- ![image](https://github.com/BH946/bh946.github.io/assets/80165014/6a83e2f5-0ad5-4871-8096-a624a50e56f1) 

<br><br>

### 검색 엔진 에러?? xmlParseEntityRef: no name??

경로 상에(ex: posts/ee/과거&현재.md) `&` 가 있으면 XML에서 특수문자 취급을 다르게 하므로 에러가 발생할 수 있다.

 따라서 애초에 `&, <, >` 특수문자의 경우 "경로 상에" 사용 안 하길 권장한다.

<br><br>

### details 태그 사용한 드롭리스트 사용법

**주의사항**

- markdown=”1” 할 때 “1” 꼭 지키기. 쌍따옴표 순서 안맞아도 안되더라.
- 코드에 아래 주석 문제 발생 시?? 한줄로 수정하기 `/** dd */`

```java
/**
 * 문제의 주석 -> 근데 걍 되는 주석 코드도 있음;;
 */
```

- 코드(\```)는 div markdown을 한번 더 입히는게 안전.
  - \<br>로 담줄 넘겨도 `-` 이거 들여쓰기가 탈출이 안돼서 그럼;;
- `-` 이거 들여쓰기에 details를 쓰고싶다면?? 아래 코드처럼 사용해야함. (코드 markdown 추가 입히기 포함한 예시)
  - `-` 이거와 details 사이에 공백처럼 한칸 엔터 필수!! + details 부분은 꼭 들여쓰기.


````
- **1:N, N:1** 의 경우 **N**에 사용 -> **1**은 양방향 필요시 연결(엔티티)
  
  <details><summary><b>1쪽에 FK 놓으면 2가지 단점</b></summary>
  <div markdown="1"><br>
  아무거나 입력
  <div markdown="1">
  ```java
  System.out.println("test");
  ```
  </div>
  </div>
  </details>
  
- 안녕하세요.

<details><summary><b>1쪽에 FK 놓으면 2가지 단점</b></summary>
<div markdown="1"><br>
아무거나 입력
<div markdown="1">
```java
System.out.println("test");
```
</div>
</div>
</details>
````

<br>

**예시**

- **1:N, N:1** 의 경우 **N**에 사용 -> **1**은 양방향 필요시 연결(엔티티)
  
  <details><summary><b>1쪽에 FK 놓으면 2가지 단점</b></summary>
  <div markdown="1"><br>
  아무거나 입력
  <div markdown="1">
  ```java
  System.out.println("test");
  ```
  </div>
  </div>
  </details>
  
- 안녕하세요.

<details><summary><b>1쪽에 FK 놓으면 2가지 단점</b></summary>
<div markdown="1"><br>
아무거나 입력
<div markdown="1">
```java
System.out.println("test");
```
</div>
</div>
</details>