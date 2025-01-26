---
title:  "[Java 파싱] XML 파서 SAX와 DOM 비교 분석"
categories : Java
tag : [Java, XML, SAXParser, DOMParser, Parsing]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**XML 문서를 처리하는 두 가지 주요 파싱 방식인 SAX와 DOM의 특징과 차이점을 설명합니다. - DOM parser 는 옛날 방식으로 그냥 사용하지 말자.** 

<br>

**파싱: 문서에서 필요한 정보를 얻기 위해 태그를 구별하고 내용을 추출하는 과정**

**두 파서의 차이는 전체를 다 읽고 파싱, 읽으면서 파싱이냐의 차이**

<br>

<br>

## SAX Parser (Simple API for XML)

**Simple API for XML Parser: 문서를 읽으면서 태그의 시작, 종료 등 이벤트 기반으로 처리하는 방식**

- 이벤트 기반 파싱
- 순차적으로 읽으면서 파싱 수행
- 메모리 효율적
- 대용량 XML 처리에 적합

```java
//MyHandler 만 직접 생성해주면 됨
public class MyHandler extends DefaultHandler {
    @Override
    public void startElement(String uri, String localName, String qName, 
                           Attributes attributes) {
        // 태그 시작 처리
    }
    
    @Override
    public void endElement(String uri, String localName, String qName) {
        // 태그 종료 처리
    }
}
```

<br>

**자세한 동작구조와 실습 예제 코드**

![SAXParser](https://github.com/user-attachments/assets/28da60e6-a7fb-43ab-a3b5-4d5aa49cf9f2) 

<br>

![Image](https://github.com/user-attachments/assets/fcbabbce-e825-4c8a-9798-1620c4bf3fee) 

![Image](https://github.com/user-attachments/assets/f88e9358-04ba-4b8b-aef4-fc7c884592d6)  

![Image](https://github.com/user-attachments/assets/75e7ce4a-6ac0-446a-b3a6-ce90b6a30771)  

<br>

<br>

## DOM Parser (Document Object Model)

**Document Object Model Parser: 문서를 다 읽고 난 후 문서 구조 전체를 자료구조에 저장하여 탐색하는 방식**

- 트리 구조 기반 파싱
- 전체 문서를 메모리에 로드 후 파싱
- 양방향 탐색 가능
- 작은 크기의 XML에 적합

<br>

<br>

## 주요 차이점

| 특성        | SAX         | DOM         |
| ----------- | ----------- | ----------- |
| 파싱 방식   | 이벤트 기반 | 트리 구조   |
| 메모리 사용 | 효율적      | 전체 로드   |
| 처리 속도   | 빠름        | 상대적 느림 |
| 문서 수정   | 불가능      | 가능        |

<br>

💡 **실무 권장사항**

- 대용량 XML 처리: SAX 파서 사용
- DOM 파서는 레거시 시스템이 아닌 경우 사용 지양

<span style="color:#777777">현대 애플리케이션에서는 SAX 파서나 더 현대적인 XML 처리 방식을 선호합니다.</span>
