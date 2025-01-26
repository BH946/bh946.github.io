---
title:  "[Java 파싱] 데이터 포맷 CSV, XML, JSON 비교와 JSON 라이브러리 활용"
categories : Java
tag : [Java, JSON, CSV, XML, Gson, Jackson, DataFormat, Serialization]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**JSON 처리를 위한 Jackson과 Gson 라이브러리의 사용법과 다양한 데이터 포맷의 특징을 설명합니다.**

**json(=string) to object, object to json(=string) 이 2개 하는법만 알면 충분!**

<br>

<br>

## 데이터 포맷 비교

**CSV, XML, JSON 차이:** 표 형식, 태그 기반, 객체 기반(키-값)

![포멧비교](https://github.com/user-attachments/assets/0eabd153-665e-49ed-994b-5d5f77223815)  

<br>

<br>

## Jackson 라이브러리 활용

**ObjectMapper의 readValue, writeValueAsString, convertValue 메소드 사용**

**map은 키-값 형태이다 보니 json과 유사**

```java
//json to object(=map) -> "readValue 메소드"
ObjectMapper obj = new ObjectMapper();
// 날짜 변경과 관련될 룰 지정 (생각보다 쓰임)
obj.setDateFormat(new SimpleDateFormat("yyyy-MM-dd"));
// 원하는 타입 지정하여 변환!
Map<String, Map<String, Object>> map = obj.readValue(json, Map.class);

//object(=map) to json -> "writeValueAsString 메소드"
Map<String, Long> map = new HashMap<>();
map.put("followingId", MEMBER_ID2); //to: MEMBER_ID2
ObjectMapper obj = new ObjectMapper();
String json = obj.writeValueAsString(map);

//번외) object(=map) to object -> "convertValue 메소드"
List<Map<String, Object>> list = (List)map.get("boxOfficeResult").get("dailyBoxOfficeList");
for(Map<String, Object> info: dailyBoxOfficeList) {
  BoxOffice box = obj.converValue(info, BoxOffice.class);
  this.list.add(box);
}
```

<br>

<br>

## Gson 라이브러리 활용

**Gson의 fromJson, toJson 메소드 사용**

```json
//json to object(=Message) -> "fromJson 메소드 + 타입!"
private List<Message> dataToPrevChat(String message) {
  message = message.split(":", 2)[1];
  Gson gson = new Gson();
  Type messageListType = new TypeToken<List<Message>>() {
  }.getType(); //타입 알려주기 위함! (중요)
  return gson.fromJson(message, messageListType);
}

//object(=Message) to json -> "toJson 메소드"
private void messagePrevChat(List<Message> messages) {
  Gson gson = new Gson();
  StringBuilder sb = new StringBuilder();
  sb.append("["); //배열 시작(JSON)
  for (int i = 0; i < messages.size(); i++) {
    Message msg = messages.get(i);
    String jsonMsg = gson.toJson(msg);
    sb.append(jsonMsg);
    // 마지막 메시지가 아닐 경우 쉼표 추가 -> 빈 공백 데이터 끝에 방지
    if (i < messages.size() - 1) {
      sb.append(", "); //json 형태 쉼표
    }
  }
  sb.append("]"); //배열 끝(JSON)
  
  // 클라이언트에게 메시지(=sb) 전송
  this.output.println(ConstanctMsg.PREV_CHAT_FLAG + ":" + sb);
  System.out.println(sb); //debug
}
```

<br>

**json to object 를 위해 json 데이터 가공에 위 처럼 message = message.split(":", 2)[1]; 코드로 직접 가공해도 좋지만,**
**JsonObject 를 활용할 수도 있다. - Gson + JsonObject 활용**

```java
public class Test {

	public static void main(String[] args) throws Exception {
		File file = new File("./src/io/json/emp-input2.json");
		FileReader fr = new FileReader(file);
		
		JsonObject jsonObject = (JsonObject) JsonParser.parseReader(fr);
		JsonElement jsonElement = jsonObject.get("emp-list");
		Gson gson = new Gson();
		
		Type empListType = new TypeToken<ArrayList<EmpDto>>() {}.getType();// 타입 가져오는 목적
//		List<EmpDto> empList = gson.fromJson(fr, empListType);
		List<EmpDto> empList = gson.fromJson(jsonElement, empListType);
		
		for(EmpDto emp : empList) {
			System.out.println(emp);
		}
	}
}
```

💡 **주의사항**

- 배열 형태의 JSON 처리 시 대괄호 처리 필요
  - 여러 json 반환 땐(=배열로 감싸는형태 `["{},{},{}"]`) → 직접 `"["` , `","` , `"]"` 삽입 필요할 수도 있음!
  - 일반 json반환 시 마지막에 객체로 감싸서 반환이 기본 `{ 데이터 }`
- 날짜 형식 지정 시 별도 설정 필요
- 복잡한 객체 변환 시 Type 지정 필수

<span style="color:#777777">상황에 따라 적절한 라이브러리와 데이터 포맷을 선택하여 사용하는 것이 중요합니다.</span>
