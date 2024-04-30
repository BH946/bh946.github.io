---
title:  "[Html, Jsp, Sql] JSP를 활용한 웹프로그래밍(1)"
categories : WebProgramming
tag : [html, jsp, sql, 웹프로그래밍]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
---



## Intro

웹프로그래밍을 할 때 크게 분류 3가지

* 클라이언트 : `HTML, CSS, JavaScript 등`
  * 알아둘점 -> **"컴파일 없이"** 바로 구동!

* 서버 : `JSP,PHP,ASP,Node.js,Go웹 등`
  * 서블릿 : **웹페이지를 동적**으로 생성하는 서버 프로그램이다. 
    * **JAVA코드안에 HTML코드**

  * Java Server Pages(JSP) : **서블릿을 더 편하게 사용하기 위해** 나온 서버사이드 스크립트 기술이다. 
    * **HTML코드에 java코드**

* DB : `SQL 등`
  * DB(=데이터베이스) : S/W, H/W, Language와 다른것이며 **체계/구조적**으로 잘 정리된 **data들의 집합체**
  * DBMS는 MySQL같은 시스템을 의미하며 S/W 에 포함
    * 참고 : MongoDB는 NoSQL(=SQL언어가 아닌 새로운 언어) 사용
  * JDBC(= DB를 다루기 위한 자바 API) : DBMS 상관없이 SQL문을 던져서 실행하게 해줌
    * 실행 순서 : Driver 로드(DBMS) -> DB 연결(JDBC) -> SQL문 
  * SQL(=Language) : C언어 같이 언어를 의미

<br>

## 1. HTML

### 특징

* html은 컴파일 과정이 없으므로 문법오류 있어도 무시하고 실행
* 웹 브라우저(=크롬같은 브라우저를 의미)가 하는일 : html소스를 웹 화면으로 표시
* 통신방식(ex:HTTP규약) : get, post

  * get은 보안X => 작은정보, 공개가능 정보가 어울림

  * post는 보안O => get의 반대로 생각. 큰 정보, 비공개 정보가 어울림 

<br>


### 문법정리(간략히)

* 큰따음표는 필수는 아님.  **기본 " " , ' '**  
  단, 공백에는 큰따음표나 작은따음표 해줘야함.
* 대소문자도 상관없다. (**소문자 권장**일뿐)  
  단, 서버쪽에서는 대/소 다르다.
* html5에서는 태그에 ''/'' 빼는걸로 합의!! **조건은 솔로인 태그만**  
  단, 짝지있는 html ~ /html이런건 "/" 해줄것



#### 1. 기본

* `&nbsp` 는 no-break space의 약자 (1칸 띄움)  
  * 소스에 빈공백은 실제 웹에 스페이스 한번(줄이 바뀌든 간에 빈공백은 스페이스 한번을 의미)

* `<h1~h6>`은 한줄을 포함한 진하게 큰 글자 이다(보통 제목에 사용)
* `<hr>` 가로로 한줄 선 생성.
* `<br>`은 한줄 띄움, `<p>`는 문단마다 띄우는 단위 즉, `<br><br>`과 비슷
* `<strong>`글자 진하게, 예전문법은 `<b>`
* `<a href="">`는 URL 링크 작성
  * `a태그의 target 속성`이 “\_blank”라면 새창을 띄운다. “_self”는 새창을 띄우지 않는다.(=기본값)

* `<select>`는 name속성을 사용한다
  * size속성은 input태그와 구분이 중요하다.
    * `<select>`는 해당 값 만큼 item을 보여준다. 
    * `<input>`은 길이를 의미한다.

* `<option>`은 `<select>`의 하위에 item을 적는다. 
  * `value속성`을 가진다.

* `<input>`은 `type="자료형" name="변수 명" value="입력창에 띄워진 디폴트값"` 속성을 가진다.  
  * type속성에 넣어줄 다양한 속성값들을 소개한다.
    * "text"는 텍스트, "password"는 ***
    * "radio"는 O로 표시한다. “checkbox”는 radio와 다르게 name속성을 같게 해도 멀티 선택이 가능하다.  따라서 배열로 전송이 된다.
      * name속성값이 동일한 radio는 오직 1개만 선택가능.  

    * “submit”은 form태그를 통해 정보를 제출할 수 있다. “button”,“reset”등 다양한 버튼 태그들이 존재한다.
    *  “hidden”타입은 데이터 값만 서버쪽으로 보낼 때 사용하기 좋다.

  * `required속성`이 있으면 반드시 값을 입력해야 한다.

