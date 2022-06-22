---
title:  "[Html, Jsp, Sql]Web_Programming 기초"
categories : WebProgramming
tag : [html, jsp, sql, 웹프로그래밍]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
---

**=> 자세한건 slid파일(워드파일) 정리본 보기.**

* 클라이언트 : HTML, CSS, JavaScript 등..
* 서버 : JSP,PHP,ASP,Node.js,Go웹 등..
* DB : SQL 등..
  * DB = 데이터베이스 => S/W, H/W, Language와 다른, 체계/구조적으로 잘 정리된 data들의 집합체
    JDBC = DB를 다루기 위한 자바 API => DBMS 상관없이 SQL문을 던져서 실행하게 해줌.
    즉, Driver 로드(DBMS) -> DB 연결(JDBC) -> SQL문 실행 순서
    SQL은 Language(C언어 같이 언어를 의미.)
    DBMS는 MySQL같은 시스템 => S/W 에 포함.
    참고 : MongoDB는 NoSQL(SQL언어가 아닌 새로운 언어 사용)

<br>

## 1. HTML

### 특징

* html은 컴파일 과정이없으므로 문법오류 있어도 무시하고 실행
* 웹 브라우저(=크롬같은 브라우저를 의미)가 하는일 : html소스를 웹 화면으로 표시
* 통신방식(HTTP규약) : get, post

  * get은 보안X => 작은정보, 공개가능 정보가 어울림

  * post는 보안O => get의반대로 생각. 큰 정보, 비공개 정보가 어울림 




### 문법정리(간략히)

* 큰따음표는 필수는 아님.  기본 " " , ' '  
  단, 공백에는 큰따음표나 작은따음표 해줘야함.
* 대소문자도 상관없다. (소문자 권장일뿐)  
  단, 서버쪽에서는 대/소 다르다.
* html5에서는 태그에 ''/'' 빼는걸로 합의!! 조건은 솔로인 태그만  
  단, 짝지있는 html ~ /html이런건 "/" 해줄것

#### 1. 기본

* &nbsp 는 no-break space의 약자 (1칸 띄움)  
  소스에 빈공백은 실제 웹에 스페이스 한번(줄이 바뀌든 간에 빈공백은 스페이스 한번을 의미)

* <h1~h6>은 한줄을 포함한 진하게 큰 글자 이다(보통 제목에 사용)

* \<hr> 가로로 한줄 선 생성.

* \<br>은 한줄 띄움, \<p>는 문단마다 띄우는 단위 즉, \<br>\<br>과 비슷

* \<strong>글자 진하게, 예전문법은 \<b>

