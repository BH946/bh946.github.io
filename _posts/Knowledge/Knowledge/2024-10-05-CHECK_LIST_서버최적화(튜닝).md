---
title: "[체크리스트] 서버최적화(튜닝)"
categories : [Knowledge]
tag : [spring, 스프링, 스프링부트, 서버최적화, DB튜닝]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---





**서버최적화 경험 정리 -> DB튜닝 포함**

<br>

<br>

## 서버최적화 - JPA(N+1,캐시,페이징,Index 등), connection pool

여기서 쿼리튜닝 관련 내용들(ex:Index)는 "DB쿼리튜닝" 파트 참고

N+1, 캐시, 페이징 관련 개념은 CHECK_LIST_SPRING.md 참고 (이미 잘 정리해놔서)

<br><br>

### JPA(캐시,페이징,Index)

**사용한 최적화:** `페이징, N+1 해결(=쿼리수 줄이기=적절한 JOIN사용=ORM의 문제 해결), Index 사용(단일 or 복합), db캐시(물론 db캐시는 안쓰고 서버,웹 캐시만 사용할 예정)` **전부 해당**

**SQL 페이징과 JPQL에서 페이징**

* **JPQL**에서는 **SQL에서 가능한 Limit와 Offset 키워드는 사용 불가**하고 `setFirstResult(), setMaxResults()` 를 사용해야 함.
* 또다른 방법은 제공해주는 클래스 사용 -> **Pageable 클래스 활용** (생략)



**중요한 개념 중 하나는 OFFSET**이다. 만약 N번째 데이터가 필요하면 **그 데이터까지 조회**를 해야한다는게 아쉬운 성능이다.

* **NO-OFFSET**의 경우 Where절로 그 데이터를 접근하면 된다. 성능이 제일 우수!
  * 당연히 where절 사용 컬럼에 Index 필수!!
  * 단, OFFSET을 꼭 사용해야하는 경우엔 애초에 사용 불가
    * 예로 N번째 데이터 Row 바로 조회 해달라고 할 때
  * **커서 기반 페이징**
* **OFFSET**의 경우 인덱스가 적용된 컬럼 사용 방식이 성능 그나마 좋다! (아래 예시 참고)
  * 여러 조인이 필요하면 "서브쿼리로 페이징 하고, 기존 테이블 조인 방식" ㄱㄱ
  * **커버링 인덱스 방식 -> 이걸 주로 사용할 듯**



**캐시 (서버,웹)**

- 정적 리소스들 웹 캐시 간편 등록

  - ```java
    registry.addResourceHandler("/**") // **/*.*, /resources/**
        .addResourceLocations("classpath:/static/")
        .setCacheControl(cacheControl); // 정적 리소스들 캐시 추가
    ```

- DB 데이터들 서버 메모리 캐시 등록

  - 캐시 데이터는 한번 등록하면 해당 데이터가 업뎃 된다고해서 자동 캐시 재등록이 되거나 하지 않음. 직접 로직을 짜줘야함.

  - ```java
    // 서비스단 메소드에 이런식으로 적용
    @Cacheable(value = "users", key = "#memberId", cacheNames = "users", cacheManager = "cacheManager2")
    
    // 만드는 법은 Caffeine 활용 하여 간단히 설정!
    CaffeineCacheManager cacheManager = new CaffeineCacheManager("users");
        cacheManager.setCaffeine(Caffeine.newBuilder()
            .initialCapacity(1) // 내부 해시 테이블의 최소한의 크기 (캐릭터 어차피 1개만 기록)
            .maximumSize(200) // 캐시에 포함할 수 있는 최대 엔트리 수 (멤버 200명 정도한테 적용하자)
    //                .weakKeys() // 직접 키를 설정하므로 주석처리
            .recordStats());
    ```

  - 또는 캐시 사이즈를 `application.yml` 에서도 설정 가능

  - ```properties
    # application.properties
    spring.cache.cache-names=posts
    spring.cache.caffeine.spec=maximumSize=100 # 캐시 사이즈 설정 (예시로 최대 100개의 페이지를 캐시로 관리)
    ```

<br>

#### 페이징 + 캐시 예시 2개

