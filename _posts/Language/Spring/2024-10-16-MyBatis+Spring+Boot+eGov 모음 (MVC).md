---
title:  "MyBatis+Spring+Boot+eGov MVC 모음"
categories : Spring
tag : [MyBatis,eGov프레임워크,스프링부트,JSP개발,페이징처리]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**MyBatis와 eGov 프레임워크를 활용한 스프링 웹 애플리케이션 개발 MVC방법론, 설정 방식, 레이어별 구현 전략, 페이징 처리, 동적 쿼리 작성, 테스트 코드 작성까지 실무 중심으로 상세히 설명하는 가이드입니다.**

<br>

<br>

## MyBatis+Spring+Boot+eGov 파트 (MVC)

<details><summary><b>적용 지침서 보기: 공홈>알림마당>관련참고문서>정보시스템 구축 발주자를 위한 표준프레임워크 적용가이드</b></summary>
<div markdown="1">
- 권고사항: 
  - 수정없이 사용: "실행환경", "모바일표준프레임워크" 
  - 수정가능: "개발환경"(다른 상용 솔루션 조합도 가능), "운영환경 및 공통컴포넌트"
- **기본 2가지 적용 확인:**
  1. 표준프레임워크 실행환경의 정상적인 설치 여부 점검
     - 운영서버(WAS)의 "[웹어플리케이션 루트 디렉토리]/WEB-INF/lib/" 폴더에 "org.egovframe.rte"로 시작하는 .jar 파일이 존재하는지 확인  
       **=> 즉, egovframe 라이브러리 사용하는지 체크**
  2. 실제 소스코드에서 실행환경이 활용되고 있는지 점검
     - import org.egovframe.rte 검색되는지 체크
     - EgovAbstractDAO(EgovAbstractMapper) 와 EgovAbstractServiceImpl(또는 AbstractServiceImpl) 클래스를 상속한 구문이 존재하는지 체크
       - (예: public class NotificationDAO extends EgovAbstractDAO)
       - (예: public class NotificationServiceImpl extends EgovAbstractServiceImpl)
- 상세한 적용 확인:
  1. 아키텍처 규칙 
     - Annotation 기반 Spring MVC 준수 : **@Controller 및 @RequestMapping**을 통한 URL mapping 활용 (View 부분과 model(business logic 및 data) 부분을 controller를 통해 분리) 
     - Annotation 기반 layered architecture 준수 : 화면처리, 업무처리, 데이터처리에 부분에 대하여 각각 **@Controller, @Service, @Repository** 활용 (인접 layer간 호출만 가능) 
     - 업무처리를 담당하는 서비스 클래스(@Service)는 **EgovAbstractServiceImpl**(또는 AbstractServiceImpl)을 확장하고 업무에 대한 특정 인터페이스를 구현하여야 함 
     - 데이터처리를 담당하는 DAO 클래스(@Repository)는 EgovAbstractDAO(iBatis) 또는 **EgovAbstractMapper**(MyBatis)를 상속하여야 함 (**Hibernate/JPA를 적용한 경우는 예외**이며 자세한 사항은 하단 ‘데이터처리 규칙’ 참조) 
  2. 데이터처리 규칙 
     - Data Access 서비스 준수 : 데이터처리 부분은 iBatis 활용 (SqlMapClientDaoSupport 를 상속한 EgovAbstractDAO 활용) 또는 MyBatis 활용 (SqlSessionDaoSupport를 상속 한 **EgovAbstractMapper** 활용)<br>※ MyBatis의 경우 **Mapper interface 방식**으로 사용가능(권장)하며, 이 경우는 **interface 상에 @Mapper를 지정**하여 사용되어야 함 
     - ORM 서비스 준수 : 데이터처리 부분은 Hibernate/JPA 적용 (DAO에서 SessionFactory 또는 EntityManagerFactory 설정을 통해 HibernateTemplate/JpaTemplate를 활용하거나, HibernateDaoSupport/JpaDaoSupport를 상속하여 활용) 
     - Data 서비스 준수 : 데이터 처리 부분은 다양한 persistence store(Big Data, NoSQL 등)를 지원하기 위한 Spring Data 적용 (**DAO에서 CrudRepository를 상속하는 interface 방식의 Repository를 활용**) 
       - JpaRepository와 다르게 진짜 CRUD만 제공
  3. 활용 및 확장 규칙 
     - 표준프레임워크 실행환경 준수 : 표준프레임워크 실행환경은 적극적으로 활용되어야 함 (실행환경 부분 임의 변경 금지) 
     - 확장 규칙 : 업무 클래스는 org.egovframe.rte 패키지 내에 정의될 수 없음 
  4. 기타 
     - 이외에 개발환경, 운영환경 및 공통컴포넌트 부분은 선택적으로 적용 가능하며, 임의 변경 및 확장 가능함 
     - UI부분에 RIA(Rich Internet Appliation)가 적용되는 경우는 UI Adaptor 또는 RESTful 방식을 적용 활용해야 함
</div>
</details>


> eGov 가이드가 있으니 공홈 참고 or 블로그 eGov 게시물 참고

eGov적용해본 플젝은 부트도 사용했지만 일부러 xml을 좀 경험하고자 **xml+java config** 둘 다 섞어 봤음

**[공식 홈페이지](https://www.egovframe.go.kr/home/sub.do?menuNo=94)의 eGovFframeDev-4.2.0 사용 -> Eclipse 2022-12 (4.26.0) 사용 및 JDK17 로 구동**  
**eGovFrame -> stater -> boot web 플젝 생성**  
개발하면서 라이브러리에 **Egov꺼 보이면 우선**으로 사용한 편!

참고: 순수스프링은 web.xml에 필터, 디스패처 서블릿 다 세팅한 덕분에 main함수 직접 작성 안해도 톰캣 위에서 동작  

> **ContextLoaderListener**는 **web.xml** 파일에 설정되어, 웹 애플리케이션이 시작될 때 **Spring** 애플리케이션 **컨텍스트를 초기화**
>
> 이 리스너는 **contextConfigLocation** 파라미터를 통해 **XML** 파일의 위치를 지정받고, 해당 파일을 로드하여 빈을 등록
>
> 이를 담당해주는 web.xml이 없으면 당연히 "자바코드"로 직접 작성해서 main함수로 실행해줘야 함.  
> ApplicationContext(스프링 컨테이너)를 초기화 하면 되고, 빈도 사용할 수 있다.  
> => XML 방식: ApplicationContext context = new ClassPathXmlApplicationContext("application.xml");  
> => Java Config 방식: ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);  
> AppConfig 클래스에 @Configuration 필수  
> => 부트는 스프링 컨테이너 초기화 자동 제공(@SpringBootApplication): SpringApplication.run(메인.class);

<br>

**JSP 사용하게 Setting:**

- pom.xml:
  - jar 도 가능: 어차피 boot-starter의 내장톰캣으로 실행 됨.  
    - 나중에 외부 톰캣 쓰고 싶으면?   
      war변경+starter의존성provided+web.xml 구성 or SpringBootServletInitializer를 오버라이드(main함수 대신)
  - JSP 용 톰캣인 tomcat-embed-jasper 의존성 추가 및 타임리프 의존성은 주석
- WEB-INF 하위에 index.jsp 만들고, application.properties 세팅
  - spring.mvc.view.prefix=/WEB-INF/  
    spring.mvc.view.suffix=.jsp

**Boot에서 정적 파일(js,css,images 등) 사용하게 Setting:**

- 기존 순수 스프링(WAR)는 src/main/webapp하위 사용이 표준 구조.   
  그러나, Boot는 src/resources, static, templates 등이 표준 구조. 
  - 뷰는 templates보는게 기본값이라서 위에서 webapp/WEB-INF 하위 jsp를 사용하게 뷰를 설정했었다.
- 따라서 Boot 사용할거면 본인은 정적파일을 src/resources/static 하위에 두기로!

<br><br>

### VO

**JPA파트도 참고(연결해서 보기)**

