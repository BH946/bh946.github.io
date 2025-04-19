---
title:  "(JPA+Boot)Java Config와 properties"
categories : Spring
tag : [스프링부트,YAML설정,프로파일관리,환경분리,외부설정]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**스프링 부트 애플리케이션에서 application.yml 파일을 활용한 설정 관리입니다.**

**application.properties(yml)**은 런타임 시 다양한 환경에서 동작할 수 있도록 필요한 옵션들을 제공하는데 사용된다.  

- "부트"는 디폴트로 이 설정 파일을 사용하며, "순수스프링"은 기본 프로젝트에 애초에 없다.

- "부트"는 보통 properties(yml) & java config 로 설정을 하며, "순수스프링"은 xml로 설정하는 편이다. (순수 스프링 XML은 "MyBatis + Spring 파트" 참고)

- 요즘은 "부트"를 기본으로 사용하다보니 java config 방식을 잘 알아두자.

<br><br>

### (Java Config) application.yml

**API와 WEB(타임리프), 테섭과 본섭(프로필 차이) 등**

**운영환경.yml TIP**: 

- 자동 DB생성하지 말자(실수로 깃에 올리면 머리아픔)
- 외부 yml + 내부 resources하위 yml 둘다 사용하자(숨겨야 할 DB정보, 설정변경 용이)

<br>

**application.yml(properties) 사용 코드 간단 설명**

- `spring: datasource: url, username, password...` 에서 h2 db 등 연결 세팅
  - 이 한줄로 **DataSource 빈이 자동 등록!**
  - **spring-boot-jpa** 사용 시 **이 부분을 주석 해야지 "자동 메모리DB" 사용.**
    - **단, 테이블에 @Entity, @Id, @GeneratedValue로 JPA 어노테이션 사용 해야 "테이블 자동 생성"**

- `spring: jpa: hibernate, properties`
  - **create, none** 옵션으로 "실행마다 테이블 생성" 또는 "기존 테이블 사용" 설정
  - **show_sql**로 jpa가아닌 **실제 sql문** 체크(튜닝 하려면 sql 필수로 체크)
    - 그러나 본인은 show_sql 사용 안하고, 아래 **logging.level의 SQL을 사용**

- `server: servlet: session: timout: 30m` 으로 **세션 타임아웃** 설정 (세션값 30분 유지)
- `logging.level: org.hibernate.SQL: debug` 는 **SQL쿼리를 디버그 레벨로 로깅(단, MyBatis는 안됨 -> 하이버네이트는 JPA 구현체)**
  - `com.level: debug`로 level설정도 debug로 하면 프로젝트 로그레벨은 debug 레벨 사용
    - level하위 프로젝트의 debug 로그를 전부 볼 수 있게 됨.
  - **로그레벨 포함 관계(왼쪽 가장 상세): trace > debug > info > warn > error > fatal**
- `management` 에서 endpoint 노출 설정했음. (**모니터링**)
  - ex) shutdown 활성화해서 해당 앱을 외부에서 종료할 수 있게 엔드포인트 노출.
    - 기본적으로 비활성화지만 활성화하면 관리자가 **http 요청으로 앱 종료 가능**
  - exclude옵션에는 env, beans 엔드포인트 노출을 제외했음. (**환경정보, 빈 정보**)
- `spring.cache.caffeine.spec=maximumSize=100` 는 캐시 사이즈 설정이다. 
  - **물론 자바 코드에서도 설정 가능하다.(Java Config)**

- `spring.messages.basename=message,errors` 로 **"메시지 기능" 사용**
  - `messages.properties` 에 공통 관리할 messages 내용 세팅
  - `erros.properties` 에도 가능. 즉, 여러개 추가 가능
  - **"국제화"** 기능은 Java Config로! -> 아래 "메시지 국제화" 파트 참고
- `spring.config.activate.on-profile: prod` 로 **프로필 설정 가능**
  - 아무 설정 안하면?? -> default 프로필로 실행 (기본값임)
- `spring.thymeleaf.cache: false` 로 **thymeleaf 캐시 사용X** -> 실시간 reload (새로고침ㄱ)
- `my.datasource.imgPath: "C:/images/` 로 **"외부설정”** 사용
  - yml에 값을 @ConfigurationProperties로 불러와 사용하는게 현대적인 방법!

- `server.port.8080` 로 포트 8080(개발용), 포트 80(배포용) 를 지정 가능
  - 참고) http는 80포트, https는 443포트를 기본값으로 사용