* \<a href="">는 프로토콜 꼭 작성(예 : https://www.,,,,)  
  target 속성이 “\_blank”라면 새창을 띄운다. “_self”는 새창을 띄우지 않는다.(기본값)

* \<select>는 name속성이 기본적으로 있으며, size속성이 input태그와 구분 해야한다.  
  size속성을 통해 해당 값만큼 item을 보여준다. \<input>에서는 길이를 의미.

* \<option>은 \<select>의 내부에 item을 적는 것이다. value속성을 가진다.

* \<input>의 속성은 type="자료형" name="변수 명" value="입력창에 띄워진 디폴트값"  
  type속성에 넣어줄 "text"는 글자그대로 텍스트의미  
  "password"는 ***로 표시, "radio"는 O표시다.   
  “submit”은 form태그에서 정보를 제출할 수 있다.  
  “button”,“reset”등 다양한 버튼 태그들이 있음.  
  “checkbox”는 radio와 다르게 name속성을 같게 해도 멀티 선택이 가능함.  따라서 배열로 전송이 됨.  
  “hidden”타입은 데이터 값만 서버쪽으로 보낼 때 사용 => name속성값이 동일한 radio는 오직 1개만 선택가능.  
  required속성이 있으면 반드시 값을 입력해야함.

* \<table>은 표이다.   
  border속성은 0이 기본값(투명라인)  
  한줄은 <tr>이고 데이터는 <th>, <td>이다. th : 진하게 가운데정렬, td : 평범 왼쪽정렬  
  rowspan, colspan속성을 <td>에서 사용시 테이블 셀 병합을 함.

* \<form>은 서버로 데이터를 보낼 수 있음.  
  method속성은 get, post형식으로 데이터 전송방식을 정함.  
  action속성은 보내는 위치를 정할수 있음.  
  form안에 input태그의 타입이 submit인 버튼을 누르면 action을 수행한다.

#### 2. id, class 정리

* ID 는 스타일을 지정할 때 한 가지만 지정해서 쓰는 이름이다.(표기방식은 #이름)  
  => 하나의 문서에 고유한 Id 하나 밖에 쓸 수 없습니다.  
* class 는 그룹으로 묶어서 스타일을 지정할 때 쓰는 이름이다.(표기방식은 .이름)  
  => 하나의 문서에 여러번 쓸수 있다. 고유값이 아님.  
* id와 class를 html에서 속성처럼 지정해서 사용하면 된다. 
  => \<div class="menu"> 라던지 \<p id="btn">라던지

#### 3. 구역

* \<div>는 화면 구역 나눔.   
  align="center"같은 경우 align속성은 center정렬.

* \<span style="color:blue"><%=a%>\</span> 글자색 파랑으로 바꿔줌.  
  => 구역 나누는것 div, sapn 2개이고, span은 한 문장 색 변환 등 이럴 때 사용.  
  style속성은 CSS와 관련, 문법 : style = "~~:~~"    예전엔 \<body bgcolor=" ">

* \<fieldset>는 그룹화 해주는 태그  
  \<legend>와 함께 사용

#### 4. 텍스트, 그림

* \<em> 기울여 쓰기, \<mark> 형광펜 효과, \<sup> 위첨자(예:x^2), \<sub> 아래첨자

* \<img src="~.jpg" alt="사진을 못찾"> img태그는 그림불러오기, alt속성은 그림X 때 글이 대신함.  
  속성 width="200" height="300" 경우 그림 크기 조절

* \<font color="blue">~\</font> 글꼴 태그의미

* \<textarea>는 텍스트 입력 태그이다.(input태그의 text타입과는 차이가 있음)  
  속성: cols, rows => text area의 크기

* \<ul>~\</ul> 글머리 기호(동그란점), \<ol>~\</ol> 번호매기기  
  => 내부에 \<li> 하나하나 나열해서 LIST 형성(순서X와 순서O)
* 선호도 : 1 \<input type="range" min="1" max="5" value="3" > 5  
  => range 속성이다. (막대바같은)

<br>

## 2. JSP

### 특징

* .jsp -> .java-> .class
  JSP     서블릿  
  * 톰캣은 WS, WAS 둘다이다. (WAS안에 서블릿컨테이너 가짐)
    => JSP -> WAS -> 서블릿(java) 이렇게 볼 수도 있다.

* jsp소스에는 html, java파트가 있고,  html소스에는 html파트는 그대로 들어가고 나머지 부분은 java파트가 들어간다!
  => 그래서 html소스에 java파트는 빈공간으로 나타내는 경우도 있음.

* jsp에서 큰따음표 생략X, html에선 생략 가능!



### 문법정리(매우 간략히)

```jsp
1.<%~~%>안의 내용이 JAVA파트, 나머지는 html파트
2.페이지 지시자 표기 : <%@ page~~ %> 여기서는 UTF-8로 통일
EX)
<%@page import~~%> page 지시자
<%@include ~~ %> include 지시자
3.주석 
java파트 : //, /*~*/
html파트 : html주석, jsp주석 : <!-- -->, <%-- --%>
=> 따라서 자바파트말고(1,2제외) html파트 주석넣고 싶은데 페이지 소스에는 안나오고 싶다?? jsp주석사용.(html주석은 페이지소스에 나오니까 탈락!)
4.스크립트릿(자바파트)
5.표현식(1개의 값을 찍어주고 싶을때 사용함) - out.print()와 동일 기능!

<부호 정리!>
<% ~~~%> 자바파트 => 스크립트릿
<%@ ~~ %> 는 지시자
<%-- jsp주석
<%=값1개(변수명)%> - 표현식(변수, 수식, 메소드호출 등)
<%! ~~ %> 선언문

### JSP 내장객체 9가지! ("내장객체명.메소드명"으로 호출)
request, response, out, session, application, pageContext, page, config, exception
스크립트릿 안에서 JSP 내장객체를 지역변수 명으로 선언 불가.
=> 선언문에서는 가능하지만 혼동 피하기위해 아무도 사용 안함.

request.setCharacterEncoding("UTF-8");
=> 고객이 보내는 한글이 안깨지게 하는것!
=> 다른 한글안깨지는것은? + html의 <meta charset="UTF~, jsp의 <%@page ~~~UT~
request.getParameter("aa");
=> 값 가져오기.

response.sendRedirect("https://~~");
=> 개발자가 사용
=> 고객은? a태그, form태그 사용

### 액션태그 액션태그 6개에서 내장객체 2개 배움.
include, forward, +param
=> XML문법 따르는 액션태그임!!
1.<jsp: 형식으로 사용
2.solo 태그는 '/' 생략X(XML문법), html5는? 빼는걸로 합의
3.include 지시자와, include 액션태그의 차이점 알기!
=> 소스가져옴, 실행결과 가져옴.
=> <jsp:include page="bottom.jsp"/> 이런식으로 사용 및 결과 가져옴
4.forward 액션태그는sendRedirect()와 99%비슷!!(페이지 강제이동)
=> 1%? a.jsp 에서 forward하면 b.jsp에서 주소창이 a.jsp로 변함X

<데이터 전송 방법들 정리>
1. form 안에서 text, radio, select등을 통해 사용자가 입력한 자료전송(get/post)
2. form 안에서 input태그의 type속성값을 hidden으로 해서 전송
3. url에 직접 붙이기(get)
4. 액션태그 param 사용
```





## 3. SQL문

### 특징

* Language입니다.
* mysql -u root -p; 로 접근



### 문법정리(매우 간략히)

```sql
-- 주석입니다.('--')
show databases; => db들 보여줌
create database db이름; => db를 생성
use db이름; => 해당 db사용
show tables; => db안의 테이블들 보여줌
create table tb이름 ( => 테이블 생성
-- 테이블 구성..
);

<테이블 구성부분>
name varchar(10), => 문자열(데이터 타입)
userid varchar(10) not null, => Null 허용X
admin int, => 정수
day date, => 날짜 (datetime도 있는데 이건 시간까지)
primary key(userid) => 고유값 (여기까지 실행시 userid는 not null과 Key PRI 됨)

desc tb이름; -- 특정 테이블의 컬럼정보 확인(열 정보)
drop table tb이름; -- 테이블 삭제

insert into tb이름 values ('이소미','somi',12,'날짜,,,'); 이런식으로 컬럼에 맞춰서 다 적던지.
=> 다만, 큰 따옴표말고 작은 따옴표 사용바람. (MySql은 큰 따옴표도 가능은함)
=> values, value 둘다 가능이지만 values를 권장.
insert into member_ (name,userid,pw,email,phone,admin) VALUES ('이소미','somi','1234', 'gmd@naver.com','010-2362-5157',0); 이런식으로 컬럼 골라서 적던지
=> 이런형식으로 할 경우 안쓰려는 컬럼은 NULL이 꼭 허용되어야 에러가 발생X
=> 즉, not null한 경우 에러 뜰 가능성 있다.
select name, admin from tb이름; -- 필요한 컬럼만 확인.
select * from tb이름; -- 모든 컬럼 보겠다. (*=all)
select * from tb이름 where name='이소미'; 특정 row(=레코드)만 보겠다.
=> where에 여러개 넣는법은 and 넣기.
update tb이름 set name='gogi' where userid='somi'; => where없으면 모든 데이터 name부분 일괄수정
=> 여러개 컬럼 업데이트는 set name='gogi', admin=4 이런식으로 콤마 추가
delete from tb이름 where userid='somi'; => where없으면 모든 데이터 삭제
즉, set은 콤마, where은 and
즉, from사용 구문은 delete from tb이름 where~ 과 select * from tb이름 where~
MySQL에서 제공하는 현재일시 구하는 함수 : now()
예시들
(1) : select count(*) from bankmember_; -- count(id) 이런것도 가능.
(2) : update bankmember_ set balance=200000000 where id='bread';
(3) : select sum(balance) from bankmember_;
(4) : select max(no) from bankmember_;

```





## 4. JSP를 통해 웹화면에 DB데이터 출력방식

### 특징

* rs.getString("name") => rs.getString(1) 상관없음. 인덱스가 기존과 다르게 여기선 1부터 시작

* import="java.sql.*" 부분 페이지 지시자 꼭 필요 => SQL문 사용위해

* 참고로 페이지 지시자에 errorPage="error.jsp" 넣어줌으로써 예외처리 하는법 기억.

  * 따로 만든 에러페이지 안에서는? 자신이 에러페이지 라는걸 페이지 지시자에 먼저 선언.

    ```jsp
    <%@ page isErrorPage="true"%> => 페이지 지시자에 선언
    exception.getClass().getName() => 에러종류
    exception.getMessage() => 에러내용
    ```

### 문법정리(매우 간략히)

```jsp
<기본 방식(3C의 예로 보여주겠다)>
1. Class.forName => 드라이버 로드 com.mysql.jdbc.Driver => com.mysql.cj.jdbc.Driver (암기할필욘 x)
2. DriverManager.getConnection(url,uid,pass) => DB 연결
3. createStatement() or prepareStatement() 는 3C와 3P => SQL문 담을 준비
4. executeQuery(sql)(는 s사용) or executeUpdate(sql)(는 i,u,d경우) => SQL문 수행(select경우 ResultSet으로 받아야)
5. next() => select 경우 조회된 데이터들 중 한 row씩 가져옴. 예로 rs.next()시 rs.getString("")통해 가져올수있음.(getInt또한)
=> select는 5번까지, i/u/d는 4번까지 확실히 있어야 함.
=> select는 from다음 where다음 order by 이름(id라던지) 이렇게 다양하게 있음.(order by id는 id 순서대로)
=> 참고로 executeQuery를 i/u/d도 사용해도 되지만 비효율적이라서 Update로 사용하는거임. 
=> 참고로 executeUpdate도 사실 반환값이 존재한다. (update를 몇개 했는지 이런것 반환)

<3P경우>
String sql = "insert into bankmember_ values(?, ?, ?, ?, 0, now())"; => 이런식으로
PreparedStatement stmt = conn.prepareStatement(sql); => sql이 여기에!!
stmt.setString(1,id); stmt.setString(2,pw);... => ?를 여기서 넣는거임. id는 지역변수 겠죠.
stmt.executeUpdate(); => sql여기 없음!!

<3P => 3C 바꿀때의 경우>
1. SQL문(변화 과정 보여줌)
(0) = "update bankmember_ set pw=?,name=?,phone=? where id=?"; => 3P방식 SQL문.
(1) = "update bankmember_ set pw='pw',name='name',phone='phone' where id='id'";
(2) = "update bankmember_ set pw='"+pw+"',name='"+name+"',phone='"+phone+"' where id='"+id+"'"; => 3C방식 SQL문
2. PreparedStatement들 -> Statement로 수정.
3. createStatement(sql); => createStatement();
4. executeUpdate(); => executeUpdate(sql);
5. pre.setString(1,pw);관련 전부 삭제.
=> 끝이지만, 3C형식 쓰니까 변수명도 pre=>cre 로 바꾸는걸 추천.
```

