---
title:  "(JPA+Boot)컨트롤러Thymeleaf 구현"
categories : Spring
tag : [타임리프,스프링MVC,웹개발,HTTP,컴포넌트스캔]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**스프링 MVC 패턴에서 타임리프 템플릿 엔진을 활용한 웹 개발 방법과 HTTP 구현, 컴포넌트 스캔, DTO 패턴 등 웹 애플리케이션 구축의 핵심 요소를 상세히 설명하는 가이드입니다.**

<br><br>

### (컨트롤러Thymeleaf) HTTP 구현 + 컴포넌트 스캔 + DTO

**위 API 컨트롤러(DTO, JPA&MyBatis관점&컴포넌트 스캔)를 잘 참고 -> 이 곳은 타임리프+웹 관련 정리**

**@Controller:** 자동 컨트롤러 빈 등록 (@RestController 미사용)

- **메소드단 @GetMapping, @PostMapping** 로 HTTP 매핑

- **클래스단 @RequestMapping**으로 공통 URL 매핑
- 반환타입 void: 뷰리졸버는 요청URL과 동일한 뷰를 탐색해서 반환!
- **반환타입 String**: 해당 반환 문자열과 동일한 뷰를 탐색해서 반환! -> **주로 사용**

**@RequestParam, @PathVariable, @ModelAttributes 헷갈릴 때:**

- **@ModelAttributes는 자세한 기능을 알고 사용**하자. 웬만하면 **GET엔 @RequestParam이나 @PathVariable(권장!) 쓰자.**
  - **@PathVariable** - URL 뒤의 값 바로 사용 (자주 사용)
  - **@RequestParam** - 쿼리 파라미터 값 가져올때 자주사용 (기본값 설정도 가끔사용)
- **@ModelAttributes의 자세한 동작**은 Model로 addAttribute하는걸 자동화 함.
  - Model은 MVC의 M부분이다.
  - @ModelAttributes는 넘어온 데이터가 없다면 **빈 객체**를 자동으로 Model에 담거나, `@ModelAttributes("item") ItemDto dto`이면 form 데이터를 자동으로 ItemDto에 매핑 및 Model엔 item이름으로 기록한다.  
  - 주의: form으로 넘길 때는 선언한 name(id)필드는 전부 매핑이 되어야함. 
    - ex: `메서드(@ModelAttribute UpdateItemDto dto)` 라면 form의 필드들이 최소한 UpdateItemDto에 전부 있어야 함

**return "jsp/gallery", {return "redirect:/gallery", return "forward:/gallery"}, return this.gallery(item, model) 헬갈릴 때:**

- **jsp반환**은 "직접 뷰 반환"
- **redirect&forward**는 "HTTP URL로 매핑"
- **메소드**는 "일반 메소드 생각" -> 사실 forward랑은 URL패턴으로 호출하냐 안하냐 차이라 생각

**PRG 패턴 적용: 무한 POST 방지** -> PRG 패턴이 궁금하다면 "HTTP 중요지식" 파트 참고

- **자원 재활용(forward) : 폼 분리 가능한건 분리해서 작성 권장 - addForm, editForm**

  * GET에 꼭 빈 값이라도 엔티티 Model에 삽입 -> 널 에러 방지
  * forward 사용가능한 건 forward 형태로 하는게 효과적
  * BindingResult장점은 검증 실패시 html로 바로 return!  
    -> forward 이기 때문에 자원 재활용!! (redirect 안해도 되는거즤~)

- **PRG 패턴** 값 넘길 때 **redirectAttributes**를 주로 활용함. (메소드: addAttribute, addFlashAttribute) 

  - ```java
    //서버 사용 예시 코드
    redirectAttributes.addAttribute("itemId", itemId);
    redirectAttributes.addAttribute("test", test);
    redirectAttributes.addFlashAttribute("status", "updateOFF");
    return "redirect:/gallery/itemDetail/{itemId}"; // 기존 화면 다시 로딩
    ```

  - **addAttribute**는 자동으로 URL에 붙어서(쿼리 파라미터) 넘어간다.  

    - 예로 위 코드에선 itemId는 이미 URL에 있으니 test만 URL에 추가로 **"?test=값"** 형태로 넘어간다.

  - **addFlashAttribute**를 사용하면 1번만 값을 넘겨주고 **자동으로 지워준다.** 

    - **왜써?** PRG패턴으로 무한 POST를 피해도 클라URL-status값 보고 alert를 띄운다면, GET이여도 새로고침하면 클라 자체에서 계속 alert가 뜨는 불편함이 있는데 이를 피할 수 있는 유용한 기능이다.<br>**반면,** alert가 아니라 html에 "저장완료"를 출력해야 하는 상황이라면 **"상태 유지"위해 addAttribute 사용이 옳다.**
      - 근데, 무한POST 피한 상태라면 alert 여러번 떠도 사용자 입장에선 크게 상관은 없을것 같긴함. **그냥 addAttribute 사용으로 통일**하는것도 좋아보임.
      - **클라 사용법**: 웹에서 `<body data-status="${status}">` 이렇게 해줘야 `document.body.getAttribute('data-status');` 로 상태값을 js에서 잘 활용할 수 있다.