<details><summary><b>본섭API:application.yml</b></summary>
<div markdown="1"><br>
application.yml 과 application.properties 는 똑같은 역할 (문법만 다를 뿐)<br>
```yaml
spring:
  datasource:
    url: jdbc:h2:tcp://localhost/~/lepldb/lepl
    username: sa
    password:
      driver-class-name: org.h2.Driver
#    url: jdbc:oracle:thin:@localhost:1521:STR
#    username: testUser
#    password: 1234
#    driver-class-name: oracle.jdbc.OracleDriver
  jpa:
    hibernate:
#      ddl-auto: create # table 자동 생성
      ddl-auto: none # db 초기화 없앰
  properties:
    hibernate:
#        show_sql: true
      format_sql: true
server:
  servlet:
    session:
      timeout: 30m # session timeout 30min
logging.level:
  org.hibernate.SQL: debug
  org.hibernate.type: trace
logging:
  level:
    com.lepl: debug
management:
  endpoint:
    shutdown:
      enabled: true
  endpoints:
    web:
      exposure:
        include: "*"
        exclude: "env,beans"
```
</div>
</details>
<details><summary><b>테섭API:application.yml</b></summary>
<div markdown="1"><br>
application.yml 과 application.properties 는 똑같은 역할 (문법만 다를 뿐)<br>
```yaml
# 테스트 코드는 메모리 DB를 사용하기 위해 datasource 주석
spring:
#  datasource:
#    url: jdbc:h2:tcp://localhost/~/lepldb/lepl
#    username: sa
#    password:
#      driver-class-name: org.h2.Driver
jpa:
#    hibernate:
#      ddl-auto: create # table 자동 생성
###      ddl-auto: none
properties:
hibernate:
#        show_sql: true
format_sql: true
logging.level:
org.hibernate.SQL: debug
org.hibernate.type: trace
logging:
level:
com.lepl: debug
```
</div>
</details>
<details><summary><b>본섭&테섭WEB:application.yml -> 프로필 적용 ver</b></summary>
<div markdown="1">
```yaml
# default 프로필 -> 개발모드로 사용
spring:
  # thymeleaf 캐시 사용 X로 실시간 reload
  thymeleaf:
    cache: false
    prefix: file:src/main/resources/templates/ # thymeleaf 경로지정
  # H2 DB
  datasource:
    url: jdbc:h2:tcp://localhost/~/secret-art-typing-gallery/secret
    username: sa
    password:
      driver-class-name: org.h2.Driver
  jpa:
    hibernate:
      ddl-auto: create # DB 초기화 사용
    properties:
      hibernate:
#        show_sql: true
        format_sql: true
  # 메시지 - thymeleaf 연동
  messages:
    basename: messages
server:
  port: 8080
#  address: 223.39.212.163
  servlet:
    session:
      timeout: 30m # session timeout 30min
logging.level: # DB 쿼리 로그
  org.hibernate.SQL: debug
  org.hibernate.type: trace
my: # 외부설정 값
  datasource:
    imgPath: C:/images-spring/
logging:
  level:
    com.dau.secretarttypinggallery.controller: debug # 컨트롤러에서만 로그확인
--- # 프로필 구분자
# prod 프로필 -> 배포모드로 사용
spring:
  config:
    activate:
      on-profile: prod
  # thymeleaf 캐시 사용!!
  thymeleaf:
    cache: true
    prefix: classpath:/templates/ # 리눅스용(배포환경) 경로지정
  # MYSQL DB
  datasource:
    url: jdbc:mysql://localhost:3306/secret?useSSL=false&useUnicode=true&serverTimezone=Asia/Seoul
    username: secretUser
    password: 1234
    driver-class-name: com.mysql.cj.jdbc.Driver
  jpa:
    hibernate:
#      ddl-auto: none # DB 초기화 사용 X
      ddl-auto: create # DB 초기화 사용
  # 메시지 - thymeleaf 연동
  messages:
    basename: messages
server:
  port: 8080 # 포트 포워딩으로 http(80) -> 8080 설정하면 "URL에 포트없이 바로 접속가능"
  servlet:
    session:
      timeout: 30m # session timeout 30min
my: # 외부설정 값
  datasource:
    imgPath: /var/www/images-spring/
logging:
  level:
    com.dau.secretarttypinggallery.controller: info # 배포는 기본값(=info) 사용
```
</div>
</details>


<br><br>
