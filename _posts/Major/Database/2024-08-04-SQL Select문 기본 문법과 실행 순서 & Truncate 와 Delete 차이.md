---
title:  "SQL Select문 기본 문법과 실행 순서 & Truncate 와 Delete 차이"
categories : [Database]
tag : [SQL,Select,Oracle,쿼리,그룹함수,실행순서,WHERE,GROUP BY,HAVING]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**SQL Select문의 기본 문법과 실행 순서를 체계적으로 설명하는 가이드로, FROM부터 ORDER BY까지 각 절의 역할과 사용법을 상세히 다룬다. 그리고 Truncate 와 Delete 의 차이를 설명한다.**

<br>

<br>

## SQL Select문의 실행 순서

SQL 쿼리를 작성할 때는 문법적 순서와 실제 실행 순서가 다르다는 점을 이해해야 한다. 오라클 기준으로 실제 실행 순서는 다음과 같다.

```
실행 순서: FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY
```

이 순서를 이해하면 쿼리 작성과 디버깅이 훨씬 수월해진다.

<span style="color:#ff9300">SQL 실행 순서를 이해하는 것은 효율적인 쿼리 작성과 성능 최적화의 기본이다.</span>

<br><br>

### FROM 절

FROM 절은 쿼리에서 가장 먼저 실행되며, 데이터를 가져올 테이블을 지정한다.

```sql
SELECT column_name
FROM table_name;
```

여러 테이블을 조인할 경우 다음과 같이 작성한다:

```sql
SELECT column_name
FROM table1
JOIN table2 ON table1.column = table2.column;
```

💡 **FROM 절에서는 테이블뿐만 아니라 서브쿼리(인라인 뷰)도 사용할 수 있다.**

<br><br>

### WHERE 절

WHERE 절은 FROM 절 다음에 실행되며, 조건에 맞는 행만 필터링한다.

```sql
SELECT column_name
FROM table_name
WHERE condition;
```

WHERE 절에서 자주 사용되는 조건식:

```sql
-- 기본키와 기본키 비교
WHERE employee_id = 100

-- 기본키와 외래키 비교
WHERE department.dept_id = employee.dept_id

-- 컬럼과 값 비교
WHERE last_name = 'Smith'

-- 다중 조건
WHERE salary > 5000 AND department_id = 20
```

⚡ **WHERE 절은 GROUP BY 이전에 실행되므로 집계 함수(SUM, AVG 등)를 WHERE 절에서 사용할 수 없다.**

<br><br>

### GROUP BY 절

GROUP BY 절은 지정된 컬럼을 기준으로 데이터를 그룹화한다.

```sql
SELECT department_id, AVG(salary)
FROM employees
GROUP BY department_id;
```

GROUP BY 사용 시 주의사항:

- SELECT 절에는 GROUP BY에 명시된 컬럼과 집계 함수만 사용 가능
- GROUP BY에 명시된 컬럼 순서대로 그룹화 진행

```sql
-- 다중 컬럼 그룹화
SELECT department_id, job_id, SUM(salary)
FROM employees
GROUP BY department_id, job_id;
```

<span style="color:#ff9300">GROUP BY 절에 명시되지 않은 컬럼을 SELECT 절에 단독으로 사용하면 오류가 발생한다.</span>

<br><br>

### HAVING 절

HAVING 절은 GROUP BY로 그룹화된 결과에 조건을 적용한다.

```sql
SELECT department_id, AVG(salary)
FROM employees
GROUP BY department_id
HAVING AVG(salary) > 8000;
```

HAVING과 WHERE의 차이점:

- WHERE: 개별 행에 대한 필터링 (그룹화 전)
- HAVING: 그룹화된 결과에 대한 필터링 (그룹화 후)

```sql
-- WHERE와 HAVING 함께 사용
SELECT department_id, AVG(salary)
FROM employees
WHERE hire_date > TO_DATE('01-JAN-2005', 'DD-MON-YYYY')
GROUP BY department_id
HAVING AVG(salary) > 8000;
```

💡 **HAVING 절에서는 집계 함수를 사용할 수 있지만, WHERE 절에서는 사용할 수 없다.**

<br><br>

### SELECT 절

SELECT 절은 쿼리 결과에 포함될 컬럼을 지정한다.

```sql
SELECT column1, column2, ...
FROM table_name;
```

SELECT 절에서 사용할 수 있는 요소:

<br>

#### **DISTINCT 키워드**

