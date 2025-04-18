---
title:  "트랜잭션 격리 수준(Isolation Level)의 이해와 이상현상 분석"
categories : [Database]
tag : [데이터베이스,트랜잭션,격리수준,IsolationLevel,DirtyRead,NonRepeatableRead,PhantomRead,MySQL]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**트랜잭션 격리 수준(Isolation Level)은 동시에 실행되는 트랜잭션들 간의 상호작용을 제어하여 데이터 일관성과 무결성을 보장하는 메커니즘으로, 각 수준에 따라 허용되는 이상현상(Dirty Read, Non-repeatable Read, Phantom Read)이 다르다.**

<br>

<br>

## 트랜잭션 격리 수준(Isolation Level)의 개념

트랜잭션 격리 수준은 ACID 속성 중 'I'(Isolation, 격리성)를 구현하는 방법으로, 여러 트랜잭션이 동시에 실행될 때 한 트랜잭션의 작업이 다른 트랜잭션에 어떻게 영향을 미치는지 제어한다. 데이터베이스 시스템은 일관성과 성능 사이의 균형을 맞추기 위해 다양한 격리 수준을 제공한다.

**트랜잭션 격리 수준은 다음과 같은 이유로 중요하다:**

- 데이터 일관성 보장: 동시 실행되는 트랜잭션 간의 데이터 일관성 유지
- 성능 최적화: 적절한 격리 수준 선택으로 시스템 성능 향상
- 이상현상 제어: 다양한 데이터 불일치 문제(이상현상) 방지

<span style="color:#ff9300">격리 수준은 데이터 일관성과 시스템 성능 사이의 균형을 조절하는 중요한 메커니즘으로, 애플리케이션의 요구사항에 맞게 적절히 선택해야 한다.</span>

<br>

<br>

## 트랜잭션 동시 실행 시 발생하는 이상현상

동시에 여러 트랜잭션이 실행될 때 발생할 수 있는 세 가지 주요 이상현상이 있다.

<br><br>

### Dirty Read (더티 리드)

Dirty Read는 한 트랜잭션이 아직 커밋되지 않은 다른 트랜잭션의 변경사항을 읽는 현상이다.

**예시 시나리오:**

- 트랜잭션1: x += y (y값을 x에 더함)
- 트랜잭션2: y = 70 (y값을 70으로 변경)
- 두 트랜잭션이 동시에 실행되는 상황에서, 트랜잭션1이 트랜잭션2에서 변경한 y=70 값을 읽어 x에 더함
- 이후 트랜잭션2가 롤백되어 y가 원래 값(20)으로 돌아감
- 결과적으로 트랜잭션1은 실제로는 존재하지 않는(커밋되지 않은) y=70 값을 사용하여 x를 계산했으므로 x 값이 부정확해짐

```
트랜잭션1                트랜잭션2
-----------             -----------
읽기: y=20
                       y=70으로 변경(커밋 전)
읽기: y=70 (더티 리드)
x += 70
                       롤백: y=20으로 복원
커밋: 잘못된 x 값 저장
```

💡 **Dirty Read는 데이터 무결성에 심각한 위협이 될 수 있으며, 트랜잭션이 롤백된 변경사항을 기반으로 의사결정이 이루어질 수 있다.**

<br><br>

### Non-repeatable Read (반복 불가능한 읽기)

Non-repeatable Read는 한 트랜잭션 내에서 같은 데이터를 두 번 이상 읽을 때 그 값이 다른 현상이다.

**예시 시나리오:**

- 트랜잭션1: x 값을 두 번 읽음
- 트랜잭션2: x += 40 (x 값에 40을 더함)
- 트랜잭션1이 처음에 x=10을 읽은 후, 트랜잭션2가 x를 50으로 변경하고 커밋
- 트랜잭션1이 다시 x를 읽으면 x=50을 얻게 됨
- 동일한 트랜잭션 내에서 같은 데이터에 대해 다른 결과를 얻게 되어 일관성이 깨짐

```
트랜잭션1                트랜잭션2
-----------             -----------
읽기: x=10
                       x += 40 (x=50)
                       커밋
읽기: x=50 (값이 변경됨)
```

⚡ **Non-repeatable Read는 트랜잭션 내에서 데이터 일관성을 해치며, 같은 쿼리를 여러 번 실행할 때 예측 가능한 결과를 얻을 수 없게 한다.**

