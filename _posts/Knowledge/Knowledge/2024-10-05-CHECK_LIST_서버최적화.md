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

**SQLP 자격증 준비하면서 경험을 정리 -> 자세한 원리 내용보단 어떤식으로 튜닝할지 내용 정리할 목적 (체크리스트 만드는 느낌)**

**쿼리튜닝 방법은 정말 많다.**

* **[더 빠른 SQL 쿼리를 위한 21가지 데이터베이스 튜닝 규칙](https://www.itworld.co.kr/tags/2665/SQL/105792?page=0,0)**
* **[추가적인 튜닝 규칙](https://velog.io/@gillog/SQL-%ED%8A%9C%EB%8B%9D#%EC%B6%94%EA%B0%80%EC%A0%81%EC%9D%B8-%ED%8A%9C%EB%8B%9D-%EA%B7%9C%EC%B9%99)**
* **[DB전문가의 TIP - Youtube](https://youtu.be/qY-nOOX_Smc?si=28sjP31btwGLlcDM)**
* **SQLP 관련 서적 : 친절한 SQL 튜닝, SQL 전문가 가이드, SQL 자격검정 실전문제(aka 노랭이), 국가공인 SQLP 자격검정 핵심노트 1,2, 오라클 성능 고도화 원리와 해법 1,2**
  * 국가공인 SQLP 자격검정 핵심노트가 문제풀이로 이해하기 좋아서 많이 참고
  * 오라클 성능 고도화 원리와 해법이 정말 많은 원리들을 담아 이해하기 좋다.

<br>

**"전체범위 처리", "부분범위 처리"** -> "가공이 있냐 없냐로 구분하자!"

* **좀 포괄적인 개념이다. Index도 포함 할 수 있고,, 기본적으로 인지해야할 부분이다.**

* 전체범위 처리 예시 : `select * from 테이블 order by asc`

  * order by, count함수 등 가공이 있다.

  - Full Range Scan과 다를바 없다.

* 부분범위 처리 예시 : `select * from 테이블`

  - 딱 봐도 데이터 관점에서는 가공이 없다. 바로 출력하면 된다.
  - where절이 있으면 실패지만 Index를 탈 수 있다면 그 또한 부분범위다. order by도 사실 Index 타면 부분범위로 볼 수 있다. -> 가공이 없어지니까.

* 참고 : **[전체vs부분범위 처리](https://kjw1313.tistory.com/m/96)**

Not 조건의 중요성 - “아니다”란 조건이 있으면 맞다는 조건을 찾을것

UPDATE 대신 CASE 문 사용, 배치 모드로 Delete, Update 작업

OR 보다는 AND 사용, distinct 는 가급적 사용X (내부적으로 정렬연산 발생하기 때문)

가급적 where 조건에는 Index 컬럼 모두 사용 -> 가급적 "=" 연산 사용(LIKE 같은것은 효율X)

**추가정보) Index 사용 주의점** - 참고 문헌:[인덱스 사용](https://jie0025.tistory.com/509)

1. 인덱스는 **열 단위에 생성**
2. 인덱스는 **WHERE 절에서 사용되는 열에 생성**
3. WHERE 절에 사용되는 열이라도 자주 사용해야 가치가 있음
4. 데이터 중복도가 높은 열에는 인덱스를 만들어도 효과가 없음 (선택도가 높은 경우는 효과 없음)
5. **외래키를 설정한 열**에는 **자동으로 외래키 인덱스가 생성**됨
6. **조인에 자주 사용되는 열**에는 인덱스를 생성하는 것이 좋음
7. **데이터 변경(삽입, 수정, 삭제) 작업이 얼마나 자주 일어나는지 고려**해야 함
8. 단일 테이블에 인덱스가 많으면 속도가 느려질 수있다. (**테이블당 4~5개 권장**)
9. **클러스터형 인덱스는 테이블당 하나만** 생성할 수 있음
10. 테이블에 클러스터형 인덱스가 아예 없는 것이 좋은 경우도 있음
11. 사용하지 않는 인덱스는 제거
12. 복합 인덱스는 **WHERE절에서 OR조건이 아니라 AND 조건일 때** 사용하는 것이 좋음
13. [복합 인덱스의 컬럼 순서 결정법](https://khdscor.tistory.com/51)