**검증(Valid)** 적용할 때 **웹은 클라단도** JS만들어 **적용**하기

**타임리프 사용한 문법 예(JSP비교)**: fragment -> jsp:include, th:src -> c:url, th:text -> spring:message, text -> c:out, th:each -> c:forEach,  th:if -> c:if, th:error -> 직접 등 코드는 아래 참고

<br>

#### Thymeleaf

서버로 실행(뷰 템플릿 사용)하면 타임리프 문법들이 적용해서 **동적으로 변경!**

* 스프링 부트는 "뷰 리졸버" 를 자동 등록하는데, 이때 설정파일에 등록한  `spring.mvc.view.prefix , spring.mvc.view.suffix` 정보를 사용해서 등록한다.
* "뷰 리졸버" 에 **필요한 "경로" 를 설정**하는 부분인데 요즘 Thymeleaf 는 이것도 자동으로 등록해줘서 설정할 필요가 없다.
  * **JSP 사용할 경우에는 이부분 기억**

> 참고 공식문서: [부트스트랩 공문](https://getbootstrap.com/docs/5.3/components/navbar/#toggler), [타임리프 공문](https://www.thymeleaf.org/doc/tutorials/3.1/usingthymeleaf.html#including-template-fragments)

<br>

**개발은 VSCode(정적) + IntelliJ(동적)로 개발 -> 본인은 귀찮아서 인텔리J로만 함**

- **보통 VSCode 가 html 문법 잘 지원해서(플러그인 다양함+Go Live로 바로 실행 지원) 이것으로 개발<br>IntelliJ는 서버 실행하여 Thymeleaf 문법 적용할 때 주로 사용**
  - 근데, 이것도 캐시만 잘 설정하면 reload를 빠르게 가능!! -> 본인은 그래서 IntelliJ로 개발
  - 설정 법: `spring.thymeleaf.cache=false`,  `spring.thymeleaf.prefix=file:src/main/resources/templates/`
    - 배포할땐 꼭 true로!
- **html+타임리프 작성 예: \<span class="pt-2" th:field="\*\{\{date1}}" th:text="*\{\{date1}}">23.05.22.16:00k\</span>**
  - 서버 동작 없이 html(정적)에서 텍스트 확인 위해 span태그 사이에 "23.05.22.16:00k"입력까지 한 것


<br>

**반응형 웹 개발을 위해 "부트스트랩(+인라인스타일링)"+"basic.css(전체), 커스텀.css(부분)"**

* 부트스트랩은 자동으로 반응형(해상도)에 맞게 스타일 지정되어 있다. 따라서 가져다 사용만 하면 됨!<br>**단, 원하는 스타일대로 커스텀하고 싶다면 “부트스트랩 문법에 인라인 style" or "일반 CSS를 직접 커스텀” 하자. 이때, 반복(재사용)되는것들은 공통CSS로 묶자** 

* **본인은 보통 basic.css로 전체 페이지에 적용할 css파일 만들고, 부분적으로 커스텀할 부분은 gallery.css같이 해당 페이지명으로 만들어서 적용 중이다.**

  <details><summary><b>css 문법 주의점</b></summary>
  <div markdown="1">
  - **부트스트랩**은 수 많은 것을 **자동 제공!!**
    - 일반적인 방식 `font-size:100px;` 는 `100px`로 글자가 **"고정"**
      - **따라서 반응형 웹 CSS는 원래 em, rem, vw, vh 같은 단위를 사용해줘야 한다.**
        - **em은 "부모"의 font-size의 크기에 따라서 결정**
        - **rem은?? -> em과 동일하지만 제일 root 부모의 font-size를 따름**
        - **vw, vh는 뷰포트 화면(장치:예로 브라우저)크기에 따라 사이즈 결정됨**
      - **따라서 본인은 `vw, vh`로 "부모"부분 먼저 크기 결정후 `em`으로 "자식" 부분 크기 결정** (장치 기준이 젤 부모가 되는게 좋다고 생각해서)
    - 부트스트랩 방식 `fs-1, fs-2 등` 은 **"반응형"** 으로 글자 크기 제공 (fs: font-size)
    - 부트스트랩만으로 해결이 안되어서 **css 혼합하여 커스텀 예시**
      - `<div class="d-flex custom-header-flex">`
      - **d-flex**는 부트스트랩이 제공하는 display:flex를 의미 (flex:1을 지원안함 ㅠ)
      - 따라서 **custom-header-flex** 부분은 style태그로 따로 flex:1을 적용한 방식
  - **스타일 우선순위**: inline stylet속성 -> style태그 -> css파일 (차례대로 우선순위)
  - **class, id 별 접근자**: id=#, class=.
  - **웹 폰트 적용??**: `font-family: 'SUITE-Regular', sans-serif;` 로써 해당 우선순위대로 폰트 적용 (suite 폰트는 외부파일)
  - **전역 변수 정의??: basic.css**
      <div markdown="1">
      ```css
      // 글꼴, 색상, 스타일 전역변수 정의
      @font-face {
        font-family: 'SUITE-Regular';
        /* src: url('../static/SUITE-Regular.woff2') format('woff2'); */
        src: url('/SUITE-Regular.woff2') format('woff2');
        font-weight: 400;
        font-style: normal;
      }
      :root {
        --line: #393A40;
        --main-1: #0C0C0C;
        --main-2: #323338;
        --text-2: #8E8F9B;
      }
      ```
      </div>
  - `&times;` **는 "X" 표시**: close 버튼을 이것으로 입력된 구조가 많음
  - **웹(프론트)에서의 검증(ex:비밀번호)**: 정규식 검사하는 js 문법 활용
    - `onkeypress` 방식을 사용! 단, 복붙을 구별X. 따라서 서버단에서도 추가 검증이 더욱 안전
      <div markdown="1">
      ```html
      <input type="password" class="no-spin" th:field="*{password}" id="password" name="password" pattern="\d+" required onkeypress='return checkNumber(event)'/>
      <script th:fragment="scripts3">
        // 비밀번호 정규식 등록
        function checkNumber(event) {
          var pw = event.key
          if (/^\d+$/.test(pw)) {
              return true;
            } else {
              return false;
            }
          return false;
        }
      </script>
      ```
      </div>
  * **텍스트 가로유지(텍스트 크기 초과 방지)**: `white-space: nowrap;` 스타일 적용
  * **flex로 0.98:0.02 로 비율 나눠도 덮어 씌어질 경우**: `max-width:92%` 로 해결
  * **hover, active 문법 실수 조심** : `.page-link:hover` 과 `.page-link.active` 처럼 hover은 ":", active는 "." 사용
  * **백그라운드 컬러 변경으로 개발 테스트 용이**: `bg-info, primary, secondary` 등으로 간편하게 적용!
  * **border은 항상 style을 지정**:  `solid` 나 굵기는 `width` 함께해야 잘 적용!
  </div>
  </details>

<details><summary><b>HTML 태그 위치별 사용 구조</b></summary>
<div markdown="1"><br>
- **\<html>** → thymeleaf 선언
  - **\<head>**
    - **\<meta>** → utf-8(문자인코딩), viewport(모바일 뷰크기 설정) : 이 2개는 필수 사용
    - **\<link>** → 주로 bootstrap(css), 커스텀css(ex:/basic.css) 선언
      - bootstrap(js)의 경우 \<script>에서!!!!
      - **css코드 예시?! basic.css, custom.css**<br>basic은 전역(기본) css, custom은 basic말고 적용하려고 추가로 만든 css
        <details><summary><b>basic.css → 글꼴, 색상, 스타일 전역변수 정의도 함!</b></summary>
          <div markdown="1">
          ```css
          //글꼴, 색상, 스타일 전역변수 정의
          @font-face {
            font-family: 'SUITE-Regular';
            /* src: url('../static/SUITE-Regular.woff2') format('woff2'); */
            src: url('/SUITE-Regular.woff2') format('woff2');
            font-weight: 400;
            font-style: normal;
          }
          :root {
            --line: #393A40;
            --main-1: #0C0C0C;
            --main-2: #323338;
            --text-2: #8E8F9B;
          }
          //Base structure
           body {
              background-color: var(--main-1);
              /* SUITE 못 찾으면 sans-serif 사용 */
              font-family: 'SUITE-Regular', sans-serif;
              margin-top: 15vh;
           }
           .field-error {
              color: red; font-weight: 700; padding:10px;
           }
          //Header
            nav {
              border-left-width: 0px;
              border-right-width: 0px;
              border-top-width: 0px;
              border-bottom-width: 0px;
              border-style: solid;
              border-color: var(--line);
            }
            .nav-item {
              position: relative;
              text-align: center;
              font-size: 1.2vw;
              width: 15vw;
              padding: 20px;
              padding-bottom: 30px;
            border-left-width: 0px;
            border-right-width: 1px;
            border-top-width: 0px;
            border-bottom-width: 0px;
            border-style: solid;
            border-color: var(--line);
          }
          /* 여기서 header는 navbar-nav 의 높이를 기준으로 정해진다고 볼 수 있음 */
          .navbar-nav {
            height: 15vh;
          }
          #nav-item-first {border-left-width: 1px; }
          .nav-link {
            color: white; font-weight: 700;
          }
          #custom-nav-item:hover,
          #custom-nav-item:active,
          #custom-nav-item.visited {
            background-color: var(--main-2);
          }
          .nav-item-inner {
            position:absolute; 
            bottom:0;
            left:0;
            font-size: 0.7em;
            color:white; font-weight: 400; letter-spacing: -0.14px; opacity: 0.6;
            padding: 16px;
          }
          //Main
              .custom-container-default {
              padding-top: 5vh;
              padding-bottom: 5vh;
              padding-left: 15vw;
              padding-right: 15vw;
              }
              #enterBtn {
              background-color: var(--main-1); color: white;
              border-radius: 0px;
              }
              #enterBtn:hover{
              background-color: white;
              color: var(--main-1);
              border-radius: 10px;
              }
            /* 모달에 스타일링 */
            /* 스크롤을 숨기는 스타일 */
            .no-scroll::-webkit-scrollbar {
            width: 0px;
            }
            /* 스핀 버튼 숨기기 */
            input[type="number"]::-webkit-inner-spin-button,
            input[type="number"]::-webkit-outer-spin-button {
            -webkit-appearance: none;
            }
            //Footer
          .custom-footer {
            color:var(--text-2);
            display: flex;
            justify-content: center; 
            align-items: center; 
            border-style: solid;
            border-top-width: 1px;
            border-left-width: 0px;
            border-right-width: 0px;
            border-bottom-width: 0px;
            border-color: var(--line);
            margin-left: 15vw; /* custom-container-default 의 padding 과 너비 맞출것 */
            margin-right: 15vw;
          }
          ```
        </div>
        </details>
        <details><summary><b>custom.css(=gallery.css)</b></summary>
          <div markdown="1">
          ```css
          .gallery-item-first{
            display: flex;
            flex-direction: row;
            padding-top: 20px;
            padding-bottom: 30px;
            /*height: 25vh;*/
          }
          .gallery-item-many{
            display: flex;
            flex-direction: row;
            padding-top: 20px;
            padding-bottom: 30px;
            /*height: 25vh;*/
            /* first와 차이점은 아래 border부분뿐 */
            border-color: var(--line);
            border-style: solid;
            border-left-width: 0px;
            border-right-width: 0px;
            border-bottom-width: 0px;
            border-top-width: 1px;
          }
          .page-link{
          background-color: var(--main-1);
          border-width:0px;
          font-weight: 500;
          text-align: center;
          color:white;
          }
          .page-link.active{
          background-color: white;
          color:var(--main-1);
          }
          .page-link:hover{
          background-color: var(--main-2);
          color:white;
          }
          ```
        </div>
        </details>
    - **\<title>** → 브라우저의 URL링크 상단바에 실제 제목
  - **\<body>** → 배경색, 커서 설정하기 좋지, 전체 폰트도~!
    - **\<style>** → css처럼 스타일링. 위 \<link> 스타일 보다 더 높은레벨 (실제 \<div style=""> 처럼 태그안에 스타일은 최상위레벨)
    - **\<nav>** → header 부분으로 시작~!
    - **\<div class="container">** 처럼 이제 쭉 레이아웃 형성 + 실제 웹 화면 구성 ㄱㄱ
    - **\<footer>** → 마지막은 footer로 마무리~!
  - **\<script>** → javascript 관련 모든 것 (+jquery, bootstrap 설치도 포함)
      bootstrap4까지는 jquery 사용 때문에 jquery먼저 설치코드 필요하지만, bootstrap5부터는 그런 의존성 없애서 꼭 상관없다~
      **물론, jquery 유용하니까 항상 설정하는것도 좋지.**
  <details><summary><b>html 전체 코드</b></summary>
  <div markdown="1">
    ```html
    <!doctype html>
    <html xmlns:th="http://www.thymeleaf.org">
      <head th:replace="fragments/head :: head('갤러리')">
      </head>
      <body>
        <style>
          .page-link{
            font-size:1.2vw;
            padding: 1vw;
          }
          /* body태그, id=enterBtn 에 적용 */
          @media all and (min-width: 1921px) and (max-width: 4096px) {
              body, #enterBtn {
                  cursor: url('/cursor.svg') 10 60, auto;
              }
          }
          @media all and (max-width: 1920px) {
              body, #enterBtn {
                  cursor: url('/cursor_1920.svg') 10 60, auto;
              }
          }
        </style>
        <!-- header -->
        <nav th:replace="fragments/header :: header">
        </nav>
        <!-- main 개발 -->
        <!-- 메인 그림 -->
        <div class="container-fluid p-0">
          <img class="img-fluid" src="../static/6.png" 
            th:attr="src=@{/6.png},title=#{logo},alt=#{logo}"
          style="width:100%;"/>
        </div>
        <!-- 본문  -->
        <div class="custom-container-default">
          <!-- 제목(층수) -->
          <div class="d-flex flex-column" style="min-height: 0vh; padding-bottom: 5vh;">
            <div class="container" style="text-align: center;">
            ...
            </div>
          </div>
          <!-- 작품 나열 -->
          <div class="d-flex flex-column" style="min-height: 80vh;"
          th:replace="fragments/item :: item">
            <div class="gallery-item-first">
              ...
            </div>
            <div class="gallery-item-many">
              ...
            </div>
          </div>
          <!-- pagination -->
          <br><br><br><br>
          <nav aria-label="Page navigation">
            <ul id="dyn_ul" class="pagination" style="justify-content: center;">
            </ul>
          </nav>
        </div>
        <!-- footer -->
        <footer th:replace="fragments/footer :: footer"
        class="custom-footer">
        </footer>
        <!-- Jquery CDN 로드 : 항상 최신 버전 사용 -->
        <script th:replace="fragments/scripts :: scripts4" src="https://code.jquery.com/jquery-latest.min.js"></script>
        <!-- bootstrap5(JS) CDN 로드 -->
        <script th:replace="fragments/scripts :: scripts1"  src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.1/dist/js/bootstrap.bundle.min.js" integrity="sha384-HwwvtgBNo3bZJJLYd8oVXjrBZt8cqVSpeBNS5n7C8IVInixGAoxmnlMuBnhbgrkm" crossorigin="anonymous">
        </script>
        <script th:replace="fragments/scripts :: scripts2">
          // nav 클릭때마다 배경색 변경 커스텀
          // 스크롤시 NavBar 불투명도 변경
        </script>
        <!-- 페이징 -->
        <!--    var pageCount = /*[[${(totalCount/10)+1}]]*/ null; // 총 페이지 크기 -> 통신으로 받음-->
        <script th:inline="javascript">
        ...
        </script>
      </body>
    </html>
    ```
  </div>
  </details>
</div>
</details>


<details><summary><b>HTML 문법 주의점</b></summary>
<div markdown="1"><br>
- **conatiner+flex+position?! -> div태그로 class=container 사용해서 레이아웃 구성 꼭 해주고! 필요에 따라 flex, position사용을 하면서 구현 ㄱㄱ**
  - **container** 는 보통 위에서 아래로 쌓지. 그럼 내부엔 수직, 수평, 절대위치로 삽입하고 싶다?
  - 난 grid는 안쓰고 **flex** 사용해서 하는중. **아래 속성도 유용함.**
    - `text-center` : 텍스트 중앙 정렬(좌우)
    - `align-items-start,center,end` : items 정렬이므로 자신의 자식들 정렬
    - `align-self-start,center,end` : self 정렬이므로 자신이 정렬
  - 절대위치는 그냥 **position:absolute랑 relative** 씀
    - absolute 사용하는데, 중앙에 위치하고 싶어서 `top:50%, left:50%, transform: translate(-50%, -50%)` 
      - `top: 50%;`: 이 요소의 상단이 부모 요소의 50% 위치에 배치됩니다.<br>`left: 50%;`: 이 요소의 왼쪽이 부모 요소의 50% 위치에 배치됩니다.<br>`transform: translate(-50%, -50%);`: 이 속성은 요소를 자신의 너비와 높이의 50%만큼 위와 왼쪽으로 이동시킵니다. 결과적으로 이 두 가지 설정을 조합하면 요소가 화면 중앙에 위치하게 됩니다.
- **padding, margin?! → 굉장히 많이 사용!! 이것도 위와 같이 사용 굉장히 많이 함!**
  - `padding` : 요소 “내부”의 여백을 설정
    - **내부다 보니 배경색에 영향O**
    - **내부다 보니 전체 너비와 높이에 “포함”**
  - `margin` : 요소 “외부”의 여백을 설정
    - **외부다 보니 배경색에 영향X**
    - **외부다 보니 전체 너비와 높이에 “추가”!!!!!!!!**
    <details><summary><b>동작원리(사진포함)</b></summary>
    <div markdown="1"><br>
    **총 3개 박스: margin, padding, margin + padding**
    ```html
    <div style="margin: 20px; background-color: lightblue;">
      콘텐츠가 있는 박스
    </div>
    <div style="padding: 20px; background-color: lightblue;">
      콘텐츠가 있는 박스
    </div>
    <div style="padding: 20px; margin: 20px; background-color: lightblue;">
      콘텐츠가 있는 박스
    </div>
    ```
    <br>
    <img src="https://github.com/user-attachments/assets/da89b7e3-849d-4c82-b1e9-d5793d0800ba" alt="image" style="zoom:80%;" /><br>
    <img src="https://github.com/user-attachments/assets/ee779963-18e1-4fc8-a9de-db2540c18ed9" alt="image" style="zoom:80%;" /><br>
    <img src="https://github.com/user-attachments/assets/f3817275-edb9-422b-800e-1255bc10ba9f" alt="image" style="zoom:80%;" /><br>
    <img src="https://github.com/user-attachments/assets/6b0b8162-0d79-4c39-a002-12cc4c865bce" alt="image" style="zoom:80%;" />
    </div>
    </details>
* **fragment를 head, header, footer, modal, scripts는 꼭 사용 중**
  - head는 \<head>에, header는 \<nav>에, footer은 \<footer>에, modal은 \<div>(class명은 modal)에, scripts는 \<script>에 사용 중!
  - **modal은 공.문 참고!**
* **데이터 표현** : `table, td, tr 등등` 또는 `div로 잘 구현`
  * `th:each` + `<th:block>` 도 적절히 함께 활용
  <details><summary><b>참고 코드</b></summary>
  <div markdown="1"><br>
    ```html
    <!-- 렌더링시 제거 block -->
    <th:block th:each="item : ${items}" th:object="${item}">
      <div th:class="${item.id==items[0].id}? 'gallery-item-first' : 'gallery-item-many'"
      style="flex:1; padding-top: 30px;">
        <div class="d-flex flex-column pe-4" style="flex:0.15;">
          <span class="" style="font-weight: 500; color: white; font-size: 1.2vw;" th:text="'No.'+*{No}">No.15</span>
          <span class="pt-2" style="font-weight: 400; color: var(--text-2); font-size: 1vw; letter-spacing: -0.16px;
          " th:field="*{{date1}}" th:text="*{{date1}}">23.05.22.16:00k</span>
        </div>
        <div class="d-flex flex-column pe-4" style="flex:0.42;">
          <span class="text-truncate" style="font-weight: 500; color: white; font-size: 1.2vw; width:25vw;" th:text="*{title}">최근에 있엇던 대외비</span>
          <span class="text-truncate pt-2" style="font-weight: 400; color: var(--text-2); font-size: 1vw; letter-spacing: -0.16px; width:25vw;
          " th:text="*{nickName} ">방문자가작성한닉네임</span>
          <a class="btn btn-light mt-4" type="button" id="enterBtn" onclick="redirectSavedBgm()"
          style="font-size: 1vw;
          align-self: flex-start; width: 12vw; padding:0.5em;"
          href="#" th:href="@{|/gallery/${pageId}/itemDetail/*{id}|}">
            <span class="fw-bold" id="enterBtn1" style="font-size: 1em; white-space: nowrap;" th:text="|*{No}전시실 입장|">15전시실 입장 test용</span>
          </a>
        </div>
        <div class="d-flex flex-column" style="flex:0.43;">
          <img class="img-fluid" src="../static/6.png"
             th:src="@{|/image/*{imgSrc}|}"
          style="height:15vh; border-radius: 10vh 10vh 0 0;"/>
        </div>
      </div>
    </th:block>
    ```
  </div>
  </details>
* **FORM 데이터** : `label, input, 체크박스 등등` 권장
  * `th:field`(name,id,value자동생성) 와 `*{...}` 랑 `th:object`(데이터 관리쉽게) 함께 사용 권장
  * `th:errors` 등등 도 함께 사용<br>-> ex: `<div class="field-error" th:errors="${item.imgSrc}">이미지 오류</div>`
    * 이 문장은 **`item.imgSrc` 필드에 오류가 발생했을 경우**에만 해당 `<div>` 태그가 렌더링되며, 오류 메시지가 출력됩니다. 오류 없으면 이 태그는 렌더링 되지 않음!
    * \<div> 태그의 기본 텍스트 "이미지 오류"는 오류 메시지가 없을 경우 기본 메시지로 사용할 수 있습니다. 그러나 보통은 **Spring Validation에서 오류 메시지를 자동**으로 가져옵니다.
* **문법 잘 활용**
  * \|\...\| : `<span th:text="|Welcome to our application, ${user.name}!|">`
  * @{} : 간편) -`th:href="@{|/basic/items/${item.id}|}"`
  * 등등 아래 문법 정리 참고...