* `<table>`은 표이다.   
  * `border속성`은 0이 기본값(투명라인)  
  * 한줄은 `<tr>`이고 데이터는 `<th>`, `<td>`이다. 
    * 성격 -> th : 진하게 가운데 정렬, td : 평범 왼쪽정렬  
    * 참고) `rowspan, colspan속성`을 `<td>`에서 사용시 테이블 셀 병합을 한다.

* `<form>`은 서버로 데이터를 보낼 수 있다.  
  * `method속성`은 get, post형식 중 하나로 데이터 전송방식을 정한다.
  * `action속성`은 보내는 위치(URL)을 정할 수 있다.
  * input태그 type 속성값이 `submit`인 버튼을 누르면 **action을 수행**한다.




#### 2. id, class 정리

* id는 스타일을 지정할 때 한 가지만 지정해서 쓰는 이름이다.(표기방식은 #이름)  
  * 하나의 문서에 고유한 id 하나 밖에 쓸 수 없다.
  
* class 는 그룹으로 묶어서 스타일을 지정할 때 쓰는 이름이다.(표기방식은 .이름)  
  * 하나의 문서에 여러번 쓸수 있다. 고유값이 아님.  

* 실제 사용방식) id와 class를 html에서 속성처럼 지정해서 사용하면 된다. 
  * `<div class="menu">` 라던지 `<p id="btn">`라던지




#### 3. 구역

* `<div>`는 화면 구역을 나누기 좋다.   
  * `align="center"` 등을 통해 정렬도 할 수 있다.
  * `style` 을 지정해서 스타일링을 많이 하는 편이다.
  
* `<span style="color:blue"><%=a%>\</span>` 글자색 파랑으로 바꿔준다.  
  * span은 한 문장 색 변환과 같이 한 문장에 주로 사용한다.

* `<fieldset>`는 그룹화 해주는 태그  
  * `<legend>`와 함께 사용




#### 4. 텍스트, 그림

* `<em>` 기울여 쓰기
* `<mark>` 형광펜 효과
* `<sup>` 위첨자
  * 예 : `x<sup>2<sup/>` x<sup>2<sup/>

* `<sub>` 아래첨자
* `<img src="~.jpg" alt="사진을 못찾">` img태그는 그림불러오기, alt속성은 그림X 때 글이 대신
  * 속성 width="200" height="300" 경우 그림 크기 조절

* `<font color="blue">~</font>` 글꼴 태그의미
* `<textarea>`는 텍스트 입력 태그이다.(input태그의 text타입과는 차이가 있다)  
  * 속성: cols, rows => textarea의 크기

* `<ul>~</ul>` 글머리 기호(동그란점)
  * `<ol>~</ol>` 번호매기기(순서O)
  * 하위에 `<li>` 하나하나 나열해서 LIST 형성(순서X)

* 선호도 : 1 `<input type="range" min="1" max="5" value="3">` 5
  * `range 속성` 은 막대바 같은것을 의미한다.


<br><br>

## 2. JSP

### 특징

* `.jsp(JSP) -> .java(서블릿) -> .class`
  * 톰캣은 WS, WAS 둘다이다. (WAS안에 서블릿 컨테이너 가짐)
  * `.jsp(JSP) -> WAS -> .java(서블릿)` 이렇게 볼 수도 있다.
* jsp소스에는 html, java파트가 있다.
  * <%~~%>안의 내용이 java파트, 나머지는 html파트
  * 그래서 html소스에 java파트는 빈공간으로 나타내는 경우도 있음.
  
* jsp에서 큰따음표 생략X, html에선 생략 가능!

<br>

### 문법정리(간략히)

#### 1. 기본

* <%~~%>안의 내용이 JAVA파트, 나머지는 html파트

* 페이지 지시자 표기 : <%@ page~~ %> 여기서는 UTF-8로 통일
  * <%@page import~~%> page 지시자
  * <%@include ~~ %> include 지시자
* 주석 
  * java파트 : `//, /*~*/`
  * html파트 : html주석, jsp주석
    * html주석 : `<!-- -->`
    * jsp주석 : `<%-- --%>`
  * 페이지 소스에 안나오고 싶다?? jsp주석사용.(html주석은 페이지소스에 나오니까 탈락!)
