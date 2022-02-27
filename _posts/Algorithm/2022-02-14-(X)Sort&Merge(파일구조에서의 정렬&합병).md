---
title:  "[X]Sort & Merge(파일구조에서의 정렬&합병)"
categories : Algorithm
tag : [Internal Sorting(내부정렬), External Sorting(외부정렬), Run 생성방법, 내부정렬 (internal sort), 대체선택 (replacement selection), 자연선택 (natural selection), Merge 수행방법, m-원 합병(m-way merge), 균형 합병(balanced merge), 다단계 합병(polyphase merge), 계단식 합병(cascade merge)]
toc: true
toc_sticky: true
author_profile: false
sidebar:
   nav: "docs"
---

## File구조에서의 정렬

* 파일처리론에서 배운 내용을 배경으로 정리하겠다. (자세한건 책 확인)
* 보통 Run생성후 합병함으로써 정렬을 끝냄(File) 
  => 제한된 용량 안에서 정렬을 위해 보조 저장장치 개념의 Run이 필요한 것이다.



### 1. Internal Sorting(내부정렬)

* **데이터가 적어서 메인 메모리 내에 모두 저장시켜 정렬이 가능할 때**
* Record Read, Write에 걸리는 시간이 문제되지 않는다.



### 2. External Sorting(외부정렬)

* **데이터가 많아서 메인 메모리의 용량을 초과하여 보조 저장장치에 저장된 파일을 정렬할 때**
* Record, Read, Write에 걸리는 시간이 중요
* 정렬/합병(sort/merge)
  * Run : 하나의 파일을 여러 개로 분할하여 내부 정렬 기법으로 정렬시킨 서브파일(subfile)
  * Run들을 합병(merge)하여 원하는 하나의 정렬된 파일로 만든다.



#### 2-1. Sort Phase(정렬 단계)

##### Run 생성 방법

* 내부 정렬 (internal sort) 
  * 내부 정렬 또한 Run을 생성하고 merge를 하는것임!! 1번에서 말한 내부정렬의 의미는 그냥 메인 메모리 내에 데이터가 모두 저장이 된다면, merge이런게 필요없이 바로 정렬되고 끝날거라는 의미.
* 대체 선택 (replacement selection) -> 블로그에 코드 작성해서 올린 게시글 있으니 참고.
* 자연 선택 (natural selection)



#### 2-2. Merge Phase(합병 단계)

##### Merge 수행 방법

* m-원 합병(m-way merge)
* 균형 합병(balanced merge)
* 다단계 합병(polyphase merge)
* 계단식 합병(cascade merge)