</div>
</details>


<br>

<details><summary><b>타임리프 문법</b></summary>
<div markdown="1">
* **타임리프 사용 선언**
  * `<html xmlns:th="http://www.thymeleaf.org">`
* **속성 변경**
  * `th:href="@{/css/bootstrap.min.css}"`
  * `th:onclick="|location.href='@{/basic/items/add}'|"`
  * `<td th:text="${item.price}">10000</td>`
  * `th:value="${item.id}"`
  * `th:action`
  * ... 등등 매우 다양
* **URL 링크표현식 - @{...}**
  * `th:href="@{/css/bootstrap.min.css}"`
  * `th:href="@{/basic/items/{itemId}(itemId=${item.id})}"`
    * 생성된 링크: `http://localhost:8080/basic/items/1`
  * `th:href="@{gallery/productDetail/(id=${item.id})}"`
    * 생성된 링크: `http://localhost:8080/gallery/productDetail/?id=1`
    * 심화) `th:href="@{/basic/items/{itemId}(itemId=${item.id}, query='test')}" `
      * 위 2개 둘 다 사용한 방식임. **{itemId}(itemId=${item.id})랑 (id=${item.id})**
      * 생성된 링크: `http://localhost:8080/basic/items/1?query=test`
  * **간편) `th:href="@{|/basic/items/${item.id}|}"`**
    * **"리터럴 대체" 문법도 적용가능 => 이거 함께 쓰자(아래 참고)**