> MyBatis만 사용할거면 JPA 어노테이션(ex:@OneToMany) 사용할 필요 없음. SQL 매퍼(xml)에서 해야함.  
> => JPA는 엔티티 개념을 사용했지만, MyBatis는 직접SQL이라 DB개념으로 좀 더 생각하자.  
>
> 즉, JPA의 영속성 컨텍스트로 도메인 패턴 필요 없음.   
> update도 더티체킹 없이 전부 SQL로 처리 해야 함.
>
> PK값 자동증가 ID도 SQL 매퍼에서 MySQL은 useGeneratedKeys로 적용(auto_increment)  
> => [Mybatis 키 자동 생성 - useGeneratedKeys(MySQL), selectKey(Oracle)](https://sesoc.tistory.com/41)
>
> N+1 문제는 JPA-"페치조인(즉시로딩)+컬렉션은 distinct까지" 로 해결  
> MyBatis는 SQL문 사용하므로 조인이나 서브쿼리 덕분에 직면할 문제가 아님
>
> update는 JPA에선 더티체킹 방식이지만, SQL인 MyBatis는 아님.  
> @Transactional에서 JPA는 영속성컨텍스트 덕분에 쿼리모아서 한번에 전송지만, MyBatis는 매순간 전송

<br>

PK값 자동증가 ID의 경우 eGov-GenId껀 @Deprecated니까 사용하지 말고,  
MySQL이니 MyBatis의 useGeneratedKeys(=auto_increment) 쓰자.   
JPA라면 @GeneratedValue(strategy = GenerationType.IDENTITY) 가 auto_increment 역할!

JPA 사용 안하더라도 boot-jpa-data 의존성으로 "테스트DB + 자동 테이블생성"을 활용한다면,   
@Entity+@Id+@GeneratedValue 선언은 필수! (JPA한테 알려주는것) -> 자세한건 test case 작성파트를 참고

JPA의 더티체킹을 위한 엔티티에 update로직이나 연관메서드 등 MyBatis에선 사용할 필요가 없어짐.

<br><br>

### Mybatis DAO & Service

**JPA파트도 참고(연결해서 보기)**

**eGov는 crudRepository... 활용해야 eGov 정책에 맞다. 물론, 여기선 MyBatis를 사용하므로 자세한 언급은 하지 않겠따.**

@Repository, @Service로 구분 -> @Mapper 방식 사용 시 레포로 봐도 될 듯.  
EgovAbstractMapper, EgovAbstractServiceImpl 상속 필수(단, @Mapper 사용 시 첫번째꺼 생략가능)

생성자 빈 주입방식 사용! -> @RequiredArgsConstructor + final  
DBIO로 쿼리 작성도 좋음.  
인터페이스에 주석 넣어서 구현할 때 무슨 메소드인지 보기좋게 하자. (서비스 인터페이스에 했음)

(mybatis 1)MyBatis 인터페이스 방식 구현(=@Mapper 방식): ItemService 인터페이스 + ItemServiceImpl 클래스(@Service로 자동 빈) + ItemMapper 인터페이스(@Mapper로 프록시로 자동 빈)  
=> 특히, ItemServiceImpl 클래스 = EgovAbstractServiceImpl 상속 + ItemService 구현체 역할

(mybatis 2)xml설정부분: Mybatis-boot-starter 의존성 없으면(=순수스프링ver) context-mybatis.xml 필수(=빈 등록: SqlSessionFactoryBean:sql-mybatis-config.xml등록, MapperConfigurer:@Mapper 인터페이스 자동 스캔 위치 지정) +  
sql-mybatis-config.xml 에서 \<mapper Item.xml> + lazy설정, 별칭, 캐시 등 ㄱㄱ +  
Item.xml 에는 SQL문 작성!  

(mybatis3)xml 사용 위해 main함수위에 `@ImportResource("classpath:/spring/*.xml")` 로 등록   
참고) context-common.xml 도 추가했음. (eGov서비스에 leaveaTrace빈 때매) 

**자세한 사용과 문법은 "DB필독 MyBatis 파트" 참고**

<details><summary><b>전체코드와 사용 파일 한눈에 보기</b></summary>
<div markdown="1"><br>
{src/main/resources/}spring/context-mybatis.xml -> sqlSession빈<br>
{src/main/resources/}sqlmap/sql-mybatis-config.xml -> Item.xml(매퍼)연결 및 별칭,캐시 등<br> 
{src/main/resources/}sqlmap/mappers/Item.xml -> SQL<br>
{src/main/java/}...service.impl/ItemMapper.java -> @Mapper<br>
{src/main/java/}...service.impl/ItemServiceImpl.java -> @Service<br>
{src/main/java/}...service/ItemService.java -> (그냥 확장성 위해 인터페이스 추가한거일 뿐)<br>
**context-mybatis.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd">
	<!-- 애초에 boot-mybatis-starter 라이브러리 사용했으면 이부분 자동 설정해 줌. 안 사용해서 추가한거임. -->
<!-- -->
	<!-- SqlSession setup for MyBatis Database Layer -->
	<!-- MyBatis와 Spring 연동 설정
	물론, 스프링부트는 java 파일에서 빈 등록을 권장 -->
	<bean id="sqlSession"
		class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource" />
		<property name="configLocation"
			value="classpath:/sqlmap/sql-mybatis-config.xml" />
		<!-- <property name="mapperLocations" value="classpath:**/lab-*.xml" /> -->
	</bean>
	<!-- MapperConfigurer setup for @Mapper -->
	<!-- MyBatis의 Mapper Interface 자동스캔 설정 
	물론, 스프링부트는 java 파일에서 빈 등록을 권장 -->
	<bean class="org.egovframe.rte.psl.dataaccess.mapper.MapperConfigurer ">
		<property name="basePackage"
			value="com.secretgallery.service.impl" />
	</bean>