* 스크립트릿(자바파트) : `<% ~~~%>`
* 표현식 : `<%=값1개(변수명)%>` 
  * out.print()와 동일 기능
* **부호 다시 정리**
  * <% ~~~%> 자바파트 => 스크립트릿
  * <%@ ~~ %> 는 지시자
  * <%-- --%> jsp주석
  * <%=값1개(변수명)%> - 표현식(변수, 수식, 메소드호출 등)
  * <%! ~~ %> 선언문



#### 2. JSP 내장객체 9가지

"내장객체명.메소드명"으로 호출한다.

`request, response, out, session, application, pageContext, page, config, exception`

스크립트릿 안에서 JSP 내장객체를 지역변수 명으로 선언 불가.  
선언문에서는 가능하지만 혼동 피하기위해 아무도 사용 안함.

**request**

* `request.setCharacterEncoding("UTF-8");` : 고객이 보내는 한글이 안깨지게 하는것!
  * 다른 한글안깨지는것은? 
    * html의 `<meta charset="UTF~`
    * jsp의 `<%@page ~~~UTF~~%>`
* `request.getParameter("aa");` : 값 가져오기.

**response**

* `response.sendRedirect("https://~~");`
  * 개발자가 사용하는 리다이렉트
  * 고객은 a태그나 form태그 사용



#### 3. 액션태그

`include, forward, +param`

* XML문법 따르는 액션태그

* `<jsp:` 형식으로 사용
* solo 태그는 '/' 생략X(XML문법), html5는? 빼는걸로 합의
* include 지시자 vs include 액션태그의 차이점 알기!
  * 소스가져옴 vs 실행결과 가져옴.
  * `<jsp:include page="bottom.jsp"/>` 는 사용 및 결과 가져옴
* forward 액션태그는sendRedirect()와 99%비슷!!(페이지 강제이동)
  * 1%가 다른 이유? a.jsp 에서 forward하면 b.jsp에서 주소창이 a.jsp로 변하지 않는다.
  * redirect는 클라에서 동작하고, forward는 서버에서 동작한다.
  * 아직 궁금하다면 리다이렉트와 포워드를 검색해보자



#### 4. 데이터 전송 방법들 정리

* form태그 안에서 text, radio, select등을 통해 사용자가 입력한 자료전송(get/post)

* form태그 안에서 input태그의 type속성값을 hidden으로 해서 전송

* url에 직접 붙이기(get 전송 방식)

* 액션태그 param을 사용

<br><br>

## 3. SQL문

### 특징

* DB에서 사용하는 Language입니다.

<br>

### 문법정리(간략히)

* `--` : 주석
* `show databases;` : db들 보여줌
* `create database db이름;` : db를 생성
* `use db이름;` : 해당 db사용
* `show tables;` : db안의 테이블들 보여줌
* `create table tb이름 ( 테이블 구성.. );` : 테이블 생성 방법
  * `name varchar(10),` : 문자열(데이터 타입)
  * `userid varchar(10) not null,` : Null 허용X
  * `admin int,` : 정수
  * `day date,` : 날짜 (datetime도 있는데 이건 시간까지)
  * `primary key(userid)` : 고유값 (여기까지 실행시 userid는 not null과 Key PRI 됨)
* `desc tb이름;` 특정 테이블의 컬럼정보 확인(열 정보)
* `drop table tb이름;` 테이블 삭제
* (1) `insert into tb이름 values ('이소미','somi',12,'날짜,,,');` 이런식으로 컬럼에 맞춰서 다 적던지
  * 큰 따옴표말고 **작은 따옴표** 사용바람. (MySql은 큰 따옴표도 가능은함)
  * values, value 둘다 가능이지만 **values를 권장.**
* (2) `insert into tb이름 (name,userid,pw,email,phone,admin) values ('이소미','somi','1234', 'gmd@naver.com','010-2362-5157',0);` 이런식으로 컬럼 골라서 적던지
  * 이런형식으로 할 경우 안쓰려는 컬럼은 **NULL이 꼭 허용**되어야 에러가 발생X
    * not null한 경우 에러 뜰 가능성 있다.