굉장히 좋은 참고 문서: [페이지네이션 최적화 - Offset 문제 가져간 이유](https://taegyunwoo.github.io/tech/Tech_DBPagination) 참고!

여기서 기억에 남은 말: **가장 먼저 서브쿼리를 통해서 커버링 인덱스로 페이징을 진행합니다. 그리고 그 결과와 기존 테이블을 조인시켜서 ‘인덱스에 포함되지 않은 칼럼’을 가져옵니다.**

<details><summary><b>(1)회원 랭킹 보여주는 페이지(정렬필수) -> 30분 마다 갱신</b></summary>
<div markdown="1"><br>
**레포지토리**<br>
- **서브쿼리에 인덱스로 member 테이블을 빠르게 조회 (정렬 된)**<br>
  * 서브쿼리, limit, offset 사용 위해 Native Query 사용<br>
- 이후 기존 테이블과 조인해서 결과 반환<br>
<div markdown="1">
```java
//Limit, Offset -> SQL
List<Object[]> objects = em.createNativeQuery(
"select m.member_id, m.nickname, e.level " + 
"from (select * from member order by member_id desc limit " + offset + "," + limit + ") m " +
"inner join character c on m.character_id=c.character_id " +
"inner join exp e on c.exp_id=e.exp_id;")
.getResultList();
```
</div>
**서비스**<br>
- 30분 마다 갱신이라 **@CacheEvict, @Cacheable, @Scheduled로 충분**<br>
- 페이지별로(pageId) 묶어서 캐시 관리가 좋아서 이렇게 진행. (게시물마다 하는건 너무 많은 캐시 메모리 사용?) + 캐시사이즈 설정<br>
<div markdown="1">
```java
/** 회원 최신순 조회 + 캐시 */
@Cacheable(value = "members", key = "#pageId") // [캐시 없으면 저장] 조회
public List<FindMemberResponseDto> findAllWithPage(int pageId) {
    return memberRepository.findAllWithPage(pageId);
}
// 캐시에 저장된 값 제거 -> 30분 마다 실행하겠다.
// 초(0-59) 분(0-59) 시간(0-23) 일(1-31) 월(1-12) 요일(0-6) (0: 일, 1: 월, 2:화, 3:수, 4:목, 5:금, 6:토)
@Scheduled(cron = "00 30 * * * *") // 30분 00초 마다 수행
@CacheEvict(value = "members", allEntries = true)
public void initCacheMembers() {
}
```
</div>
</div>
</details>
<details><summary><b>(2)게시물 10개씩 출력하는 페이지(홈페이지) -> 수정, 삭제, 추가에 갱신</b></summary>
<div markdown="1"><br>
**레포지토리**<br>
- 서브쿼리 사용할 필요 없어서 바로 JPQL의 페이징 기법 활용 -> 이 또한, 인덱스 사용<br>
<div markdown="1">
```java
//setFirstResult(), setMaxResults() -> JPQL
public List<Item> findAllWithPage(int pageId) {
  return em.createQuery("select i from Item i" +
                        " order by i.id desc", Item.class)
      .setFirstResult((pageId-1)*10)
      .setMaxResults(10) // 개수임!!
      .getResultList();
}
```
</div>
**서비스 -> 여기선 이게 중요!!**<br>
- 페이지별로 url(?page=1) 접근하면 해당 페이지별로 데이터를 가져올거고 이 데이터를 **@CachePut로 기록하고, @Cacheable로 조회, 삭제는 @CacheEvict**<br>
  - 만약 게시물 삭제되면 애초에 게시물No(순번)이 갱신되어야해서 그냥 @CacheEvict로 삭제후 다시 기록하면 됨.<br>
  - 게시물 수정이면 @CachePut으로 해당 PageId 부분만 갱신하면 됨. 게시물 개수는 그대로니까!<br>
- 페이지별로(pageId) 묶어서 캐시 관리가 좋아서 이렇게 진행. (게시물마다 하는건 너무 많은 캐시 메모리 사용?) + 캐시사이즈 설정<br>
<div markdown="1">
```java
// page 단위로(key) 캐시 기록 -> 참고 : value 로 꼭 캐시 영역을 지정해줘야 함
@Cacheable(value = "posts", key = "#pageId") // [캐시 없으면 저장] 조회
public List<Item> findAllWithPage(int pageId) {
    return itemRepository.findAllWithPage(pageId);
}
// page 단위로(key) 캐시 기록 -> 참고 : value 로 꼭 캐시 영역을 지정해줘야 함
@CachePut(value = "posts", key = "#pageId") // [캐시에 데이터 있어도] 저장
public List<Item> updateAllWithPage(int pageId) {
    // pageId 로 간단히 캐시 업데이트용 함수
    return itemRepository.findAllWithPage(pageId); // 반환값을 캐시에 기록하기 때문에 만든 함수
}
// 캐시에 저장된 값 제거
@CacheEvict(value="posts", allEntries = true)
public void initCachePosts(){}
// totalCount 이름으로 캐시 메모리에 기록 [캐시 없으면 저장] 조회
@Cacheable(value = "totalCount")
public Long findTotalCount() { return itemRepository.findTotalCount(); }
// [캐시에 데이터 있어도] 저장
@CachePut(value = "totalCount")
public Long updateTotalCount() { return itemRepository.findTotalCount(); }
```
</div>
</div>
</details>
<br><br>

### db 테스트 특징

#### h2 database

**h2 발견한 특징??**

* h2는 `explain analyze` 를 사용해야 rows 까지 확인 가능
* Index에 사용한 컬럼 정보는 h2웹에서 직접 확인 가능
* mysql의 `show index` 대신할 명령어 -> index 정보확인
  * `SELECT * FROM INFORMATION_SCHEMA.INDEXES WHERE TABLE_NAME = 'LISTS';`
* 그냥 컬럼 정보는?
  * `SELECT * FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'LISTS';`
* 특히 "외래키 매핑" 하면 "**자동**으로 해당 외래키와 현재키 매핑한 Index 생성"
* h2 db테스트 할때 처음 db connection 에 제일 많은 시간이 소요
  * 이는 db connection pool 기법을 활용해서 개선가능 (나중에 다룰 문제)
* db connection 이후엔 캐시를 고려해야 함
  * **캐시를 제거안해주면, 정확한 속도 측정이 어려우므로 꼭 캐시 제거하면서 테스트하자**
* h2에 "최대 행 수" 부분을 전체로 해줘야 limit 설정을 적용하기 쉬우니 이부분도 체크

<br>

**Index로 개선한 예시 -> h2 db로 테스트**

* **상황**

  * H2 디비 사용
  * 테스트 쿼리 : `select * from member m inner join character c on m.character_id=c.character_id order by m.member_id desc limit 10000,5;`
  * 데이터 약 5만개 존재
  * **테스트 실행코드**
    * `explain analyze select * from member m inner join character c on m.character_id=c.character_id order by m.member_id desc limit 10000,5;`
    * **`SET CACHE_SIZE 0;` => (중요) 캐시 꼭 초기화해줘야 정확한 성능 측정이 가능**
    * `select * from member m inner join character c on m.character_id=c.character_id order by m.member_id desc limit 10000,5;`

  * 문제점 : order by 정렬 때문에 "전체범위 처리"이므로 기존 offset 1만을 읽는것도 문제지만 전체 5만을 다 읽는 더 큰 문제가 발생 중!
    * 성능 : 50ms / scanCount: 98146
    * scanCount가 약 10만개인건 join 때문인지 member 5만개, character 5만개 데이터 읽은거라 예상

* **Index 적용**

  * 필요없는 Index는 `drop index 인덱스이름 on member;` 삭제

  * **`create index idx_member_id on member(member_id desc);` 로 인덱스 생성**

    * 해당 Index를 사용하기 위해서는 JPQL을 수정해야 한다!! - 서브쿼리 사용

    * JPQL로 서브쿼리는 from절에 사용불가하므로 이땐 네이티브쿼리 쓸수밖에

    * **핵심은 Index 사용하는 부분을 "서브쿼리"로 가져와 나머지 JOIN**

      * **`order by member_id desc` 가 있으니 자동으로 index 사용!!**

    * **이때, JOIN들도 외래키로 등록된 Index 를 통해 빠르게 조회**

    * ```java
      // 자바에서 "서브쿼리"
      return em.createNativeQuery("select m.member_id, m.nickname, e.level " +
                                  "from (select * from member order by member_id desc limit "+offset+","+limit+") m " +
                                  "inner join character c on m.character_id=c.character_id " +
                                  "inner join exp e on c.exp_id=e.exp_id;")
          .getResultList();
      ```

      ```
      // h2 db에서 "서브쿼리"
      select m.member_id, m.nickname, e.level from (select * from member order by member_id desc limit 10000,10) m inner join character c on m.character_id=c.character_id inner join exp e on c.exp_id=e.exp_id;
      ```

  * 성능 : 12ms / scanCount: 20009 / index sorted

    * 신기하게 index sorted 라고 정렬된 index 사용한것을 알려준다.

  * scanCount가 약 2만개 인건 order by가 이미 적용된 index를 사용하게 되기 때문!

  * 따라서 기존 OFFSET의 문제인 해당 자리까지 데이터 읽는것 때문에 2만개일 뿐!

    * sql문에 offset 1만개로 설정되어 있기에 member 1만개, character 1만개 읽은 걸로 예상

* **정리**

  * order by 한 index를 사용한 경우 거의 5배 가까이 성능이 개선되었다.
    * 물론 OFFSET의 문제는 가져가긴 하지만, 
    * 제일 큰 문제인 매번 조회마다 "order by desc" 실행을 해결한것이 큰 이득
    * OFFSET의 문제를 가져간 이유가 궁금하다면 [페이지네이션 최적화](https://taegyunwoo.github.io/tech/Tech_DBPagination) 참고!

<br><br>

### connection pool

**+다중요청) DB Connection Pool, Thread Connection Pool**

**DB Connection Pool**

* pool size 도출 예시 : `((core_count * 2) + effective_spindle_count) = 4*2+1=9`
  * 기본값은 10
  * 참고로 **스프링부트 yml 파일 HikariCP 설정** 로 간단히 바꿀수 있다.

* **[시리즈3 db 커넥션풀](https://hyuntaeknote.tistory.com/m/12)**

<br>

**Thread Connection Pool**

* **[Thread Pool 간단 적용법](https://velog.io/@rara_kim/Spring-Thread-Pool-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0feat.-Scheduler)**

<br>

<br>

## DB 쿼리튜닝

**SQLP 자격증 준비하면서 경험과 프로젝트 경험 정리 -> 자세한 원리는 드롭리스트로!**

**나만의 튜닝 체크리스트를 만드는게 최우선!**

**쿼리튜닝 방법은 정말 많다.**

* **[더 빠른 SQL 쿼리를 위한 21가지 데이터베이스 튜닝 규칙](https://www.itworld.co.kr/tags/2665/SQL/105792?page=0,0)**
* **[추가적인 튜닝 규칙](https://velog.io/@gillog/SQL-%ED%8A%9C%EB%8B%9D#%EC%B6%94%EA%B0%80%EC%A0%81%EC%9D%B8-%ED%8A%9C%EB%8B%9D-%EA%B7%9C%EC%B9%99)**
* **[DB전문가의 TIP - Youtube](https://youtu.be/qY-nOOX_Smc?si=28sjP31btwGLlcDM)**
* **SQLP 관련 서적 : 친절한 SQL 튜닝, SQL 전문가 가이드, SQL 자격검정 실전문제(aka 노랭이), 국가공인 SQLP 자격검정 핵심노트 1,2, 오라클 성능 고도화 원리와 해법 1,2**
  * 국가공인 SQLP 자격검정 핵심노트가 문제풀이로 이해하기 좋아서 많이 참고
  * 오라클 성능 고도화 원리와 해법이 정말 많은 원리들을 담아 이해하기 좋다.

<br><br>

### 튜닝 체크리스트

**+) [데이터 모델 설계 품질 자가진단](https://blog.naver.com/cjsong/222910170620), [SQL문 품질 자가진단](https://blog.naver.com/PostView.naver?blogId=cjsong&logNo=222910186677&categoryNo=13&parentCategoryNo=0&viewDate=&currentPage=2&postListTopCurrentPage=1&from=postView)**

<details><summary><b>SQL 튜닝 프레임워크(체계, 구조, 틀) -> 절대 이게 무조건 맞다는건 아님. 보통의 기준일 뿐!</b></summary>
<div markdown="1"><br>
**(액세스하는 데이터양 기준)소량 데이터? 부분범위 처리 가능? 기준**
- **(액세스하는 데이터양 기준) 소량 데이터?**
  - YES -> 인덱스, NL조인 활용!
    - 인덱스 구성: 필수 조건 중 가급적 "=" 선두, 테이블 액세스 최소화 컬럼 추가
  - **NO -> 부분범위 처리 가능?**
    - YES -> 인덱스, NL조인, 페이징 처리 활용!
      - 소트 생략 인덱스 구성: 필수 "=" 조건 컬럼, order by 컬럼
    - NO -> Full Scan, 해시조인, 클러스터링, Array처리
      - Full Scan -> 병렬처리, Partition
      - 클러스터링 -> IOT, Cluster
- (+)파싱 부하 경감?
  - 바인드 변수
  - 세션 커서 캐싱
  - 애플리케이션 커서 캐싱
- (+)주요 체크리스트?
  - DB 함수 활용 기준
  - 페이징 처리 패턴
  - 옵션조건 처리 패턴
  - 통계정보 수집 정책
</div>
</details>

<details><summary><b>자주 사용 힌트 목록</b></summary>
<div markdown="1">
- 힌트에 **오류**가 있으면 **SQL Server는 컴파일 에러, 오라클은 그냥 실행(무시)**
- (오라클)옵티마이저 힌트를 사용할 때 유의할 점? (SQL최적화하는 DBMS핵심 엔진)
  1. 힌트 안에 인자를 나열할 땐 ','(콤마)를 생략이나 사용가능 하지만, 힌트와 힌트 사이에 사용하면 안 된다.
  2. 테이블을 지정할 때 스키마명까지 명시하면 안 된다
  3. FROM 절 테이블명 옆에 ALIAS를 지정했다면, 힌트에도 반드시 ALIAS를 사용해야 한다
  - 오라클 예: `/*+ index(C 고객_X1) index(B (가입일자, 고객명)) index(A) index(D (가입일자 고객명))*/`
  - SQL Server 예: `with(index(0))` 또는 `option(table hint(고객, index(고객_pk)))`
    - ‘클러스터형 인덱스’ 없으면 `index(0)` 은 table full, 있으면 그거 scan
    - `index(1)` 은 애초에 ‘클러스터형 인덱스’ 검색
    - `with(forcescan)` -> table full    
</div>
<table>
    <thead>
        <tr>
            <th>분류</th>
            <th>힌트</th>
            <th>설명</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="2">최적화 목표</td>
            <td><strong>ALL_ROWS</strong></td>
            <td>쿼리 전체 처리속도 최적화<br>일반적으로 배치 작업이나 대용량 데이터 처리를 위해 적합<br>특히, DML(삽입,삭제,갱신)을 일부만 처리하고 멈출 수 없어서 항상 사용(옵티마이저가 자동)</td>
        </tr>
        <tr>
            <td><strong>FIRST_ROWS(N)</strong></td>
            <td>처음 N건의 행을 반환하는 속도를 최적화<br>예로 min, max 구할 때 사용(옵티마이저가 자동)</td>
        </tr>
    </tbody>
</table>
<table>
    <thead>
        <tr>
            <th>분류</th>
            <th>힌트</th>
            <th>설명</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="5">액세스 방식</td>
            <td><strong>FULL</strong></td>
            <td>Table Full Scan으로 유도<br>인덱스가 없거나, 전체 테이블 스캔이 더 효율적일 때 사용</td>
        </tr>
        <tr>
            <td><strong>INDEX</strong></td>
            <td>Index Scan으로 유도<br>인덱스를 사용하고, 조건에 맞는 데이터가 적을 때 유리(데이터 중복도가 높으면=선택도가 높으면 효과 없음)<br><strong>Index Range Scan</strong>은 선두 컬럼이 가공되지 않고 where(조건절)에 필수(반드시 `=`일 필요는 없다)<br><strong>Index Full Scan</strong>은 인덱스 선두 컬럼이 조건절에 없으면 자동 사용<br><strong>Index Unique Scan</strong>은 인덱스 구성 컬럼 모두 ‘=’ 조건일 때 사용 가능(수직적 탐색만 하게 됨)</td>
        </tr>
        <tr>
            <td><strong>INDEX_DESC</strong></td>
            <td>인덱스를 역순으로 스캔하도록 유도<br>기본적으로 인덱스는 오름차순으로 정렬되어 있지만, 내림차순으로 데이터를 조회할 필요가 있을 때 사용</td>
        </tr>
        <tr>
            <td><strong>INDEX_FFS</strong></td>
            <td>Index Fast Full Scan으로 유도<br>인덱스에 포함된 컬럼만 사용할 때 사용</td>
        </tr>
        <tr>
            <td><strong>INDEX_SS</strong></td>
            <td>Index Skip Scan으로 유도<br>인덱스 선두 컬럼이 조건절에 없을 때 사용(있어도 부등호 넓은 범위면 효과적)<br>복합 인덱스의 첫 번째 열이 아닌 두 번째 이후 열을 기준으로 검색할 때 사용<br>인덱스 선행컬럼 NDV적고(=선택도높고), 후행컬럼 NDV많을 때(선택도작을 때) 효과적(=in-list와 동작 유사)</td>
        </tr>
    </tbody>
</table>
<table>
    <thead>
        <tr>
            <th>분류</th>
            <th>힌트</th>
            <th>설명</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="5">조인 순서</td>
            <td><strong>ORDERED</strong></td>
            <td>FROM 절에 나열된 테이블 순서대로 조인<br>첫 번째 테이블은 BUILD INPUT로 자동</td>
        </tr>
        <tr>
            <td><strong>LEADING</strong></td>
            <td>힌트 괄호 안에 명시한 테이블 순서대로 조인<br>첫 번째 테이블은 BUILD INPUT로 자동<br>예로 <code>LEADING(T1 T2)</code></td>
        </tr>
        <tr>
            <td><strong>SWAP_JOIN_INPUTS</strong></td>
            <td>해시 조인을 할 때 BUILD INPUT(해시 테이블을 생성하는 테이블)을 명시적으로 선택<br>일반적으로 작은 테이블이 해시 테이블(=해시 맵)을 생성하는 역할<br>예로 <code>SWAP_JOIN_INPUTS(T1)</code></td>
        </tr>
        <tr>
            <td><strong>NO_SWAP_JOIN_INPUTS</strong></td>
            <td>해시 조인을 할 때 PROBE INPUT(탐색하는데 사용되는 테이블)을 명시적으로 선택<br>일반적으로 큰 테이블이 해시 테이블(=해시 맵)을 탐색하면서 조인<br>예로 <code>NO_SWAP_JOIN_INPUTS(T1)</code></td>
        </tr>
        <tr>
            <td><strong>SQL Server는?</strong></td>
            <td>force order = 오라클의 ordered, loop join = 오라클의 nl join<br>예로 <code>option(force order, loop join)</code>또는 <code>option(force order), from절엔 T1 t1 inner loop join T2 t2 on(t2.id=t1.id)</code><br>참고로 nl만 sql server에선 loop 이고, 나머지는 merge join, hash join으로 오라클과 동일 명칭</td>
        </tr>
    </tbody>
</table>
<table>
    <thead>
        <tr>
            <th>분류</th>
            <th>힌트</th>
            <th>설명</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="6">조인 방식</td>
            <td><strong>USE_NL</strong></td>
            <td>Nested Loop(NL) 조인으로 유도<br>작은 데이터셋을 조인할 때 효율적</td>
        </tr>
        <tr>
            <td><strong>USE_MERGE</strong></td>
            <td>소트 머지 조인으로 유도<br>조인 대상 집합(=조인 조건 이외 필터 조건을 만족하는 집합)을 정렬해 PGA에 저장 후 조인<br>조인 과정은 NL조인과 동일<br>‘두 번째 집합’은 반드시 정렬해 PGA저장 후 조인시작, ‘첫 번째 집합’도 마찬가지지만 인덱스 사용시 생략하고 바로 조인시작</td>
        </tr>
        <tr>
            <td><strong>USE_HASH</strong></td>
            <td>해시 조인으로 유도<br>큰 데이터셋을 조인할 때 효과적<br>둘 중 작은 쪽 집합만 PGA에 해시 맵으로 저장 (Build)하고 큰 집합을 스캔 (Probe) 조인<br>
주의: 조인 조건 중 하나 이상이 ‘=’ 조건이어야 함</td>
        </tr>
        <tr>
            <td><strong>NL_SJ</strong></td>
            <td>NL Semi Join으로 유도<br>exists 라고 생각하자!<br>반대로, Anti Join은 not exists</td>
        </tr>
        <tr>
            <td><strong>MERGE_SJ</strong></td>
            <td>Sort Merge Semi Join을 유도</td>
        </tr>
        <tr>
            <td><strong>HASH_SJ</strong></td>
            <td>Hash Semi Join을 유도</td>
        </tr>
    </tbody>
</table>
<table>
    <thead>
        <tr>
            <th>분류</th>
            <th>힌트</th>
            <th>설명</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="2">서브쿼리 팩토링</td>
            <td><strong>MATERIALIZE</strong></td>
            <td>WITH 문으로 정의한 집합을 물리적으로 생성하도록 유도<br>큰 데이터 집합을 많이 줄일 수 있을 때 사용하자!<br>옵티마이저는 with절 한번 참조에 Inline방식, 두 번이상 참조에 Materialize방식 작동<br>실행계획 확인법은 <code>TEMP TRANSFORMATION</code>있으면 Materialize, 없으면 Inline<br>예로 <code>WITH /*+ MATERIALIZE */ T AS (SELECT ...)</code></td>
        </tr>
        <tr>
            <td><strong>INLINE</strong></td>
            <td>WITH 문으로 정의한 집합을 물리적으로 생성하지 않고 INLINE 처리하도록 유도<br>예로 <code>WITH /*+ INLINE */ T AS (SELECT ...)</code><br>(단, SQL Server는 Inline만 제공)</td>
        </tr>
    </tbody>
</table>
<table>
    <thead>
        <tr>
            <th>분류</th>
            <th>힌트</th>
            <th>설명</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="10">쿼리 변환</td>
            <td><strong>MERGE</strong></td>
            <td>뷰 머징(=병합) 유도(=뷰를 해체해라)<br>인라인 뷰를 먼저 실행하는게 아니고 쿼리 블록을 다 풀어서 기존 쿼리와 함께 최적화<br>실행계획에 View가 없어야 함</td>
        </tr>
        <tr>
            <td><strong>NO_MERGE</strong></td>
            <td>뷰 머징 방지<br>건수를 줄이기 위해 인라인 뷰로 따로 그룹핑 하여 조인을 많이 하는데 “자동 뷰머징”주의<br>뷰 머징 막는 키워드: rownum, order by, distinct, group func, window func, set연산, connect by</td>
        </tr>
        <tr>
            <td><strong>UNNEST</strong></td>
            <td>서브쿼리 Unnesting 유도(=서브쿼리를 해체해라)<br>만약, 다른 조인 방식을 이용하려면 일반 조인문으로 변환이 필요함<br>스칼라 서브쿼리 Unnesting라면 해당 스칼라 서브쿼리가 해체되고, 일반 join문 처럼 쿼리가 변경</td>
        </tr>
        <tr>
            <td><strong>NO_UNNEST</strong></td>
            <td>서브쿼리 Unnesting 방지</td>
        </tr>
        <tr>
            <td><strong>PUSH_PRED</strong></td>
            <td>조인조건 Pushdown 은(=조인조건 값을 건건이 뷰 안으로 밀어 넣는 기능) <code>no_merge push_pred</code> 조합으로 힌트 적용<br>조건절 Pushdown 은 일반 조건절을 건건이 뷰 안으로 밀어 넣어 줌<br>실행계획은 VIEW PUSHED PREDICATE<br><strong>GROUP BY를 포함한 인라인뷰를 부분범위 처리 가능하게 유도하기 유용</strong></td>
        </tr>
        <tr>
            <td><strong>NO_PUSH_PRED</strong></td>
            <td>조인조건 Pushdown 방지</td>
        </tr>
        <tr>
            <td><strong>PUSH_SUBQ</strong></td>
            <td>서브쿼리를 가능한 빨리 필터링하도록 유도<br>항상 <code>push_subq no_unnest</code> 힌트</td>
        </tr>
        <tr>
            <td><strong>NO_PUSH_SUBQ</strong></td>
            <td>서브쿼리 필터링을 늦게 처리하도록 유도<br>항상 <code>no_push_subq no_unnest</code> 힌트</td>
        </tr>
        <tr>
            <td><strong>USE_CONCAT</strong></td>
            <td><code>OR</code> 또는 <code>IN-List</code> 조건을 <code>OR-Expansion</code>으로 유도(=OR 조건을 분해하여 Union All)<br>실행계획은 CONCATENATION</td>
        </tr>
        <tr>
            <td><strong>NO_EXPAND</strong></td>
            <td><code>OR</code> 또는 <code>IN</code> 조건을 그대로 처리하도록 <code>OR-Expansion</code>을 방지</td>
        </tr>
    </tbody>
</table>
<table>
    <thead>
        <tr>
            <th>분류</th>
            <th>힌트</th>
            <th>설명</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="3">병렬 처리</td>
            <td><strong>PARALLEL</strong></td>
            <td>테이블 스캔 또는 DML 작업을 병렬로 처리하도록 유도<br>예로 <code>PARALLEL(T1 2) PARALLEL(T2 2)</code></td>
        </tr>
        <tr>
            <td><strong>PARALLEL_INDEX</strong></td>
            <td>인덱스 스캔을 병렬로 처리하도록 유도</td>
        </tr>
        <tr>
            <td><strong>PQ_DISTRIBUTE</strong></td>
            <td>병렬 처리 시 데이터를 어떻게 분배할지 결정<br>“분배” 과정에만 관여, hash 방식으로 분배 되어도 조인은 소트 머지 조인 방식이 가능하다는 것<br>사용법: pq_distribute(inner 테이블명(별칭), outer 테이블의 분배 방식, inner 테이블의 분배 방식)<br>예로 <code>/*+ ordered use_merge(e) parallel(d 4) parallel(e 4) pq_distribute(e hash hash) */</code></td>
        </tr>
    </tbody>
</table>
<table>
    <thead>
        <tr>
            <th>분류</th>
            <th>힌트</th>
            <th>설명</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="2">기타</td>
            <td><strong>APPEND</strong></td>
            <td>Direct Path Insert 방식을 유도<br>Insert 시 DB 버퍼 캐시를 거치지 않고 segment의 HWM 다음에 직접 data를 입력. 이후 commit을 해야만 data를 볼 수 있고, <strong>undo entry를 생성하지 않아 빠름</strong><br><strong>(insert문에서만 작동)"Nologging 모드" 사용시 최소한의 data dictionary 변경 사항만이 redo log에 적용</strong></td>
        </tr>
        <tr>
            <td><strong>DRIVING_SITE</strong></td>
            <td>DB 링크를 사용할 때, 쿼리가 원격(Remote) 또는 로컬(Local)에서 실행되도록 최적화</td>
        </tr>
    </tbody>
</table>
</details>

<details><summary><b>"전체범위 처리", "부분범위 처리" 구분</b></summary>
<div markdown="1"><br>
**가공이 있냐 없냐로 구분하자!**
* **좀 포괄적인 개념이다. Index도 포함 할 수 있고,, 기본적으로 인지해야할 부분이다.**
* 전체범위 처리 예시 : `select * from 테이블 order by asc`
  * order by, count함수 등 가공이 있다.
  - Full Range Scan과 다를바 없다.
* 부분범위 처리 예시 : `select * from 테이블`
  - 딱 봐도 데이터 관점에서는 가공이 없다. 바로 출력하면 된다.
  - where절이 있으면 실패지만 Index를 탈 수 있다면 그 또한 부분범위다. order by도 사실 Index 타면 부분범위로 볼 수 있다. -> 가공이 없어지니까.
* 참고 : **[전체vs부분범위 처리](https://kjw1313.tistory.com/m/96)**
</div>
</details>
**Not 조건의 중요성**: “아니다”란 조건이 있으면 반대로 맞다는 조건을 찾아보자!

**OR 보다는 AND 사용, distinct 는 가급적 사용X**: distinct는 내부적 정렬연산 발생

<details><summary><b>where 조건 컬럼 Index 권장 -> Index 사용 주의점(참고용)</b></summary>
<div markdown="1">
1. 인덱스는 **WHERE 절에서 사용되는 열에 생성**
2. WHERE 절에 사용되는 열이라도 자주 사용해야 가치가 있음 (당연한 말)
3. 데이터 중복도가 높은 열에는 인덱스를 만들어도 효과가 없음 **(선택도가 높은 경우는 효과 없음. 극단적으로 NDV가 1이면 굳이 인덱스로 스캔 할 필요가 있냐는 것)**
4. Index Skip Scan은 인덱스 선행컬럼 NDV적고(=선택도높고), 후행컬럼 NDV많을 때(선택도작을 때) 효과적(=in-list와 동작 유사)
5. **외래키를 설정한 열**에는 **자동으로 외래키 인덱스가 생성**됨 (보통 지원하는 편)
6. **데이터 변경(삽입, 수정, 삭제) 작업이 얼마나 자주 일어나는지 고려**해야 함 (인덱스 효율 때문! 굉장히 중요)
7. 단일 테이블에 인덱스가 많으면 속도가 느려질 수있다. (**테이블당 4~5개 권장**)
8. **클러스터형 인덱스는 테이블당 하나만** 생성할 수 있음
9. 테이블에 클러스터형 인덱스가 아예 없는 것이 좋은 경우도 있음
10. 사용하지 않는 인덱스는 제거 (당연한 말, 인덱스도 많은 비용이 발생)
11. 복합 인덱스는 **WHERE절에서 OR조건이 아니라 AND 조건일 때** 사용하는 것이 좋음 (당연히 OR 조건은 인덱스 안타니까)
</div>
</details>

**UPDATE 대신 CASE 문 사용, 배치 모드로 Delete, Update (=DML) 작업 지향!**

**INSERT할 때** `/*+APPEND*/` **힌트 +** `Nologging` **모드 사용 시 -> redo log 최소한 적용 및 undo log 생성X 효과! (속도 업!)**

**Truncate & Insert**

- **delete** **보다** **truncate** **가 빠름**
- **대량의 데이터 갱신** 때 **CTAS(+nologging)로 복제** 후 기존 테이블 **truncate 하고 insert 하자.**
- **인덱스**도 제거 했다가 DML 후 **재생성**이 더 빠를 수 있음.

**조인을 내포한 Update는 merge into문 사용 권장**

**클러스터링 팩터(=군집성 계수)가 좋은 인덱스를 사용하면 버퍼 피닝 효과로 I/O를 줄일 수 있다.<br>-> 인덱스 잘 고르기**

**바인드 변수로 하드파싱 최소화 + 앱 커서 캐싱으로 Parse Call 최소화(이건 자동이니 개념 잘 알자)**

**OLTP환경에서 바인드 변수보다 리터럴이 더 나은 경우?**

- 수행빈도가 낮고 한 번 수행할 때 수십 초 이상 수행 SQL

- Distinct Value(NDV)가 작고, 값 분포가 균일하지 않을 때

`select id컬럼` 가 `select *(=전체컬럼)` 보다 데이터 정렬 시 소트 공간 사용량(PGA), 네트워크 사용량 더 많음

- 단, 블록 단위 I/O 이므로 Table Full Scan과 Index Scan 량은 둘 다 **동일**
- 물론, select절 컬럼이 인덱스에 모두 있으면 Table Scan은 없겠지~

**리버스 키 인덱스**는 **블록 경합 해소**에 도움 (=**인접된 값을 멀리 분산** 시키는 특성 )

- '11112' 와 '11113' 은 연속된 값이지만 적용 시 '21111' 과 '31111' 이 되어 **분산**
- 대량 데이터가 지속적으로 증가하는 '판매주문' 테이블의 일련번호를 생성했다면<br>최근 데이터는 유사한 값을 가질테고, 해당 부분에 액세스가 집중되는 경합 문제
- 리버스 키 인덱스 적용 시 이를 해결하여 **랜덤 액세스의 효율이 향상**

**IOT(테이블), 클러스터형 인덱스**는 테이블을 인덱스 구조로 관리(정렬기준 정의)

- 리프블록에 rowId가 아닌 실제 데이터를 저장 -> **테이블 랜덤 액세스가 발생X**

**비트맵 인덱스**는 각 value마다 비트맵을 만들고, leaf node에 <컬럼,비트맵>을 저장

- rowId가 아닌 각 키 값에 대한 비트맵을 저장 -> 테이블 랜덤 액세스 이점은 없다
- **Distinct Value가 적을수록 적은 공간을 사용하므로 이점 (분포도가 나쁜 컬럼에 좋음)**
- **B-tree 인덱스 단점 해결 (OR, NOT, NULL 등)**
- 하나의 레코드만 변경해도 관련된 모든 비트맵 lock이 걸리므로 **OLTP환경엔 부적합 (DW, OLAP 적합)**

**크게 2가지 인덱스 튜닝 (세부적으론 굉장히 많음)**

1. **인덱스 스캔 효율화 튜닝**: 인덱스를 스캔한 양에 비해 얻은 결과 건수가 적으면 비효율적**(인덱스 컬럼순서)**
2. **테이블 액세스 횟수 줄이기**: 테이블을 액세스한 양에 비해 얻은 결과 건수가 적으면 비효율적**(인덱스 컬럼추가)**

- **조인 순서 변경(건수 잘 비교해서)**도 효과없으면 NL이면 **해시조인으로 변경**도 효과적
- 조인 조건절을 인덱스에 추가는 드라이빙쪽은 연관없고 inner쪽만 효과있다. **일반 조건을 우선시하자.**
- **부분범위 처리** 가능하면 **order by, group by쪽** 컬럼을 인덱스 추가 고려하자 (**소트생략** 하자는 것) -> **부분범위 처리=소트연산 생략**임!
  - 번외) **push_pred**로 건건이 뷰안에 조인조건절 넣어서 부분범위 처리 성공하게 하는법?
    - 예로 group by는 당연히 전체범위를 읽어 그룹핑을 할텐데 인덱스를 사용해서 부분범위 처리를 했다. 근데, 이를 인라인뷰에 작성하고 메인쿼리와 조인을 하면 "뷰머징"으로 인해 소트가 발생한다.(아마 인덱스 적용이 안되어서 ) 이를 부분범위 처리 하려면?<br>답: `nl조인+no_merge push_pred` -> 뷰를 독립적으로 실행할 때처럼 부분범위 처리가 가능

**IN 조건은 '=' 또는 '필터' 로 처리!**

- '필터' 처리하면 전체 스캔하는 비효율이라 in-list- iterator. 단, 필터가 오히려 좋은 경우도 있다.
- **[꼭 이해! 넘 잘 설명해두신 분 글](https://m.blog.naver.com/fbfbf1/223275530812)**

**결합인덱스 키 컬럼 선정 중요 순서도**

1. 조건 절에 항상 또는 자주 사용되는 컬럼

2. ‘=’ 조건을 자주 사용하는 컬럼

3. 분포도(NDV)가 좋은 컬럼 (데이터량과 구별할 것!)

4. order by절에 주로 사용하는 컬럼 (소트 튜닝 목적!)

**NL 조인 (2중 for 문)** -> `부분범위 처리`와 `소량 데이터 처리`에 효과적

- NL조인이면 **배치I/O**가 동작할 수 있어서 정렬된 결과가 필요한 경우 **외부에도** **order by** **권장**

  - 배치 I/O를 사용하는 경우 인덱스(=기본asc) 순서대로 I/O를 진행하지 않기 때문

  - 배치I/O 안쓰게 하는 힌트를 써도 됨 -> `no_nlj_batching()`

**해시 조인 -> 큰 데이터셋을 조인할 때 효과적**

**distinct**는 결과 집합에서 중복제거 하므로, **group by**로 결과 집합을 미리 줄여 버리는 튜닝도 있다.

**서브쿼리들은 캐싱 효과가 있어서 "값이 같아야 효과적"(내부캐시=용량 작), NL 조인 방식!**

- **서브쿼리는 자동 merge, unnest 꼭 주의! (쿼리변환 챕터 참고)**
- **조인건수(rows)를 줄이기 위해 인라인 뷰로 따로 그룹핑 하여 조인**도 많이 하는데 항상 **“자동 뷰머징”주의**

**배타적erd**는 상황에 따라 union all(소트생략 불가)이나 outer join을 활용 + nvl2, decode(case) 함수 -> 자세한건 개념정리 챕터 참고

**점이력, 선분이력** 최적화 쿼리 예시 -> 자세한건 개념정리 챕터 참고

- **점이력**은 변경시점(시작)만 사용

- **선분이력**은 변경시점(시작) + 종료시점 까지 사용

**공통 표현식 제거**는 같은 조건식을 한번씩만 평가 되도록 튜닝

**union all 사용 시 오히려 늘어나는 인덱스 구성도 주의!!**  

- union all 사용 시 sql문이 2개로 나뉘어서 **인덱스도 2번 접근**

<details><summary><b>소트 튜닝 요약(1~5) – 이걸 해야 “부분범위처리”도 가능</b></summary>
<div markdown="1">
1. **데이터 모델 측면에서의 검토**
   - 잘 정규화된 모델을 통합해버려서 원하는 데이터를 위해 빈번히 group by를 해야 한다면 성능이 좋을 리 없다.
   - **이처럼 group by, union, distinct 가 많은 경우 정규화가 잘 되지 않았음을 암시한다**
2. **소트가 발생하지 않도록 SQL 작성**
   - **union -> union all** 대체 (반드시 중복 없을거라 판단 했을때!)
     - “각 집합 내 + 위 아래 코드간” 둘다 중복 없어야함! **PK, 구별되는 조건, select절에 명시** 확인!
   - **distinct -> exists** 서브쿼리 대체
   - 데이터 존재 여부 확인 때 불필요한 **count** **연산 제거** -> rownum, exists 등으로 충분히 대체
   - **효과적인 페이징 처리**로 소트가 발생하더라도 Top N Sort 로 소트 부하 경감!!
3. **인덱스를 이용한 소트 연산 대체** => “NL조인 방식 필수”(부분범위처리!)
   - 예로 **sort order by** **대체** **+ sort group by** **대체** **+ min, max** **구하기**
     - **first row(min,max)가 동작하려면 “테이블 액세스X”** 여야 한다. (즉, 인덱스로 끝나야 함!)
   - 최종 데이터라 하면 `max(일자)`로 끝내고 다른 컬럼도 필요하다면 `서브쿼리or윈도우orTop1`를 사용<br>다만, 서브쿼리(아래그림)는 from절 2번 조회하다 보니 **윈도우 함수나 TopN를 권장!**
     - <img src="https://github.com/user-attachments/assets/219689df-f629-4e24-890a-6763fd7195ce" alt="image" style="zoom:80%;" />  
   - **그렇다면 Top-N 쿼리로 소트 부하 경감 원리는?**
     - <img src="https://github.com/user-attachments/assets/91351f99-302e-4ded-abc7-e34704cbf3e8" alt="image"  /> 
     - 인덱스가 없어도 경감 가능한 이유가 아래 그림의 맨 오른쪽 값과 비교해서 더 작은 값이 나타날 때만 배열 내에서 다시 정렬을 시도하는 방식으로 처리하기 때문이다. (rownum<=10)
     - **윈도우 함수도** **Top-N** **효과 똑같이 가능**. rank(), row_number() 등 활용해서 조건절에 rnum=1 이런식!
4. **소트 영역을 적게 사용하도록 SQL 작성**
   - **소트 완료 후 데이터 가공**<br>예로 만약 메인쿼리의 select 절에 컬럼을 가공하여 내용이 길다면?
     - 메인쿼리에 소트를 사용하는 것보다 뷰에서 먼저 기존 컬럼을 사용하여 소트하는게 소트영역을 **훨씬 적게 사용**
   - <img src="https://github.com/user-attachments/assets/23b707f7-3605-460d-aef3-f7bfffb41e10" alt="image" style="zoom:80%;" /> 
     - 소트영역 **비효율** 예시 코드 
   - <img src="https://github.com/user-attachments/assets/c0f4eb0b-a24a-4e7b-b7b6-62bcabb3e233" alt="image" style="zoom:80%;" /> 
     - 소트영역 **효율** 예시 코드
5. **소트 영역 크기 조정 -> 메모리 내에서 끝나야 최적. 디스크 소트는 비효율**
   - SQL Server는 소트 영역 수동 조정법 제공X
   - 오라클은 9i부터 자동 PGA 메모리 관리 기능이 도입. 이전엔 sort_area_size 파라미터로 조정
   - 예로 야간에 트랜잭션 없을 때 수동으로 크기 조정해서 작업 수행이 효과적일 수 있다.
   - `alter session set work_area_size_policy = manual;` // 기존엔 auto가 기본값 (자동 관리)
   - `alter session set sort_area_size = 10485760;`
</div>
</details>

함수를 풀어서 **조인문이나 스칼라 서브쿼리**로 변경하는 것이 **함수 호출 부하를 최소화**<br>-> 즉, 근본적인 해결법: **One SQL**

**데이터베이스 Call 최소화 + 고급 SQL 활용(One SQL)**

1. User Call (DBMS 외부) - One SQL, Array Processing을 Array단위 Fetch, 부분범위처리, 효과적 페이징

   - 루프를 도는 작업을 **One SQL**로 구현시 굉장한 성능 향상 **(구현이 어려울 뿐)**
   - **부분범위처리**는 **일정량의 data를 설정**해 클라이언트의 Call이 있을 때마다 읽어서 전송
     - 주의: **Insert**는 맨 마지막 data까지 처리되어야 하므로 **부분범위처리 불가**

2. Recursive Call (DBMS 내부) - 바인드 변수로 하드파싱 줄이기 (바인드 변수+앱 커서 캐싱=Parse 1번)

   - Parse Call 단계에서 하드파싱 하면 딕셔너리 조회로 Recursive Call 발생 가능

3. 둘 다 사용자 정의 함수/프로시저/트리거는 알잘딱

   - **DB 저장형 사용자 정의 함수/프로시저**는 호출 시마다 ’SQL 실행엔진 <-> PL/SQL 가상머신‘ 사이에 Context Switching 발생 (인터프리팅 언어)

     - 함수에 SQL이 내장되어 있다면, 함수 실행 횟수만큼 Recursive Call 발생
     - 함수를 풀어서 **조인문이나 스칼라 서브쿼리**로 변경하는 것이 **함수 호출 부하를 줄이는** 근본적인 해결법 -> **One SQL**
     - **그나마 오라클은 함수에 내장된 SQL을 자동 앱 커서 캐싱하긴 함**

   - **Recursive Call 줄이는 방안? (아래는 효과 큰 순서)**

     1. 스칼라 서브쿼리를 이용한 함수 결과 캐싱(PGA)

     2. Deterministic 함수 캐싱(Fetch Call 단위 캐싱. PGA)

     3. Result 캐시(SGA)

     4. Native 컴파일(인터프리팅 부하를 줄여주지만, 스위칭과 Recurrsive 부하는 못 줄임)

4. **(공통)Parse Call 자체를 줄이는 근본은 "앱 커서 캐싱"이 최고**

   - 참고로 DB Call을 어떤 기준으로 분류하냐에 따라 User, Recursive / Parse, Execute, Fetch 로 나뉘는 것
   - 따라서 Recursive에는 Parse Call이 있는데 User만 Parse Call이 없다던지 이런게 아님. 둘다 Parse, Execute, Fetch 전부 관계가 있는 것

**One SQL**로 구현하는데 적용할 수 있는 유용한 기법들

- CASE문(decode) 활용 -> 현재 pivot, unpivot 으로 발전 (ex: decode(컬럼, 조건1, 결과1, 조건2, 결과2...))
- 데이터 복제 기법 활용(copy_t) -> 현재 dual 테이블의 start with절 없는 **connect by level <= 2** 로 발전!
  - 예전 방식은 `select * from emp a, copy_t b where b.no<=2;` 로 2배 복제, `b.no<=3`은 3배 복제
- union all 활용: full outer join 대체나 M:M 관계 조인 해결방안
- 페이징 처리 활용
- 윈도우 함수 활용
- with 구문 활용: Materialize, Inline 방식 존재 (앞에서 힌트표에 언급)

**Full Scan 범위를 파티션 단위로 접근 처리 -> 따라서 파티션에선 index or table full 잘 고려**

**Local 파티션을 권장 (성능 좋음) -> global은 작업시 unsable 상태가 되기 때문**

<img src="https://github.com/user-attachments/assets/1607bf97-0499-4e67-bd8c-cbe53c7f658a" alt="image"  /> 

> 아마 2번째 행은 OneSQL 얘기인가 흠.. 표가 잘 못 되어 있는건가 싶다

**PWJ(Partition Wise Join)으로 멀티-테이블 조인의 성능 개선 -> 병렬처리!!**

- **Partition Wise Join(PWJ)**은 조인에 참여하는 두 테이블을 조인 컬럼에 대해 같은 기준으로 파티셔닝(equi-partitioning) 하고서 **각 파티션 짝끼리 독립적으로(intra...) 조인**을 수행하는 것을 말한다.<br>파티션 짝이면 통신(inter...)할 필요가 없음.(따라서 성능 업! - 뇌피셜인지 기억이 안남 ㅜ)

- 예시 힌트: `pq_distribute(o partition none)` -> outer를 파티셔닝

- **주의: ROWNUM을 병렬 프로세스들이 처리하면 중복 값**이 생김. 이를 unique한 값 생성위해 QC가 처리하고, 여기서 병목이 생긴다.
  - ROWNUM 대신 ROW_NUMBER 윈도우 함수 사용을 권장한다.
  - UPDATE문이면 MERGE문으로 바꿔 윈도우 함수 사용을 하자.

**Lock에 의한 성증 저하 최소화 방안**

- 트랜잭션 짧게(빨리 lock해제) -> **Lock 경합(Race Condition)** 낮춤

- 같은 데이터를 동시에 갱신 하지 않도록 트랜잭션 설계

- 블로킹으로 인해 사용자가 무한정대기 하는 현상이 발생하지 않도록 `select절 for update` 사용(nowait, wait 0)
  - 블로킹(Blocking): Lock 경합이 발생해 특정 세션이 작업을 진행하지 못하고 멈춰 선 상태

- 트랜잭션 격리성 수준을 불필요하게 상향시키지 말 것

- SQL문장이 가장 빠른 시간 내에 처리를 완료할 것

<br><br>

### 경험한(할) 튜닝 예시?? -> 계속 갱신

**[LEPL](https://github.com/BH946/LePl_Spring/tree/kbh) 플젝에서 튜닝 일화**

1. **페이징 튜닝 -> 가장 먼저 서브쿼리를 통해서 커버링 인덱스로 페이징. 그리고 그 결과와 기존 테이블을 조인시켜서 ‘인덱스에 포함되지 않은 칼럼’을 가져옴**

   - **페이징 + 캐시 예시 2개**

     <details><summary><b>(1)회원 랭킹 보여주는 페이지(정렬필수) -> 30분 마다 갱신</b></summary>
     <div markdown="1"><br>
     **레포지토리**<br>
     - **서브쿼리에 인덱스로 member 테이블을 빠르게 조회 (정렬 된)**<br>
       * 서브쿼리, limit, offset 사용 위해 Native Query 사용<br>
     - 이후 기존 테이블과 조인해서 결과 반환<br>
     <div markdown="1">
     ```java
     //Limit, Offset -> SQL
     List<Object[]> objects = em.createNativeQuery(
     "select m.member_id, m.nickname, e.level " + 
     "from (select * from member order by member_id desc limit " + offset + "," + limit + ") m " +
     "inner join character c on m.character_id=c.character_id " +
     "inner join exp e on c.exp_id=e.exp_id;")
     .getResultList();
     ```
     </div>
     **서비스**<br>
     - 30분 마다 갱신이라 **@CacheEvict, @Cacheable, @Scheduled로 충분**<br>
     - 페이지별로(pageId) 묶어서 캐시 관리가 좋아서 이렇게 진행. (게시물마다 하는건 너무 많은 캐시 메모리 사용?) + 캐시사이즈 설정<br>
     <div markdown="1">
     ```java
     /** 회원 최신순 조회 + 캐시 */
     @Cacheable(value = "members", key = "#pageId") // [캐시 없으면 저장] 조회
     public List<FindMemberResponseDto> findAllWithPage(int pageId) {
         return memberRepository.findAllWithPage(pageId);
     }
     // 캐시에 저장된 값 제거 -> 30분 마다 실행하겠다.
     // 초(0-59) 분(0-59) 시간(0-23) 일(1-31) 월(1-12) 요일(0-6) (0: 일, 1: 월, 2:화, 3:수, 4:목, 5:금, 6:토)
     @Scheduled(cron = "00 30 * * * *") // 30분 00초 마다 수행
     @CacheEvict(value = "members", allEntries = true)
     public void initCacheMembers() {
     }
     ```
     </div>
     </div>
     </details>
     <details><summary><b>(2)게시물 10개씩 출력하는 페이지(홈페이지) -> 수정, 삭제, 추가에 갱신</b></summary>
     <div markdown="1"><br>
     **레포지토리**<br>
     - 서브쿼리 사용할 필요 없어서 바로 JPQL의 페이징 기법 활용 -> 이 또한, 인덱스 사용<br>
     <div markdown="1">
     ```java
     //setFirstResult(), setMaxResults() -> JPQL
     public List<Item> findAllWithPage(int pageId) {
       return em.createQuery("select i from Item i" +
                             " order by i.id desc", Item.class)
           .setFirstResult((pageId-1)*10)
           .setMaxResults(10) // 개수임!!
           .getResultList();
     }
     ```
     </div>
     **서비스 -> 여기선 이게 중요!!**<br>
     - 페이지별로 url(?page=1) 접근하면 해당 페이지별로 데이터를 가져올거고 이 데이터를 **@CachePut로 기록하고, @Cacheable로 조회, 삭제는 @CacheEvict**<br>
       - 만약 게시물 삭제되면 애초에 게시물No(순번)이 갱신되어야해서 그냥 @CacheEvict로 삭제후 다시 기록하면 됨.<br>
       - 게시물 수정이면 @CachePut으로 해당 PageId 부분만 갱신하면 됨. 게시물 개수는 그대로니까!<br>
     - 페이지별로(pageId) 묶어서 캐시 관리가 좋아서 이렇게 진행. (게시물마다 하는건 너무 많은 캐시 메모리 사용?) + 캐시사이즈 설정<br>
     <div markdown="1">
     ```java
     // page 단위로(key) 캐시 기록 -> 참고 : value 로 꼭 캐시 영역을 지정해줘야 함
     @Cacheable(value = "posts", key = "#pageId") // [캐시 없으면 저장] 조회
     public List<Item> findAllWithPage(int pageId) {
         return itemRepository.findAllWithPage(pageId);
     }
     // page 단위로(key) 캐시 기록 -> 참고 : value 로 꼭 캐시 영역을 지정해줘야 함
     @CachePut(value = "posts", key = "#pageId") // [캐시에 데이터 있어도] 저장
     public List<Item> updateAllWithPage(int pageId) {
         // pageId 로 간단히 캐시 업데이트용 함수
         return itemRepository.findAllWithPage(pageId); // 반환값을 캐시에 기록하기 때문에 만든 함수
     }
     // 캐시에 저장된 값 제거
     @CacheEvict(value="posts", allEntries = true)
     public void initCachePosts(){}
     // totalCount 이름으로 캐시 메모리에 기록 [캐시 없으면 저장] 조회
     @Cacheable(value = "totalCount")
     public Long findTotalCount() { return itemRepository.findTotalCount(); }
     // [캐시에 데이터 있어도] 저장
     @CachePut(value = "totalCount")
     public Long updateTotalCount() { return itemRepository.findTotalCount(); }
     ```
     </div>
     </div>
     </details>

2. **인덱스 튜닝 -> 인덱스 추가와 인덱스 사용 위해 컬럼가공 제거**

   - **findByCurrent(), findByDateWithMemberTask(), findOneWithMemberTask() 함수**

     <details><summary><b>findByCurrent 함수 튜닝</b></summary>
     <div markdown="1"><br>
     튜닝: memberId, l.listsDate 인덱스 추가 + 인덱스 사용 위해 날짜컬럼 함수 제거(=컬럼가공 제거 `FORMATDATETIME` 함수 제거)
     ```sql
     -- 튜닝 전
     select * from lists
     where member_id = 1 and formatdatetime(lists_date, 'yyyy-MM-dd') = '2023-11-26';
     -- 튜닝 후
     select * from lists
     where member_id = :memberId and listsDate = :curDate
     ```
     **튜닝 전과 후 사진**<br>
     <img src="https://github.com/user-attachments/assets/154b067b-6a0e-4923-9180-412b9e735802" alt="findByCurrent 인덱스적용전" style="zoom:80%;" /><br>
     <img src="https://github.com/user-attachments/assets/048e2f5e-f678-4e43-a168-963ec8b3e1ae" alt="findByCurrent 인덱스적용후" style="zoom:80%;" />
     </div>
     </details>

3. **Update 튜닝(JPA) -> 더티체킹 대신 "벌크연산"**

   - **쿼리 여러개에서 1개로 변경**

     <details><summary><b>TaskServiceV2.java 에 updateAll() 추가</b></summary>
     <div markdown="1"><br>
     in 절로 task_id 값 비교 + start~end 날짜들 내용(content) 일괄 수정
     ```java
     public void updateAll(List<Task> taskList, String content, LocalDateTime startTime, LocalDateTime endTime) {
         startTime = startTime.toLocalDate().atTime(0, 0, 0);
         endTime = endTime.toLocalDate().atTime(23, 59, 59);
         List<Long> idList = taskList.stream().map(o -> o.getId()).collect(Collectors.toList());
         int updatedCount = em.createQuery(
             "update Task t set t.content = :content" +
             " where t.startTime >= :startTime and t.endTime <= :endTime" +
             " and t.id in :idList")
             .setParameter("content", content)
             .setParameter("idList", idList)
             .setParameter("startTime", startTime)
             .setParameter("endTime", endTime)
             .executeUpdate();
         System.out.println("Updated count: " + updatedCount); // 업데이트된 개수 확인
     	em.clear(); //이거 필수! 까먹었었네
     }
     ```
     **튜닝 전과 후**<br>
     <img src="https://github.com/user-attachments/assets/98969fa9-b667-4267-9041-da9eca365e3a" alt="더티체킹 (쿼리수차이)" style="zoom:80%;" /><br>
     <img src="https://github.com/user-attachments/assets/faa1f1d4-cb49-43b7-997a-581d64b70a18" alt="더티체킹 대신 벌크연산 적용(쿼리수차이)" style="zoom:80%;" />
     </div>
     </details>

<br>

**[SECRET](https://github.com/BH946/secret-art-typing-gallery) 플젝에서 튜닝 일화**

1. 페이지 조회 성능 개선 방안 → Auto Increment vs **No 필드 사용 때**

   - 간단한 조회는 Auto Increment 로 효과적이지만, 확장성을 따져 봤을 때 No 필드도 효과적

   - 테이블에 No속성을 추가 + 해당 인덱스에만 desc 사용을 추천!<br>-> 인덱스 재정렬 비용만 생각하면 됨!
     - 예로 게시물 추가 시: 테이블엔 그냥 추가만하면 됨(비용 약함) + 인덱스 재정렬 비용
     - 예로 게시물 삭제 시: 테이블엔 그냥 삭제하면 됨(비용 약함) + 인덱스 재정렬 비용
       - **No가 중간에 숫자 몇개 생략**되어도 사용자 입장에서 거리감 없어서 그냥 삭제 굿!
     - 예로 게시물 수정 시: 데이터 접근 비용 말고는 없음(비용 매우약함)

<br>

**[BCHAT](https://github.com/BH946/bchat) 플젝에서 튜닝 일화**

1. 메시지 이전 대화 출력 개선 방안 -> **테이블 파티셔닝**
   - 보통 시간별 파티셔닝을 사용해 하루 단위로 메시지를 저장하거나, 사용자별 파티셔닝을 통해 유저 ID에 따라 데이터를 분할
   - 여기선 유저ID에 따라 파티셔닝(귀찮아서 안했던가? 담에 파티셔닝 해보자~!)

<br>

<details><summary><b>SQL튜닝사례 PDF</b></summary>
<div markdown="1">
1. **온라인 SQL 튜닝 사례**
   - 불충분한 인덱스 컬럼 구성 -> **해결: 인덱스 컬럼 수정**
     - 필터 조건을 인덱스에 추가함으로써 테이블 블록 액세스 량 323K -> 5693블록으로 크게 감소!
     - **튜닝 전) 센터일괄출금관리_PK: 자동이체종류코드, 센터컷처리일자, 일괄출금관리번호**
     - <img src="https://github.com/user-attachments/assets/679065ab-33a2-4aff-b762-c8e44accf3c5" alt="image" style="zoom:80%;" />
     - **튜닝 후) 센터일괄출금관리\_IX03\_TMP: 자동이체종류코드, 카드원장조회일련번호, 센터컷처리일자**
     - <img src="https://github.com/user-attachments/assets/1caff89a-e4bd-4ea7-b258-ebc889a336b7" alt="image" style="zoom:80%;" />  
   - 선택적 조건절 이슈와 최적화 -> **해결: 원하는 인덱스 사용하도록 최적화 로직을 분기**
     - 참고: 오라클의 `||` 은 concat임 (문자 붙이기)<br>`like 'ab' || '%'` 는 인덱스 가능, `like '%' || 'ab' || '%'` 는 인덱스 불가능!
     - 문제: BIC사용여부 컬럼 외 모두 선택적인데, 운영DB를 확인 시 지연 발생은 주로 BIC코드 값이 입력된 경우 -> 그런데, 인덱스는 IX02가 사용 되는 문제 (PK인덱스가 훨씬 효과적)
     - PK인덱스 사용 시 테이블 블록 엑세스 량 58752 -> 520블록으로 크게 감소!
     - **튜닝 전)** 
     - <img src="https://github.com/user-attachments/assets/eed6d393-4a34-4bc2-957d-7fa9dabea236" alt="image" style="zoom:80%;" />
     - **튜닝 후) 인라인뷰가 튜닝POINT**
     - <img src="https://github.com/user-attachments/assets/0164fe8a-163f-4b3b-9a3c-507c6da6fdbb" alt="image" style="zoom:80%;" /> 
   - INDEX SKIP SCAN 기능을 활용한 최적화 -> **해결: 인덱스의 2번째 이후 조건절을 액세스로 사용**
     - 상황: **어음발행취소일자(between 사용)** 컬럼 조건으로 액세스 범위를 크게 줄일 수 있는데 액세스 조건이 아닌 **필터 조건**으로 사용 중<br>어음발행취소일자 컬럼이 있는 인덱스는 **외상매출채권원장_IX05( 어음상품코드 ,어음발행취소일자)가 유일 -> IX07 인덱스는 애초에 해당 컬럼이 없음**
     - **주의: 해당 테이블에 인덱스가 이미 많아서 기존 인덱스 활용을 위해 SKIP SCAN 유도**
     - **튜닝 전)**
     - <img src="https://github.com/user-attachments/assets/a62a4458-42d9-4c5b-8fdc-7e53779cd399" alt="image" style="zoom:80%;" /> 
     - **튜닝 후) 어음상품코드 컬럼 조건값이 없더라도, 실데이터를 확인해 보면 이 값이 7개만 존재하므로(즉, ‘스킵’을 7번만 수행하면 되므로) SKIP SCAN의 비효율적 측면은 크지 않음!**
     - <img src="https://github.com/user-attachments/assets/92b254c5-1d98-488b-bc32-63c9d03b76e6" alt="image" style="zoom:80%;" />  
   - 데이터 분포 변화를 고려한 최적화 -> **해결: 소트생략 튜닝과 신규 인덱스**
     - 상황: 넓은 범위 조건 데이터를 액세스 해야 하는데, **자료상태구분코드 컬럼**으로 액세스 범위를 줄일 수 있지만 적절한 인덱스가 없고 ORDER BY로 **SORT 연산**도 발생
     - <img src="https://github.com/user-attachments/assets/de0eb088-5012-439a-98be-382aba3f8e92" alt="image" style="zoom:80%;" /> 
     - **첫 번째 튜닝 방안: 부분 범위 처리 -> 불충분한 개선**<br>인라인 뷰로 PK인덱스 사용 + rownum으로 COUNT (STOPKEY) 유도<br>단, 자료상태구분코드 컬럼은 여전히 필터로 처리 중이라 액세스 범위가 N차 조회 시 큼
     - <img src="https://github.com/user-attachments/assets/b96fb74f-bb22-4a5c-aa0d-7f93b1607edb" alt="image" style="zoom:80%;" />  
     - **두 번째 튜닝 방안: 신규 인덱스 -> 그림의 화살표(=액세스 범위)가 충분히 개선**<br>조회 조건과 부분범위 처리 한번에 다 해결 (필터 조건이 액세스 조건으로!)
     - <img src="https://github.com/user-attachments/assets/03e6e64f-792d-4801-8932-d98d33b9a9be" alt="image" style="zoom:80%;" /> 
   - 정렬 회피 방안으로 부분범위 처리 유도 -> **해결: 소트생략 튜닝(index_desc)**
     - 내부적으로 조건에 해당하는 모든 로우를 액세스한 후 FETCH -> 전체범위 처리 상태
     - 결과 로우를 100건씩 실행/조회하는 경우라면(=**페이징 처리** 하는 경우라면), **부분범위 처리를 통해 성능 개선**이 가능
     - **튜닝 전) 아래 예시는 FETCH 제한 건수가 100을 가정**
     - <img src="/images/2024-10-05-CHECK_LIST_서버최적화(튜닝)/image-20241115205219544.png" alt="image-20241115205219544" style="zoom:80%;" />
     - **튜닝 후) 인덱스 구성 컬럼 참고 -> index_desc로 소트생략 가능**
     - <img src="/images/2024-10-05-CHECK_LIST_서버최적화(튜닝)/image-20241115205439173.png" alt="image-20241115205439173" style="zoom:80%;" /> 
   - 최종 데이터 1건 추출 로직 개선 -> **해결: 소트생략 튜닝(index_desc)**
     - 데이터 1건 추출하는데 데이터를 모두 액세스하고 정렬 해야하는 상황
     - **튜닝 전) 1건 추출을 위해 인덱스 로우504K건, 블록2687건 액세스**
     - <img src="https://github.com/user-attachments/assets/c4a6613b-53b9-4917-837a-bea65bfdc853" alt="image" style="zoom:80%;" />
     - <img src="https://github.com/user-attachments/assets/cda1078a-dd78-40c0-9fe3-4a37127bb5a9" alt="image" style="zoom:80%;" />  
     - **튜닝 후) 앞의 사례처럼 PK 인덱스를 index_desc 힌트로 해결**
     - <img src="https://github.com/user-attachments/assets/469f9e17-55df-49bb-9861-e1287939ab78" alt="image" style="zoom:80%;" /> 