* **리터럴 대체 - \|\...\|**
  * 타임리프에서 **문자**와 **표현식** 등은 분리되어 있기 때문에 **더해서 사용**해야 한다.
    * `<span th:text="'Welcome to our application, ' + ${user.name} + '!'">`
  * 다음과같이 리터럴 대체문법을 사용하면, **더하기 없이 편리**하게 사용할 수 있다.
    * `<span th:text="|Welcome to our application, ${user.name}!|">`
    * `th:onclick="|location.href='@{/basic/items/{itemId}/edit(itemId=${item.id})}'|"`
* **변수표현식 - ${...}**
  * `<td th:text="${item.price}">10000</td>`
* **반복출력 - th:each**
  * `<tr th:each="item : ${items}">`
  * 컬렉션의 수 만큼 `<tr>..</tr>` 이 하위 태그 반복 생성!
  * ```html
    <table>
        <tr th:each="item : ${items}">
            <td th:text="${item.id}"></td>
            <td th:text="${item.name}"></td>
            <td th:text="${item.price}"></td>
        </tr>
    </table>
    ```
* **조건문 - th:if or Default**
  * **th:if 문들은 false인 경우 아예 태그를 렌더링을 안함. 그럴 경우 사용!!**  
    param?의 ?는 null safe 처리 지원
    * `<h2 th:if="${param?.status}" th:text="'저장 완료'"></h2>`
    * `<h2 th:unless="${param?.status}" th:text="'저장 실패'"></h2>`
  * **삼항 연산자 + Default(=Elvis 연산자) 경우**
    * 삼항 연산자: `th:text="|B1 ~ B${(totalCount!=null) ? (totalCount/10+1) : '??'}F|"`
      * 실제로 **B1 ~ B4F** 이런식으로 출력
    * Default(=Elvis 연산자) 활용 : `th:text="|B1 ~ B${(totalCount) ?: '??'}F|"`
      * Default는 **totalCount**가 유효한 값이 있으면 그 값을 사용!<br>0이거나 null이면 **"??"가 출력!** -> 즉, 자동으로 null을 잡아줌! 
      * 다만, `(totalCount/10+1)` 이라면?<br>null/10+1 로 에러떠서 이런 경우는 위처럼 **it-then-else 사용**
    * 추가정보) `"${data}? : _"` 라면? 
      * No-Operation : "_" 로써 마치 타임리프 실행 안한것처럼 동작
