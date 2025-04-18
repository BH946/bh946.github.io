---
title:  "SQL에서의 NULL과 VIEW 완벽 이해하기"
categories : [Database]
tag : [SQL,NULL,VIEW,데이터베이스,가상테이블,단순뷰,복합뷰]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
---



**SQL에서 NULL은 미확정 또는 알 수 없는 값을 의미하며, VIEW는 물리적 테이블에 근거한 논리적인 가상 테이블로 보안 강화와 접근 단순화를 위해 사용된다.**

<br>

<br>

## NULL의 의미와 특성

NULL은 데이터베이스에서 특별한 의미를 가진 값으로, 단순히 비어있는 상태가 아니라 더 복잡한 개념을 나타낸다.

<br><br>

### NULL이 아닌 것

NULL에 대한 오해를 먼저 정리하자면:

- **0이 아님**: NULL은 숫자 0과 다르다. 0은 명확한 값이지만 NULL은 값 자체가 없는 상태다.
- **빈공간이 아님**: 빈 문자열('')이나 공백(' ')과는 다른 개념이다. 이들은 모두 특정 값이 있는 상태다.

<br><br>

### NULL의 실제 의미

NULL의 진짜 의미는 다음과 같다:

- **미확정, 알 수 없는 값**: 데이터가 존재할 수 있지만 현재 알려지지 않았거나 적용되지 않는 상태를 의미한다.
- **알 수는 없지만 값은 존재함**: 예를 들어, 아직 결정되지 않은 출생일이나 알려지지 않은 전화번호 등이 있다.
- **? 또는 ∞(무한대)**: 질문 표시나 무한대처럼 정의할 수 없는 상태를 나타낸다.

<span style="color:#ff9300">NULL은 '값이 없음'이 아니라 '알 수 없는 값'을 의미하므로, 데이터의 부재와 불확실성을 표현하는 중요한 개념이다.</span>

<br><br>

### NULL의 연산 특성

NULL의 가장 중요한 특성은 연산 관련 제약이다:

- **연산 불가능**: NULL과의 산술 연산(+, -, *, /)은 항상 NULL을 반환한다.

  ```sql
  SELECT 10 + NULL; -- 결과: NULL
  ```

- **할당 불가능**: 일반적인 비교 연산자로 NULL을 할당하거나 비교할 수 없다.

- **비교 불가능**: 등호(=)나 부등호(<, >)로 NULL을 비교할 수 없다. NULL과 NULL의 비교도 불가능하다.

  ```sql
  SELECT * FROM employees WHERE salary = NULL; -- 잘못된 방법
  SELECT * FROM employees WHERE salary IS NULL; -- 올바른 방법
  ```

💡 **NULL을 확인할 때는 반드시 IS NULL 또는 IS NOT NULL 연산자를 사용해야 한다.**

<br>

<br>

## VIEW의 의미와 활용

VIEW는 데이터베이스에서 중요한 논리적 객체로, 실제 데이터를 저장하지 않고 쿼리 결과를 가상 테이블 형태로 제공한다.

VIEW는 물리적 테이블에 근거한 논리적인 **가상 테이블**이다. 실제로 데이터를 저장하지 않고, 기존 테이블의 데이터를 특정 방식으로 보여주는 SQL 쿼리의 저장 형태다.

```sql
CREATE VIEW employee_view AS
SELECT employee_id, first_name, last_name, department_id
FROM employees
WHERE department_id = 10;
```

⚡ **VIEW는 데이터 자체가 아닌 SQL 쿼리만 저장하므로 추가적인 저장 공간을 거의 차지하지 않는다.**

<br><br>

### VIEW 사용 이유

VIEW를 사용하는 주요 이유는 다음과 같다:

1. **보안 강화**:
   - 민감한 데이터를 숨기고 필요한 정보만 제공할 수 있다.
   - 사용자별로 접근 권한을 다르게 설정할 수 있다.
2. **접근 단순화**:
   - 복잡한 쿼리를 단순화하여 사용자가 쉽게 데이터에 접근할 수 있게 한다.
   - 자주 사용하는 복잡한 JOIN이나 서브쿼리를 VIEW로 만들어 재사용성을 높인다.

<span style="color:#ff9300">VIEW는 데이터베이스 설계에서 데이터 추상화와 보안을 강화하는 중요한 도구로, 복잡한 쿼리를 단순화하고 사용자에게 필요한 데이터만 제공할 수 있다.</span>

<br><br>

### VIEW의 종류 비교

VIEW는 크게 단순 뷰와 복합 뷰로 나눌 수 있으며, 각각의 특성과 제한사항이 다르다.

| 단순 뷰                               | 복합 뷰                              |
| ------------------------------------- | ------------------------------------ |
| 하나의 테이블에서 생성                | 여러 개의 테이블에서 생성(JOIN 사용) |
| 그룹함수(SUM, AVG 등) 사용 불가능     | 그룹함수 사용 가능                   |
| DISTINCT 사용 불가능                  | DISTINCT 사용 가능                   |
| DML(INSERT, UPDATE, DELETE) 사용 가능 | DML 사용 불가능 또는 제한적          |

<br><br>

### 단순 뷰 예시

```sql
CREATE VIEW simple_view AS
SELECT employee_id, first_name, last_name
FROM employees
WHERE department_id = 20;
```

단순 뷰는 하나의 테이블만 참조하므로 일반적으로 원본 테이블에 대한 DML 작업이 가능하다.

<br><br>

## 복합 뷰 예시

```sql
CREATE VIEW complex_view AS
SELECT e.employee_id, e.first_name, d.department_name
FROM employees e
JOIN departments d ON e.department_id = d.department_id
WHERE e.salary > 5000;
```

복합 뷰는 여러 테이블을 JOIN하거나 그룹 함수를 사용하므로 일반적으로 DML 작업이 제한된다.

💡 **VIEW를 통한 DML 작업은 WITH CHECK OPTION을 사용하여 제약조건을 설정할 수 있다.**

<br>

<br>

## NULL과 VIEW 활용 시 주의사항

**NULL 처리 시 주의사항**

1. **NULL 비교**: 항상 IS NULL 또는 IS NOT NULL 연산자를 사용한다.

2. **NULL 함수**: COALESCE() 또는 NVL() 함수를 사용하여 NULL을 다른 값으로 대체할 수 있다.

   ```sql
   SELECT COALESCE(commission_pct, 0) FROM employees;
   ```
   
3. **집계 함수**: SUM, AVG 등의 집계 함수는 NULL 값을 자동으로 제외하고 계산한다.

<br>

**VIEW 사용 시 주의사항**

1. **성능**: 복잡한 VIEW는 성능 저하를 일으킬 수 있으므로 적절히 사용해야 한다.
2. **갱신 가능성**: 모든 VIEW가 갱신 가능한 것은 아니므로 DML 작업 전 제약사항을 확인해야 한다.
3. **의존성**: 기본 테이블이 변경되면 VIEW도 영향을 받을 수 있다.

<span style="color:#777777">SQL에서 NULL과 VIEW의 개념을 정확히 이해하고 적절히 활용하면 데이터베이스 설계와 쿼리 작성이 더욱 효율적이고 안전해진다. NULL은 단순한 빈 값이 아닌 '알 수 없는 값'으로 특별한 처리가 필요하며, VIEW는 데이터 접근을 단순화하고 보안을 강화하는 강력한 도구다.</span>