2. **배치 튜닝 사례**
   - SNAPSHOT TOO OLD 에러 해소 -> UNDO 관련 에러
     - 해당 에러는 데이터 손실과는 관련이 없어서 안전한 오류지만, 직면 시 쿼리 진행이 멈춤
     - UNDO 영역은 시스템공용 공간이라서 보통 3시간 경과하면 해당 영역(블록)을 재사용(덮어씌움) 할 수 있음
     - <img src="https://github.com/user-attachments/assets/8c041b2a-74ac-448b-9ad5-f4cd90950e00" alt="image" style="zoom:80%;" /> 
     - **튜닝 전)**
     - <img src="https://github.com/user-attachments/assets/54241d1c-723a-41e0-8b7d-57f67f1ff8bf" alt="image" style="zoom:80%;" /> 
     - **튜닝 후) 참고로 더 다양한 배치 성능 개선 방법들이 존재**
     - <img src="https://github.com/user-attachments/assets/0361d5bd-b512-49ea-8cd1-873b9fa8d6e7" alt="image" style="zoom:80%;" /> 
   - 대량 SQLIO 호출 부하 개선 -> **해결: 메인 쿼리와 서브쿼리를 머지**
     - 메인쿼리 결과 건수 약 65만건 인데, **서브쿼리가 해당 건수 만큼 반복 호출로 3000초** 소요<br>참고: 순수 메인쿼리 수행 시간은 80초, 나머지 2920초는 서브쿼리 호출 시간
     - **튜닝 전) 65만 번의 Call, Fetch...**
     - <img src="https://github.com/user-attachments/assets/cae8e5a4-a1ce-440e-ab06-09e11861fea5" alt="image" style="zoom:80%;" /> 
     - **튜닝 후) 65만 번의 Call, Fetch가 없어짐!**
     - <img src="https://github.com/user-attachments/assets/8a493420-9d07-4761-9c3c-f5cb7183df76" alt="image" style="zoom:80%;" /> 
   - ORA_HASH를 통한 효율적 배치 작업 분할 -> 해시 값 생성 함수
     - 음.. ORA_HASH  를 잘 모르겠음
     - 상황: 배치 처리를 위한 **데이터 분할 로직이 먼저 사용되지 않아서** 데이터 액세스 량이 많고, 인라인뷰 T는 인덱스 만으로 액세스 가능한데 **FULL(T) 힌트**로 항상 전체 테이블 액세스
     - **튜닝 전)**
     - <img src="https://github.com/user-attachments/assets/12e34ffa-502a-432d-81f0-165a046e6f21" alt="image" style="zoom:80%;" />
     -  **튜닝 후) ora_hash조건절을 인라인뷰 T로 옮김 + INDEX_FFS(T) 힌트**
     - <img src="https://github.com/user-attachments/assets/ec69e520-2ad9-40fb-bf45-b58ed8fc2f23" alt="image" style="zoom:80%;" /> 
   - 병렬 전체 스캔을 통한 대량처리 개선 -> **해결: full, parallel, use_hash, pq_distribute 힌트**
     - 상황: 최종 데이터가 100만건 이상인데, 대량 데이터 처리에 **부적합한 인덱스**와 **NL 조인** 사용함
     - **튜닝 전)**
     - <img src="https://github.com/user-attachments/assets/3bb7e949-d9b6-4b4a-a289-9aa6b3cb008a" alt="image" style="zoom:80%;" /> 
     - **튜닝 후) 테이블/파티션을 병렬로 전체 스캔**
     - <img src="https://github.com/user-attachments/assets/123ba34a-bb03-4456-b3d7-6f228ac9ffd5" alt="image" style="zoom:80%;" /> 
