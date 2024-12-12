---
title: "IllegalQueryOperatioinException - Spring"
categories : [Err]
tag : [spring, 스프링, 스프링부트]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..

---



`org.hibernate.query.IllegalQueryOperatioinException: Result type given for a non-SELECT Query...`

**해당 오류는 작성한 쿼리문에 문제가 있으므로 이를 추적하여 해결해야 한다.**

DELETE 쿼리문은 반환값이 없지만, 필자는 `createQuery()` 함수의 매개변수에 `Task.class`를 적용하여 반환된 결과를 Task(엔티티)에 자동 매핑하려고 시도하면서 문제가 발생했다.

매개변수 `Task.class` 를 지우고 실행시 해결완료!