* **변수선언 - th:with**
  * `th:with="first=${users[0]}"` -> frist 로 재사용 가능
  * ```html
    <div th:with="first=${users[0]}">
        <p th:text="${first.name}">User Name</p>
    </div>
    ```
* **태그 인식 유무 - text, utext == [[...]], [(...)]**
  * text vs utext -> 속성 사용
    - th:text = Hello \<b>Spring!\</b>
    - th:utext = Hello **Spring!** -> 진하게 태그(\<b>) 자동 적용된 모습
  * [[...]] vs [(...)] -> 속성이 아니라 컨텐츠 안에서 직접 출력!
    - [[...]] = Hello \<b>Spring!\</b>
    - [(...)] = Hello **Spring!**
* **편의 객체 제공 - param, session 등**
  * `param.title` 같이 파라미터 바로 접근 가능하게 **편의 객체를 제공**
  * `session.user.name` 은 세션 바로 접근
* **비교연산(ex: >) - HTML 엔티티 주의!!** 
  * `>` : gt 로 표기 한다.
* **타임리프 파서 주석 :** `<!--/* [[${data}]] */-->`
  * 참고로 `/*사이에서 여러줄 가능*/`
  * 일반 HTML 주석과 비슷하지만, `/*`와 `*/` 사이에 있는 내용은 **타임리프의 파서에 의해 처리 된 후 렌더링 때 삭제**되는 것