</div>
</details>

<details><summary><b>클러스터링 팩터 PDF</b></summary>
<div markdown="1">
- **성능이슈**
  - <img src="https://github.com/user-attachments/assets/36dcd807-f4c2-4605-bf3c-92fa23521bc7" alt="image" style="zoom:80%;" /><br><img src="https://github.com/user-attachments/assets/60a09bb5-65e0-4303-93b0-b1dc87f90eca" alt="image" style="zoom:80%;" /><br><img src="https://github.com/user-attachments/assets/0e5938b1-c826-4f95-a639-2c19b73119bd" alt="image" style="zoom:80%;" /><br><img src="https://github.com/user-attachments/assets/c705cf91-e041-4b4a-a575-cf525ab334fa" alt="image" style="zoom:80%;" />
- **해결방안**
  - <img src="https://github.com/user-attachments/assets/44696412-4ab1-41d8-9d6c-614acd718c4b" alt="image" style="zoom:80%;" /> 
</div>
</details>

<br><br>

### 개념정리) SQLP 공부하며

<details><summary><b>인덱스</b></summary>
<div markdown="1"><br>
#### **1) 인덱스 기본 원리**
**B*Tree 인덱스 구조 (Balanced – 예로 높이 동일)**
- 브랜치 블록의 각 로우는 하위(=자식) 블록의 주소
- 브랜치 블록의 각 로우의 **키 값**은 하위 블록의 **값 범위**
- 리프 블록의 각 로우는 테이블 로우의 주소
- **리프** 블록의 각 로우의 키 값은 **테이블** 로우의 키 값과 일치 **(로우가 1:1)**
**인덱스 탐색은 수직적+수평적 순서**
- 인덱스 리프 노드(수직)의 ROWID를 이용한 테이블 액세스(수평) 때 디스크 읽기 전에 **버퍼캐시 먼저 확인**
  - **인덱스 클러스터링 팩터**: 테이블 전체 로우를 액세스할 때 읽을 것으로 예상되는 **논리적인 블록** 개수를 의미
