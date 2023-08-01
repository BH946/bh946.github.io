---
title: "[Error] Spring-IllegalQueryOperatioinException"
categories : [Knowledge]
tag : [spring, 스프링, 스프링부트]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..

---



# Error

**`org.hibernate.query.IllegalQueryOperatioinException: Result type given for a non-SELECT Query...`**

**관련 에러는 쿼리문의 문제이므로 작성한 쿼리문을 추적해서 해결해줘야 한다.**

**delete 쿼리문의 경우 반환값이 없는데, 본인은 `createQuery()` 함수의 매개변수에 `Task.class` 를 적용함으로써 반환된 결과를 해당 Task(엔티티) 에 자동 매핑을 해줘서 문제가 발생했다.**

**매개변수 `Task.class` 를 지우고 실행시 해결완료!**