* **블록 - \<th:block>**
  * `<th:block>` 는 타임리프가 제공하는 유일한 자체 **"태그"**
  * **렌더링 할때는 아예 태그가 삭제**
  * **\<div> 로 데이터 잘 표현 했을 경우 \<th:block> 추가하면 깔끔**
  * ```html
    <!-- 렌더링 후 아래 div 태그만 남게 되는 것 -->
    <th:block th:each="item : ${items}" th:object="${item}">
      <div th:class="${item.id==items[0].id}? 'gallery-item-first' : 'gallery-item-many'" style="flex:1; padding-top: 30px;">
      </div>
    </th:block>
    ```
* **fragment, js**
  * fragment : 코드 재사용
    * fragment생성: `<head th:fragment="head(title)">`
    * ```html
      <!DOCTYPE html>
      <html xmlns:th="http://www.thymeleaf.org">
        <head th:fragment="head(title)">
          <!-- Required meta tags -->
          <meta charset="utf-8">
          <meta name="viewport" content="width=device-width, initial-scale=1">
          <!-- Bootstrap CSS -->
          <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.1/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-4bw+/aepP/YC94hEpVNVgiZdgIC5+VKNBQNGCHeKRQN+PtmoHDEXuppvnDJzQIu9" crossorigin="anonymous">
          <!-- 커스텀(기본) CSS -->
          <link rel="stylesheet" type="text/css" media="all" 
                href="../static/basic.css" th:href="@{/basic.css}"/>
          <title th:text="${title}">타이틀 화면</title>
        </head>
        <body>    
        </body>
      </html>
      ```
    * fragment 적용: `<head th:replace="fragments/head :: head('스튜디오')">`<br>**fragments/head** 는 **resources/templates/fragments/head.html** 경로를 의미
    * ```html
      <head th:replace="fragments/head :: head('스튜디오')">
      </head>
      ```
  * js : 타임리프 문법 inline으로 javascript 에서 사용 가능 + css도 마찬가지
    * ```html
      <script th:inline="javascript">
      var pageCount = /*[[${(totalCount/10)+1}]]*/ null;
      // 물론, /* */ 주석을 제거해도됨. 단지 빨간줄 떠서 추가함.
      ```