- `index(col1,col2,col3)` 생성 + `where col1=’A’ and col2 between 111 and 113 and col3 <= 40`
  - 위의 **수평 탐색 시작** 지점은 **‘A,111’** 이다.
  - 만약 **index_asc**이고 `col3 >= 40` 이면 첫 시작 지점은 **‘A,111,40’** 이 될 수 있다.
  - 만약 **index_desc**이고 `col3 <= 40` 이면 **‘A,113,40’** 이 될 수 있다. 
- **인덱스, 테이블 필터링 시점 주의** – 만약, 인덱스 구성이 `A+B+C+D` 라면,
  - `where A=1 and C=2 and B=3` 은 정상인데, `A=1 and C=2 and D=3` 이면 **C=2부터 인덱스 필터링**으로 바뀐다.
  - 인덱스에 없는 컬럼을 조건절에 사용중이면 이는 **테이블 액세스 단계**에서 **필터링** 한다.
  - 인덱스에 있는 컬럼만 조건절에 사용중이여도 select절에 다른 컬럼이 있으면 **테이블 액세스 필요**하다.
**힌트에 따라 인덱스 분류**
- `FULL` - **Table Full Scan**으로 유도
  - 인덱스가 없거나, 전체 테이블 스캔이 더 효율적일 때 사용
  - 멀티블럭I/O 사용