* `select name, admin from tb이름;` 필요한 컬럼만 확인.
* `select * from tb이름;` 모든 컬럼 보겠다. (*=all)
* `select * from tb이름 where name='이소미';` 특정 row(=레코드)만 보겠다.
  * where에 여러개 넣는법은 and 넣기
* `update tb이름 set name='gogi' where userid='somi';` : where없으면 모든 데이터 name부분 일괄수정
  * 여러개 컬럼 업데이트는 `set name='gogi', admin=4` 이런식으로 콤마 추가
* `delete from tb이름 where userid='somi';` where없으면 모든 데이터 삭제
* 암기 : set은 `콤마`로 구분, where은 `and`로 구분
* 암기 : from사용 구문은 delete와 select -> `delete from tb이름 where~` 과 `select * from tb이름 where~`
* MySQL에서 제공하는 현재일시 구하는 함수 : `now()`
* 예시들
  * (1) : `select count(*) from bankmember_;` count(id) 이런것도 가능.  
    (2) : `update bankmember_ set balance=200000000 where id='bread';`  
    (3) : `select sum(balance) from bankmember_;`  
    (4) : `select max(no) from bankmember_;`

<br><br>

## 4. JSP를 통해 웹화면에 DB데이터 출력방식

### 특징

* `rs.getString("name") => rs.getString(1)` 상관없음. 인덱스가 기존과 다르게 여기선 1부터 시작

* `import="java.sql.*"` 부분 페이지 지시자 꼭 필요 => SQL문 사용위해

* 참고로 페이지 지시자에 `errorPage="error.jsp"` 넣어줌으로써 예외처리 하는법 기억.

  * 따로 만든 에러페이지 안에서는? 자신이 에러페이지 라는걸 **페이지 지시자에 먼저 선언.**

    ```jsp
    <%@ page isErrorPage="true"%> => 페이지 지시자에 선언
    exception.getClass().getName() => 에러종류
    exception.getMessage() => 에러내용
    ```

<br>

### 문법정리(간략히)

#### 기본 방식 - 3C의 예

1. Class.forName => 드라이버 로드 com.mysql.jdbc.Driver => com.mysql.cj.jdbc.Driver (암기할필욘 x)
2. DriverManager.getConnection(url,uid,pass) => DB 연결
3. createStatement() or prepareStatement() 는 3C와 3P => SQL문 담을 준비
4. executeQuery(sql)(는 s사용) or executeUpdate(sql)(는 i,u,d경우) => SQL문 수행(select경우 ResultSet 타입으로 받아야 함)
5. next() => select 경우 조회된 데이터들 중 한 row씩 가져옴. 예로 rs.next()시 rs.getString("")통해 가져올수있음.(getInt또한)
=> select는 5번까지, i/u/d는 4번까지 확실히 있어야 함.  
=> select는 from다음 where다음 order by 이름(id라던지) 이렇게 다양하게 있음.(order by id는 id 순서대로)  
=> 참고로 executeQuery를 i/u/d도 사용해도 되지만 비효율적이라서 Update로 사용하는거임.   
=> 참고로 executeUpdate도 사실 반환값이 존재한다. (update를 몇개 했는지 이런것 반환)



#### 3P의 경우

1. String sql = "insert into bankmember_ values(?, ?, ?, ?, 0, now())"; => 이런식으로
2. PreparedStatement stmt = conn.prepareStatement(sql); => sql이 여기에!!
3. stmt.setString(1,id); stmt.setString(2,pw);... => ?를 여기서 넣는거임. id는 지역변수
4. stmt.executeUpdate(); => sql여기 없음!!



#### 3P -> 3C 바꿀때의 경우

1. SQL문(변화 과정 보여줌)
  (0) = "update bankmember_ set pw=?,name=?,phone=? where id=?"; => 3P방식 SQL문  
  (1) = "update bankmember_ set pw='pw',name='name',phone='phone' where id='id'";  
  (2) = "update bankmember_ set pw='"+pw+"',name='"+name+"',phone='"+phone+"' where id='"+id+"'";    
  => 3C방식 SQL문으로 변화 완료
2. PreparedStatement들 -> Statement
3. createStatement(sql); -> createStatement();
4. executeUpdate(); -> executeUpdate(sql);
5. pre.setString(1,pw);관련 전부 삭제  
=> 끝이지만, 3C형식 쓰니까 변수명도 pre에서 cre 로 바꾸는걸 추천.