* **\<input>과\<label> 에서 th:for로 id값 연결 하는 편 -> 보통 form 에 잘 구성**
  * 동적 id 인식도 지원 됨! - `#ids.prev()`
  * 참고: \#ids.prev('regions')를 사용한 이유는 label에서 for 속성에 input의 id 속성과 맞추기 위해서 입니다.
    이것을 맞추어 두어야 label을 선택했을 때, 그러니까 **글자를 선택했을 때도 input 요소가 선택되기 때문**입니다.
  * HTML의 기본 동작: `label`의 `for` 속성이 `input`의 `id`와 일치하면, 사용자가 `label`을 클릭할 때 브라우저가 자동으로 해당 `input` 요소에 포커스를 주도록 설계되어 있습니다.
    ```html
    <!-- 대충 이런느낌으로 연결 해두면 된다는 거~! -->
    <div class="container" th:each="region : ${regions}">
        <label th:for="${region.id}">지역 선택:</label>
        <input type="text" th:id="${region.id}" placeholder="지역을 입력하세요">
    ```
* **보통 \<table> \<tr> \<td> \<th> 형태로 데이터를 표현** 
* **젤 중요!! th:field 는 "검증"에 매우 유용해서 무조건 사용**<br>**여러개 쓸땐 th:object로 관리쉽게끔 함께 고고**
  * value 속성에 값 자동 삽입 + **id, name 속성 자동 생성** + 체크박스도 자동체크 등 -> **input태그나 체크박스**에 주로 사용 (그냥 다 사용하면 되긴 해~)
  * `*{...}` : 선택 변수 식으로써 **th:object** 에서 선택한 객체에 접근
    * **\*\{\{...}}**: `*{date1}` 은 @DateTimeFormat 표현식(타입컨버터)이 안 먹히기 때문에 \*\{\{date1}} 로 지정하는 것이다.