- `INDEX` - Index Scan으로 유도
  - 인덱스를 사용하고, 조건에 맞는 데이터가 적을 때 유리(데이터 중복도가 높으면=선택도가 높으면 효과 없음)
  - **Index Range Scan**은 선두 컬럼이 가공되지 않고 where(조건절)에 필수(반드시 `=`일 필요는 없다)
    - 선두컬럼 주의점:  타입변환, null유무, 컬럼가공, 선두컬럼생략, or사용 등
    - 컬럼가공 예시 - `max(주문번호+1)` -> `max(주문번호)+1` 로 튜닝
    - `or`사용은 `union all`로 변경시 해결 가능
      - `지수구분코드 || 지수업종코드 IN(‘1001’,‘2003’)` 를 or->union all 예시
      - `지수구분코드=‘1’ and 지수업종코드=‘001’ union all ... 지수구분코드=‘2’ and 지수업종코드=‘003’` 로 튜닝
  - **Index Full Scan**은 인덱스 선두 컬럼이 조건절에 없으면 자동 사용(힌트X. 자동선택)
    - 인덱스 트리를 처음 리프 블록부터 끝까지 수평적 탐색으로 검색
  - **Index Unique Scan**은 인덱스 구성 컬럼 모두 ‘=’ 조건일 때 사용 가능(수직적 탐색만 하게 됨)
