---
title:  "JPQL vs SQL (JPA와 MyBatis)"
categories : Spring
tag : [JPA,MyBatis,ORM,영속성관리,데이터접근기술]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**JPA와 MyBatis의 핵심 개념, 아키텍처 차이, 장단점, 성능 특성 및 적합한 사용 시나리오를 비교 분석하여 프로젝트에 맞는 데이터 접근 기술을 선택할 수 있도록 안내하는 종합 가이드입니다.**

<br>

<br>

## JPQL(JPA-ORM) vs SQL(MyBatis-SQL Mapper)

**MyBatis는 "스프링 DB 관련 추가 지식" -> "MyBatis" 파트를 보자**
=> JPA와 MyBatis 개발은 확연히 다르단걸 인지하자. 그차이도 "MyBatis"파트를 보자

> [JPA vs Mybatis 참고 문헌](https://www.elancer.co.kr/blog/detail/231?seq=231)

ORM과 SQL Mapper 는 둘 다 객체와 SQL 매핑을 도와줘서 유사하긴 하다.<br>

- ORM은 SQL을 작성 할 필요 없을 정도로 더 많은 걸 지원한다. (JPQL을 작성 하긴 함)

- SQL Mapper는 Java 코드와 SQL(XML) 문을 아예 분리 + 동적쿼리를 세밀하게 지원 해준다.

<br><br>

### JPA - 영속성 컨텍스트

**중요!! - 영속성 컨텍스트**

- **4가지 상태 : 영속, 비영속, 준영속, 삭제**
  - 비영속(transient)은 말 그대로 persist(=영속) 하지 않은 상태 **-> 영속성과 아예 무관**
  - 준영속(detached)은 영속성 컨텍스트에서 **분리(em.detach()) 된 상태**
  - 삭제(removed)는 말 그대로 영속성에서 **삭제 된 상태**

* **flush : 영속성 내용 DB 반영 (쿼리)**

  * **(1)em.createQuery** 같이 쿼리문 작성하는 것들이 **flush 자동 발생**
  * **(2)트랜잭션**은 당연히 **flush 자동 발생**
    * 목적: 쌓인 영속성 내용을 DB에 먼저 반영해줘야 jpql로 조회할 때 데이터 **불일치 방지**
    * 단, 이건 내가 테스트해서 확인한거긴 한데 **jpql 에 member 를 사용중이고 이미 member가 영속성(변경내역)에 존재할 때 flush 자동발생. 그게 아니면 굳이 영속성 내용을 먼저 반영할 필요가 없다보니 자동발생 안함.**  
      => 예로 `select m from Member m` 를 사용하기전 Member 영속성(변경할 내역)을 먼저 체크 후 flush 동반 유무 결정

* **persist : 영속성 등록**

  * 우선 em(엔티티매니저) 사용하려면 **트랜잭션이 필수!!**

  * save(=persist) : **(1)영속성 등록**, (2)쓰기지연 SQL저장소에 insert쿼리, (3)순차번호 구하기위해 select전송

    * 쓰기지연 SQL저장소의 insert 는 **flush 때 전송**

    * **기본 키 자동 생성**의 경우, 순차번호 구하는 **select 는 바로 전송**

      <details><summary><b>generatedKey 보충 설명 : nextval 쿼리</b></summary>
      <div markdown="1"><br>
      persist때 generatedKey 등록한 엔티티는 자동 nextval 시퀀스 쿼리 발생해 줌<br>
      - 단, identity 방식 사용중이면 insert쿼리 시점에 DB에서 키를 생성<br>
        - `IDENTITY` 전략을 사용한다고 해서 **JPA의 쓰기 지연 기능을 전혀 쓸 수 없는 것은 아닙니다**. 엔티티에 기본 키가 필요할 때만 즉각적으로 `flush()`가 발생하고, 나머지 쿼리들은 여전히 지연될 수 있습니다.<br>
        - `SEQUENCE` 전략은 기본 키를 미리 할당받기 때문에 **쓰기 지연과 더 원활**하게 작동할 수 있습니다.<br>
      - **H2, PostgreSQL, Oracle** 등에서는 기본적으로 `GenerationType.SEQUENCE` 를 사용<br>
        - 오라클12c 부턴 시퀀스 뿐만 아니라 identity 문법도 제공<br>
      - **MySQL, SQL Server** 같은 DB에서는 기본적으로 `GenerationType.IDENTITY`가 사용<br>
      </div>
      </details>


      <details><summary><b>save() 보충 설명: JPA와 Spring Data JPA</b></summary>
      <div markdown="1"><br>
      `JPA`는 직접 구현 필요 (본인은 persist만 사용), `Spring Data Jpa`는 save()함수 제공(persist+merge 형태)<br>
      * isNew() 함수로 엔티티가 이미 있는지 체크하는데 isNew()는 엔티티의 Id가 null인지 체크한다. 따라서 @GeneratedValue를 사용하지 않은 id의 경우엔 임의로 id를 넣어 줄텐데 이러면 여기서 문제가 발생한다. **-> Spring Data Jpa의 save() 함수 로직임.**<br>
        * 왜 문제? id가 null이어야 persist(영속성등록) 할테니까<br>
      * id가 null이 아니니까 persist가 아닌 merge 가 발생하는데 DB에 해당 엔티티를 찾아내려고 select쿼리가 나가는 문제이다.(없을텐데 불필요하게 나가는..)<br>
        - 해결법은 [이 분꺼 참고](https://velog.io/@pjh612/JPA-Spring-Data-JPA의-save의-동작-과정)<br>
        - 참고) merge동작은 이미 있는 엔티티(준영속상태)를 영속성상태로 바꿔줘서 업뎃이 가능하게 만드는 것. 이 과정에서 DB 조회도 발생! (해당 엔티티 찾으려고)
      </div>
      </details>

  * em.find : **(1)영속성 등록**, (2)찾는 id 인 값 select전송

    * **(1)영속성 등록이란** 엔티티를 find() 할 때 **바로 영속성에 있다면 거기서 해당 주소**를 가져와 줌. => db에 쏘는 **쿼리 발생 안함.**
    * **영속성에 없다면, select 는 바로 전송** -> flush 아님.

  * em.remove : **(1)영속성 해제**, (2)쓰기지연 SQL저장소에 delete쿼리, (3)삭제할 아이템 구하기 위해 select전송

    * 쓰기지연 SQL저장소의 delete 는 **flush 때 전송**

    * **영속성에 없다면,** 삭제할 아이템 구하는 **select 는 바로 전송**

      <details><summary><b>remove 예시 코드</b></summary>
      <div markdown="1">
      ```java
      // given
      Task findTask = taskService.findOne(taskId); // Task 찾기
      log.info("findTask : {}", findTask);
      // when
      //1. 영속성 컨텍스트에서 삭제 된걸 확인했기 때문에 아래 findOne 에서 db 조회까지 안가고 종료
      taskService.remove(findTask); // 영속성 컨텍스트에서 상태4개 중 "삭제 상태"로 운영 됨. (taskStatus 랑 cascade 이므로 지연로딩이였어서 여기서 taskStatus 조회가 발생)
      findTask = taskService.findOne(taskId); // 로그 없음. (삭제되어서) -> 영속성에서 이미 삭제된 걸 확인. 이해 안되면 정리한 em.find 확인
      //
      //2. 원래 em.find() 는 영속성 컨텍스트에 엔티티 없으면 db 조회까지 하기 때문에 그 부분을 로그로 보려고 함.
      em.flush();  // 삭제된 엔티티를 DB에 반영 (강제 플러시)
      em.clear();  // 영속성 컨텍스트 초기화
      //
      // 이제 다시 조회를 시도하면 DB에서 조회
      findTask = taskService.findOne(taskId); // 삭제된 엔티티 조회 시도. 쿼리 발생! (null이어야 함)
      ```
      </div>
      </details>

- **FK오류 방지**

  * id를 @GeneratedValue 로 지정했기 때문에 em.persist를 해야 id를 생성 해준다.
  * 따라서 em.persist를 안한 엔티티를 외래키로 쓸때 id가 없어서 FK오류가 발생!!
  * **추가 Update 쿼리(더티체킹) 방지**
    * FK오류를 방지하면서 코드를 짰으면 사실 문제없을텐데, 그게아니라면??  
      => 추가 update 쿼리가 나갈 수 있다.
    * 예로 em.persist를 하지않은 엔티티를 먼저 외래키로 사용한 후에 persist한 경우  
      나중에 flush 할때 더티체킹(id가 생겼으니)으로 update 쿼리가 추가 생성된다.
  * **따라서 그냥 FK오류 방지를 준수하면서 꼭 작성!**

- **Update 참고 (더티체킹,벌크 추천)**: `em.merge()` 로 준영속을 영속성 만들면 데이터 수정시 "더티체킹(flush시점)" 발생  
  하지만 `em.merge()` 보단 `em.find()` 로 영속성 가져와 데이터 수정을 더 추천

  - **update 예시 -> em.find() 영속성 활용 방식**

    <details><summary><b>예시 코드</b></summary>
    <div markdown="1">
    ```java
    // entity part
    // 준속성 엔티티 -> 영속성 엔티티 에 사용
    public void change(String name, int price, int stockQuantity) {
      this.name = name;
      this.price = price;
      this.stockQuantity = stockQuantity;
    }
    // service part
    @Transactional
    public void updateItem(Long id, UpdateItemDto itemDto){
      Item item = itemRepository.findOne(id); // 영속성 엔티티
      item.change(itemDto.getName(), itemDto.getPrice(), itemDto.getStockQuantity()); // 준속성 엔티티(itemDto) -> 영속성 엔티티
    }
    // controller part
    public String updateItem(@PathVariable Long itemId, @ModelAttribute("form") BookForm form){
      UpdateItemDto itemDto = new UpdateItemDto(form.getName(), form.getPrice(), form.getStockQuantity());
      itemService.updateItem(itemId, itemDto);
      return "redirect:/items"; // 위에서 "상품 목록" 매핑한 부분으로 이동
    }
    ```
    </div>
    </details>

  - 단, 변화가 넘 많으면 **"벌크연산 추천"** -> 간단함. (우리가 잘 쓰는 쿼리문일 뿐)  
    -> 예: `UPDATE Task t SET t.status = :status WHERE t.dueDate < :currentDate`

  - **벌크 연산 -> 여러 데이터 한번에 "수정, 삭제" 연산**

    * JPA 는 보통 실시간 연산에 치우쳐저 있는데, 대표적인 예가 "더티 체킹"
      * 100개 데이터가 변경되었으면 100개의 Update 쿼리가 나가게 되는 문제
      * **이런건 "벌크 연산" 으로 해결하자**
    * **올바른 사용법**
      * **벌크 연산을 먼저 실행**
      * **벌크 연산 수행 후 영속성 컨텍스트 초기화 -> em.clear()**
        * 더 이상 더티 체킹 일어나지 않게 하기 위함!! (중요)

<br><br>

### JPQL + 페이징(limit,offset)

#### JPQL (distinct, 연관관계, sql vs jpql)

<details><summary><b>반환 방식(TypeQuery, Query) -> `query.getResultList()`</b></summary>
<div markdown="1">
* TypeQuery: 반환 타입이 명확할 때 사용<br>
  * **일반적(자주 사용!)** : `List<Member> findMembers = em.createQuery("select m from Member m", Member.class)`<br>
  * **Dto** : `List<MemberDTO> result = em.createQuery("select new jpql.MemberDTO(m.username, m.age) from Member m", MemberDTO.class)`<br>
    * **QueryDSL 사용 시 패키지 명(jpql.)까지 없앨 수 있음 (자주 사용!)**<br>
* Query: 반환 타입이 명확하지 않을 때 사용<br>
  * `List<Query> findMembers = em.createQuery("select m from Member m")`<br>
  * 이땐 굳이 반환타입 `Member.class`를 명시할 필요가 없음<br>
* **query.getResultList(): 결과가 하나 이상일 때, 리스트 반환** **-> 자주사용!! (1개 여도!)**<br>
* query.getSingleResult(): 결과가 정확히 하나, 단일 객체 반환
</div>
</details>


<details><summary><b>경로 표현식 3가지(.을 찍어 "탐색") -> 상태, 연관 필드(단일 연관, 컬렉션 연관)  </b></summary>
<div markdown="1">
* **상태 필드(탐색X)**: 단순히 값을 저장하기 위한 필드 (ex: `m.username`)<br>
* **단일 값 연관 필드(탐색O)**: @ManyTo**One**, @OneTo**One**, 대상이 엔티티(ex: `m.team`)<br>
  * **"즉시 로딩" 이 기본값**이므로 꼭 "지연 로딩" + "join fetch" 사용 권장 <br>
  * 탐색가능 예시: `select m.team.id from Member m` <br>
* **컬렉션 값 연관 필드(탐색X)**: @OneTo**Many**, @ManyTo**Many**, 대상이 컬렉션(ex: `m.orders`) <br>
  * 컬렉션은 "Object"객체 여러개로 생각하면 되며, **"지연 로딩" 이 기본값**<br>
* **주의: 일반 join 필요 시 "묵시적 내부 조인" 이 아닌 "명시적 조인" 을 사용할 것**<br>
  * `select m.team from Member m` 이나 `select m.orders from Member m` 처럼 사용 가능한데, 이 경우 **묵시적 내부 조인이 발생(자동 inner join)** **-> 매우 비권장!!**<br>
  * 쿼리 튜닝하기에 매우 힘듦
</div>
</details>


<details><summary><b>엔티티 직접 사용 -> count(m) == count(m.id) 로 자동 SQL 변형!</b></summary>
<div markdown="1">
* **JPQL에서 엔티티를 직접 사용하면 SQL에서 해당 엔티티의 "기본 키" 값을 사용**<br>
  * (JPQL) : select count**(m)** from Member m <br>
  * (SQL) : select count**(m.id)** as cnt from Member m <br>
* **참고: JPQL은 SQL의 m.* 조회를 m 으로 동일하게 가능.** <br>
  * 예: JPQL의 `select m from Member m` 와 SQL의 `select m.* from Member m` 동일!!
</div>
</details>


<details><summary><b>연관관계(1:1,1:N,N:1,N:N)의 DB관점(SQL) vs JPA관점(JPQL)</b></summary>
<div markdown="1"><br>
참고로 **SQL은 영속성 컨텍스트 자체를 사용X**, 따라서 SQL의 join은 "객체"가 아닌 "row"만 반환할 뿐!
- **DB관점**은 1:N, N:1 이든 1쪽은 데이터 중복 발생. 1쪽이 N만큼 생성되는건 조인이라면 자명. 따라서 **결과(row)는 같고**, **관점을 양방향**으로 봤을 뿐이다. 
- **JPA 컬렉션(1:N) 관점**은 객체가 생성이 되는데 해당 객체 주소가 동일 해버리는 문제 발생할 수 있다.**(1:1, N:1은 발생X)**
  - **그럼 DB관점은** N:N이 아니면 항상 튜플은 중복이 아니니까 걱정안해도 되자나? 왜 distinct를 쓸 때가 있었을까?
  - 만약 `order:orderItem=1:N` 에서 **order쪽 컬럼만 사용**하면?? 중복 데이터니까 distinct나 group by로 중복제거 하여 해결하는 것!!
- **직관적 이해를 위해 각각의 관점 출력 예시를 보여주겠다.**
  - **DB관점 조인 (Order:OrderItem=1:N 관계, OrderItem:Order=N:1 관계)**<br>
  ![image](https://github.com/user-attachments/assets/c087ebc7-3e42-43c7-aa4b-c0507eb76f05) 
  - **JPA 컬렉션 관점 조인 (Order:OrderItem=1:N 관계)**  → jpql의 distinct 미사용.<br>
  객체다보니 같은 ORDER_ID인 COUNT 2개가 한번에 묶여있는 형태를 가지다보니 조인의 결과가 완전 동일한 객체 2개가 생성이 되는것.<br>
  ![image](https://github.com/user-attachments/assets/3cfd43eb-cd18-4960-838b-f7e4545a0cf4) 
</div>
</details>


<details><summary><b>DB관점(SQL) JOIN문과 JPA관점(JPQL) JOIN, FETCH JOIN문</b></summary>
<div markdown="1"><br>
참고로 **“조인문”**은 두 테이블 필드 정보가 필요하거나, 한 개 테이블 필드 정보만 출력하더라도 외래키로 이어진 두 관계를 활용해서 연관있는 정보를 조회해야 할 때 사용하는거.
- **DB관점 JOIN**은 위에서 설명했듯이 **영속성 컨텍스트 자체를 사용X**, 따라서 SQL의 join은 “객체(엔티티)”가 아닌 **“row”만 반환할 뿐!**
- **JPA관점 JOIN**은 **FETCH JOIN과 차이**가 있다. **JOIN은** **"지연로딩+연관된 엔티티 자동 포함X"** 라는 점! 
  - 지연로딩으로 N+1 문제 해결하고자 평범히 `select o, oi from o랑 oi 조인;` 한다면 해결은 가능!!
  - 다만, order랑 orderItem 각각을 개별적으로 반환! -> order이 `List<OrderItem>` 를 가진다고 해도 이 관계에 자동 주입이 안된다는 말!! **(fetch join은 자동 포함!!)**
  - **JPA관점 FETCH JOIN**은 애초에 JPA만 존재하는 문법이다. 해당 문법은 **"지연로딩->즉시로딩(N+1방지), 연관된 엔티티 자동 포함"**(`List<OrderItem>`) 한다.
</div>
</details>


<details><summary><b>distinct의 DB관점(SQL) vs JPA관점(JPQL)</b></summary>
<div markdown="1"><br>
**SQL에서 distinct**는 전체 row가 모두 똑같아야 중복이 제거<br>
**JPA에서 distinct**는 위 기능 + pk(식별자 , id값)가 같다면 중복을 제거 (영속성 컨텍스트 상 엔티티 같은 주소들 제거 해버리는 거지)<br>
중복은 앞서 정리한 JPA 출력사진 참고
</div>
</details>


<details><summary><b>fetch join(즉시로딩) -> `XToOne`는 바로 페치조인O, `XToMany`는 페치조인O+distinct / 만약 페이징 필요하면? 페치조인X + BatchSize (일반 Select!, 글로벌로 100정도 깔아두고 개발ㄲㄲ)</b></summary>
<div markdown="1">
* 주의: **페치 조인**은 **객체 그래프 유지**할 때 사용 시 효과적인 반면, 여러 테이블을 조인해서 엔티티가 가진 모양이 아닌 전혀 다른 결과를 내야 하면, 페치 조인 보다는 **일반 조인**을 사용해서 필요한 데이터들만 조회해서 **DTO**로 반환하는 것이 효과적.<br>
* `XToOne` 문제없음, `XToMany` 는 Distinct 함께 사용(컬렉션이라 중복 문제!! -> 중복은 앞서 정리한 JPA 출력사진 참고)<br>
  * 근데, **하이버네이트6 부터 Distinct 자동 적용**해주는듯. 아직 테스트 못해봤다.<br>
* **페치 조인 대상에는 별칭X** - 유일하게 연속으로 join 가져오는 경우에만 사용<br>
* **둘 이상의 컬렉션은 페치 조인 사용하지 말 것**<br>
* 페치조인X + BatchSize 는 페이징 정리 부분 참고
</div>
</details>



<details><summary><b>Named 쿼리 -> Spring Data JPA의 interface에서 간단히 쿼리 커스텀!!</b></summary>
<div markdown="1">
* **실무에서는 Spring Data JPA 를 사용하는데 @Query("select...") 문법이 바로 "Named 쿼리"** **-> 원하는 쿼리문 바로 작성가능한 편리성**<br>
  * JPQL 예: `@Query("SELECT m FROM Member m")`<br>
  * Natvie SQL 예: `@Query(value="SELECT m FROM Member m", nativeQuery=true)`<br>
* **참고: 물론 일반 JPA로 구현로직 추가해서 인터페이스 상속 추가해서 사용도 좋다~** <br>
  * 본인은 이 방식으로 JPA+Spring Data JPA 함께 쓰는 편
</div>
</details>

**JAP 사용 때 동적 쿼리**는 **Querydsl** 을 권장

<br>

#### 페이징 (+최적화 예시)

**페이징**

* x번째 row를 구하라 하면 **페이징(sql: limit, offset)** 으로 간단히 구할 수 있다.

  * 다만, offset은 설정한 범위까지 데이터 전부 scan한다는 단점이 있다.
  * 이를 극복하기 위해 보통 인덱스(소트생략)를 활용해서 부분범위 처리를 가능하게 한다.
  * 따라서 페이징은 **"부분범위 처리에서 좋은 효과"**를 가진다.

* **JPA에서** limit, offset을 대신할 간단한 **문법**을 제공한다.

  * `setFirstResult(int startPosition)` : 조회 시작 위치 (0부터 시작)
  * `setMaxResults(int maxResult)` : 조회할 데이터 수

* **만약 조인문에서 페이징을 원할 때는??** -> 컬렉션 조인을 제외하고 문제 없다.<br>참고로 **Order:OrderItem=1:N** 테이블 사용 가정

  * **"관리자가 최신순 주문상품 내역"** 보려고 한다. 이때는 주문상품 기준 최신순으로 나타내도 충분하다. N:1로 페이징하면 될거니까 어려움이 없다.

  * **"관리자가 최신순 주문 내역"**을 보려고 한다. 이때는 주문이 중복없이 나열되고, 해당 주문에 해당되는 주문상품들이 함께 포함되어 나열되어야 한다. 1:N(컬렉션)로 페이징해야 하니까 어려움이 생긴다.<br>왜? 데이터 뻥튀기 때문. Order가 중복 생성되므로 동일한 OrderId 때문에 순서를 정할 수가 없으니까!

    * ToOne 관계는 fetch join으로 쿼리 수를 줄이고, 지금같은 컬렉션(ToMany)는 **default_batch_fetch_size로 최적화** 한다. JPA가 IN절로 size만큼 자동 처리하여 해결!!

    <details><summary><b>예시 - Batch Size (In절)</b></summary>
    <div markdown="1"><br>
    **Order, OrderItem, Item** 연관 관계를 가질 때 총 **3개 쿼리문**으로 IN절 활용해서 중복없고 1+N문제도 없이 해결이 가능한 예시.<br>
    <br>
    아래 잘 보면, Order를 우선 페이징 적용하고 나서 이와 연관된 엔티티들을 따로 쿼리문 더 날려서(In절 포함) 데이터를 가져와 해결!
    <div markdown="1">
    ```java
    select order0_.order_id       as order_id1_6_0_,
           member1_.member_id     as member_i1_4_1_,
           delivery2_.delivery_id as delivery1_2_2_,
           order0_.delivery_id    as delivery4_6_0_,
           order0_.member_id      as member_i5_6_0_,
           order0_.order_date     as order_da2_6_0_,
           order0_.status         as status3_6_0_,
           member1_.city          as city2_4_1_,
           member1_.street        as street3_4_1_,
           member1_.zipcode       as zipcode4_4_1_,
           member1_.name          as name5_4_1_,
           delivery2_.city        as city2_2_2_,
           delivery2_.street      as street3_2_2_,
           delivery2_.zipcode     as zipcode4_2_2_,
           delivery2_.status      as status5_2_2_
    from orders order0_
             inner join
         member member1_ on order0_.member_id = member1_.member_id
             inner join
         --     페이징이 적용된다.
             delivery delivery2_ on order0_.delivery_id = delivery2_.delivery_id limit ?
    offset ?
    //
    select orderitems0_.order_id      as order_id5_5_1_,
           orderitems0_.order_item_id as order_it1_5_1_,
           orderitems0_.order_item_id as order_it1_5_0_,
           orderitems0_.count         as count2_5_0_,
           orderitems0_.item_id       as item_id4_5_0_,
           orderitems0_.order_id      as order_id5_5_0_,
           orderitems0_.order_price   as order_pr3_5_0_
    from order_item orderitems0_
    -- in 절로 땡겨온다.
    where orderitems0_.order_id in (
                                    ?, ?
        )
    //
    select item0_.item_id        as item_id2_3_0_,
           item0_.name           as name3_3_0_,
           item0_.price          as price4_3_0_,
           item0_.stock_quantity as stock_qu5_3_0_,
           item0_.artist         as artist6_3_0_,
           item0_.etc            as etc7_3_0_,
           item0_.author         as author8_3_0_,
           item0_.isbn           as isbn9_3_0_,
           item0_.actor          as actor10_3_0_,
           item0_.director       as directo11_3_0_,
           item0_.dtype          as dtype1_3_0_
    from item item0_
    -- in 절로 땡겨온다.
    where item0_.item_id in (
                             ?, ?
        )
    ```
    </div>
    </div>
    </details>


    <details><summary><b>IN 절은 어떻게 접근?</b></summary>
    <div markdown="1"><br>
    * **IN 조건은 '=' or '필터' 로 처리!**
      - **IN 조건이 '=' 되려면 IN-LIST Iterator 방식으로 풀려야만 한다.**
        - 컬럼이 인덱스를 타야 함 (옵티마이저가 자동으로 in-list iterator 동작)
      - **그렇지 않으면 IN 조건은 "필터" 로 처리한다.**
        - 컬럼이 인덱스를 타지 않아야 함 -> '='아닌 조건절로 인해 뒤 컬럼 전부 필터링도 포함
      - **[꼭 이해! 넘 잘 설명해두신 분 글](https://m.blog.naver.com/fbfbf1/223275530812)**
    * **IN 조건과 OR 조건 구분 -> OR-Expansion 과 IN-List Iterator구분**
      - `in`과 `or`은 다른 표현 방식일 뿐 동일 -> 원래 or 연산자는 Index Range Scan 불가능!!<br>그러나, 둘다 UNION ALL 방식으로 작성시 Index Range Scan 가능!!
      - **옵티마이저는 인덱스가 있으면 자동으로 쿼리변환**: in, or 연산자를 예전엔(9i) OR-Expansion방식을 사용했고, 현재 in 연산자는 IN-List Iterator방식을 사용(10g)
        - `OR-Expansion`은 union all 로 변환하여 Index Range Scan을 사용하게 하는 효과
        - `IN-List Iterator`는 in-list 갯수만큼 Index Range Scan을 반복하는 것이며, union all 로 변환한 것과 같은 효과를 얻을 수 있다.
    </div>
    </details>

<br>

**페이징 + 캐시 예시 2개**

- 굉장히 좋은 참고 문서: [페이지네이션 최적화 - Offset 문제 가져간 이유](https://taegyunwoo.github.io/tech/Tech_DBPagination) 참고!

- 여기서 기억에 남은 말: **가장 먼저 서브쿼리를 통해서 커버링 인덱스로 페이징을 진행합니다. 그리고 그 결과와 기존 테이블을 조인시켜서 ‘인덱스에 포함되지 않은 칼럼’을 가져옵니다.**

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

### 엔티티 조회 권장 순서 - 필수!

**엔티티 조회 권장 순서**

1. **엔티티 조회 방식**으로 우선접근 : **지연로딩+DTO변환 방식 추천**

   1. **일반 엔티티(ToOne)** 최적화 : 페치조인으로 쿼리 수를 최적화
   2. **컬렉션(ToMany)** 최적화
      1. 페이징 필요O : `hibernate.default_batch_fetch_size` , `@BatchSize` 로 최적화
      2. 페이징 필요X : 페치조인 사용

2. 엔티티 조회 방식으로 해결이 안되면 DTO 조회 방식 사용

   <details><summary><b>엔티티 조회(DTO변환 방식) vs DTO직접 조회</b></summary>
   <div markdown="1">
   - 요즘 스펙상 **재사용성이 좋은 엔티티 조회(DTO변환)**가 낫다고 생각
   - 그래도 너무 쿼리 성능이 좋지 않다면 DTO직접 조회로 넘어가자. **(확실히 select절의 필드 수가 줄어듦, 물론 엔티티가 아니니까 일반 join문을 사용해야 함)**
   </div>
   </details>

3. DTO 조회 방식으로 해결이 안되면 NativeSQL or 스프링 JdbcTemplate or QueryDSL 등

<br>

**엔티티 조회 권장 순서 자세히**

1. **엔티티 조회 -> JPA가 최적화를 많이 제공**

   - 엔티티 조회 후 DTO로 변환해서 반환하는게 훨씬 안전. (**엔티티 외부노출 방지**) -> **DTO 변환 방식**

     - **요청, 응답 둘다 DTO**사용해주자. → +**Valid** 적용하기도 좋다. 
     - 참고: “DTO 직접 조회”는 select절에 바로 DTO조회하는거 의미.

     <details><summary><b>페치조인으로 쿼리 수 최적화. -> N+1방지</b></summary>
     <div markdown="1"><br>
     연관된 엔티티를 함께 조회하는 방식으로 지연로딩을 즉시로딩! **(N+1방지)**
     - **LAZY 강제 초기화**까지 해줘야 메모리에 null로 저장되는걸 방지
       - 1:N, N:1, 1:1 이든 조인을 할 때 `select new com.example.dto.OrderItemDto(oi.id, i.name, oi.orderPrice, oi.count)` 처럼 **“DTO직접조회”**가 아니라 `select oi from OrderItem oi` 처럼 **“엔티티 조회”**하게 되면 나중에 응답 DTO로 변환할 때 꼭 LAZY 강제 초기화를 해줘야 null 방지한다는 의미
       - 내부적으로 연관 엔티티를(조인한) 필드로 가지고 있을텐데 그 부분을 활성화 해야 **“영속성 등록”으로 메모리에 가지니까!!**
     - **distinct유무는?** 1:1, N:1 은 distinct가 필요없지만 1:N 은 필요!!
       - 설명 생략 (이미 정리했으니까)
     - **작성 코드 형태 예시**
       - `("select m from Member m " + "join fetch m.character c " + "where m.id = :memberId", Member.class)`
         - 이 정보만 봐도 얼마나 **객체지향적**인지 알 수 있다. **(1:1조인중)**
           - **Member 내에 필드로 Character를 가지고 있는**데, 그 흐름 그대로 jpql을 작성가능 하다.
           - **반환된 해당 Member로 Character 정보를 사용가능!** `member.getCharacter().getId() 등..`
         - 실제 SQL이라면 **select c from character c, member m where m.id = :memberId** 이런 느낌이었을거다. -> from절에서 두개 자동 inner조인 하고 where절에서 필터링 되는..
           - 물론 나라면 **select c from character c, (select \* from member where m.id = :memberId)** 이런 형태로 튜닝할거 같다.
             - member가 굉장히 데이터 많았다면 비효율이여서 수정했다. 물론, 조인 자체는 문제없다! memberId 덕분에 튜닝 포인트가 생긴것일 뿐이지! <br>→ memberId 없었으면 애초에 이렇게 전부 조인하는게 맞으니까!
           - **실제 SQL**뭐가나가는지 로그 찍어보니 예상대로다.<br>![image](https://github.com/user-attachments/assets/54fc3a64-7022-4770-ad76-925a0f8f6173) 
       - `"select distinct l from Lists l" + " join fetch l.tasks t" + " join fetch t.taskStatus ts" + " where l.member.id = :memberId"`
         - 앞서 언급한것처럼 **1:N은 distinct 권장! (1:N조인중)**
         - **다만, 엔티티 조회란걸 생각!**
       - `"select t from Task t" + " join fetch t.lists l" + " where t.id = :taskId and" + " l.member.id = :memberId"`
         - 앞서 언급한것처럼 N:1은 중복 걱정 노! **(N:1조인중)**
         - **다만, 엔티티 조회란걸 생각!**
       - `Character character = characterService.findCharacterWithMember(memberId);`<br>`List<Follow> follows = followService.findAllWithFollowing(character.getId());`
         - API로직인데 Follow 조회하려고 캐릭터를 먼저 조회해서 총 2번의 쿼리가 나간다. 당연히 1개로 합칠 수 있다. (튜닝POINT)
           - 기존 jpql: `"select f from Follow f" + " where f.character.id = :characterId"`
           - `:characterId` 부분만 서브쿼리로 삽입하면 된다. **=(select id from character where memberId = :memberId)**
         - 근데, API로직에서 character쪽을 또 활용한다면?? 이런걸 생각하면 상황에 따라 맞게 개발해야한다.
           - 본인 생각엔 굳이 서브쿼리 넣으면서까지 하지말고, **“객체지향적”** 답게 **기존 API로직처럼 사용을 우선** 하는게 좋다고 생각! **쿼리 2개→1개 줄어봤자 너무 미미**한 성능 차이니까
     </div>
     </details>


     <details><summary><b>컬렉션(1:N) 페이징은 페치조인이 불가능. 따라서 Batch활용 ㄱ (=JPA에서 Batch는 In절 만듦) 
     → In절 만드는 원리는 아래에 DTO 컬렉션(1:N) 조회 코드방식 임!</b></summary>
     <div markdown="1"><br>
     [Batch 방법 참고 - In절 쿼리 출력문 참고](https://velog.io/@guns95/컬렉션-FetchJoin의-문제점과-한계-돌파BetchSize)
     - **컬렉션 페이징은 페치조인이 불가능한 이유는 1:N**은 1쪽이 N개 복사된다고 봐야하니까 **순서를 정확히 알 수가 없는 문제 (물론 조인은 어느 관계든 복제 ㅇㅇ)**
       - ex: orderId=1 과 연관있는 orderItemId가 3개라면 orderId=1이 3개가 복제된다. 그럼 orderId 기준으론 순서를 정할 수 없으니까!
       - **물론, orderItemId 기준**으론 순서 정할 수 있음. **양방향 관점(원래1:N 관점에서 N:1로)**에선 순서가 이렇게 정해지니까 페이징 가능하단겨~
       - **다만, 여기선 비지니스 상 orderId기준을 원하고 있는거임.**
     - 따라서 **ToOne에 페치조인한 쿼리에만 페이징 적용ㄲ**. 나머진 Batch를 통해 **JPA가 IN절에서 연관엔티티(지연로딩)를 Batch Size만큼 1번의 추가 쿼리에 가져와서 해결하자**
       - in절은 or절과 거의 동일하게 동작하던걸로 기억한다. 특히 SQL 튜닝관점에서는 OR Expansion, IN-List Iterator 로 union all 형태로 동작 시킨다.(아마도? 까먹음ㅋ) 수직적 탐색이 그만큼 발생할거고.
       - **어쨋든 in절에 연관엔티티(ex:orderItemId)들을 나열해서 1번만에 추가 쿼리(1+N) 없이 가져오는 좋은 해결방안이다!!**
     - 물론, 1:N 페치조인시 **중복제거(그룹핑, distinct)**하면 페이징이 가능할거다. 다만, 너무 비효율! **FULL SCAN** 해야하니까!
       - 단순 정렬문제면 서브쿼리로 먼저구하고 + 인덱스 활용하면 효과적이지만, **중복제거는 인덱스를 쓴다해도 FULL SCAN**일거다. (물론, UIQUE INDEX 사용 시 빠르다지만 전체 컬럼에 쓸것도 아니잖아?)
     - **이것도 별로일때 DTO직접 조회로 해결도 해볼 수 있는것!**
     </div>
     </details>

2. **DTO직접 조회 -> 원하는 컬럼 지정하는 일반 SQL과 유사**

   - **fetch join은 절대 불가!**(DTO는 영속성관리 엔티티X) → **join 사용!**

   <details><summary><b>DTO직접 조회 - select new com.example.dto.OrderDto(컬럼)</b></summary>
   <div markdown="1"><br>
   이런다고 엔티티 조회에서의 응답DTO는 안 만들어도 되나? 라는 바보같은 생각은X!!<br>
   그 DTO가 여기서 select절에 쓰이고 있는거다.
   </div>
   </details>


   <details><summary><b>DTO 컬렉션(1:N) 조회는 IN절을 직접 활용! → (1+N해결!)<br>JPA의 Batch처럼 유사한 동작원리. (참고: JPA Batch Size를 설정하면 Lazy 로딩된 연관 엔티티를 해당 Size 만큼 IN절 활용해 가져옴)</b></summary>
   <div markdown="1"><br>
   아래 결과는 쿼리2개로 개선 한방 쿼리는?? → 플랫 조회 방식으로!
   <div markdown="1">
   ```java
   // dto보면 컬럼명 oi.order.id 기준이 되는 중. Order:OrderItem:Item=1:N:1 관계인 상태.
   // orderId는 매개변수로 입력 받은 값이다. orderId가 N개이면 1+N이 발생할거다.
   private List<OrderItemDto> fun(Long orderId) {...}
   "select new com.example.dto.OrderItemDto(oi.order.id, i.name, oi.orderPrice, oi.count) " +
       "from OrderItem oi " +
       "join oi.item i " +
       "where oi.order.id = :orderId"
   //
   // 1+N 해결법은?? in활용 -> 쿼리 2개 발생 (findOrders랑 아래 jpql)
   // orderIds는 findOrders()로 구한 값
   private List<OrderItemDto> fun() {...}
   List<OrderItemDto> result = findOrders();
   List<Long> orderIds = result.stream().map...
   "select new com.example.dto.OrderItemDto(oi.order.id, i.name, oi.orderPrice, oi.count) " +
       "from OrderItem oi " +
       "join oi.item i " +
       "where oi.order.id " +
       "in :orderIds"
   result.forEach(o -> o.setOrderItems(...)); // orderItem 넣기
   return result;
   ```
   </div>
   </div>
   </details>


   <details><summary><b>플랫 조회는 말 그대로 JOIN 결과를 전부 조회 후 앱단에서 모양을 커스텀한다. → 한방쿼리! (1개쿼리)</b></summary>
   <div markdown="1">
   - 우리가 잘 아는 일반 SQL의 join 결과는 아래처럼 중복이 있다. (참고로 1:N 조인 모습 사진!) → 보통은 그룹핑해서 해결하면 된다. 여기서도 마찬가지로 **Order쪽 그룹핑**으로 해결
   - Order:OrderItem=1:N 이고 Order(1)쪽 중복 모습(order_id)<br>
     ![image](https://github.com/user-attachments/assets/180282b1-203a-4198-ad1e-49baf25ba5ff)<br>
   - (1)위 결과처럼 가져오게끔 일반적인 join문 쓰면 됨 ㅇㅇ. -> **"쿼리1개"**
   - (2)위 그림에서 원하는 필드들 가져다 사용하자
   - (3)핵심은 여기서 orderId 필드 사용해서 기준으로 쓸거면 **그룹핑을(orderId 기준)** 먹여서 **"중복해결"**
   - 개인적으로 후처리가 넘 많고, **그룹핑 먹이는건 뭐.. 성능이 좋진 않겠지.(FULL SCAN)**
       - 물론, 엔티티 조회가 아니라 DTO직접 조회 방식 사용할때의 최적화 방안이니까!! 필요할때 꼭 사용!
   </div>
   </details>

<br>

<details><summary><b>참고용) 엔티티 조회 에러 해결</b></summary>
<div markdown="1">
* **엔티티를 외부에 노출하면서 발생하는 문제들이라서 사실상 이부분을 알 필요는 없다.**
* @JsonIgnore : **양방향 무한 반복의 문제**를 해결
  * 하지만 지양하고 **DTO** 방식으로 해결을 지향
* Hibernate5Module을 @Bean 등록 까지 해주면 **Lazy 문제**도 해결 
  * 하지만 이 또한 지양하고 **LAZY 강제 초기화**로 다 해결
</div>
</details>

<br>

<br>