<br><br>

### Phantom Read (유령 읽기)

Phantom Read는 한 트랜잭션이 같은 조건으로 데이터를 두 번 조회할 때, 첫 번째에는 없던 데이터가 두 번째 조회에서 나타나는 현상이다.

**예시 시나리오:**

- 트랜잭션1: v=10인 데이터를 두 번 조회
- 트랜잭션2: v=10인 새 데이터 삽입
- 트랜잭션1의 첫 번째 조회에서는 하나의 결과만 반환
- 트랜잭션2가 새 데이터를 삽입하고 커밋
- 트랜잭션1의 두 번째 조회에서는 두 개의 결과가 반환됨

```
text트랜잭션1                    트랜잭션2
-----------                 -----------
SELECT * WHERE v=10 (결과 1개)
                           INSERT v=10 값을 가진 새 행
                           커밋
SELECT * WHERE v=10 (결과 2개)
```

<span style="color:#ff9300">Phantom Read는 집계 함수나 데이터 분석 쿼리에서 특히 문제가 될 수 있으며, 트랜잭션 내에서 일관된 데이터 세트를 보장하지 못한다.</span>

<br>

<br>

## 트랜잭션 격리 수준의 종류

SQL 표준에서는 네 가지 트랜잭션 격리 수준을 정의하며, 각 수준은 허용하는 이상현상이 다르다.

<br><br>

### Read Uncommitted (레벨 0)

Read Uncommitted는 가장 낮은 격리 수준으로, 커밋되지 않은 데이터까지 읽을 수 있다.

- **허용하는 이상현상**: Dirty Read, Non-repeatable Read, Phantom Read
- **특징**:
  - 어떤 트랜잭션의 변경 내용이 COMMIT이나 ROLLBACK과 상관없이 다른 트랜잭션에서 보임
  - 데이터 정합성에 문제가 많아 실제로 사용하는 경우가 거의 없음
  - 성능은 가장 좋음

```sql
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
```

💡 **Read Uncommitted는 데이터 일관성보다 성능이 중요한 대량 데이터 처리나 통계 작업에서 제한적으로 사용될 수 있다.**

<br>Mbr>

### Read Committed (레벨 1)

Read Committed는 커밋된 데이터만 읽을 수 있는 격리 수준이다.

- **허용하는 이상현상**: Non-repeatable Read, Phantom Read
- **방지하는 이상현상**: Dirty Read
- **특징**:
  - 대부분의 데이터베이스 시스템의 기본 격리 수준(Oracle, SQL Server 등)
  - 트랜잭션이 커밋되어 확정된 데이터만 다른 트랜잭션에서 볼 수 있음
  - 하나의 트랜잭션 내에서 같은 SELECT 문을 실행해도 결과가 달라질 수 있음

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

⚡ **Read Committed는 일반적인 웹 애플리케이션에서 많이 사용되며, Dirty Read를 방지하면서도 적절한 성능을 제공한다.**

<br><br>

### Repeatable Read (레벨 2)

Repeatable Read는 트랜잭션 내에서 같은 데이터를 여러 번 읽어도 동일한 결과를 보장하는 격리 수준이다.

- **허용하는 이상현상**: Phantom Read (일반적으로, 단 MySQL InnoDB에서는 예외)
- **방지하는 이상현상**: Dirty Read, Non-repeatable Read
- **특징**:
  - MySQL InnoDB의 기본 격리 수준
  - 트랜잭션이 시작된 시점의 데이터를 스냅샷으로 유지
  - 다른 트랜잭션에서 데이터를 변경해도 트랜잭션 내에서는 변경 전 데이터를 읽음

```sql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
```

<span style="color:#ff9300">MySQL의 InnoDB 스토리지 엔진은 Repeatable Read 수준에서도 특별한 방식으로 Phantom Read를 방지한다. 이는 MVCC(Multi-Version Concurrency Control)와 넥스트-키 락(Next-Key Lock)을 통해 구현된다.</span>

<br><br>

### Serializable (레벨 3)

Serializable은 가장 높은 격리 수준으로, 모든 이상현상을 방지한다.