</div>
</details>



<details><summary><b>타임리프 + 스프링 통합 문법</b></summary>
<div markdown="1">
* **th:object, th:field, *{itemName} 활용**
  * Form과 함께 Input, 체크박스, 라디오버튼, 셀렉트 박스에서 주로 활용
  * TIP) addForm, editForm 이렇게 2개 따로 만드는게 개발하기 수월
* **(중요)"메시지, 국제화 기능"**
  * `application.properties` 에 `spring.messages.basename=messages` 를 추가!!
  * `messages.properties` 를 생성해서 messages에 담을 내용을 세팅
    * properties에 `page.addItem=상품 등록` 메시지 추가
    * 타임리프로 `<h2 th:text="#{page.addItem}">상품 등록</h2>` `
  * 여러개 추가할거면?? 예로 errors.properties 추가한다고 하면 =message, errors 이렇게 이어적으면 됨
* **(참고) nullsafe**
  * `th:if="${errors?.containsKey('globalError')}"` 에서 ?를 통해 null 로 나타나므로 if는 false로 반환
  * ?가 없으면 null.containesKey... 로 에러
* **"컨트롤러"에서 @GET** 으로 페이지 로딩할때 **item을 빈값이라도 선언**해둬서 Model에 담아 반환하는걸 권장
  * **검증 실패 때 forward로 "자원 재활용"이 됨.**
  * HTML 코드도 더 깔끔 -  if문으로 null인지 확인하는 코드 필요없이 그냥 item을 타임리프 문법으로 사용하면 되기때문
  * **Model에 빈 객체를 미리 담아 전달**하면, 타임리프가 폼을 렌더링할 때 **객체가 null인지 확인할 필요 없이 바로 사용할 수 있기 때문 (코드 더 깔끔)**
    * **폼 페이지에서 제출된 데이터**가 검증에 실패하면, 다시 폼 페이지로 돌아가야 합니다. 이때, 새로운 GET 요청(redirect)을 발생시키지 않고, **forward**로 같은 페이지를 다시 렌더링합니다.
    * 이렇게 하면 **입력한 값들이 그대로 유지**되므로, 사용자는 데이터를 다시 입력할 필요가 없습니다.<br>타임리프에서 `th:field`를 사용하면, **검증 실패 시 자동으로 폼 필드에 사용자가 입력한 값**을 다시 채워줍니다. 이게 가능하려면, 컨트롤러에서 **빈 값이 아닌 객체**를 넘겨주는 것이 중요합니다.
  * ```java
    @Controller
    public class ItemController {
        @GetMapping("/items/new")
        public String showItemForm(Model model) {
            // 빈 Item 객체를 Model에 추가
            model.addAttribute("item", new Item());
            return "itemForm";  // form 템플릿을 반환
        }
        @PostMapping("/items/new")
        public String createItem(@ModelAttribute("item") Item item, 
                                 BindingResult bindingResult,
                                 RedirectAttributes redirectAttributes) {
            // 검증 실패 시
            if (bindingResult.hasErrors()) {
                return "itemForm";  // 다시 폼을 forward하여 자원 재활용
            }
            // 성공적으로 저장한 후
            redirectAttributes.addAttribute("status", "success");
            return "redirect:/items/" + item.getId();
        }
    }
    ```
  * ```html
    <form th:object="${item}" th:action="@{/items/new}" method="post">
        <label for="name">Item Name</label>
        <input type="text" th:field="*{name}" id="name" />
        <label for="price">Price</label>
        <input type="number" th:field="*{price}" id="price" />
        <button type="submit">Save</button>
    </form>
    ```
</div>
</details>

<br><br>