- `INDEX_DESC` - 인덱스를 역순으로 스캔하도록 유도
  - 기본적으로 인덱스는 오름차순으로 정렬되어 있지만, 내림차순으로 데이터를 조회할 필요가 있을 때 사용
- `INDEX_FFS` - **Index Fast Full Scan**으로 유도
  - 인덱스에 포함된 컬럼만 사용할 때 사용
  - 멀티블럭I/O 사용 -> 인덱스 트리 무시
- `INDEX_SS` - **Index Skip Scan**으로 유도
  - 인덱스 선두 컬럼이 조건절에 없을 때 사용(있어도 부등호 넓은 범위면 효과적)
  - 복합 인덱스의 첫 번째 열이 아닌 두 번째 이후 열을 기준으로 검색할 때 사용
  - 인덱스 선행컬럼 NDV적고(=선택도 높고), 후행컬럼 NDV많을 때(선택도 작을 때) 효과적(=in-list iterator와 동작 유사! **아래 참고**)
  - **아래 그림 둘다** **index range scan** : **왼쪽은** **between**으로 범위 형태, **오른쪽은** **in**으로 ‘=’ 형태(in-list iterator사용모습 -> union all 형태가 됨)
    - **즉, 왼쪽 between에 index skip scan 사용 시 in-list iterator 처럼 효과적으로 동작!**
    - 예로 월별로 100만개 레코드와 판매구분코드 like ‘A%’가 2%정도 데이터일 때 1년은 100만\*12=1200만 건수고 이를 스캔했더니 30000블록을 읽었을 경우 2%는 (30000\*0.02=600) 600+브랜치레벨+알파개 블록이다. 월별은 NDV가 12(1~12월)고, 판매구분코드는 NDV 가 매우 많다. 이때, **인덱스가 월별+판매구분코드**라면 Index Skip Scan이 효과적
  <img src="https://github.com/user-attachments/assets/48241a0f-2a5f-4b16-b2f1-e31a2908dc7d" alt="image"  /> 
- **힌트말고 추가 인덱스(테이블)**
  - **리버스 키 인덱스** – 블록 경합 해소에 도움 (인접된 값을 멀리 분산 시키는 특성)
    - '11112' 와 '11113' 은 연속된 값이지만 적용 시 '21111' 과 '31111' 이 되어 **분산**
    - 대량 데이터가 지속적으로 증가하는 '판매주문' 테이블의 일련번호를 생성했다면<br>최근 데이터는 유사한 값을 가질테고, 해당 부분에 액세스가 집중되는 경합 문제
    - 리버스 키 인덱스 적용 시 이를 해결하여 **랜덤 액세스의 효율이 향상**
    - 물론, 제약이 많으므로 성능 비교는 필수로 해봐야 한다.
    - `CREATE INDEX reverse_index ON order(reverse_key) REVERSE;`
  - **IOT(테이블), 클러스터형 인덱스** – 테이블을 인덱스 구조로 관리(정렬기준 정의)
    - 리프블록에 rowId가 아닌 실제 데이터를 저장 -> 테이블 랜덤 액세스가 발생X
    - 오라클의 IOT는 PK 컬럼으로 정의, SQL Server 클러스터형 인덱스는 PK가 아닌 컬럼으로도 정의 가능
    - `create table index_iot t (a number primary key, b varchar(10)) organization index;`
  - **비트맵 인덱스** - 각 value마다 비트맵을 만들고, leaf node에 <컬럼,비트맵>을 저장
    - rowId가 아닌 각 키 값에 대한 비트맵을 저장 -> 테이블 랜덤 액세스 이점은 없다
    - **Distinct Value가 적을수록 적은 공간을 사용하므로 이점 (분포도가 나쁜 컬럼에 좋음)**
    - **B-tree 인덱스 단점 해결 (OR, NOT, NULL 등)**
    - 하나의 레코드만 변경해도 관련된 모든 비트맵 lock이 걸리므로 **OLTP환경엔 부적합 (DW, OLAP 적합)**
    - 여러 비트맵 인덱스를 동시에 사용 가능
    - **Bitwise Not 연산도 가능(010 -> 101)**
  - **자주 사용되는 데이터를 같은 디스크 위치에 저장? (속도개선)**
    - **클러스터** – 값이 같은 레코드를 **한 블록**에 모아 저장
    - **테이블 파티션** – 파티션 키에 따라 **세그먼트 단위**로 데이터 모아 저장
<br>
#### **2) 테이블 액세스 최소화 + 인덱스 스캔 효율화**
**크게 2가지 (세부적으론 굉장히 많음)**
1. **인덱스 스캔 효율화 튜닝**: 인덱스를 스캔한 양에 비해 얻은 결과 건수가 적으면 비효율적**(인덱스 컬럼순서)**
2. **테이블 액세스 횟수 줄이기**: 테이블을 액세스한 양에 비해 얻은 결과 건수가 적으면 비효율적**(인덱스 컬럼추가)**
- **조인 순서 변경(건수 잘 비교해서)**도 효과없으면 **NL조인 -> 해시조인으로 변경**도 효과적
- 조인 조건절을 인덱스에 추가는 드라이빙쪽은 연관없고 inner쪽만 효과있다. **일반 조건을 우선시하자.**
- **부분범위 처리** 가능하면 **order by, group by쪽** 컬럼을 인덱스 추가 고려하자 (**소트생략** 하자는 것)
  1. group by 인덱스 태우기 주의점
     - GROUP BY 절 컬럼이 인덱스 컬럼의 순서와 위치가 동일해야 함
     - 인덱스 구성 컬럼 중에서 뒷쪽에 있는 컬럼은 GROUP BY 절에 명시되지 않아도 인덱스를 사용할 수 있지만 앞쪽에 있는 컬럼이 GROUP BY 절에 명시되지 않으면 인덱스를 사용할 수 없다.
     - GROUP BY 절 컬럼이 하나라도 인덱스에 없으면 GROUP BY 절은 인덱스를 사용할 수 없다.
  2. order by 인덱스 태우기 주의점
     - group by 주의점과 동일 + 각 컬럼 정렬 옵션(asc, desc)가 인덱스와 맞춰야 함!
**(1)인덱스 컬럼추가, 인덱스 컬럼순서 생각하는 예시**
<img src="https://github.com/user-attachments/assets/f4b71bbe-4af4-4e43-be3a-2f7d4551b1c6" alt="image"  />  
- **index 쪽** `cr=1011, rows=266476`은 **1011개 블록** 스캔했고, **266476건수** 데이터 흭득
- **table 쪽** `cr=266968, rows=1909`은 index건수만큼(=266476번) 테이블 랜덤 액세스 했는데, **265957(266968-1011)개 블록**을 읽었고, **1909건수** 데이터 흭득
  - cr의 경우 자식꺼 다 합친 값을 표현하므로 -1011 를 했음