중복된 행을 제거한다.

```sql
SELECT DISTINCT department_id
FROM employees;
```

<br>

#### **별칭(Alias)**

컬럼이나 표현식에 별칭을 부여한다.

```sql
SELECT first_name AS name, salary * 12 AS annual_salary
FROM employees;
```

<br>

#### 와일드카드(*)

모든 컬럼을 선택한다.

```sql
SELECT *
FROM employees;
```

<br>

#### 그룹 함수

데이터를 집계하는 함수를 사용한다.

```sql
SELECT COUNT(*), SUM(salary), AVG(salary), MAX(salary), MIN(salary)
FROM employees;
```

<span style="color:#ff9300">SELECT 절은 쿼리에서 마지막에서 두 번째로 실행되므로, SELECT 절에서 정의한 별칭은 ORDER BY 절에서만 사용할 수 있다.</span>

<br><br>

### ORDER BY 절

ORDER BY 절은 쿼리 결과를 정렬한다.

```sql
SELECT column1, column2
FROM table_name
ORDER BY column1 [ASC|DESC], column2 [ASC|DESC];
```

ORDER BY 사용 예시:

```sql
-- 오름차순 정렬(기본값)
SELECT employee_id, last_name, salary
FROM employees
ORDER BY salary ASC;

-- 내림차순 정렬
SELECT employee_id, last_name, salary
FROM employees
ORDER BY salary DESC;

-- 다중 컬럼 정렬
SELECT department_id, last_name, salary
FROM employees
ORDER BY department_id ASC, salary DESC;
```

⚡ **ORDER BY 절은 쿼리에서 가장 마지막에 실행되므로, SELECT 절에서 정의한 별칭을 사용할 수 있다.**

<br>

<br>

## DDL, DML, TCL, DCL, DQL

**데이터 정의어 (DDL : Data Definition Language) – Auto Commit**

- Create: DB 생성 
- Alter: DB 변경 
- Drop: DB 삭제 
- Rename: DB 이름변경 
- Truncate: 데이블을 모든 행을 삭제

**데이터 조작어 (DML: Data Manipulation Language)**

- Insert: 데이터 입력 
- Update: 데이터 수정 
- Delete: 데이터 삭제

**트랜잭션 처리어 (TCL: Transation Control Language)**

- Commit: 트랜잭션 작업 정상 처리 
- RollBack: 트랜잭션 작업 취소 
- Savepoint: 임시 작업구간 저장

**데이터 제어어 (DCL : Data Control Language)**

- Grant: DB에 대한 일련의 권한 부여 
- Revolk: DB에 대한 일련의 권한 취소

**데이터 질의어 (DQL : Data Query Language)**

- Select

<br>

<br>

## Truncate 와 Delete

Truncate 는 DDL, Delete 는 DML

- Truncate:  테이블을 모든 행을 삭제하는 명령어, 롤백이 안됨, **자동 커밋** 

- Delete:  롤백이 가능, 부분 삭제 가능, 삭제를 위한 공간이 필요 , 처리 속도가 느림.

<br>

<br>

## 전체 SELECT문 예제

다음은 모든 절을 포함한 완전한 SELECT문 예제이다:

```sql
SELECT department_id, job_id, COUNT(*) AS employee_count, AVG(salary) AS avg_salary
FROM employees
WHERE hire_date > TO_DATE('01-JAN-2005', 'DD-MON-YYYY')
GROUP BY department_id, job_id
HAVING COUNT(*) > 1
ORDER BY department_id ASC, avg_salary DESC;
```

이 쿼리는:

1. employees 테이블에서 데이터를 가져온다(FROM)
2. 2005년 1월 1일 이후 입사한 직원만 필터링한다(WHERE)
3. 부서와 직무별로 그룹화한다(GROUP BY)
4. 각 그룹에 2명 이상의 직원이 있는 경우만 선택한다(HAVING)
5. 부서 ID, 직무 ID, 직원 수, 평균 급여를 표시한다(SELECT)
6. 부서 ID 오름차순, 평균 급여 내림차순으로 정렬한다(ORDER BY)

<span style="color:#777777">SQL Select문의 각 절을 이해하고 실행 순서를 숙지하면 더 효율적이고 정확한 쿼리를 작성할 수 있다. 특히 그룹 함수와 관련된 GROUP BY와 HAVING 절의 사용법을 정확히 이해하는 것이 중요하다.</span>