</beans>	
```
**sql-mybatis-config.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
<!--  -->
	<settings>
		<setting name="cacheEnabled" value="true" />
		<setting name="lazyLoadingEnabled" value="true" />
		<setting name="multipleResultSetsEnabled" value="true" />
		<setting name="mapUnderscoreToCamelCase" value="true" />
	</settings>
	<!-- 별칭 지정시 sql문쪽 resultType 이런곳에서 "클래스명"만으로 바로 사용 가능! 
	단, 클래스단위임. 패키지 단위는 application.properties에서 해야함. -->
	<typeAliases>
		<typeAlias alias="Item"
			type="com.secretgallery.vo.Item" />
	</typeAliases>
<!--  -->
	<typeHandlers>
		<typeHandler
			handler="org.egovframe.rte.psl.dataaccess.typehandler.CalendarMapperTypeHandler" />
	</typeHandlers>
<!--  -->
	<mappers>
		<mapper resource="sqlmap/mappers/Item.xml" />
	</mappers>
</configuration>
```
**Item.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.secretgallery.service.impl.ItemMapper">
	<insert id="save" useGeneratedKeys="true" keyProperty="id">
		insert
		into item (nickname, password, title, content, img_src, date1, date2)
		values (#{nickname}, #{password}, #{title}, #{content}, #{imgSrc},
		#{date1}, #{date2})
	</insert>
	<update id="update">
		update item
		set password=#{password},
		title=#{title},
		content=#{content}
		where item_id = #{id}
	</update>
	<select id="findById" resultType="Item">
		select item_id as id, nickname,
		password, title, content, img_src, date1,
		date2
		from item
		where item_id =
		#{id}
	</select>
	<delete id="delete">
		delete from item where item_id = #{id}
	</delete>
	<select id="findAll" resultType="Item">
		select item_id as id, nickname,
		password, title, content, img_src,
		date1,
		date2
		from item
	</select>
<!--  -->
	<!-- DBIO 써보기 + as별칭 말고 resultMap 써보기 -->
	<resultMap id="item" type="Item">
		<result property="id" column="item_id" />
		<result property="nickname" column="nickname" />
		<result property="password" column="password" />
		<result property="title" column="title" />
		<result property="content" column="content" />
		<result property="imgSrc" column="img_src" />
		<result property="date1" column="date1" />
		<result column="date2" property="date2" />
	</resultMap>
	<!-- (동적쿼리)검색+페이징 -->
	<select id="findAllWithPage" resultMap="item">
		SELECT *
		FROM item
		<where>
			<if test="searchKeyword != null and searchKeyword != ''">
				<choose>
					<when test="searchCondition == 0">
						and id like concat('%', #{searchKeyword}, '%')
					</when>
					<when test="searchCondition == 1">
						and title like concat('%', #{searchKeyword}, '%')
					</when>
				</choose>
			</if>
		</where>
		order by item_id DESC 
		LIMIT #{recordCountPerPage} OFFSET #{firstIndex}
	</select>
	<select id="findTotalCount" resultType="int">
		SELECT count(*) FROM Item
		<where>
			<if test="searchKeyword != null and searchKeyword != ''">
				<choose>
					<when test="searchCondition == 0">
						and id like concat('%', #{searchKeyword}, '%')
					</when>
					<when test="searchCondition == 1">
						and title like concat('%', #{searchKeyword}, '%')
					</when>
				</choose>
			</if>
		</where>
	</select>
	<!-- 자동검색(ajax) -->
	<select id="findTitleListForSuggest" resultType="string">
		SELECT TITLE
		FROM item
		where title like '%' || #{value} || '%'
		<!-- where title like concat('%', #{value}, '%') -->
	</select>
	<select id="findPrevNextById" resultMap="item">
		<![CDATA[
		SELECT * FROM Item WHERE
		item_id >= #{prevId} and item_id <= #{nextId}
		]]>
	</select>
</mapper>
```
**ItemMapper.java**
```java
//Mapper Interface - 메서드명과 쿼리ID를 매핑하여 쿼리호출
@Mapper
public interface ItemMapper {
	public Long save(Item item);
	public Long update(Item item);
	public Long delete(Item item);
	public Item findById(Long id);
	public List<Item> findAll();
//	
	public List<Item> findAllWithPage(ItemDefault searchItem);
	public int findTotalCount(ItemDefault searchItem);
	public List<String> findTitleListForSuggest(String value);
	public List<Item> findPrevNextById(@Param("prevId") Long prevId, @Param("nextId") Long nextId);
}
```
**ItemServiceImpl.java**
```java
@Service
@Transactional(readOnly = true) 
@RequiredArgsConstructor
@Slf4j
public class ItemServiceImpl extends EgovAbstractServiceImpl implements ItemService {
	private final ItemMapper itemMapper;
	//CRUD
	@Override
	@Transactional // 쓰기모드
	public Long save(Item item) throws Exception {
		return itemMapper.save(item);
	}
	@Override
	@Transactional // 쓰기모드
	public Long update(Item item) throws Exception {
		// TODO Auto-generated method stub
		return itemMapper.update(item);
	}
	@Override
	@Transactional // 쓰기모드
	public Long delete(Item item) throws Exception {
		// TODO Auto-generated method stub
		return itemMapper.delete(item);
	}
	@Override
	public Item findById(Long id) throws Exception {
		return itemMapper.findById(id);
	}
	@Override
	public List<Item> findAll() throws Exception {
		// TODO Auto-generated method stub
		return itemMapper.findAll();
	}
//
	//추가 함수
	@Override
	public List<Item> findAllWithPage(ItemDefault searchItem) throws Exception {
		// TODO Auto-generated method stub
		return itemMapper.findAllWithPage(searchItem);
	}
	@Override
	public int findTotalCount(ItemDefault searchItem) throws Exception {
		// TODO Auto-generated method stub
		return itemMapper.findTotalCount(searchItem);
	}
	@Override
	public List<String> findTitleListForSuggest(String value) throws Exception {
		// TODO Auto-generated method stub
		return itemMapper.findTitleListForSuggest(value);
	}
	@Override
	public List<Item> findPrevNextById(Long id) throws Exception {
		// TODO Auto-generated method stub
		return itemMapper.findPrevNextById(id-1, id+1);
	}
}
```
**ItemService.java**
```java
/**
 * CRUD + 
 * findAllWithPage + findTotalCount + findTitleListForSuggest + findPrevNextById
 	* 총 게시물 수 구하는 함수: findTotalCount()
 	* 이전, 이후 전시실 버튼 생성용: findPrevNextById()
 	* 검색 자동완성 함수: findTitleListForSuggest()
 */
public interface ItemService {
	/**
	 * CRUD - C
	 * @param item
	 * @return count(개수)
	 * @throws Exception
	 */
	public Long save(Item item) throws Exception;
	/**
	 * CRUD - U
	 * @param item
	 * @return count(개수)
	 * @throws Exception
	 */
	public Long update(Item item) throws Exception;
	/**
	 * CRUD - D
	 * @param item
	 * @return count(개수)
	 * @throws Exception
	 */
	public Long delete(Item item) throws Exception;
	/**
	 * CRUD - R
	 * @param id
	 * @return 
	 * @throws Exception
	 */
	public Item findById(Long id) throws Exception;
	/**
	 * CRUD - R
	 * @return 
	 * @throws Exception
	 */
	public List<Item> findAll() throws Exception;
	//
	//추가 함수
	/**
	 * 해당 페이지 Item 전부 조회 by desc
	 * @param pageId
	 * @return
	 * @throws Exception
	 */
	public List<Item> findAllWithPage(ItemDefault searchItem) throws Exception;
	/**
	 * 전체 Item의 총 개수
	 * @return
	 * @throws Exception
	 */
	public int findTotalCount(ItemDefault searchItem) throws Exception;
	/**
	 * 검색에 자동완성 기능
	 * @param value
	 * @return
	 * @throws Exception
	 */
	public List<String> findTitleListForSuggest(String value) throws Exception;
	/**
	 * Item의 이전, 이후 Item 구하기
	 * @param id
	 * @return
	 * @throws Exception
	 */
	public List<Item> findPrevNextById(Long id) throws Exception;
}
```
</div>
</details>


<br><br>

### Controller JSP

**JPA파트도 참고(연결해서 보기)**

@Controller 및 @RequestMapping 로 Spring MVC 준수 필수!  
=> 어차피 @GetMapping, @PostMapping 와 사용법도 기능도 유사하다. 오히려 유지보수좋게 이걸로 ㄱ  
=> 클래스 단에는 @RequestMapping 유용하니 써줘도 좋고! (공통URL)  

참고: 타임리프 플젝에서 좀 엉망인 "주석"을 eGov적용 웹 플젝에서 좀 다듬었다.

반환타입 void일 때: 자동으로 뷰리졸버는 요청URL과 동일한 뷰를 탐색해서 반환! (직접 String반환 안해도!)

**특히, JSP는 타임리프꺼 복붙하여 문법 JSP로 바꾸는게 전부.**  

- 예로: fragment -> jsp:include, th:src -> c:url, th:text -> spring:message, text -> c:out, th:each -> c:forEach,  th:if -> c:if, th:error -> form:error 등 코드는 아래 참고

- <details><summary><b>타임리프 -> JSP 예시: img, message, fragment, text, forEach, if, error</b></summary>
  <div markdown="1"><br>
  ```jsp
  <!-- img 예시 -->
  <img class="img-fluid" src="<c:url value='/images/gallery/6-2.svg'/>"
       style="width: 100%;"/>
  <!-- message 예시 -->
  <span class="px-3" style="font-size: 2vw; font-weight: 700; color: white; white-space: nowrap;">
      <spring:message code="page.gallery" text="전시실" />
  </span>
  <!-- fragment 예시 (param 도 가능) -->
  <jsp:include page="fragments/header.jsp"/>
  <jsp:include page="fragments/head.jsp">
      <jsp:param value="갤러리" name="title" />
  </jsp:include>
  <!-- text 예시 (c:out, fmt: 등 다양함) -->
  <span class="nav-item-inner">B1 ~ B<fmt:formatNumber value="${paginationInfo.getTotalRecordCount() / paginationInfo.getRecordCountPerPage()}" pattern="#" />F
  </span>
  <span style="font-weight: 500; color: white; font-size: 1.4vw;">B
      <c:out value='${paginationInfo.getCurrentPageNo()}'/>F
  </span>
  <!-- text 번외: fmt는 Date만 지원, LocalDate는?? -->
  <%@ page import="java.time.format.DateTimeFormatter" %>
  ${itemResDto.date1.format(DateTimeFormatter.ofPattern('yy.MM.dd.HH:mm'))}
  <!-- forEach 예시 (반복문) -->
  <c:forEach items="${itemsResDto}" var="itemResDto" varStatus="status">
      ...
  </c:forEach>
  <!-- if 예시 Java or JSP-->
  <%
  if (item != null && item.getImgSrc() != null) {
  %>
      <img src="<%= item.getImgSrc() %>" alt="이미지">
  <%
  }
  %>
  <c:if test="${item != null && item.imgSrc != null}">
      <img src="${item.imgSrc}" alt="이미지">
  </c:if>
  <!-- error 예시: th:error처럼 form:error로 표현 가능 -->
  <!-- th:error, form:error 둘다 bindingResult의 검증결과를 활용하여 메시지 출력 -->
  <!-- 자세한건 뒤에 검증 파트를 보는걸 추천 --> 
  <!-- 아래 코드는 "직접 bindingResult 다루는 예시" -->
  컨트롤러에서 bindingResult를 model에 담아서 반환했다 가정:
  <div class="field-error">
      <c:if test="${not empty bindingResult.fieldErrors}">
          비밀번호 오류
          <%-- 비밀번호 오류: <c:out value="${bindingResult.fieldErrors['password']}" /> --%>
      </c:if>
  </div>
  ```
  </div>
  </details>

"게시물 페이징, 자동완성(검색)"에 AJAX를 적용했다! (SPA 방식. BGM 사용중이면 끊길 걱정도 없어짐)  
=> 아래 eGov 방식의 "검색(동적쿼리)+페이징, 자동완성(검색)" 파트 참고

<br>

#### JSP

**"HTTP 중요 지식" 파트와 "타임리프 파트"를 먼저 참고할 것**

구조분석

<details><summary><b>구조분석</b></summary>
<div markdown="1">
**스프링부트에서 "타임리프"와 "JSP" 접근 비교:** 
- 타임리프?
  - 웹 브라우저 -> 톰캣(서블릿 컨테이너) -> 스프링 컨테이너 순으로 이동하여,  
    Controller를 찾고 있으면 자동 등록된 **viewResolver**로 화면에 응답.
  - 이때, templates/hello.html 처럼 Thymeleaf 템플릿 엔진 처리가 **"기본 경로"**로 가능!
    - 즉, **boot-starter-thymeleaf 의존성이 있다면** 부트가 "뷰 리졸버" 자동 등록 + "경로 설정" 자동 등록
    - 애초에 뷰 리졸버랑 이런건 **boot-starter-web 의존성** 덕분에 기본적으로 부트가 자동 지원.
- JSP?
  - 부트를 사용하니 의존성들 덕분에 뷰 리졸버 이런 등록은 자동이다. (순수 스프링은 하나부터 열까지 직접 xml로...)
  - 단, **"JSP 템플릿 엔진 처리"**를 따로 라이브러리로 설치해야하고 **"경로도 설정"**해야 한다.
<br>
**왼쪽 스프링부트+JSP / 오른쪽 일반 스프링부트 구조 비교: src/main/webapp/WEB-INF 가 있음!**<br>
<img src="https://github.com/user-attachments/assets/65986172-ecce-4a97-9912-1ac386b40341" alt="Image" style="zoom:80%;" /> <br> <img src="https://github.com/user-attachments/assets/d3f7731a-54e9-4aa0-a3c5-3c133c887ea2" alt="Image" style="zoom:80%;" /> 
</div>
</details>


<details><summary><b>실행과정 - 3가지만 지키자</b></summary>
<div markdown="1">
**3가지만 지키자:** 
- JAR는 골치아파서 JSP엔 WAR 사용! -> **(정정: 부트의 경우 내장콤캣 덕분에 JAR도 똑같이 잘 구동)**
- 의존성: 
  - implementation 'org.apache.tomcat.embed:tomcat-embed-jasper'
  - 이거 추가 안하면, jsp용 톰캣이없어서 jsp 접근시 걍 파일설치로 뜸.
- application.properties: 
  - spring.mvc.view.prefix=/WEB-INF/views/   
    spring.mvc.view.suffix=.jsp
  - WEB-INF 하위 경로는 클라 URL로 접근 안된다. 서버에서만 접근 가능하다. (보안위해)
  - 그래서 서버(스프링)한테 경로를 알려주는것! (예상이지만 원래는 /templates 일 듯)<br>
<img src="https://github.com/user-attachments/assets/c5f41f27-f2af-4e23-acd0-a25c18a6214d" alt="Image" style="zoom:80%;" /> <br>
**컨트롤러 코드보면 기존 스프링부트(+타임리프)로 개발한거랑 다를게 없음! 편.안.**<br>
 <img src="https://github.com/user-attachments/assets/c599c025-c601-4128-96ce-d0e9c66d0c08" alt="Image" style="zoom:80%;" />
</div>
</details>


<details><summary><b>JSP 문법</b></summary>
<div markdown="1">
- **스크립트 요소**: JSP에서 자바 코드를 작성하는 곳 = 스크립트릿(Scriptlet)
  - 형식: `<% 자바 코드 %>`
  - 지역변수이며, 메서드 선언은 불가능(불가능이였던가?)
- **선언문**: 변수나 메서드를 선언하는 곳.
  - 형식: `<%! 자바 코드 %>`
  - JSP -> 서블릿 변환될 때 클래스, 멤버로 변환
- **표현식**: 값을 웹 브라우저에 출력할 때 사용하는 곳
  - 형식: `<%= 자바 코드 %>`
  - 세미콜론(;)을 붙이지 않는다.
- **주석 두 가지 방식:**
  - 형식1: `<!-- HTML 주석 -->`
  - HTML 주석: 브라우저 소스 보기에서 보임
  - 형식2: `<%-- JSP 주석입니다 --%>`
    - JSP 주석 (브라우저 출력되지 않음)
- **지시어**: JSP 페이지의 전반적인 처리 방식을 설정
  - 형식: `<%@ 자바 코드 %>`
  - | 지시어  | 설명                      | 예시                                                |
    | ------- | ------------------------- | --------------------------------------------------- |
    | page    | 페이지 설정 정보          | `<%@ page contentType="text/html; charset=UTF-8"%>` |
    | include | 다른 파일 포함            | `<%@ include file="header.jsp" %>`                  |
    | taglib  | 태그 라이브러리 사용 선언 | `<%@ taglib uri="..." prefix="c"%>`                 |
- **액션태그**: JSP 문서 내에서 간단하게 다양한 구현을 할 수 있도록 만든 태그
  - 형식: `<jsp:?>`
  - | 액션 태그       | 설명                                |
    | --------------- | ----------------------------------- |
    | jsp:include     | 다른 페이지를 현재 페이지에 포함    |
    | jsp:forward     | 현재 페이지에서 다른 페이지로 이동  |
    | jsp:useBean     | 자바 빈 객체 생성                   |
    | jsp:setProperty | 자바 빈 객체의 프로퍼티 값을 설정   |
    | jsp:getProperty | 자바 빈 객체의 프로퍼티 값을 가져옴 |
  - 헷갈리는 부분 체크: `include` 의 경우 -> [지시어 include, 액션태그 include 동작차이](https://doublesprogramming.tistory.com/64)
    - 예로 footer에 "저작권표시"는 정적으로써 지시어 include 사용하면 될거다.
    - "현재 시간"은 해당 파일이 실행될 때 나타내줘야 하니까 동적으로써 액션태그 사용!
      - 즉, 모듈화할 때 사용한다.(웹 특정 영역을 독립된 파일로 나눈다공)
- **내장객체**: JSP에서는 별도의 선언 없이 사용할 수 있는 내장 객체가 있다.
  - `request`: 클라이언트 요청 정보를 담고 있는 객체
  - `response`: 클라이언트에게 응답을 보내기 위한 객체
  - `out`: 출력 스트림을 통해 데이터를 출력하기 위한 객체
  - `session`: 세션 정보를 저장하고 관리하는 객체
  - `application`: 웹 애플리케이션 전체에서 공유되는 데이터를 저장하는 객체
- **JSTL**: 자주 사용하는 로직(조건문, 반복문 등)을 태그 형태로 제공하는 JSP 표준 태그 라이브러리
  - | 태그 라이브러리     | URI                                      | prefix(접두어) | 주요 기능                           |
    | ------------------- | ---------------------------------------- | -------------- | ----------------------------------- |
    | Core(가장많이 사용) | `http://java.sun.com/jsp/jstl/core`      | `c`            | 조건문, 반복문, 변수 선언, URL 처리 |
    | Formatting          | `http://java.sun.com/jsp/jstl/fmt`       | fmt            | 숫자, 날짜, 시간 포맷 및 국제화     |
    | Functions           | `http://java.sun.com/jsp/jstl/functions` | fn             | 문자열 처리 함수 제공               |
    | XML                 | `http://java.sun.com/jsp/jstl/xml`       | x              | XML 문서 처리                       |
    | SQL                 | `http://java.sun.com/jsp/jstl/sql`       | sql            | JSP 내에서 SQL 처리                 |
</div>
</details>


<details><summary><b>JSP 문법 예시 코드:</b></summary>
<div markdown="1"><br>
**제일 좋은건 JSP로 진행했던 플젝 보는게..!**
```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>JSP Example</title>
</head>
<body>
<!-- 선언부(선언만 가능) -->
<%! String name = "김철수"; %>
<%! 
    public String greeting(String name) {
        return "안녕하세요, " + name + "님!";
    }
%>
<!-- 스크립트릿 + 내장객체(request) -->
<%
    String userName = request.getParameter("user");
    if(userName == null) {
        userName = "손님";
    }
%>
<h2>환영합니다!</h2>
<!-- 표현식 -->
<p><%= greeting(userName) %></p>
<!-- 액션 태그 -->
<jsp:include page="footer.jsp" />
```
```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<body>
<h2>안녕하세요 테스트 입니다.</h2>
<!-- 컨트롤러에서 모델에 담긴 변수명 'custNm' -->
<c:choose>
    <c:when test="${custNm == '홍길동'}">
        홍길동님 환영합니다.
    </c:when>
    <c:when test="${custNm == '이순신'}">
        이순신님 환영합니다.
    </c:when>
    <c:otherwise>
        등록되지 않은 사용자입니다.
    </c:otherwise>
</c:choose>
</body>
</html>
```
</div>
</details>


<br><br>

### +) 검색(동적쿼리)+페이징, 자동완성(검색)

**페이징은 eGov꺼 라이브러리(PaginationInfo, Manager, Renderer) 사용**

1. **"동적쿼리(검색), 페이징" 위한 엔티티 부모 만들어 상속! -> 페이징, 검색조건을 가짐**  

   egov 예제의 SampleDefaultVO.java 형태를 사용! (Serializable 제외)

   <details><summary><b>SampleDefaultVO.java</b></summary>
   <div markdown="1"><br>
   ```java
   @Getter @Setter
   @NoArgsConstructor
   @EqualsAndHashCode //메모리캐시 때문에 넣음(원래도 있는게 좋긴하지)
   public class ItemDefault {
   	/** 검색조건->카테고리 ID, NAME 등 선택 */
   	private String searchCondition = "";
   	/** 검색Keyword */
   	private String searchKeyword = "";
   	/** 검색사용여부 */
   	private String searchUseYn = "";
   	/** 현재페이지 */
   	private int pageIndex = 1;
   	/** 페이지갯수->페이지당 뷰에 나타낼 게시물 수 */
   	private int pageUnit = 10;
   	/** 페이지사이즈->5면 하단 네비게이션에 1 2 3 4 5 까지 표시 */
   	private int pageSize = 10;
   	/** firstIndex->페이지 출력 시작할 게시물 위치: (pageIndex-1)*recordCountPerPage */
   	private int firstIndex = 1;
   	/** lastIndex->페이지 출력 마지막 게시물 위치: (pageIndex)*recordCountPerPage */
   	private int lastIndex = 1;
   	/** recordCountPerPage->limit 쿼리용 */
   	private int recordCountPerPage = 10;
   }
   ```
   </div>
   </details>

2. **기능개발+TEST -> "검색(동적쿼리)+페이징, 자동완성(검색)"**

   **메소드 추가 및 XML쿼리 작성** -> 동적쿼리(검색)+페이징, 자동완성(검색)  

   - **동적쿼리(검색)+페이징을 합친이유**: 기본 게시물조회 자체가 검색 키워드 빈값("")과 동일하기 때문  
     따라서 전체 조회 쿼리에 "동적쿼리 where,if-searchKeyword" + "limit,offset"구조를 사용!  
     +) 페이징을 위해 전체 게시물 수 조회 쿼리도 필수!
   - **페이징에 필요한 것 2가지**: 변수3개(pageUnit, pageSize, pageIndex) + PaginationInfo클래스(egov)
     - **직접 설정**하는 변수: pageUnit, pageSize
       - 페이지당 뷰에 나타낼 게시물 수: pageUnit
       - 하단 네비게이션 바에 표시할 페이지 수: pageSize  
         예: pageSize=5 라면, 1 2 3 4 5 가 출력
     - **웹에서 받아야**할 변수(=현재 페이지 번호): pageIndex
     - **PaginationInfo클래스(egov): Required Fields**, Not Required Fields
       - **Required Fields: 이 필드들은 페이징 계산을 위해 반드시 입력되어야 하는 필드 값**
         - **currentPageNo : 현재 페이지 번호 -> pageIndex 매치**
         - **recordCountPerPage : 한 페이지당 게시되는 게시물 건 수 -> pageUnit 매치**
         - **pageSize : 페이지 리스트에 게시되는 페이지 건 수 -> pageSize 매치**
         - **totalRecordCount : 전체 게시물 건 수 -> 서비스 로직으로 구하기 (+데이터도)**
       - Not Required Fields: 이 필드들은 Required Fields 값을 바탕으로 **"자동 계산"**된 필드 값
         - totalPageCount: 페이지 개수
         - firstPageNoOnPageList : 페이지 리스트의 첫 페이지 번호
         - lastPageNoOnPageList : 페이지 리스트의 마지막 페이지 번호
         - firstRecordIndex : 페이징 SQL의 조건절에 사용되는 시작 rownum. 
         - lastRecordIndex : 페이징 SQL의 조건절에 사용되는 마지막 rownum.
   - **페이징 쿼리 계산 원리와 자동완성(검색) 원리**
     - 페이징 쿼리 계산 원리: `LIMIT #{recordCountPerPage} OFFSET #{firstIndex}`<br>
       (참고: jpa의 jpql은 페이징의 limit쿼리 사용불가하고 제공되는 메소드를 사용)
       - pageUnit값이 recordCountPerPage가 된다.
       - `(pageIndex-1)*recordCountPerPage` 수식이 firstIndex(**페이지 출력 시작** 게시물 위치)가 된다.
       - `(pageIndex)*recordCountPerPage` 수식이 lastIndex(**페이지 출력 마지막** 게시물 위치)가 된다.
         - pageUnit=5, pageIndex=2 라면 시작 게시물 위치는 (2-1)\*5=5이고 마지막은 2\*5=10이다.
     - 자동완성(검색) 원리: SampleDefaultVO의 searchCondition, searchKeyword를 활용!
       - searchCondition는 어떤 카테고리로 검색할지 담당하고,
       - searchKeyword는 검색 키워드를 담당한다.


   **컨트롤러에서 페이징과 자동완성 검색** 마무리 해보자.

      - 자동완성은 검색 키워드로 매칭되는 데이터 찾아서 JSON으로 응답하면 됨
    
        - <details><summary><b>자동완성 컨트롤러 예시 코드</b></summary>
          <div markdown="1"><br>
          ```java
          //Ajax 사용한 컨트롤러 -> 페이징도 사용했지만 그건 GalleryController 꺼로 함.
          @Controller
          @RequiredArgsConstructor
          @Slf4j
          public class AjaxController {
          	private final ItemServiceImpl itemService;
          //
          	@ResponseBody
          	@PostMapping("/suggestKeyword")
          	public List<String> suggestKeyword(HttpServletRequest request) throws Exception {
          	    String searchKeyword = URLDecoder.decode(request.getParameter("searchKeyword"), "utf-8");
          	    List<String> results = itemService.findTitleListForSuggest(searchKeyword);
          	    return results; // 자동으로 JSON으로 변환되어 응답됨
          	}
          }
          ```
          </div>
          </details>

   - 페이징은 PaginationInfo(페이징 데이터)와 ItemDefault(=1번에서 만든 SampleDefaultVO) 필요

   - 서비스 메소드 findAllWithPage, findTotalCount 를 사용해 각각 model, paginationInfo에 담음

     - <details><summary><b>페이징 컨트롤러 예시 코드</b></summary>
       <div markdown="1"><br>
       ```java
       @GetMapping()
       public String search(@ModelAttribute Item item, Model model) throws Exception {
       	return this.gallery(item, model); //HTTP말고 그냥 메소드 호출한거.(포워드,리다이렉트 아님)
       }
       @PostMapping() // ...?pageIndex=1 이런식으로 페이지 파라미터 넘어 올거임(pageIndex란 Item이 상속받고 있는 DefaultItem의 필드)
       public String gallery(@ModelAttribute Item item, Model model) throws Exception {
       	// item.setPageUnit(myDataSource.getPageUnit());
       	// item.setPageSize(myDataSource.getPageSize());
       	item.setPageUnit(10);
       	item.setPageSize(10);
       //
       	// pagination setting
       	PaginationInfo paginationInfo = new PaginationInfo();
       	paginationInfo.setCurrentPageNo(item.getPageIndex());
       	paginationInfo.setRecordCountPerPage(item.getPageUnit());
       	paginationInfo.setPageSize(item.getPageSize());
       //
       	item.setFirstIndex(paginationInfo.getFirstRecordIndex());
       	item.setLastIndex(paginationInfo.getLastRecordIndex());
       	item.setRecordCountPerPage(paginationInfo.getRecordCountPerPage());
       //
       	// List
           List<Item> items = itemService.findAllWithPage(item);
           List<ItemResDto> itemsResDto = items.stream().map(o -> new ItemResDto(o)).collect(Collectors.toList());
           int totCnt = itemService.findTotalCount(item);
       //
           model.addAttribute("itemsResDto", itemsResDto);
           paginationInfo.setTotalRecordCount(totCnt);
           // Pagination
           model.addAttribute("paginationInfo", paginationInfo);
           log.info("cnt: {}, resultList: {}", totCnt, items);
           return "jsp/gallery";
       }
       ```
       </div>
       </details>

3. **페이징과 검색란 JSP**

   **(1)"페이징"**은 `<ui: pagination` **형태로 사용 -> 이것이 PaginationTag 이다.**  
   ui 태그 라이브러리 선언 + linkPage 함수 작성 후 ui 태그 사용

   <details><summary><b>jsp 예시 코드 Ver1 - 페이징</b></summary>
   <div markdown="1"><br>
   ```jsp
   <%@ taglib prefix="ui" uri="http://egovframework.gov/ctl/ui"%>
   ...
   <script type="text/javascript">
       //pageNo는 사용한 ui태그가 알아서 담아준다.
   	function linkPage(pageNo) {
   		location.href = "/gallery?pageIndex=" + pageNo;
   	}
   </script>
   <body>
   ...
   ${resultList}
   	<ui:pagination paginationInfo="${paginationInfo}" type="image"
   		jsFunction="linkPage" />
   ...
   </body>
   ```
   </div>
   </details>


   <details><summary><b>jsp 예시 코드 Ver2 - 페이징(AJAX 적용)</b></summary>
   <div markdown="1"><br>
   **현대식으로 AJAX를 추가하여 SPA와 유사한 방식 적용법**<br>
   Ver1방식은 페이지 자체가 새로고침되는 거라면, 이 방식은 화면의 원하는 부분만 바뀐다.<br>
   **기존 코드의 js 함수부분 수정(태그변경, AJAX통신) + 변경할 태그에 id 지정하면 됨!**<br>
   jquery 문법을 활용해서 ajax 적용 했음. (jstl은 jpa 표준 태그 라이브러리 - c, fmt 등)
   ```jsp
   <%@ page contentType="text/html; charset=UTF-8"%>
   <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
   <%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt"%>
   <%@ taglib prefix="ui" uri="http://egovframework.gov/ctl/ui"%>
   <%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
   <%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
   <!DOCTYPE html>
   <html>
   <head>
   <meta charset="UTF-8">
   <title>Insert title here</title>
   <link rel="stylesheet" href="/css/basic.css"/>
   <link rel="stylesheet" href='/css/gallery.css'/>
   <link rel="stylesheet" href='/css/jqueryui.css'/>
   <script src="<c:url value='/js/jquery.js'/>"></script>
   <script src="<c:url value='/js/jqueryui.js'/>"></script>
   </head>
   <script type="text/javascript">
   	//ui:pagination에서 pageNo 파라미터 자동으로 구해옴
   	function linkPageAjax(pageNo) {
   		//location.href = "/gallery?pageIndex=" + pageNo;
           $.ajax({
               url: '/gallery',
               type: 'post',
               data: {
                   pageIndex: pageNo
               }
           }).done(function(fragment) {
               $('#content_area').html(fragment);
           });
   	}
   </script>
   <body>
   <div id="content_area" >
   	${resultList} ... 생략
       <ui:pagination paginationInfo="${paginationInfo}" type="image"
                      jsFunction="linkPageAjax" />
   </div>
   </body>
   </html>
   ```
   </div>
   </details>


   **(2)"검색의 자동완성"**은 애초에 검색란에 검색 시 ajax+jquery의 auto 사용하여 로드

   <details><summary><b>jsp 예시 코드 - 검색란(+자동완성): 얜 애초에 AJAX</b></summary>
   <div markdown="1"><br>
   jquery 기능인 autocomplete(자동완성)이 중요. 특히, jquryui.js 가 꼭 필요.
   ```javascript
   // ajax사용 - 자동완성 
   $(function() {
   	// jquery autocomplete 코드를 생성한다.
   	$("#searchKeyword")
   		.autocomplete(
   			{
   				source : function(request, response) {
   					$.ajax({
   							url : '/suggestKeyword',
   							type : 'post',
   							contentType : "application/x-www-form-urlencoded; charset=UTF-8",
   							data : {
   									searchKeyword : request.term
   							},
   							dataType : 'json',
   							success : function(returnData) {
   							// @ResponseBody 사용 시: response(returnData);
   							// ModelAndView 사용 시: response(returnData.nameList);
   								response(returnData);
   							}
   						});
   					},
   				minLength : 1,
   				select : function(event, ui) {
   					$("#searchKeyword").val(this.value);
   				}
   		});
   });
   function notNullCheck(value) {
   	return !(value === '' || value == null || (typeof value == 'object' && !Object
   			.keys(value).length));
   }
   // ajax사용 - 검색 작업 -> HTTP URL이 페이징과 동일!
   function sampleSearch() {
   	if (notNullCheck($('#searchKeyword').val())) {
   		$.ajax(
   				{
   					url : '/gallery',
   					type : 'post',
   					data : {
   						searchCondition : $(
   								'#searchCondition option:selected').val(),
   						searchKeyword : $('#searchKeyword').val()
   					}
   				}).done(function(fragment) {
   			$('#content_area').replaceWith(fragment);
   		});
   	} else {
   		const errorMessage = '<spring:message code="search.error" />';
   		alert(errorMessage);
   	}
   }
   ```
   - source 함수: Autocomplete의 소스코드 설정 -> 사용자가 입력 필드에 텍스트를 입력할 때마다 호출 (input 이벤트 감지는 Autocomplete API에 구현되어 있을거임)
   - request.term: 사용자가 입력 필드에 입력한 현재 텍스트
   - encodeURIComponent(request.term): 입력한 텍스트를 URL 인코딩하여 서버로 전송 -> 특수 문자가 올바르게 전송
   - minLength: 1: 최소 1글자 이상 입력해야 자동 완성 기능이 작동
   - select: 자동 완성 목록에서 항목을 선택할 때 호출 -> searchName 필드로 선택 값(this.value) 저장
   </div>
   </details>



4. **하단 네비게이션바 커스텀은? -> [공식문서](https://www.egovframe.go.kr/wiki/doku.php?id=egovframework%3Arte%3Aptl%3Aview%3Apaginationtag) 잘 정리되어 있음**

   **XML에 Pagination빈 등록** 필요 -> Renderer(egov꺼 상속&구현), Manager(egov꺼 바로사용)

   - 빈 등록 안해도 jsp에서 ui태그 불러와 paginationInfo속성으로 사용해도 기본값으로 되긴 함.
   - 단, 이 빈까지 등록해야 하단 네비게이션바를 직접 꾸밀 수 있다.

   **동작 설명**: PaginationTag인 ui태그 사용 시

   1. 어떤 PaginationRenderer를 사용할지 PaginationManager에게 위임

   2. 실제 페이징을 위한 작업은 PaginationManager가 반환한 PaginationRenderer이 담당
      - 그럼 PaginationRenderer 을 커스텀 해야겠지?

   3. PaginationInfo는 페이징 기능 렌더링에 필요한 데이터들이 담겨있는 클래스

   **PaginationRenderer 커스텀 예시:**

   - **AbstractPaginationRenderer를 상속받고 생성자를 만들면 됨. 이때, "<a태그와 <img태그"를 건들 수 있음**
   - 생성되는 하단 페이지 이동란 태그를 직접 커스텀 할 수 있어서 적당한 class를 지정하여 폰트도 css로 쉽게 적용해주자.
   - 부트 사용 시 resources/static 하위에 img를 둬서 관리하자.

   <details><summary><b>ImagePaginationRenderer 클래스 구현 예시 코드</b></summary>
   <div markdown="1"><br>
   ```java
   public class ImagePaginationRenderer extends AbstractPaginationRenderer implements ServletContextAware {
   	private ServletContext servletContext;
   	public ImagePaginationRenderer() {
   	}
   //
   	public void initVariables(ServletContext servletContext) {
   		// String strWebDir =
   //		String strWebDir = servletContext.getContextPath() + "/images/pagination/";
   		String strWebDir = "/images/pagination/"; //boot는 resources/static 하위 자동으로 탐색
   		//class='page-link'와 class='page-link-act'는 css 적용위해 설정.
   		firstPageLabel = "<a class='page-link' href=\"#\" onclick=\"{0}({1}); return false;\">" + "<image src='" + strWebDir
   				+ "bt_first.gif' border=0/></a>&#160;";
   		previousPageLabel = "<a class='page-link' href=\"#\" onclick=\"{0}({1}); return false;\">" + "<image src='" + strWebDir
   				+ "bt_prev.gif' border=0/></a>&#160;";
   		currentPageLabel = "<a class='page-link-act'><strong>{0}</strong></a>&#160;";
   		otherPageLabel = "<a class='page-link' href=\"#\" onclick=\"{0}({1}); return false;\">{2}</a>&#160;";
   		nextPageLabel = "<a class='page-link' href=\"#\" onclick=\"{0}({1}); return false;\">" + "<image src='" + strWebDir
   				+ "bt_next.gif' border=0/></a>&#160;";
   		lastPageLabel = "<a class='page-link' href=\"#\" onclick=\"{0}({1}); return false;\">" + "<image src='" + strWebDir
   				+ "bt_last.gif' border=0/></a>&#160;";
   	}
   //
   	public void setServletContext(ServletContext servletContext) {
   		this.servletContext = servletContext;
   		initVariables(servletContext);
   	}
   }
   ```
   </div>
   </details>
   <details><summary><b>xml 빈 등록 예시 코드</b></summary>
   <div markdown="1"><br>
   resources/spring/context-common.xml
   ```xml
   <!-- For Pagination Tag -->
   <bean id="imageRenderer" class="com.secretgallery.tag.ImagePaginationRenderer"/>
   <bean id="paginationManager" class="org.egovframe.rte.ptl.mvc.tags.ui.pagination.DefaultPaginationManager">
       <property name="rendererType">
           <map>
               <entry key="image" value-ref="imageRenderer"/> 
           </map>
       </property>
   </bean>
   ```
   </div>
   </details>

5. **자동완성란 css 스타일링 방법은?**

   아래처럼 ui를 활용하자. 이렇게 해야 css 스타일링이 적용 됨.

   ```css
   .ui-autocomplete {
   color: black;
   background: white;
   width: 120px;
   }
   ```

<br><br>

### Test Code

**JPA파트도 참고(연결해서 보기)**

eGov4.2 순수 스프링은 jUnit4를 사용, 부트는 jUnit5를 사용. -> 문법 다른점 주의.  

> 부트여도 jUnit4로 강제로 사용 당연히 가능

이클립스: 우클릭>new>junit test case>파일이름 설정 + Browse에서 테스트할 클래스(com.secretgallery...) > 이후 테스트 코드 작성. (tdd로 자동완성했던 방식으로 ㄱㄱ)

테스트를 위해 "메모리DB"를 사용하려는데 boot-jpa-data 의존성은 application.properties에 DB설정 없으면 메모리DB 자동 제공 및 테이블도 만들어 줌!   
따라서 의존성을 추가 및 테이블에 @Entity, @Id, @GeneratedValue를 VO에서 붙였었음(본인은 이게 편함)  
=> 가이드에선 테스트코드에 @Before에 hsqld의 쿼리문 호출하여 메모리DB에 table생성했다.  
예: `ScriptUtils.executeSqlScript(dataSource.getConnection(), new ClassPathResource("META-INF/testdata/sample_schema_hsql.sql"));`

<details><summary><b>MyBatis 테스트를 위해 꼭 참고할 점:</b></summary>
<div markdown="1"><br>
1. main/resources하위 XML수동 빈 등록은 test/resources 하위로 복제하자  
   (단, XML내 빈에 property로 연동한 XML은 복제 안해도 됨. 자동으로 main하위도 찾아줌.  
   예로 XML내 빈에 연동된 XML이 아닌 context-common.xml의 경우  `@ImportResource("classpath:/spring/*.xml")` 로 main함수위에 직접 등록해야하는데,  
   빈 내에서 XML연동한 파일의 경우 "test하위로 복제 안해도 main에서 찾아 주더라"  
   **=> 따라서 SQL을 작성한 XML은(=따로 빈에서 연동된) main에서만 관리하면 됨!**
   테스트를 위한 XML 관리법을 다시 정리하자면,   
   **부트니까 @SpringBootTest 를 사용하여 메인환경의 빈을 자동 등록(자동 빈, 수동 빈 둘다)  
   메인환경의 수동 빈 등록법은 "@ImportResource("classpath:..) 필수 + 테스트환경에 ImportResource로 등록한 XML 파일 복제"**
2. boot-jpa-data 의존성으로 자동 테이블 생성 사용 시 "데이터 타입 매핑과 자동 언더스코어(db)에서 카멜케이스(java)"를 알자.  
   테이블 자동 생성 JPA: `Long` → `bigint`, `String` → `varchar(255)`, `LocalDateTime` → `datetime(6)`    
   `@Id+@GeneratedValue(strategy = GenerationType.IDENTITY)` → `PK+not null auto_increment`  
   **MyBatis에서 맞춰주자: XML에서 언더스코어에서 카멜케이스 자동 매핑 설정.   **
   **예: sql에 ${imgSrc} <- img_src 매핑**  
   **객체 매핑 주의점:** sql에 아예 필드명 다른건 select **item_id as id** .. where item_id = #{id} 이런식으로 하거나,  
   **Result Maps**을 사용 -> **as 별칭을 대체!**
</div>
</details>


controller 테스트는 JSP 웹을 보면서 개발했으니 생략하자. (API면 반드시 Mock으로 ㄱㄱ)

<details><summary><b>서비스단 테스트코드 - 페이징 포함:</b></summary>
<div markdown="1"><br>
```java
package com.secretgallery.service.impl;
import static org.assertj.core.api.Assertions.*;
import java.util.List;
import org.egovframe.rte.ptl.mvc.tags.ui.pagination.PaginationInfo;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.transaction.annotation.Transactional;
import com.secretgallery.vo.Item;
import com.secretgallery.vo.ItemDefault;
import com.secretgallery.vo.UpdateItemDto;
import lombok.extern.slf4j.Slf4j;
@SpringBootTest // @SpringBootApplication을 찾아서 테스트를 위한 Bean을 생성
@Transactional // 쓰기모드 -> 서비스코드에 트랜잭션 유무 반드시 확인
@Slf4j
class ItemServiceImplTest {
	@Autowired
	private ItemServiceImpl itemService;
	@Test
	void 저장과조회() throws Exception {
		// 테스트 데이터 준비
		Item item = Item.createItem("테스트 닉네임", "비밀번호", "테스트 제목", "테스트 내용", "이미지 경로");
		// 저장 후 ID 확인 -> 1,2 id
		Long cnt = itemService.save(item);
		log.info("itemId: {}", item.getId());
		itemService.save(item);
		log.info("itemId: {}", item.getId());
		assertThat(cnt).isNotNull();
		// 저장된 데이터 조회
		Item findItem = itemService.findById(item.getId());
		log.info("nickname: {}", findItem.getNickname());
		assertThat(findItem.getNickname()).isEqualTo(item.getNickname());
		assertThat(findItem.getTitle()).isEqualTo(item.getTitle());
	}
	@Test
	public void 수정() throws Exception {
		// given
		Item item = Item.createItem("테스트 닉네임", "비밀번호", "테스트 제목", "테스트 내용", "이미지 경로");
		itemService.save(item);
		log.info("itemId: {}, title: {}", item.getId(), item.getTitle());
		// when
		UpdateItemDto updateItem = new UpdateItemDto();
		updateItem.setId(item.getId());
		updateItem.setNickname(item.getNickname());
		updateItem.setPassword("비번");
		updateItem.setTitle("제목");
		updateItem.setContent("내용");
		itemService.update(updateItem);
		Item findItem = itemService.findById(item.getId());
		// then
		assertThat(findItem).isNotNull();
		assertThat(findItem.getId()).isEqualTo(updateItem.getId());
		assertThat(findItem.getTitle()).isEqualTo(updateItem.getTitle());
		log.info("itemId: {}, title: {}", findItem.getId(), findItem.getTitle());
	}
	@Test
	public void 삭제() throws Exception {
		// given
		Item item = Item.createItem("테스트 닉네임", "비밀번호", "테스트 제목", "테스트 내용", "이미지 경로");
		itemService.save(item);
		Long itemId = item.getId();
		// when
		log.info("delete 전: {}", item);
		itemService.delete(item);
		Item findItem = itemService.findById(itemId);
		log.info("delete 후: {}", findItem);
		// then
		assertThat(findItem).isNull();
	}
	@Test
	public void 전체조회_검색조건조회() throws Exception {
		// given
		Item item = Item.createItem("테스트 닉네임", "비밀번호", "테스트 제목", "테스트 내용", "이미지 경로");
		itemService.save(item);
		itemService.save(item);
		itemService.save(item);
		// when
		List<Item> findItems = itemService.findAll();
		// then
		assertThat(findItems).isNotNull();
		for (Item i : findItems) {
			log.info("itemId: {}, title: {}", i.getId(), i.getTitle());
		}
	}
	// CRUD 테스트 끝
	// 추가 함수 테스트 시작
	@Test
	public void 페이지내아이템_페이징조회() throws Exception {
		// given
		for (int i = 0; i < 15; i++) { // 테스트 데이터 15개 생성
			Item item = Item.createItem("테스트 닉네임" + i, "비밀번호", "테스트 제목" + i, "테스트 내용", "이미지 경로");
			itemService.save(item);
		}
		ItemDefault searchItem = new ItemDefault();
//
		// when
		// pagination setting
		// 첫 번째 페이지 조회 (최신 10개)
		searchItem.setPageIndex(1);
		PaginationInfo paginationInfo = new PaginationInfo();
		paginationInfo.setCurrentPageNo(searchItem.getPageIndex());
		paginationInfo.setRecordCountPerPage(searchItem.getPageUnit());
		paginationInfo.setPageSize(searchItem.getPageSize());
//
		searchItem.setFirstIndex(paginationInfo.getFirstRecordIndex());
		searchItem.setLastIndex(paginationInfo.getLastRecordIndex());
		searchItem.setRecordCountPerPage(paginationInfo.getRecordCountPerPage());
//
		List<Item> firstPage = itemService.findAllWithPage(searchItem);
//
		// 두 번째 페이지 조회 (나머지 5개)
		searchItem.setPageIndex(2);
		paginationInfo.setCurrentPageNo(searchItem.getPageIndex()); // 2페이지로 설정
//
		searchItem.setFirstIndex(paginationInfo.getFirstRecordIndex());
		searchItem.setLastIndex(paginationInfo.getLastRecordIndex());
		searchItem.setRecordCountPerPage(paginationInfo.getRecordCountPerPage());
//
		List<Item> secondPage = itemService.findAllWithPage(searchItem);
		// then
		assertThat(firstPage).isNotNull();
		assertThat(firstPage.size()).isEqualTo(10);
		assertThat(secondPage).isNotNull();
		assertThat(secondPage.size()).isEqualTo(5);
		// 첫 번째 페이지의 id가 두 번째 페이지보다 큰지 확인 (내림차순 정렬)
		assertThat(firstPage.get(0).getId()).isGreaterThan(secondPage.get(0).getId());
		// 로그 출력
		log.info("=== 첫 번째 페이지 ===");
		for (Item i : firstPage) {
			log.info("itemId: {}, title: {}", i.getId(), i.getTitle());
		}
		log.info("=== 두 번째 페이지 ===");
		for (Item i : secondPage) {
			log.info("itemId: {}, title: {}", i.getId(), i.getTitle());
		}
	}
//
	@Test
	public void 아이템이전이후_조회() throws Exception {
		// given
		Item item = null;
		for (int i = 0; i < 5; i++) { // 테스트 데이터 5개 생성
			item = Item.createItem("테스트 닉네임" + i, "비밀번호", "테스트 제목" + i, "테스트 내용", "이미지 경로");
			itemService.save(item);
		}
		// when
		List<Item> findItems = itemService.findPrevNextById(item.getId() - 1); // 마지막id-2, 마지막id-1, 마지막id 가 나와야 정상
		List<Item> findItemsFirst = itemService.findPrevNextById(item.getId() - 4); // 첫번째id, 첫번째id+1 가 나와야 정상
		List<Item> findItemsLast = itemService.findPrevNextById(item.getId()); // 마지막id-1, 마지막id 가 나와야 정상
		// then
		assertThat(findItems).isNotNull();
		assertThat(findItems.size()).isEqualTo(3);
		assertThat(findItemsFirst).isNotNull();
		assertThat(findItemsFirst.size()).isEqualTo(2);
		assertThat(findItemsLast).isNotNull();
		assertThat(findItemsLast.size()).isEqualTo(2);
		// 로그 출력
		log.info("=== 아이템 이전 이후: findItems ===");
		for (Item i : findItems) {
			log.info("itemId: {}, title: {}", i.getId(), i.getTitle());
		}
		log.info("=== 아이템 이전 이후: findItemsFirst ===");
		for (Item i : findItemsFirst) {
			log.info("itemId: {}, title: {}", i.getId(), i.getTitle());
		}
		log.info("=== 아이템 이전 이후: findItemsLast ===");
		for (Item i : findItemsLast) {
			log.info("itemId: {}, title: {}", i.getId(), i.getTitle());
		}
	}
//
	@Test
	public void 아이템총개수_조회() throws Exception {
		// given
		for (int i = 0; i < 10; i++) { // 테스트 데이터 10개 생성
			Item item = Item.createItem("테스트 닉네임" + i, "비밀번호", "테스트 제목" + i, "테스트 내용", "이미지 경로");
			itemService.save(item);
		}
		// when
		ItemDefault searchItem = Item.createItem(null, null, null, null, null);
		int findCnt = itemService.findTotalCount(searchItem);
		// then
		assertThat(findCnt).isNotNull();
		assertThat(findCnt).isEqualTo(10);
		// 로그 출력
		log.info("=== 아이템 총 개수 findCnt:{} ===", findCnt);
	}
//
	@Test
	public void 검색자동완성() throws Exception {
		// given
		for (int i = 0; i < 10; i++) { // 테스트 데이터 10개 생성
			Item item = Item.createItem("테스트 닉네임" + i, "비밀번호", "테스트 제목" + i, "테스트 내용", "이미지 경로");
			itemService.save(item);
		}
		// when
		List<String> result = itemService.findTitleListForSuggest("테스트 제");
		// then
		assertThat(result).isNotNull();
		log.info("=== 자동완성 result: {} ===", result);
	}
}
```
</div>
</details>


<br>

<br>