- **즉, 266476rows->1909rows로 비효율, 인덱스 클러스터링 팩터가 좋지 않다.**
  - 이 경우 **테이블 랜덤 액세스 후 필터링 과정**에서 많은 데이터가 필터링 된 것
  - 265957(266968-1011)개 블록을 읽은것도 충분히 **캐시가 활용되지 못한 것**을 보면 데이터들이 모여있지 않다.<br>-> pr이 27000여 개나 생길 수 밖에 없었고 디스크 블록 I/O 대기현상으로 인해 cpu(처리), elapsed(실행) 시간 차이가 39초나 생기게 되었다.
- **해결방안: 데이터 액세스 최소화를 위해 "인덱스 컬럼추가" 고려**
- **참고) 지금은 index쪽이 cr=1011, rows=266476 로 스캔 효율화가 되게 좋지만 만약 반대라면 (cr=12001, rows=10) 인덱스 스캔 효율화를 위해 "인덱스 컬럼순서" 고려**
**(2)조인 순서 변경 생각하는 예시**
<img src="https://github.com/user-attachments/assets/5f4e39d6-b7d8-4eee-acf6-a9ce4d3bd13e" alt="image"  /> 
<br>
#### **3) 인덱스 설계**
**IN 조건은 '=' or '필터' 로 처리!**
- **IN 조건이 '=' 되려면 IN-LIST Iterator 방식으로 풀려야만 한다.**
- **그렇지 않으면 IN 조건은 "필터" 로 처리한다.**
- **[꼭 이해! 넘 잘 설명해두신 분 글](https://m.blog.naver.com/fbfbf1/223275530812)**
**점이력, 선분이력 테이블 인덱스 설계할 때**
- **점이력**은 **시작일자** 조건만 사용
- **선분이력**은 ‘최근 시점 이력’을 조회는 **종료일자** + ‘오래된 과거 이력’ 조회는 **시작일자** 조건을 우선 설계
**결합인덱스 키 컬럼 선정 중요 순서도**
1. **조건 절에 항상 또는 자주 사용되는 컬럼**
2. **‘=’ 조건을 자주 사용하는 컬럼**
3. **분포도(NDV)가 좋은 컬럼 (데이터량과 구별할 것!)**
   - 선택도: 조건절에 의해 선택될 것으로 예상되는 레코드 **비율**
     - 카디널리티 = 총 로우 수 x 선택도 = 총 로우 수 / NDV
     - 선택도 = 1 / NDV
   - 선택도가 낮은 컬럼이 더 **인덱스 스캔량이 많다.** → 따라서 **선택도가 낮은 컬럼을 인덱스(range scan) 우선 컬럼!**
   - **참고: 그러나 인덱스 선행컬럼 NDV적고, 후행컬럼 NDV많을 때 Index Skip Scan이 효과적(=in-list literator과 유사) 스캔량이 작은게 오히려 skip scan에선 좋은거징**
     - **trunc(sysdate-6) 은 초 단위**라서 굉장히 NDV가 크고, **to_char(sysdate-6,‘yyyymmdd’) 는 일 단위**라서 NDV(=7)가 작으므로 후자의 경우에만 Index Skip Scan이 효과적!
4. **order by절에 주로 사용하는 컬럼 (소트 튜닝 목적!)**
</div>
</details>

<details><summary><b>쿼리변환</b></summary>
<div markdown="1"><br>
#### **2) 쿼리 변환**
**서브쿼리문으로 실행할지 vs 조인의 형태로 변경해서 실행할지**
- **MERGE, UNNEST 공통점은 서브쿼리를 해체하라는 의미 -> "조인 형태"**
  - 실행계획에 메인쿼리가 위, 서브쿼리가 아래에 표시 됨
  - 이때 **M 집합**의 건수가 나올 경우 **semi** **조인** 방식이 적용 된다. (exists)
    - exists 는 semi join
    - not exists 는 anti join
- **NO_MERGE, NO_UNNEST 공통점은 서브쿼리를 해체하지 말라는 의미 -> "필터 형태"**
  - 실행계획에 메인쿼리가 아래, 서브쿼리가 위에 표시 됨
  - NO_MERGE는 from절 서브쿼리**(인라인뷰)**에 사용
  - NO_UNNEST는 인라인뷰 외의 select절, where절 서브쿼리**(스칼라, 중접된)**에 사용
**쿼리변환 중 뷰 처리는 뷰머징을 먼저 고려하지만, 조건절 Pushing도 고려할 수 있다.**
- **뷰 안에서 각 쿼리의 where절에 표현 가능한 조건인지 판단하는게 첫 번째 접근**<br><img src="https://github.com/user-attachments/assets/959cec8b-a8fc-425b-b6b2-173ba41ddc15" alt="image"  /><br><img src="https://github.com/user-attachments/assets/2e68826d-ab07-428f-bbf6-2b6afe7879f4" alt="image"  /> 
- **아래는 또다른 예시인데 조건절 Pushing 사용불가고 뷰머징하면 비효율임**
  - 각 인라인 뷰(e1,e2)가 조인이 아니어서 **조건절 Pushing이 안됨.** (위 예시를 기억해라!)
  - 두 인라인 뷰를 풀어**(뷰머징)** 고객번호 조인을 처리 후 그룹화 하면? 조인 때 이미 Cartesian product 조인이 수행 **(비효율)**
  - **그럼 어떻게 튜닝할까?**
    - e2의 고객번호=10을 e1에 전달하면 건수 대폭 줄이면서 과금_pk인덱스 덕분에 속도도 빠름(효율)
    - 메인쿼리의 수납액>0을 e2에 제공하면 조인 전 미리 필터링이 가능(효율)
    - 현재 인라인 뷰 2개 그대로 사용(뷰머징X)하고 각자 인덱스 사용(효율)
  <img src="https://github.com/user-attachments/assets/c4ff376a-e72a-4e2f-a5b6-f6c7ae8ee6b0" alt="image" style="zoom:80%;" /> 
**공통 표현식 제거**는 같은 조건식을 한번씩만 평가 되도록 튜닝
<br>
**힌트로 살펴보는 쿼리변환**
- `MERGE` - 뷰 머징(=병합) 유도(=뷰를 해체해라)
  - 인라인 뷰를 먼저 실행하는게 아니고 쿼리 블록을 다 풀어서 기존 쿼리와 함께 최적화
  - 실행계획에 View가 없어야 함
- `NO_MERGE` - 뷰 머징 방지
  - 건수를 줄이기 위해 인라인 뷰로 따로 그룹핑 하여 조인을 많이 하는데 **“자동 뷰머징”주의**
  - **자동 뷰머징을 주의하는 이유**는 서브쿼리의 그룹핑이 먼저 적용되는게 아니라 전체 조인이 먼저 된 후 그룹핑을 하게 되니까!
  - 뷰 머징 막는 키워드: rownum, order by, distinct, group func, window func, set연산, connect by
    - group함수는 group by를 말하는게 아님! 오해 금지
- `UNNEST` - 서브쿼리 Unnesting 유도(=서브쿼리를 해체해라)
  - 만약, 다른 조인 방식을 이용하려면 일반 조인문으로 변환이 필요함
  - 스칼라 서브쿼리 Unnesting라면 해당 스칼라 서브쿼리가 해체되고, 일반 join문 처럼 쿼리가 변경
- `NO_UNNEST` - 서브쿼리 Unnesting 방지
  - **자동 UNNEST를 주의하는 이유** 서브쿼리의 그룹핑이 먼저 적용되는게 아니라 조인을 성공한 집합을 group by 하고 출력해서 부분범위 처리가 불가능. -> 이땐 no_unnest 힌트 사용
  - 뷰머징 방지 키워드 처럼 서브쿼리에 rownum 같은 키워드 사용 시 **unnest 방지**
- `PUSH_PRED` - 조건 값을 건건이 뷰 안으로 밀어 넣는 기능
  - `no_merge push_pred` 조합으로 힌트 적용 (뷰 안풀리게 no_merge)
  - 조인조건 Pushdown 은 조인조건 값을 건건이 뷰 안으로 밀어 넣어 줌
  - 조건절 Pushdown 은 일반 조건절을 건건이 뷰 안으로 밀어 넣어 줌
  - 실행계획은 VIEW PUSHED PREDICATE
  - **GROUP BY를 포함한 인라인뷰를 부분범위 처리 가능하게 유도하기 유용**
    - 부분범위 처리 = 소트 생략 튜닝
    - 예로 group by는 당연히 전체범위를 읽어 그룹핑을 할텐데 인덱스를 사용해서 부분범위 처리를 했다. 근데, 이를 인라인뷰에 작성하고 메인쿼리와 조인을 하면 "뷰머징"으로 인해 소트가 발생한다.(아마 인덱스 적용이 안되어서 ) 이를 부분범위 처리 하려면?<br>**답:** `nl조인+no_merge push_pred` -> 뷰를 독립적으로 실행할 때처럼 부분범위 처리가 가능<br>**또다른 답:** `스칼라 서브쿼리 활용`  -> 스칼라 서브쿼리도 메인쿼리 반복만큼 건건이 실행
  - 참고: **조건절** **pullup**은 쿼리블록 안에 조건들을 밖으로 꺼내오고 이를 다시 **pushdown**에 사용할 수 있음
  - **2가지 답 예시**<br><img src="https://github.com/user-attachments/assets/c5244df6-b38c-4bf0-b352-35ab7c088156" alt="image"  />
- `NO_PUSH_PRED` - 조인조건 Pushdown 방지
- `PUSH_SUBQ` -  서브쿼리를 가능한 빨리 필터링하도록 유도 
  - 항상 `push_subq no_unnest` 힌트 (서브쿼리 안풀리게 no_unnest)
  - 보통 서브쿼리 필터가 메인 뒤에 적용되는데 **필터를 앞에서 먼저 적용**
- `NO_PUSH_SUBQ` - 서브쿼리 필터링을 늦게 처리하도록 유도
  - 항상 `no_push_subq no_unnest` 힌트
- `USE_CONCAT` - `OR` 또는 `IN` 조건을 `OR-Expansion`으로 유도(=OR 조건을 분해하여 Union All)
  - 실행계획은 CONCATENATION
- `NO_EXPAND` - `OR` 또는 `IN` 조건을 그대로 처리하도록 `OR-Expansion`을 방지
</div>
</details>

<details><summary><b>배타적ERD, 점이력과 선분이력</b></summary>
<div markdown="1"><br>
**배타적ERD 모습**
<img src="https://github.com/user-attachments/assets/da328388-450b-4a5b-b9c1-6df351540d8e" alt="image" style="zoom: 50%;" /> 
**'작업지시'에 방문예정일자 라는 조회조건 사용 및 union all, outer join**
<img src="https://github.com/user-attachments/assets/97249bdd-82a6-462d-8110-66be83e9da89" alt="image" style="zoom:80%;" /> 
**’개통접수,장애접수‘에 접수일시 조회조건 사용 및 union all 만 활용**
<img src="https://github.com/user-attachments/assets/8e43ced0-a1f1-4a59-a797-94b705a5f192" alt="image" style="zoom:80%;" /> 
**(그림참고)배타적erd**는 상황에 따라 union all(소트생략 불가)이나 outer join을 활용 + nvl2, decode(case) 함수
- ‘작업지시’에 조회조건이 있으면 union all 이나 outer join 활용 -> 두 번째 그림
- ‘개통접수, 장애접수’에 조회조건이 있으면 union all 활용 (조인순서. 즉, **드라이빙도 이걸로** 바꿔야 함) -> 세 번째 그림
- **(그림참고)점이력, 선분이력** 최적화 쿼리 예시
  - **점이력**은 변경시점(시작)만 사용 -> 두 번째 그림
  - **선분이력**은 변경시점(시작) + 종료시점 까지 사용 -> 세 번째 그림
</div>
</details>

**따로 오답노트 정리한 PDF를 참고하자**
