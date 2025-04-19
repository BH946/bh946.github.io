---
title:  "(JPA+Boot)외부설정 @ConfigurationProperties"
categories : Spring
tag : [외부설정,ConfigurationProperties,스프링부트,프로퍼티바인딩,타입안전설정]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**스프링 부트 애플리케이션에서 외부 설정 파일의 값을 타입 안전하게 자바 객체로 바인딩하고 스프링 빈으로 등록하여 활용하는 @ConfigurationProperties의 구현 방법과 장점을 상세히 설명하는 가이드입니다.**

<br><br>

### 외부설정(@Value -> @ConfigurationProperties)

**외부 설정(application.yml) 에서 설정한 내용을 -> 자바객체로 바꿔서 -> 스프링 빈에 등록하여 사용하자는 방법! (messages랑 다른거다 오해하지말자!)**

**특히,** `spring.config.import=optional:file:../prod.properties ` **로 외부의 설정 파일(ex:prod.properties가 우선순위)도 가져올 수 있다.(권장!)**

- `application.yml` 에 **my.datasource.imgPath=값**를 설정해서 사용하고자 한다면?
- **@Value("${my.datasource.imgPath}")** 로 간단히 가져올 수 있지만, 우린 이걸 쓰려는게 아니니까 이건 잠시 잊자.
- 우린 **@ConfigurationProperties이걸 사용**할 것이고 아래 코드를 보고 이해하자.

<details><summary><b>@ConfigurationProperties 사용코드(+외부설정 포함): 이거 적용함</b></summary>
<div markdown="1"><br>
3개 자바파일 + 내부,외부 application.properties 필요<br>**application.properties -> 내부 resources에서 코드**
```properties
# 내부설정.property + 외부설정.properties(ex: pageUnit=10)
# file:../prod.properties란 프로젝트 상위에 잇는 외부설정
spring.config.import=optional:file:../prod.properties 
my.datasource.pageUnit=10
my.datasource.pageSize=10
my.datasource.imgPath=C:/images-spring/
```
**MyDataSource.java -> 객체로 사용할 클래스**
```java
@Slf4j
@Data
public class MyDataSource {
	private int pageUnit;
	private int pageSize;
	private String imgPath;
	// 생성자
	public MyDataSource(int pageUnit, int pageSize, String imgPath) {
		this.pageUnit = pageUnit;
		this.pageSize = pageSize;
		this.imgPath = imgPath;
	}
//
	@PostConstruct // 로딩완료 후 바로 실행 (로그 확인용)
	public void init() {
		log.info("init: pageUnit={}, pageSize={}, imgPath={}", pageUnit, pageSize, imgPath);
	}
}
```
**MyDataSourceProperties.java -> @ConfigurationProperties로 application.properties 내용을 JVM에 로드**
```java
/**
 * 외부설정:
 * @ConfigurationProperties 로 application.properties 에 저장한 my.datasource 하위 내용
 * 가져와서 "자바 객체" 로 변환 + 검증까지
 */
@Slf4j
@Getter @Setter
@ConfigurationProperties("my.datasource")
//@Validated
public class MyDataSourceProperties {
//    @NotEmpty
    private int pageUnit;
    private int pageSize;
    private String imgPath;
    public MyDataSourceProperties() { // 기본 생성자
    	log.info("properties 기본생성자 TEST");
    }
    public MyDataSourceProperties(int pageUnit, int pageSize, String imgPath) {
    	log.info("properties 일반생성자 TEST");
        this.pageUnit = pageUnit;
        this.pageSize = pageSize;
        this.imgPath = imgPath;
    }
}
```
**MyDataSourceConfig.java -> @EnableConfigurationProperties로 @ConfigurationProperties를 빈 사용 활성화**<br>참고) 바로 사용해도 되지만 객체로 쉽게 사용할 수 있게 추가로 `MyDataSource.java` 를 빈 등록 했다.
```java
/**
 * 외부설정:
 * @EnableConfigurationProperties 로 MyDataSourceProperties 를 사용 및 스프링 빈
 * @Import 를 통해서 "컴포넌트 스캔 대상 꼭 지정하기!"
 */
@Slf4j
@EnableConfigurationProperties(MyDataSourceProperties.class)
public class MyDataSourceConfig {
    private final MyDataSourceProperties properties;
    public MyDataSourceConfig(MyDataSourceProperties properties) {
        this.properties = properties;
    }
//
    @Bean
    public MyDataSource getMyDataSource() {
        return new MyDataSource(
                properties.getPageUnit(), properties.getPageSize(), properties.getImgPath()
        );
    }
}
```
**TEST_CODE**
```java
//필요한 곳에서 불러오고
private final MyDataSourceConfig source; 
//이를 바로 사용하면 끝!
.addResourceLocations("file:///"+source.getMyDataSource().getImgPath());
//MyDataSource 불러와 바로 사용해도 됨
private final MydataSource source;
log.info("{}", source.getImgPath());
```
</div>
</details>


<br><br>