- **허용하는 이상현상**: 없음
- **방지하는 이상현상**: Dirty Read, Non-repeatable Read, Phantom Read
- **특징**:
  - 트랜잭션을 완전히 분리하여 순차적으로 실행한 것과 같은 결과 보장
  - 읽기 작업에도 공유 잠금(shared lock)을 획득하여 동시성 제한
  - 가장 안전하지만 성능이 가장 낮음
  - 교착 상태(deadlock)가 발생할 가능성이 높음

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

💡 **Serializable은 금융 거래나 중요한 데이터 처리와 같이 데이터 정확성이 절대적으로 중요한 상황에서 사용된다.**

<br>

<br>

## 격리 수준별 이상현상 비교 표

각 격리 수준이 허용하는 이상현상을 비교하면 다음과 같다:

| 격리 수준        | Dirty Read | Non-repeatable Read | Phantom Read |
| ---------------- | ---------- | ------------------- | ------------ |
| Read Uncommitted | 허용 (O)   | 허용 (O)            | 허용 (O)     |
| Read Committed   | 방지 (X)   | 허용 (O)            | 허용 (O)     |
| Repeatable Read  | 방지 (X)   | 방지 (X)            | 허용 (O)*    |
| Serializable     | 방지 (X)   | 방지 (X)            | 방지 (X)     |

*주의: MySQL InnoDB에서는 Repeatable Read 수준에서도 Phantom Read를 방지한다.

<span style="color:#ff9300">격리 수준이 높아질수록 데이터 일관성은 향상되지만, 동시성과 성능은 저하된다. 따라서 애플리케이션의 요구사항에 맞는 적절한 격리 수준을 선택하는 것이 중요하다.</span>

<br>

<br>

### MySQL InnoDB의 특별한 구현

MySQL의 InnoDB 스토리지 엔진은 기본 격리 수준으로 Repeatable Read를 사용하며, 표준과 달리 이 수준에서도 Phantom Read를 방지하는 특별한 구현을 가지고 있다.

<br>

#### MVCC(Multi-Version Concurrency Control)

InnoDB는 MVCC를 사용하여 각 트랜잭션이 시작된 시점의 데이터 스냅샷을 유지한다.

- 트랜잭션이 시작될 때 일관된 읽기(consistent read) 스냅샷을 생성
- 다른 트랜잭션이 데이터를 변경해도 원래 스냅샷을 기반으로 쿼리 실행
- 이를 통해 Non-repeatable Read 방지

```
MySQL 공식 문서에 따르면:
"Consistent reads within the same transaction read the snapshot established by the first read."
```

<br>

#### 넥스트-키 락(Next-Key Lock)

InnoDB는 넥스트-키 락을 사용하여 Phantom Read를 방지한다.

- 인덱스 레코드에 대한 레코드 락(record lock)과 인덱스 레코드 앞의 간격에 대한 갭 락(gap lock)을 결합
- 이를 통해 다른 트랜잭션이 특정 범위에 새 레코드를 삽입하는 것을 방지
- 결과적으로 Repeatable Read 수준에서도 Phantom Read 방지 가능

⚡ **MySQL InnoDB의 이러한 특별한 구현은 높은 데이터 일관성과 상대적으로 좋은 성능의 균형을 제공한다.**

<br>

<br>

## 격리 수준 선택 시 고려사항

적절한 트랜잭션 격리 수준을 선택할 때 고려해야 할 사항은 다음과 같다:

1. **데이터 일관성 요구사항**: 애플리케이션이 필요로 하는 데이터 일관성 수준
2. **성능 요구사항**: 처리량과 응답 시간에 대한 요구사항
3. **동시성 요구사항**: 동시에 처리해야 하는 트랜잭션의 수
4. **비즈니스 중요도**: 데이터 정확성이 비즈니스에 미치는 영향
5. **데이터베이스 시스템**: 각 DBMS별 격리 수준 구현 방식의 차이

```sql
-- 애플리케이션의 특정 부분에 대해 격리 수준 설정 예시
START TRANSACTION;
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
-- SQL 문 실행
COMMIT;
```

<span style="color:#777777">트랜잭션 격리 수준은 데이터베이스 시스템의 동시성 제어 메커니즘의 핵심 요소로, 데이터 일관성과 성능 사이의 균형을 조절한다. 각 애플리케이션의 요구사항에 맞는 적절한 격리 수준을 선택함으로써 데이터 무결성을 보장하면서도 최적의 성능을 달성할 수 있다.</span>
