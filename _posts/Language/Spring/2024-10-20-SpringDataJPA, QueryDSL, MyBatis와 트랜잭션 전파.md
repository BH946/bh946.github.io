---
title:  "SpringDataJPA, QueryDSL, MyBatis와 트랜잭션 전파"
categories : Spring
tag : [데이터베이스,JPA,MyBatis,트랜잭션관리,커넥션풀]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**Spring Data JPA, QueryDSL, MyBatis와 트랜잭션 전파, 그리고 DataSource 개념과 오라클을 알아보자.**

<br>

<br>

## 필독! 스프링 DB 관련 추가 지식 

앱에서 DB 접근시 **커넥션**뿐만 아니라 DB 내부에서는 **“세션”** 생성 -> **세션이 SQL을 실행!**

- 커넥션은 DB와 클라이언트 간의 TCP 소켓을 통한 **물리적인 연결(채널)**

- 세션은 DB와 클라이언트 간의 **논리적인 연결**

<img src="https://github.com/user-attachments/assets/ffb210a9-867e-4e4e-9090-c286f642d468" alt="image" style="zoom:67%;" /> 

<br><br>

### DataSource

DataSource 가 `(1)my.datasource.하위` 와 `(2)spring: datasource: url, username, password...` 등 여러곳에서 언급되다 보니 헷갈릴 수 있겠다고 생각한다.

`(1)my.datasource.하위` 는 **외부설정이다!** 리팩토링에 정리한 **메시지와 외부설정 파트를 참고**하자

`(2)spring: datasource: url, username, password...` 은 **DB관련 설정**으로 보자!

- **커넥션 풀**은 **실무**에서 무조건 사용하는데, **성능테스트**를 통해 크기를 결정하는 편이다.<br>또한, **hikariCP(=커넥션풀 오픈소스)** 를 **기본으로 스프링 부트가 사용중**

- **hikariCP** 는 DataSource 인터페이스의 **구현체**이고, 스프링부트는 **DataSource**를 자동으로 **application.yml** 같은 설정파일의 DB정보(URL,ID,PW 등)을 읽기 때문에 해당 설정파일을 잘 작성하면 된다.<br>물론, 직접 **Datasource**를 새로 만들어 스프링 빈에 직접 등록해서 사용해도 된다!

- **H2는 Java로 만들어진 DB이다. 따라서 메모리모드(=임베디드 모드)를 지원한다.**
  - 기존 방식: 임베디드모드(메모리모드)를 사용하기 위해 Datasource를 새로 만들어 스프링 빈에 등록한다. -> JVM(메모리)에 DB를 만들어 사용
    - `src/test/resources/schema.sql` 에 원하는 create table 을 작성 (메모리DB는 OFF시 데이터 사라져서)
  - **스프링부트 방식**: 스프링부트는 테스트의 설정파일(application.yml)에 datasource관련내용이 없다면(주석)?? 바로 임베디드 모드(메모리모드)를 자동 생성! (매우간편)
    - create table 도 코드 한줄로 매우쉽게 설정 가능

<br><br>

### 스프링 JdbcTemplate, MyBatis, JPA, 스프링 데이터 JPA, Querydsl

**[스프링 DB 2편 - 데이터 접근 활용 기술](https://github.com/BH946/spring-first-roadmap/tree/main/spring_study_7/itemservice-db)에서 JdbcTemplate, JPA 참고**

참고: Mybatis, Jpa는 귀찮은 setId가 필요 없다. (jdbc의 PreparedStatement구문엔 필요)

**(주관적)데이터접근 무엇을 추천하는가??**

- **(기본적인 CRUD 가정) JPA, 스프링 데이터 JPA, Querydsl** 을 기본으로 사용 -> **[적용 플젝(레포): v1~v3](https://github.com/BH946/LePl_Spring/tree/kbh/lepl/src/main/java/com/lepl/Repository/member), [적용 플젝(서비스): v1~v2](https://github.com/BH946/LePl_Spring/tree/kbh/lepl/src/main/java/com/lepl/Service/member)**

  - 어댑터 추가ver, 단순ver 은 상황에 맞게 선택!
  - 상관없다면?? **단순ver을 사용**하자 -> **스프링데이터JPA + QueryDSL** 를 기본으로 잡자!<br>스프링데이터JPA의 **@Query로 충분히 JPQL작성**도 가능하기 때문!

- **복잡한 쿼리나 SQL사용**의 경우 JdbcTemplate이나 **MyBatis를 함께 사용 추천!**

  - JPA랑 JDBC는 트랜잭션 매니저가 다를텐데 어떡하나??
  - `JpaTransactionManager` 가 대부분 기능들을 제공해서 괜찮다고 한다.
  - 단, JPA 플러시 타이밍에 주의

- **특히, 동적쿼리**는 **MyBatis나 QueryDSL 추천!**

  - findAll() 메서드 쿼리에 "검색 조건이 없을 때, 상품명으로 검색 때, 최대가격 검색 때, 상품명과 최대가격 둘다 검색 때" 총 4가지를 전부 동적으로 SQL 생성이 필요하다면 코드가 복잡..!

  - MyBatis나 QueryDSL로 해결 추천<br>why?? 기존엔 여러 메소드(DAO)를 더 구현하여 사용해야 하기 때문 (아래 예시코드)

  - <details><summary><b>findAll() 동적쿼리 예전 방식 예시</b></summary>
    <div markdown="1"><br>
    ```java
    //QueryDSL 사용하지 않았을 때 동적 쿼리 구현 방식
    public List<Item> findAll(ItemSearchCond cond) {
        String itemName = cond.getItemName();
        Integer maxPrice = cond.getMaxPrice();
        if (StringUtils.hasText(itemName) && maxPrice != null) {
            return repository.findItems("%" + itemName + "%", maxPrice);
        } else if (StringUtils.hasText(itemName)) {
            return repository.findByItemNameLike("%" + itemName + "%");
        } else if (maxPrice != null) {
            return repository.findByPriceLessThanEqual(maxPrice);
        } else {
            return repository.findAll();
        }
    }
    ```
    </div>
    </details>

  - <details><summary><b>findAll() 동적쿼리 현재 방식 예시 - MyBatis</b></summary>
    <div markdown="1"><br>
    ```xml
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
    ```
    </div>
    </details>

<br>

#### MyBatis

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
> MyBatis는 SQL문 사용하므로 조인이나 서브쿼리 덕분에 직면할 문제가 아님.  
> 단, JPA의 fetch join은 N+1방지(즉시로딩)과 연관엔티티 자동 매핑 해준다.   
> MyBatis도 resultMap과 SQL쿼리 작성만 잘하면 자동 연관엔티티 매핑 가능. (아래 예시코드 참고)
>
> <details><summary><b>MyBatis join의 연관엔티티 자동 매핑</b></summary>
> <div markdown="1"><br>
> Order, OrderItem 테이블이고, `List<OrderItem> orderItems` 변수가 자동 매핑!
> ```xml
> <resultMap id="OrderResultMap" type="Order">
>  <id property="id" column="order_id"/>
>  <result property="orderDate" column="order_date"/>
>  <collection property="orderItems" ofType="OrderItem">
>      <id property="id" column="item_id"/>
>      <result property="productName" column="product_name"/>
>      <result property="quantity" column="quantity"/>
>  </collection>
> </resultMap>
> <!-- -->
> <select id="getOrdersWithItems" resultMap="OrderResultMap">
>  SELECT o.id AS order_id, o.order_date, 
>         i.id AS item_id, i.product_name, i.quantity
>  FROM orders o
>  LEFT JOIN order_items i ON o.id = i.order_id
> </select>
> ```
> </div>
> </details>
>
>
> update는 JPA에선 더티체킹 방식이지만, SQL인 MyBatis는 아님.  
> @Transactional에서 JPA는 영속성컨텍스트 덕분에 쿼리모아서 한번에 전송지만, MyBatis는 매순간 전송

**MyBatis 테스트를 위해 꼭 참고할 점:**

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

<br>

**MyBatis 참고 문법**

1. namespace="패키지.매퍼인터페이스": @Mapper 선언한 매퍼 인터페이스 클래스와 연결  
   -> xml쿼리 id와 인터페이스 메소드명과 반드시 동일

2. id 값(중복불가): 이 속성으로 SQL 구분+매퍼 인터페이스의 메서드 이름과 연결되어 쿼리를 더 쉽게 호출

3. useGeneratedKeys="true": 이 속성은 데이터베이스에서 생성된 키를 반환하도록 MyBatis에 지시합니다.

4. keyColumn="user_id": db의 user_id 필드명이 generatedKeys 임을 알려줌.(아마 keyProperty를 한다면 이건 생략해도 될거임 ㅇㅇ)

5. keyProperty="user_id": 생성된 키가 User 객체의 user_id 필드에 설정되도록 합니다.

6. resultType="패키지.Item": 쿼리결과 반환타입 지정  
   -> MyBatis별칭 사용중이면 "Item"

7. SQL의 데이터 입력 매핑?

   - 단일 데이터 입력: parameterType 설정안해도 어차피 1개라 자동 매핑
   - 다중 테이터 입력: 인터페이스에서 @Param 활용해서 xml의 #{}과 매핑 or 같은 필드명 가진 객체(Object)로 매핑(=parameterType설정)  
     @Param예: `void update(@Param("id") Long id, @Param("updateParam") ItemUpdateDto updateParam)`

8. SQL에 as 별칭 사용 or  resultMap 사용: 객체 필드와 DB 컬럼을 매핑

9. 동적 SQL: `if, choose (when, otherwise) , trim (where, set), foreach`

10. 기타: 

    - `insert, update, delete` 의 반환값은 영향을 받은 행수를 반환
    - `${}`는 문자열로써 sql인젝션에 취약(대신 sql에서 수식연산 가능)  
    - `#{}`는 파라미터 형식으로써 sql인젝션에 안전(대신 sql에서 수식연산 불가해서 미리 연산해서 주기!)  
    - `<=, <, > 등` 이런 연산자는 xml 파싱 문제가 있을 수 있어서 "<![CDATA[ 사용 or xml문법 사용"

    <details><summary><b>매퍼 예시 코드</b></summary>
    <div markdown="1"><br>
    ```xml
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
    </mapper>
    ```
    ```java
    public class Item {
        @Id //db에 pk
        @GeneratedValue(strategy = GenerationType.IDENTITY) //db에 not null auto_increment
        @Column(name="item_id") //db 필드명
        private Long id; //엔티티 필드명
        private String nickname;
        private String password;
        private String title;
        private String content;
        private String imgSrc; //db엔 img_src
        @DateTimeFormat(pattern = "yy.MM.dd.HH:mm")
        private LocalDateTime date1;
        @DateTimeFormat(pattern = "yy년 MM월 dd일 HH시 mm분")
        private LocalDateTime date2;
    }
    ```
    </div>
    </details>

<br>

**MyBatis 사용한 3가지 예시 + 동적쿼리**<br>

**(1) 스프링을 사용하지 않은 MyBatis 방식**<br>**4개의 파일 활용:** mybatis-config.xml, SqlSessionTemplate.java, UserMapper.xml, UserMapper.interface

- **라이브러리 추가**: `build.gradle` 의존성 추가: `implementation 'org.mybatis:mybatis:3.5.7' // MyBatis`

- **사용흐름**

  1. `mybatis-config.xml` 에 세팅 및 `UserMapper.xml` 매핑 추가

  2. `UserMapper.xml` 파일 생성 및 sql문 작성+매핑에 namespace=UserMapper인터페이스 추가
     - `UserMapper인터페이스`  파일 생성 및 기능 함수 작성 (insert쪽은 void로!)

  3. `UserRepository` 를 마지막으로 생성 및 DAO 로직 작성
     - 참고로 `SqlSession, SqlSessionFactory` 를 추가 활용

  4. `SqlSessionTemplate` 는 `UserRepository` 에 **SqlSessionFactory를 파라미터로** 넣어 사용 위함
     - mybatis-config.xml 설정 내용도 SqlSessionFactory 자바 객체에 담게 된다.

  <details><summary><b>전체코드</b></summary>
  <div markdown="1"><br>
  **mybatis-config.xml**
  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <!-- -->
  <!-- -->
  <configuration>
    <!-- config.xml 안에서 태그 작성시 태그의 사용 순서 -->
    <!--
      properties, settings, typeAliases, typeHandlers,
      objectFactory, objectWrapperFactory, refletorFactory,
      plugins, environments, databaseIdProvider, mappers
     -->
  <!-- -->
  <!-- -->
    <!-- typeAliases를 통해서 사용하고자 하는 객체를 등록 -->
    <typeAliases>
      <typeAlias type="org.example.v2.domain.User" alias="User"/>
      <typeAlias type="org.example.v2.domain.Profile" alias="Profile"/>
      <typeAlias type="org.example.v2.domain.ChatRoom" alias="ChatRoom"/>
      <typeAlias type="org.example.v2.domain.Message" alias="Message"/>
    </typeAliases>
  <!-- -->
  <!-- -->
    <environments default="development">
      <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
          <property name="driver" value="oracle.jdbc.OracleDriver"/>
          <property name="url" value="jdbc:oracle:thin:@localhost:1521:STR"/>
          <property name="username" value="testUser"/>
          <property name="password" value="1234"/>
        </dataSource>
      </environment>
    </environments>
    <mappers>
      <mapper resource="org/example/UserMapper.xml"/>
      <mapper resource="org/example/ChatRoomMapper.xml"/>
      <mapper resource="org/example/MessageMapper.xml"/>
    </mappers>
  </configuration>
  ```
  **UserMapper.xml**
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
  <!--
  id 값(중복불가): 이 속성으로 SQL 구분+매퍼 인터페이스의 메서드 이름과 연결되어 쿼리를 더 쉽게 호출
  useGeneratedKeys="true": 이 속성은 데이터베이스에서 생성된 키를 반환하도록 MyBatis에 지시합니다.
  keyColumn="user_id": db의 user_id 필드명이 generatedKeys 임을 알려줌.
  keyProperty="user_id": 생성된 키가 User 객체의 user_id 필드에 설정되도록 합니다.
  ...
  sql의 데이터 입력은 매핑이 되어야 함.
  다중 테이터 입력: 인터페이스에서 @Param 활용해서 xml의 #{}과 매핑 or 같은 필드명 가진 객체(Object)로 매핑(=parameterType설정)
  단일 데이터 입력: parameterType 설정안해도 어차피 1개라 자동 매핑 됨
  -->
  <mapper namespace="org.example.v2.repository.mapper.UserMapper">
    <select id="findByIdNPw" resultType="User">
      select * from Member where id = #{id} and pw = #{pw}
    </select>
    <insert id="saveUser" parameterType="User" useGeneratedKeys="true" keyColumn="user_id" keyProperty="user_id">
      insert into Member (id, pw, nickname) values (#{id}, #{pw}, #{nickname})
    </insert>
    <insert id="saveProfile" parameterType="Profile">
      insert into Profile (profile_id, name, col, email) values (#{profile_id}, #{name}, #{col}, #{email})
    </insert>
    <select id="findById" resultType="User">
      select * from Member where id = #{id}
    </select>
    <select id="findByNickname" resultType="User">
      select * from Member where nickname = #{nickname}
    </select>
  </mapper>
  ```
  **UserMapper인터페이스**
  ```java
  public interface UserMapper {
    /**
     * 회원가입
     */
    void saveUser(User user);
    void saveProfile(Profile profile);
    /**
     * 로그인
     */
    User findByIdNPw(@Param("id") String id, @Param("pw") String pw); //id,pw로 찾기
    /**
     * 조회
     */
    User findById(String id); //아이디로 찾기
    User findByNickname(String nickname); //닉네임으로 찾기
  }
  ```
  **UserRepository**
  ```java
  public class UserRepository {
    private SqlSessionFactory sqlSessionFactory;
    public UserRepository(SqlSessionFactory sqlSessionFactory) {
      this.sqlSessionFactory = sqlSessionFactory;
    }
    /**
     * 회원가입
     */
    public User save(User user) {
      try (SqlSession session = sqlSessionFactory.openSession()) {
        UserMapper mapper = session.getMapper(UserMapper.class);
        mapper.saveUser(user);
        session.commit(); //트랜잭션 commit
        return user; //생성된 generatedKeys 포함
      }
    }
    public Profile save(Profile profile) {
      try (SqlSession session = sqlSessionFactory.openSession()) {
        UserMapper mapper = session.getMapper(UserMapper.class);
        mapper.saveProfile(profile);
        session.commit(); //트랜잭션 commit
        return profile; //생성된 generatedKeys 포함X
      }
    }
    /**
     * 로그인
     */
    public User findByIdNPw(String id, String pw) {
      try (SqlSession session = sqlSessionFactory.openSession()) {
        UserMapper mapper = session.getMapper(UserMapper.class);
        return mapper.findByIdNPw(id, pw);
      }
    }
    /**
     * 조회
     */
    public User findById(String id) {
      try (SqlSession session = sqlSessionFactory.openSession()) {
        UserMapper mapper = session.getMapper(UserMapper.class);
        return mapper.findById(id);
      }
    }
    public User findByNickname(String nickname) {
      try (SqlSession session = sqlSessionFactory.openSession()) {
        UserMapper mapper = session.getMapper(UserMapper.class);
        return mapper.findByNickname(nickname);
      }
    }
  }
  ```
  **SqlSessionTemplate**
  ```java
  public class SqlSessionTemplate {
    private static SqlSessionFactory sqlSessionFactory;
    public static SqlSessionFactory getSqlSessionFactory() {
      return sqlSessionFactory;
    }
    //1번 초기화 하면 충분. 싱글패턴. 정적 초기화로 ㄱ.
    static {
      try {
        String resource = "mybatis-config.xml";
        Reader reader = Resources.getResourceAsReader(resource);
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader); //Builder 로 설정 간단히.
      } catch (IOException e) {
        throw new RuntimeException("Failed to initialize SqlSessionFactory: " + e.getMessage(), e);
      }
    }
  }
  ```
  </div>
  </details>

<br>

**(2) 순수 스프링을 사용한 MyBatis 방식 + 동적쿼리**

**구현흐름(eGov를 감미한..)**

1. **MyBatis 인터페이스 방식 구현(=@Mapper 방식)**: ItemService.class 인터페이스 + ItemServiceImpl.class (@Service로 자동 빈) + ItemMapper.class 인터페이스(@Mapper로 프록시로 자동 빈)  
   => 특히, ItemServiceImpl 클래스 = EgovAbstractServiceImpl 상속 + ItemService 구현체 역할

2. **(부트와 큰 차이점)xml설정 부분**: **Mybatis-boot-starter 의존성 없으니 빈 등록 필수!** 

   - context-mybatis.xml 필수(=빈 등록: SqlSessionFactoryBean:sql-mybatis-config.xml등록, MapperConfigurer:@Mapper 인터페이스 자동 스캔 위치 지정) +  
   - sql-mybatis-config.xml 에서 \<mapper Item.xml> + lazy설정, 별칭, 캐시 등
   - Item.xml 에는 SQL문 작성!  

3. **xml 사용 위해:** main함수 위에 `@ImportResource("classpath:/spring/*.xml")` 로 등록

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

<br>

**(3) 스프링 부트를 사용한 MyBatis 방식 + 동적쿼리 -> 자세한 코드 예시는 [Spring Boot MyBatis](https://github.com/BH946/spring-first-roadmap/tree/main/spring_study_7/itemservice-db#mybatis)**<br>**3개의 파일 활용:** application.properties, ItemMapper.interface, ItemMapper.xml, ItemRepository

<details><summary><b>주요 동작방식(그림)</b></summary>
<div markdown="1"><br>
<img src="https://github.com/user-attachments/assets/e7639c7e-498d-40b3-aa93-998b66719ed0" alt="image" style="zoom:80%;" />
</div>
</details>

- **라이브러리 추가**: `implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.2.0' //spring boot mybatis`

- **사용 흐름**

  1. **(큰 차이점)mybatis-boot-starter 의존성** 덕분에 SqlSessionFactory가 자동 빈 등록 되고, MapperConfigurer도 필요없이 자동으로 @Mapper 붙은 인터페이스를 찾아 줌.
  2. `application.properties` 로 type-aliases(별칭), underscore와 camel-case 매핑, 로그 등 설정
  3. `ItemMapper.class`는 Mybatis 매핑 XML(=ItemMapper.xml)을 호출해주는 "매퍼 인터페이스"
     - **@Mapper** 를 반드시 사용 -> 최종적으로 스프링은 프록시 구현체 만들어 **"빈 등록!"**
  4. `ItemMapper.xml`는 부트니까 `src/main/resources` 하위에 위치 및 사용할 sql을 작성한다.
     - 매핑에 namespace=UserMapper인터페이스 추가
  5. `ItemRepository` 를 마지막으로 생성 및 DAO 로직 작성
     - 빈에 등록된 ItemMapper 를 바로 가져다 사용하면 됨.

  <details><summary><b>전체코드</b></summary>
  <div markdown="1"><br>
  **application.properties**
  ```properties
  #MyBatis
  mybatis.type-aliases-package=hello.itemservice.domain 
  mybatis.configuration.map-underscore-to-camel-case=true 
  logging.level.hello.itemservice.repository.mybatis=trace
  ```
  **ItemMapper인터페이스**
  ```java
  @Mapper
  public interface ItemMapper {
      void save(Item item);
      void update(@Param("id") Long id, @Param("updateParam") ItemUpdateDto updateParam);
      Optional<Item> findById(Long id);
      List<Item> findAll(ItemSearchCond itemSearch);
  }
  ```
  **ItemMapper.xml**
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <mapper namespace="hello.itemservice.repository.mybatis.ItemMapper">
      <insert id="save" useGeneratedKeys="true" keyProperty="id">
          insert into item (item_name, price, quantity)
          values (#{itemName}, #{price}, #{quantity})
      </insert>
      <update id="update">
          update item
          set item_name=#{updateParam.itemName},
              price=#{updateParam.price},
              quantity=#{updateParam.quantity}
          where id = #{id}
      </update>
      <select id="findById" resultType="Item">
          select id, item_name, price, quantity
          from item
          where id = #{id}
      </select>
      <select id="findAll" resultType="Item">
          select id, item_name, price, quantity
          from item
          <where>
              <if test="itemName != null and itemName != ''">
                  and item_name like concat('%', #{itemName}, '%')
              </if>
              <if test="maxPrice != null">
                  and price &lt;= #{maxPrice}
              </if>
          </where>
      </select>
  </mapper>
  ```
  **ItemRepository**
  ```java
  @Slf4j
  @Repository
  @RequiredArgsConstructor
  public class ItemRepository {
      private final ItemMapper itemMapper;
      @Override
      public Item save(Item item) {
          log.info("itemMapper class={}", itemMapper.getClass()); // 프록시 확인
          itemMapper.save(item);
          return item;
      }
      @Override
      public void update(Long itemId, ItemUpdateDto updateParam) {
          itemMapper.update(itemId, updateParam);
      }
      @Override
      public Optional<Item> findById(Long id) {
          return itemMapper.findById(id);
      }
      @Override
      public List<Item> findAll(ItemSearchCond cond) {
          return itemMapper.findAll(cond);
      }
  }
  ```
  </div>
  </details>

<br>

#### JPA, Spring Data JPA, QueryDSL의 조합

**참고: [적용 플젝(레포): v1~v3](https://github.com/BH946/LePl_Spring/tree/kbh/lepl/src/main/java/com/lepl/Repository/member), [적용 플젝(서비스): v1~v2](https://github.com/BH946/LePl_Spring/tree/kbh/lepl/src/main/java/com/lepl/Service/member)**

**Spring Data JPA를 바로 사용(=단순ver) vs 중간에 어댑터 추가하여 추상화(=어댑터 추가ver)**

- **단순ver**은 Spring Data JPA 구현체를 바로 사용하므로 **JpaRepository를 상속받는 인터페이스만 만들어**서 바로 사용하면 된다. 인터페이스 바로 사용이 가능한 이유는 **스프링이 프록시 구현체를 만들**어 주기 때문이다.<br>예시: `public interface MemberRepository extends JpaRepository<Item, Long> { }`

- **어댑터 추가ver**은 중간에 **인터페이스를 도입**하는 방식이다!<br>레포지토리 추상화를 통해서 **서비스단의 코드 변경없이** 레포지토리단만 수정하는 효과!!

- **트레이드 오프 -> 구조의 안정성 vs 단순한 구조와 개발 편리성**

  - **어댑터 추가ver** : DI, OCP를 지키기 위해 어댑터를 도입하고(장), 더 많은 코드를 유지한다.(단)
  - **단순ver** : 어댑터를 제거하고 구조를 단순하게 가져가지만(장), DI, OCP를 포기하고, ItemService 코드를 직접 변경한다.(단)

  <details><summary><b>트레이드 오프 유지보수 관점 (자세히) -> 결론: 단순ver 을 기본 사용 (주관적 생각)</b></summary>
  <div markdown="1">
  - **추상화를 시킬수록** 당연히 서비스단을 고칠필요 없이 레포지토리만 고치면 되는 큰 **장점**이 있다 (DI, OCP를 지킬 수 있겠지)
  - 근데, **단점**으로는 추상화 자체에도(인터페이스 도입) 큰 비용이 발생한다. 코드 조금 추가한다고 생각하면 바보다. 구조가 한단계 커지는건데 개발자의 경우 해당 레포지토리를 수정하려고 찾아가다보면 구조가 많으니까 코드 한줄 수정하는게 훨씬 오래걸릴 수 있다. 비용이 크다는 거다.
  - **반대로, 추상화없이 단순**하게 바로 레포지토리를 서비스단에서 가져와 사용한 경우에 **장점**으로는 굉장히 직관적이므로 바로 레포지토리로 들어가서 코드 한줄 수정 쉽게 가능하므로 비용을 크게 단축시킬 수 있다! 
  - 근데, 서비스단의 로직이 많이 변경될 수 있어서 (DI, OCP를 지킬 수 없는거지) 이 **단점**도 감안해야한다.
  - 그렇기에 프로젝트 마다 잘 선택해야하는 트레이드 오프 부분이라 볼 수 있겠다.
  - **결론적으로 권장 방안은?**
    - 누가봐도 대규모 프로젝트이고 구조적으로 개선해야할 일들이 많다면 추상화를 선택한다. 
    - 다만, 그런 상태가 아니거나 선택하기 애매하거나 할 때는 추상화를 선택하지 않고 오로지 **최대한 빠르게 개발하는걸 권장(=단순ver)**한다. 이후에 리팩토링 하면서 추상화가 꼭 필요하다면 그때 도입해도 되니까! 이게 빨리빨리 개발해야하는 우리에게 좀 더 실용적이라 판단이 된다. (feat:김영한 강사님 생각)
    - QueryDSL도 마찬가지로 단순ver처럼 따로 구현해서 바로 가져와 사용하겠다!
  </div>
  </details>

<br>

**단순ver 사용한 예시** -> **JPA+Spring Data JPA+QueryDSL** 의 모습을 소개할거고, Spring Data JPA만 사용할거면 뒤에 JPA추가는 고려 안해도 됨<br>**1+1개의 파일 활용:** SpringDataJpaItemRepository 인터페이스 + ItemQueryRepository 클래스 + JpaRepository 인터페이스 (이건 부트가 제공!)

<details><summary><b>이 방법의 동작구조(그림)</b></summary>
<div markdown="1"><br>
<img src="https://github.com/user-attachments/assets/eab41981-fd80-42b4-956f-b186f2581cd9" alt="image" style="zoom:80%;" /><br>
<img src="https://github.com/user-attachments/assets/b3bcbe64-5cb5-431b-98eb-ee8457635ca7" alt="image" style="zoom:80%;" /><br><br>
**QueryDSL까지 합류한다면?**
<img src="https://github.com/user-attachments/assets/d745de16-af58-4e9d-830a-211a84be0e4f" alt="image" style="zoom:80%;" />
</div>
</details>


- **사용흐름**

  1. `SpringDataJpaItemRepository 인터페이스` 를 추가하여 JpaRepository 인터페이스를 상속하자 -> Spring Data Jpa 사용 목적!

  2. `ItemQueryRepository 클래스` 를 추가하여 복잡한 쿼리(+동적) 이곳에서 처리하자!

     - QueryDSL -> JPQL -> SQL 변환

     - **라이브러리 추가 방법**

       - 자동 생성 Q클래스(QueryDSL의 사용타입)를 gradle clean으로 제거 및 gradle 에 라이브러리 추가

       - ```groovy
         //Querydsl 추가 - 스프링 2.x
         implementation 'com.querydsl:querydsl-jpa'
         annotationProcessor "com.querydsl:querydsl-apt:${dependencyManagement.importedProperties['querydsl.version']}:jpa"
         annotationProcessor "jakarta.annotation:jakarta.annotation-api"
         annotationProcessor "jakarta.persistence:jakarta.persistence-api"
         
         //Querydsl 추가 - 스프링 3.x
         implementation 'com.querydsl:querydsl-jpa:5.0.0:jakarta'
         annotationProcessor "com.querydsl:querydsl-apt:${dependencyManagement.importedProperties['querydsl.version']}:jakarta"
         annotationProcessor "jakarta.annotation:jakarta.annotation-api"
         annotationProcessor "jakarta.persistence:jakarta.persistence-api"
         
         //Querydsl 추가, 자동 생성된 Q클래스 gradle clean으로 제거
         clean {
         	delete file('src/main/generated')
         }
         ```

     - **사용할 때 Q클래스 import 필수!!**

       - `import static hello.itemservice.domain.QItem.*;`
       - Q클래스 위치: build/generated/sources/annotaionProcessor 하위

  3. 마지막으로 서비스단에서 SpringDataJpaItemRepository, ItemQueryRepository 를 가져와 사용하면 성공!

     - 이처럼 단순ver은 굉장히 구조가 단순하다.

  4. 일반 JPA도 추가하고 싶다면 단순ver 취지에 맞게 ItemQueryRepository 처럼 바로 추가 레포지토리 클래스를 만들어서 서비스에서 사용하면 된다! (생략)

  <details><summary><b>전체코드</b></summary>
  <div markdown="1"><br>
  **SpringDataJpaItemRepository 인터페이스**
  ```java
  /**
   * CRUD 는 기본으로 제공
   * 아래 메소드들은 "쿼리 메서드"
   */
  public interface SpringDataJpaItemRepository extends JpaRepository<Item, Long> {
    //변환 JQPL : select i from Item i where i.name like ?
    List<Item> findByItemNameLike(String itemName);
    //변환 JQPL : select i from Item i where i.price <= ?
    List<Item> findByPriceLessThanEqual(Integer price);
    //쿼리 메서드 (아래 메서드와 같은 기능 수행)
    List<Item> findByItemNameLikeAndPriceLessThanEqual(String itemName, Integer price);
    //쿼리 직접 실행 -> 수동 JPQL 작성 지원
    @Query("select i from Item i where i.itemName like :itemName and i.price <= :price")
    List<Item> findItems(@Param("itemName") String itemName, @Param("price") Integer price);
  }
  ```
  **ItemQueryRepository 클래스** -> JPAQueryFactory 필수 사용!<br>그리고 동적 쿼리 위해 BooleanExpression 조건문 클래스 사용한 예시이다.
  ```java
  import static hello.itemservice.domain.QItem.*;
  @Repository
  public class ItemQueryRepositoryV2 {
      private final JPAQueryFactory query;
  //
      public ItemQueryRepositoryV2(EntityManager em) {
          this.query = new JPAQueryFactory(em);
      }
      // 자동생성 Q클래스 덕분에 item 이 바로 사용 가능
      public List<Item> findAll(ItemSearchCond cond) {
          return query.select(item)
                  .from(item)
                  .where(
                          likeItemName(cond.getItemName()),
                          maxPrice(cond.getMaxPrice())
                  )
                  .fetch();
      }
  //
      private BooleanExpression likeItemName(String itemName) {
          if (StringUtils.hasText(itemName)) {
              return item.itemName.like("%" + itemName + "%");
          }
          return null;
      }
      private BooleanExpression maxPrice(Integer maxPrice) {
          if (maxPrice != null) {
              return item.price.loe(maxPrice);
          }
          return null;
      }
  }
  ```
  **서비스단 예시 코드**
  ```java
  @Service
  @RequiredArgsConstructor
  @Transactional
  public class ItemService {
      private final SpringDataJpaItemRepository springDataJpaItemRepository;
      private final ItemQueryRepositoryV2 itemQueryRepositoryV2;
  //
      public Item save(Item item) {
          return springDataJpaItemRepository.save(item);
      }
      public void update(Long itemId, ItemUpdateDto updateParam) {
          Item findItem = springDataJpaItemRepository.findById(itemId).orElseThrow();
          findItem.setItemName(updateParam.getItemName());
          findItem.setPrice(updateParam.getPrice());
          findItem.setQuantity(updateParam.getQuantity());
      }
      public Optional<Item> findById(Long id) {
          return springDataJpaItemRepository.findById(id);
      }
      public List<Item> findItems(ItemSearchCond cond) {
          return itemQueryRepositoryV2.findAll(cond);
      }
  }
  ```
  </div>
  </details>

 <br>

**어댑터 추가ver 를 사용한 2가지 예시 -> 결론: 첫번째 방식 선호!**

**(1) 첫번째 예시 -> 본인은 이게 더 직관적이라 생각**<br>**3+1개의 파일 활용:** ItemRepository인터페이스, JpaItemRepositoryV2 클래스, SpringDataJpaItemRepository 인터페이스 + JpaRepository 인터페이스 (이건 부트가 제공!)

<details><summary><b>이 방법의 동작구조(그림)</b></summary>
<div markdown="1"><br>
<img src="https://github.com/user-attachments/assets/9ad62419-38b1-429c-a36f-55ad47f2ee94" alt="image" style="zoom:80%;" />
</div>
</details>


- **사용흐름**

  1. `ItemRepository 인터페이스` 를 추가하여 인터페이스 도입! (어댑터 역할) -> **아무것도 상속받지 않는다!!!**
  2. `JpaItemRepositoryV2 클래스` 를 추가하여 ItemRepository인터페이스를 구현한다. 단, 이때 아래 SpringDataJpaItemRepository(=Spring Data JPA) 를 가져와 사용함!!
     - 여기서 레포 구현체를 만들때 JPA + Spring Data JPA 를 사용하게 된다!
  3. `SpringDataJpaItemRepository 인터페이스` 를 추가하여 JpaRepository 인터페이스를 상속하자 -> Spring Data Jpa 사용 목적!
  4. 마지막으로 서비스단에서 ItemRepository 를 가져와 사용하면 성공!

  <details><summary><b>전체코드</b></summary>
  <div markdown="1"><br>
  **ItemRepository 인터페이스**
  ```java
  public interface ItemRepository {
      Item save(Item item);
      void update(Long itemId, ItemUpdateDto updateParam);
      Optional<Item> findById(Long id);
      List<Item> findAll(ItemSearchCond cond);
  }
  ```
  **JpaItemRepositoryV2 클래스**<br>
  JPA+SpringDataJPA 사용 모습이며, QureyDSL도 원한다면 따로 클래스 생성 후 사용하면 됨!
  ```java
  @Repository
  @Transactional
  @RequiredArgsConstructor
  public class JpaItemRepositoryV2 implements ItemRepository {
      private final SpringDataJpaItemRepository repository;
  //
      @Override
      public Item save(Item item) {
          return repository.save(item);
      }
      @Override
      public void update(Long itemId, ItemUpdateDto updateParam) {
          Item findItem = repository.findById(itemId).orElseThrow();
          findItem.setItemName(updateParam.getItemName());
          findItem.setPrice(updateParam.getPrice());
          findItem.setQuantity(updateParam.getQuantity());
      }
      @Override
      public Optional<Item> findById(Long id) {
          return repository.findById(id);
      }
  //
      //QueryDSL 사용하지 않았을 때 동적 쿼리 구현 방식
      @Override
      public List<Item> findAll(ItemSearchCond cond) {
          String itemName = cond.getItemName();
          Integer maxPrice = cond.getMaxPrice();
          if (StringUtils.hasText(itemName) && maxPrice != null) {
  //            return repository.findByItemNameLikeAndPriceLessThanEqual("%" + itemName + "%", maxPrice);
              return repository.findItems("%" + itemName + "%", maxPrice);
          } else if (StringUtils.hasText(itemName)) {
              return repository.findByItemNameLike("%" + itemName + "%");
          } else if (maxPrice != null) {
              return repository.findByPriceLessThanEqual(maxPrice);
          } else {
              return repository.findAll();
          }
      }
  }
  ```
  **SpringDataJpaItemRepository 인터페이스**
  ```java
  /**
   * CRUD 는 기본으로 제공
   * 아래 메소드들은 "쿼리 메서드"
   */
  public interface SpringDataJpaItemRepository extends JpaRepository<Item, Long> {
    //변환 JQPL : select i from Item i where i.name like ?
    List<Item> findByItemNameLike(String itemName);
    //변환 JQPL : select i from Item i where i.price <= ?
    List<Item> findByPriceLessThanEqual(Integer price);
    //쿼리 메서드 (아래 메서드와 같은 기능 수행)
    List<Item> findByItemNameLikeAndPriceLessThanEqual(String itemName, Integer price);
    //쿼리 직접 실행 -> 수동 JPQL 작성 지원
    @Query("select i from Item i where i.itemName like :itemName and i.price <= :price")
    List<Item> findItems(@Param("itemName") String itemName, @Param("price") Integer price);
  }
  ```
  **서비스단 예시코드**
  ```java
  @Service
  @RequiredArgsConstructor
  public class ItemService {
      private final ItemRepository itemRepository;
      public Item save(Item item) {
          return itemRepository.save(item);
      }
      public void update(Long itemId, ItemUpdateDto updateParam) {
          itemRepository.update(itemId, updateParam);
      }
      public Optional<Item> findById(Long id) {
          return itemRepository.findById(id);
      }
      public List<Item> findItems(ItemSearchCond cond) {
          return itemRepository.findAll(cond);
      }
  }
  ```
  </div>
  </details>

<br>

**(2) 두번째 예시 -> [레포지토리 챕터에서 정리한 내용](https://bh946.github.io/checklist/CHECK_LIST_SPRING/#%EB%A0%88%ED%8F%AC%EC%A7%80%ED%86%A0%EB%A6%ACdao)**<br>**3+1개의 파일 활용:** MemberRepository 인터페이스 + MemberRepositoryCustom 인터페이스 + MemberRepositoryCustomImpl 클래스 의 조합 + JpaRepository 인터페이스 (이건 부트가 제공!)

- **인터페이스 도입은 동일하되 JPA 사용 로직을 인터페이스로 추가한 구조!**<br>여기선 대표 인터페이스가 2개의 인터페이스를 상속(=Spring Data Jpa, 일반 Jpa)하는 구조<br>1번 예시는 애초에 인터페이스 상속을 받지 않았고, 구현체를 만들었을 뿐. 구조 차이를 알자.

  - 자바는 상속되는 인터페이스들의 메소드 시그니처들이 자동 제공되니까 이런 구조가 가능
  - 물론, 메소드 오버라이딩으로 구현은 필수니까 구현체도 따로 만듬 

- **사용흐름**

  1. `MemberRepository 인터페이스` 를 추가 -> 서비스 계층에서 사용하게 될 본체! (1번과 비교하면 ItemRepository 를 의미!)
     - **여기서 2개의 인터페이스를 상속: JpaRepository, MemberRepositoryCustom**
       - JpaRepository<Member, Long> 인터페이스가 바로 Spring Data JPA 이다. (부트가 제공)
       - MemberRepositoryCustom 인터페이스가 바로 일반 JPA 이다. (우리가 만듬!!)
  2. `MemberRepositoryCustom 인터페이스` 를 추가 -> 일반 JPA 사용 목적
  3. `MemberRepositoryCustomImpl` 를 추가 -> MemberRepositoryCustom 의 구현체!
  4. 마지막으로 서비스단에서 MemberRepository 를 가져와 바로 사용하면 적용 성공!

  <details><summary><b>전체코드</b></summary>
  <div markdown="1"><br>
  ```java
  /**
   * MemberRepository 인터페이스 -> 서비스 계층에서 사용하게 될 본체
   * 여기서 2개의 인터페이스를 상속 받는다.
   * - JpaRepository<Member, Long> 이 바로 Spring Data JPA 이다.
   * - MemberRepositoryCustom 이 바로 일반 JPA 이다. 
   */
  public interface MemberRepository extends JpaRepository<Member, Long>, MemberRepositoryCustom {
    //CRUD 자동 제공
  }
  /**
   * MemberRepositoryCustom 인터페이스 -> 일반 JPA로 커스텀
   */
  public interface MemberRepositoryCustom {
    Member findOne(Long id);
    Member findByUid(String uid);
    List<FindMemberResponseDto> findAllWithPage(int pageId);
  }
  /**
   * MemberRepositoryCustomImpl 클래스 -> 일반 JPA로 커스텀 구현체
   * - save() 주석 -> JpaRepository 꺼 사용하려고
   * - @Override -> 인터페이스 구현 때 당연히 필수 키워드
   */
  @Repository
  @RequiredArgsConstructor // 생성자 주입 + 엔티티매니저 주입 제공
  public class MemberRepositoryCustomImpl implements MemberRepositoryCustom {
    private final EntityManager em;
    /**
     * save, findOne, findByUid, findAllWithPage
     */
  //  public void save(Member member) {
  //    if (member.getId() == null) {
  //      em.persist(member); // db 저장
  //    }
  //  }
    @Override
    public Member findOne(Long id) {
      return em.find(Member.class, id);
    }
    @Override
    public Member findByUid(String uid) {
      List<Member> findMembers = em.createQuery("select m from Member m where m.uid = :uid",
              Member.class)
          .setParameter("uid", uid)
          .getResultList(); // List로 반환 받아야 null처리가 쉬움
      return findMembers.isEmpty() ? null : findMembers.get(0);
    }
    @Override
    public List<FindMemberResponseDto> findAllWithPage(int pageId) {
      int offset = (pageId - 1) * 10;
      int limit = 10;
      List<Object[]> objects = em.createNativeQuery("select m.member_id, m.nickname, e.level " +
              "from (select * from member order by member_id desc limit " + offset + "," + limit + ") m "
              +
              "inner join character c on m.character_id=c.character_id " +
              "inner join exp e on c.exp_id=e.exp_id;")
          .getResultList();
      return objects.stream()
          .map(o -> new FindMemberResponseDto((Long) o[0], (String) o[1], (Long) o[2]))
          .collect(Collectors.toList());
    }
  }
  ```
  </div>
  </details>

<br><br>

### 트랜잭션 전파 + 주의사항

**(1)트랜잭션 AOP 주의 사항 - 프록시 내부 호출**

![image](https://github.com/user-attachments/assets/d8d35f31-ee92-45d3-a174-ed6126163647) 

![image](https://github.com/user-attachments/assets/40872f1f-626b-4c61-8759-6a901c07e1dc)   

- "클라요청" -> "프록시 호출" -> "실제 트랜잭션 호출" 순서가 일반적인데,
- "클라요청" -> "실제 트랜잭션 호출" 인 경우를 마주칠 수 있다. => **3. 트랜잭션 적용X**
  - 자바 문법상 `internal();` 은 `this.internal();` 로 호출하므로 이런 경우가 생긴다.

<img src="https://github.com/user-attachments/assets/d7c6149e-4dfb-489d-a978-d9db1d460b7c" alt="image" style="zoom:50%;" /> 

- 가장 간단한 해결방안은 **"internal 메소드를 별도의 클래스로 분리"**

  - 예로 `static class` 로 `InternalService` 를 하나 만들어서 `internal()` 메소드 구현

  - 사용할때 `InternalService.internal();` 형태로 사용함으로써 `this` 문제 해결 완!

![image](https://github.com/user-attachments/assets/9c2e6fc2-e9af-4d5a-8ba8-77b115148b40) 

**(2)트랜잭션 AOP 주의 사항 - 트랜잭션 적용 범위**

- 클래스 레벨에 “트랜잭션 적용”시 스프링은 **public 메서드만** 트랜잭션 적용하도록 기본 설정
  - 물론, 설정 변경 가능하다.

**(3)트랜잭션 AOP 주의 사항 - 초기화 시점**

- @PostConstruct로 선언된곳에 @Transactional 선언시 트랜잭션은 미적용
  - WHY?? **초기화코드(@PostConstruct)가 먼저 호출되고 그 다음 트랜잭션 AOP가 적용되기 때문이다. 따라서 초기화 시점에 해당 메서드는 트랜잭션 흭득 불가.**
  - 해결법?? **@EventListener(ApplicationReadyEvent.class) 사용**

<br>

레포1, 레포2 에서 각각 트랜잭션 사용 및 이 둘의 데이터가 연관된다면? **데이터 정합성 문제**가 발생

서비스에만 트랜잭션 사용시 **데이터 정합성 해결** -> 레포 하나라도 롤백이면 전부 롤백되니까.

**이때 자동 사용된 개념이 “스프링 트랜잭션 전파”** 이고, 굳이 **독립적으로 트랜잭션** 사용하겠다 한다면 **REQUIRES_NEW** 옵션을 사용해서 처리가능. -> **기본값은 REQUIRED**

- 트랜잭션이 이미 진행중인데 추가로 수행한다면?? -> 이 경우 어떻게 동작할지 결정하는 것을 **“트랜잭션 전파(propagation)”**
- 여러 트랜잭션들이 중복되어 사용된다면 이들을 구분짓기 위해 **“물리,논리 트랜잭션” 개념** 사용
  - **모든 트랜잭션 매니저(물리,논리=외부,내부)를 커밋해야 물리 트랜잭션이 커밋된다. 하나의 트랜잭션 매니저라도 롤백하면 물리 트랜잭션은 롤백된다.**
  - **트랜잭션 참여 : 외부 트랜잭션과 내부 트랜잭션이 하나의 물리 트랜잭션으로 묶이는 것**
  - **같은 물리 트랜잭션 사용 == 같은 동기화 커넥션 사용**
  - <img src="https://github.com/BH946/bh946.github.io/assets/80165014/1bd94b66-7022-47a1-a4a3-eaa68bfb6e90" alt="image" style="zoom: 80%;" />  

**스프링 트랜잭션 전파 - 트랜잭션 두 번 사용 할 때??**

- **(1) 트랜잭션을 순차적으로 2번 사용은?**
  - **"같은 커넥션(=conn0)"**를 사용 + **"다른 프록시 커넥션"**을 사용.
  - ![image](https://github.com/user-attachments/assets/dd3045ba-bea1-48b5-a718-f0c5c0becb07) 
- **(2) 트랜잭션이 순차가 아닌 중복되게 사용하면? (커밋)**  
  아래 코드처럼 "외부 시작 -> 내부 시작,커밋 -> 외부 커밋" 라면?
  - 신규 트랜잭션 이라면 -> 커밋O
  - 신규 트랜잭션 아니라면 -> 커밋X (신규가 젤 외부니까 거기서 커밋1번 해야지!!!)
  - ![image](https://github.com/user-attachments/assets/82493805-7bd4-4042-bb43-1e7f1ff0ef52) 
  - ![image](https://github.com/user-attachments/assets/c7d58ced-b0c2-4bcc-955a-1fdb102a2038) 
  - ![image](https://github.com/user-attachments/assets/d58018d7-b26a-4645-a0a7-c9e0c764a77a) 
  - ![image](https://github.com/user-attachments/assets/6855dc0c-1bf1-4996-9b3d-16e3df0879a9) 
- **(3) 트랜잭션이 순차가 아닌 중복되게 사용하면? (롤백)**  
  - 내부 롤백은 내부적으로 따로 롤백 마킹
    - 외부 커밋 시점에 이를 확인해서 롤백 -> **rollbackOnly 설정을 확인**
  - 이때, **UnexpectedRollbackException** 런타임 예외를 추가로 던진다.
    - **롤백은 중요한 일**이므로 이런 예외를 추가로 던진다.
- **(4) 트랜잭션이 순차가 아닌 중복되게 사용하면? (REQUIRES_NEW)**  
  - REQUIRES_NEW 라는 옵션을 사용하면 **"외부 트랜잭션과 내부 트랜잭션을 분리 가능"**
  - 즉, **독립적인** 트랜잭션이 2개 생기는 것이며 2개가 **동시에 사용**되는 것 -> 정확히는 1개씩 커넥션이 순차적으로 처리 된다.

**그럼 독립적으로 트랜잭션을 사용하는 경우는 뭐가 있을까??**

(1)기본값: 트랜잭션 그대로 전파 사용은 @Transactional의 `Propagation.REQUIRED` 속성

(2)트랜잭션 독립 사용은 @Transactional의 `Propagation.REQUIRED_NEW` 속성

- 사례1는 **결제 처리와 알림**이다. 결제가 성공하면 알림을 보낼건데, 알림 전송 실패하더라도 결제는 성공적으로 이루어져야 한다. 이것도 충분히 가능하다. -> 이게 실제 서비스에서 고려할 만한 부분인 것 같다.

- 사례2은 **배치 작업 및 상태 업데이트**다. 대량 데이터를 처리하는 배치 로직에서 "상태 업데이트"를 독립 트랜잭션으로 실행해서 롤백을 당해도 상태는 업데이트 할 수 있는 방안이다.


```java
@Transactional
public void processBatch(List<Data> dataList) {
  for (Data data : dataList) {
      try {
          // 데이터 처리
          processData(data);
          statusUpdateService.updateStatus(data.getId(), "Processed");
      } catch (Exception e) {
          // 처리 중 오류가 발생해도 상태 업데이트는 독립적으로 진행
          statusUpdateService.updateStatus(data.getId(), "Failed");
      }
  }
}

-------------------------------
    
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void updateStatus(Long dataId, String status) {
// 상태 업데이트
	statusRepository.updateStatus(dataId, status);
}
```

<br><br>

### Oracle+Orange, H2, MySQL (비교)

참고: 테스트용 메모리DB가 아닌 실제 DB를 사용할 경우 **반드시 DB를 실행해서 오픈해야 함!**

H2와 MySQL 사용은 너무 간단하기도 하고 많이 했어서 생략! (**자세한건 application.yml 챕터 참고!**)

**Oracle은 build.gradle에 라이브러리 추가해서 연동하는건 H2나 MySQL처럼 간단하지만, Oracle 자체를 다운받아서 실행하는게 좀 난항이 많다.**

**웬만하면 전부 관리자로 설치 ㄱㄱ**

**용어 참고**

- **HOST** : IP나 도메인을 가진 장치(컴퓨터).
- **CLIENT** : HOST중에 정보를 요청하는 장치(컴퓨터).
- **SERVER** : 요청받은 정보를 제공하는 장치(컴퓨터).
- **오라클에서의 서버** : 오라클 데이터베이스가 설치된 장치(컴퓨터).
- **오라클에서의 클라이언트** : 네트워크를 통해서 데이터베이스를 제어하기 위한 소프트웨어(SQL Plus, SQL Developer, TOAD)를 사용하는 장치(컴퓨터).

- 오라클 버전별 특징도 다르니 주의
  - **시퀀스와 트리거**: 구버전 오라클이나 특정 요구사항에 따라 사용.
  - **IDENTITY 컬럼**: 오라클 12c 이상에서 지원, 더 간편하게 사용할 수 있음.

<br>

**오라클 필수 개념**<br>[문서 참고](https://okky.kr/questions/206807)

- **Oracle Server**는 말 그대로 서버 쪽의 Oracle DB고, **Oracle Client**는 이 Oracle Server(DB)에 접속하는 용도이다. Java의 경우 JDBC를 제공하기에 바로 Oracle 접속이 가능했던 것이다.

- **Orange(Tool)** 의 경우 이 Oracle Client를 이용해서 연동한다고 볼 수 있다! (주로 32bit 활용)

  <details><summary><b>문서속 내용 참고</b></summary>
  <div markdown="1"><br>
  **오라클 DBM 클라이언트에는 오라클 DBM 서버에 접속할 수 있는 프로그램들이 내장**되어 있습니다. 그런데, **Java 에서는 DB 에 상관없이 JDBC 라는 드라이버를 통해 직접 DB 에 연결**하는 표준을 가지고 있습니다. 오라클도 이 JDBC 드라이버를 제공하고 있는 것입니다. 즉, **Oracle Clinet 의 접속 관련 부분이 JDBC 에 이미 구현**되어 있고, 그 외 SQL*PLUS, orange 같은 툴들은 없습니다. JDBC 을 내장해서 관리하는 것이 구현된 것이 SQLDeveloper (Java로 만듦) 이구요. 단순히 Java 환경에서는 JDBC 만 있으면 되므로 Oracle client 는 필요없고, Java 가 아닌 토트, 오렌지 등에서는 Oracle Clinet 가 있어야 하는 거구요. 일부 툴에서는 Oracle Client 와 같이 많은 것을 설치해야만 사용 가능하지만, 일부 툴은 **Oracle instant client 와 같이 설치 없이 간단한 설정 만으로 접속을 가능하게 해주는 JDBC 와 같은 것으로 연결이 가능**합니다. 토드 최신 버젼 등은 지원할 겁니다. (12년 전 자료. 지금은 대부분 지원)
  </div>
  </details>

<br>

**오라클 vs MySQL 간단 비교**

MySQL의 Database 단위로 만들어서 접속하는것과 오라클에 하나의 DB에 접속해서 User단위로 만들어 사용하는것과 유사  
=> 오라클 DB는 각 사용자 별로 테이블, 인덱스, 뷰 등의 **객체가 따로 생성**

1. MySQL

   - 물리적으로 분리된 여러 데이터베이스 생성 가능

   - DATABASE = SCHEMA 개념 사용

2. Oracle

   - 하나의 큰 데이터베이스 인스턴스 사용
   - USER = SCHEMA 개념 사용
   - **또한, 너무 메모리 사용이 큼. "대기업"이 선호하지 "중소는 MySQL 사용!"**

**명령어로 참고 오라클 - sql*plus**

```sql
-- db 서버(STR)와 리스너 실행 필수
sqlplus username/password            -- 일반 접속 (본인: testUser/1234)
sqlplus / as sysdba                 -- SYSDBA로 접속 (대장)
sqlplus /nolog                      -- DB 접속없이 SQL*Plus 실행[1][3]
show user							-- 현재 사용자 확인
SELECT name FROM v$database;         -- 데이터베이스 이름 확인 (본인: STR)
SELECT table_name FROM user_tables;  -- 현재 사용자의 테이블 확인
EXIT 또는 QUIT   -- SQL*Plus 종료[3]
```

<br>

**오라클 설치**<br>[문서 참고](https://velog.io/@yeoonnii/Oracle-%EC%98%A4%EB%9D%BC%ED%81%B4-19c-%EC%84%A4%EC%B9%98%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C#%EC%84%A4%EC%B9%98-%EC%A4%91-%EC%98%A4%EB%A5%981--ins-20802-oracle-database-configuration-assistant%EC%9D%84%EB%A5%BC-%EC%8B%A4%ED%8C%A8%ED%96%88%EC%8A%B5%EB%8B%88%EB%8B%A4)

- 설치버전: Oracle(19LTS) + Oracle Client(11) + Orange(7) -> 아마도 Client, Orange는 32bit

  <details><summary><b>오라클 설치 자세히</b></summary>
  <div markdown="1">
  - [Oracle Software Delivery Cloud](https://edelivery.oracle.com/osdc/faces/SoftwareDelivery) 에서 oracle database download 검색 + oracle database client download 검색 -> 19c, 11 버전 설치!!
    - <img src="https://github.com/user-attachments/assets/3175e10e-37b0-4de3-81ed-e419eeb1f4f9" alt="image" style="zoom: 80%;" /> 
    - 소프트웨어만 설치로 간단히 설치 후 **Database Configuration Assistant 실행(=dbca)**
      - "관리자권한" cmd-dbca실행: db 이름 STR, SGA(1024),PGA(128) 에서 에러. 메모리 확인이 왜 안 된다지?!
      - `dbca -J-Doracle.assistants.dbca.validate.ConfigurationParams=false` 로 dbca 실행 시 해결!!!
      - 계정 비밀번호 oracle로 설정 했음
  - **sqlplus**
    - lsnrctl status 로 리스너 확인 - 잘 연결되어 있는 상태
    - sqlplus / as sysdba 로 db 접속 (sysdba는 root 권한)
    - select instance_name from v$instance; 로 db 잘 생성 되었나 db명 체크
    - exit 로 탈출
    - **db start?**
      - sqlplus / as sysdba 로 root로 접속 후
      - startup 로 db 실행
      - lsnrctl start 로 리스너 실행!
    - **db stop?**
      - lsnrctl stop 로 외부 접속 가능하게 해주는 리스너 종료
      - sqlplus / as sysdba 로 root로 접근 후 
      - shutdown immediate 로 db 종료
    - **services.msc 윈도우 서비스 화면 이동**
      - 오라클이 자동으로 실행되는 (리붓마다) 방지하기 위해 여기서 "수동"으로 바꾸기
      - 따로 만들어둔 **dbstart.bat** 파일로 오라클 필요 때 마다 실행하는 식으로 바꾸자.
      - ```
        //dbstart.bat 소스 -> 주석 다 지우기!
        //STR, Listener 필수 구동
        net start OracleServiceSTR
        net start OracleOraDB19Home1TNSListenerLISTENERR
        ```
        - OracleServiceSTR -> DB STR 기동!
        - OracleOraDB19Home1TNSListenerLISTENERR -> 리스너 기동!
      - <img src="https://github.com/user-attachments/assets/2f179e6e-b465-474e-8ebe-c1670cae6a82" alt="image" style="zoom:80%;" /> 
    - **오라클 삭제는 오라클 홈 디렉토리에 deinstall로 삭제하는거니까 주의**
  </div>
  </details>

**오렌지 설치**<br>[Orange 공홈](https://www.warevalley.com/ko/support/download-list) 에서 설치!! -> Oracle Server, Oracle Client, 64 or 32bit 지원유무 구분하여 설치!<br>본인은 32bit dbau(유니코드) 버전 dba - DBA Edition 설치 했음 (client를 32bit 설치해서 그랬을거임)

- 참고로 dba 버전은 체험용으로 1달인가? 만 무료ㅜㅜ

  <details><summary><b>오렌지 설치 자세히</b></summary>
  <div markdown="1">
  - **[추가 오렌지 참고 문서](https://m.blog.naver.com/skeletonflower/222147717396), [추가 참고+instance client 활용ver](https://hoing.io/archives/153#__70)**
    - instance client 활용 ver은 Oracle(19LTS) + Oracle Client(11) + Orange(7) 조합과 다른 방식일거다.<br>다음에 할 일 있으면 하고! 지금은 11버전 client 설치하는 방식으로 성공 했다.
  - **Oracle Client 에러 참고**
    - Oracle Base: C:\developer\app\oracle-client
    - Oracle Home: C:\developer\app\oracle-client\product\11.2.0\client_1
    - **sibar 에러 ORA-12560: TNS:프로토콜 어댑터 오류 뜸. 환장하겠네;**
      - 리스너가 문제 있을 시 뜨는 오류라 하나보다. [해결 문서](https://200-rush.tistory.com/entry/ORA-12560-Listener)
      - 현재 오라클 서버, 클라이언트 모두 설치했기에 sqlplus가 서버꺼인지 클라꺼인지 확인필요.
        - virtualbox 리눅스로 했으면 오라클 서버 따로 구동했으니 이런 에러도 안떳을텐데 ㅠ
        - **환경변수에 보면 "클라"께 위로 우선순위 되어있다 (PATH경로). "서버"를 위로 올리자.**
      - <img src="https://github.com/user-attachments/assets/b230f2da-7f14-47ff-b09f-3e6b3e7177db" alt="image" style="zoom:80%;" />
  -  **내 컴퓨터 (윈도우) 의 오라클 CLIENT의 TNSNAMES.ORA 파일의 TNS 정보 <-> ORACLE DATABASE 서버(LINUX.난 윈도우.)가 (LISTENER) 연결되어야한다.**
    - port=1521, ip=?
    - 오라클 CLIENT 설치된 서버의 tnsnames.ora 수정!!
      - ```
        TNS별칭 =
          (DESCRIPTION =
            (ADDRESS = (PROTOCOL = TCP)(HOST = 오라클 db 서버 ip)(PORT = db port))
            (CONNECT_DATA =
              (SERVER = DEDICATED)
              (SERVICE_NAME = db sid)
            )
          )
        ```
      - ```
        // 해당 내용 추가
        STR =
          (DESCRIPTION =
            (ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.??.??)(PORT = 1521))
            (CONNECT_DATA =
              (SERVER = DEDICATED)
              (SERVICE_NAME = STR)
            )
          )
        ```
    - 이러면 이제 오렌지 TNS에 뜬다!! 물론, ORA-28040 에러 도함꼐 ㅋㅋㅋ
      - Oracle Database, Oracle Database Client 둘의 버전이 맞지 않아서 뜨는 오류!
      - Oracle Database쪽의 sqlnet.ora 를 수정하자.
      - ```
        // sqlnet.ora 파일 수정 (추가) 
        SQLNET.ALLOWED_LOGON_VERSION_CLIENT = 10
        SQLNET.ALLOWED_LOGON_VERSION_SERVER = 10
        ```
    - **참고 설정과 팁 - 오렌지)**
      - 한번에 조회되는 SQL 길이 늘이기 -> 값이 너무 길면 중간에 잘리는 현상 방지
        - Tools -> Orange Options -> Common -> Session -> Array Size, Long 수정(5000,10000쯤?)
      - 한번에 조회하는 row 수 늘리기 
        - Tools -> Orange Options -> SQL Tool -> General -> Initial Fetch , Next Fetch 수정
        - 또한 Fetch as Need 체크박스 해제하면 그냥 전체 row건이 항상 조회!
      - 단축키
        - sql 실행 : ctrl + enter / 전체 실행 : F5
        - 주석처리 : ctrl + - / 주석처리 해제 : ctrl + shift + -
        - sql문 재배치 : (드래그)ctrl + shift +f
        - 저장 : ctrl + s / 불러오기 : ctrl + o / 새로운 탭 : ctrl + t
        - 모두 소문자 : ctrl + u / 모두 대문자 : ctrl + shift + u
        - Logon 창 띄우기 : ctrl + n 
          - 계정 변경 (계정마다 소유의 테이블이 다르기 때문)
          - 한번 맺었던 세션은 위에 남아있음
  </div>
  </details>

<br>

<br>
