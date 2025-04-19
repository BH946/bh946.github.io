---
title:  "(JPA+Boot)컨트롤러API 구현"
categories : Spring
tag : [컨트롤러,RestAPI,DTO패턴,예외처리,HTTP응답]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**스프링 프레임워크에서 컨트롤러 계층을 구현할 때 API 설계, DTO 패턴 활용, 예외 처리, HTTP 응답 관리 등의 핵심 기법을 상세히 다루는 가이드입니다.**

<br><br>

### (컨트롤러API) HTTP 구현 + 컴포넌트 스캔 + DTO

@Controller: 자동 컨트롤러 빈 등록  
@RestController: API는 위보다 이걸 사용

**보통 @RestController + @RequestMapping + @RequiredArgsConstructor 조합 사용**

- @RequiredArgsConstructor + final 은 생성자 주입

- @RestController 는 API에 필수(+**@ResponseBody도 포함**하여 Json 반환 자동)

- @RequestMapping("api/v1/members") 는 전역 주소 + GET, POST 둘다 한번에 지원

  - @PostMapping, @GetMapping -> 주로 사용 (좀 더 유지보수하기 좋은듯)

  - @RequestBody, @ResponseBody : HttpEntity 처럼 **HTTP 메시지 컨버터**가 **HTTP 메시지 바디**의 내용을 우리가 원하는 문자나 객체(DTO) 등으로 자동 변환

    - 요청엔 @RequestBody, 응답엔 @ResponseBody!!

  - **응답은 항상 아래 흐름**

    - `ResponseEntity.status(HttpStatus.NOT_FOUND).body(FAIL_LOGIN)` 이런 패턴 사용중! -> ResponseEntity 활용 (상태코드 담기 좋다)

    - `@ResponseStatus(HttpStatus.BAD_REQUEST)` 이걸로ResponseEntity 없이 쉽게 반환할 수도 있음 (상태코드를 @ResponseStatus로 해결)

      <details><summary><b>HttpStatus 클래스에서 제공하는 주요 상태 코드</b></summary>
      <div markdown="1">
      - **1xx: 정보 응답**
          - **100 Continue**: 클라이언트가 요청을 계속 진행해도 좋다는 의미입니다.
          - **101 Switching Protocols**: 서버가 클라이언트의 요청에 따라 프로토콜을 전환하고 있다는 의미입니다.
      - **2xx: 성공**
          - **200 OK**: 요청이 성공적으로 처리되었습니다.
          - **201 Created**: 요청이 성공적으로 처리되어 새로운 자원이 생성되었습니다.
          - **202 Accepted**: 요청이 수락되었으나 아직 처리되지 않았음을 의미합니다.
          - **204 No Content**: 요청이 성공적으로 처리되었으나 반환할 내용이 없음을 의미합니다.
      - **3xx: 리다이렉션**
          - **300 Multiple Choices**: 요청에 대해 여러 개의 선택지가 있음을 의미합니다.
          - **301 Moved Permanently**: 요청한 자원이 영구적으로 새로운 URI로 이동하였음을 나타냅니다.
          - **302 Found**: 요청한 자원이 다른 URI로 임시로 이동되었음을 의미합니다.
          - **304 Not Modified**: 클라이언트가 캐시한 자원이 수정되지 않았음을 나타냅니다.
      - **4xx: 클라이언트 오류**
          - **400 Bad Request**: 요청이 잘못되어 서버가 이해할 수 없음을 의미합니다.
          - **401 Unauthorized**: 요청에 인증이 필요하며, 인증 정보가 없거나 유효하지 않음을 나타냅니다.
          - **403 Forbidden**: 서버가 요청을 이해했지만, 요청을 수행할 권한이 없음을 의미합니다.
          - **404 Not Found**: 요청한 자원을 찾을 수 없음을 나타냅니다.
          - **405 Method Not Allowed**: 요청한 HTTP 메서드가 해당 자원에서 지원되지 않음을 의미합니다.
      - **5xx: 서버 오류**
          - **500 Internal Server Error**: 서버에서 요청을 처리하는 도중 오류가 발생했음을 나타냅니다.
          - **501 Not Implemented**: 요청한 메서드가 서버에서 구현되지 않았음을 의미합니다.
          - **503 Service Unavailable**: 서버가 현재 요청을 처리할 수 없음을 나타내며, 보통 서버가 과부하 상태이거나 유지보수 중일 때 발생합니다.
      </div>
      </details>

    - **Valid 사용하면 Api응답양식**까지 포함해서 body에 반환
      `ApiResponse res = ApiResponse.error(HttpStatus.BAD_REQUEST.value(), bindingResult);` -> (ApirResponse는 아래 "리팩토링 Valid 파트"에서 참고)

- **세션 얻으려고 직접 HttpServletRequest** 파라미터를 받기도 함. -> request 가 세션 관리 기억!

  ```java
  // 세션 있으면 세션 반환, 없으면 신규 세션 생성
  HttpSession session = request.getSession(); // UUID 형태로 알아서 생성 (기본값 : true)
  // 세션에 로그인 회원 정보 보관
  session.setAttribute(SESSION_NAME_LOGIN, findMember.getId());
  ```

<br>

**"컴포넌트 스캔"은 전체와 부분으로 적용 가능**

- @SpringBootApplication 는 자동으로 **전체 범위**로 설정!!
  - `@SpringBootApplication(scanBasePackages = "hello.itemservice.web")` 는 **web하위만** 컴포넌트 스캔!!<br>

- **@Import(이름.class)**로 Java Config 파일 따로 스캔등록 가능 (즉, 빈 등록 가능)
  - **@ImportResource("classpath:/spring/*.xml")** 는 xml등록 할 때!


<br>

**컨트롤러 에서의 JPA vs MyBatis 관점 POINT**: "웹에서 Item정보를 form 담아 요청 가정"

- **JPA**에선 update나 delete할때 항상 findOne으로 해당 Item을 DB에서 찾아와서 "영속성 만들고" update, delete를 적용했다. (객체 지향적인 JPA)
  - 특히, "더티체킹" 사용하려면 영속성을 꼭 만들어야 한다.
- **MyBatis**에선 SQL직접 작성하니 findOne을 따로하지 않고 웹에서 얻은 Item정보로 바로 update, delete 쿼리를 적용했다. (영속성이란게 없음)
- **즉 JPA는 쿼리2개, MyBatis는 쿼리1개 날라갔다.**

- 따라서 복잡한 도메인(객체) 중심은 JPA, 쿼리 최적화가 중요하면 MyBatis가 좋겠다.  
  JPA가 더티체킹위해 비즈니스 로직(update)이 엔티티단에 캡슐화 가능한 관점처럼 둘은 차이가 있다.

<br>

**파라미터 요청이나 응답** 둘다 “**DTO(+Valid)”는 거의 필수** 사용 -> Jackson, Gson (json변환 라이브러리) 알아두면 좋다.

- **DTO는 경로 상 제일 하위에서 가지는 계층에 두자(규칙은 아니고 보통 그럼)**

  - 컨트롤러면 `controller/dto/*`, 엔티티쪽 비즈니스 로직으로 사용한다면 `entity/dto/*`, 간단한건 `static class(inner)`로 내부에서 끝내던지.  

- **API 응답 스펙**에 맞추어 별도의 **DTO를 반환** 권장 (**요청도** 마찬가지)

  - 굳이 필드 숨길게 없으면 안해도 되겠지만 password처럼 있으면 꼭 생성하자.   
    어차피 "요청DTO, 응답DTO" 둘 다 사용한다고 해서 코드만 조금 복잡해지지 성능이 떨어지고 할건 없다.  

  - **검증(ex:@Validation)도 따로 지정**할 수 있어 더 효과적

  - **DTO는 setter를 최대한 사용하지않고, 파라미터를 줄여주는 효과**

    <details><summary><b>본인 판단하에 DTO 사용 & 사용안한 코드 예시:</b></summary>
    <div markdown="1"><br>
    WEB) GalleryController -> gallery(), StudioController -> studioIdUpdate
    ```java
    /**
    	 * 페이지 별로 조회 메서드 -> 페이징
    	 * 
    	 * @param item  -> 어차피 숨기고 싶은 password가 안들어 올거라서 요청은 DTO로 안 받겠다.
    	 * @param model -> 응답DTO 사용
    	 * @return
    	 * @throws Exception
    	 */
    	@GetMapping()
    	public String search(@ModelAttribute Item item, Model model) throws Exception {
    //		log.info("itemId: {}",item.getId());
    		return this.gallery(item, model); // HTTP말고 그냥 메소드 호출한거.(포워드,리다이렉트 아님)
    	}
    	@PostMapping() // ...?pageIndex=1 이런식으로 페이지 파라미터 넘어 올거임(pageIndex란 Item이 상속받고 있는 DefaultItem의
    					// 필드)
    	public String gallery(@ModelAttribute Item item, Model model) throws Exception {
    		item.setPageUnit(myDataSource.getPageUnit());
    		item.setPageSize(myDataSource.getPageSize());
    //
    		// pagination setting
    		PaginationInfo paginationInfo = new PaginationInfo();
    		paginationInfo.setCurrentPageNo(item.getPageIndex());
    		paginationInfo.setRecordCountPerPage(item.getPageUnit());
    		paginationInfo.setPageSize(item.getPageSize());
    //
    		item.setFirstIndex(paginationInfo.getFirstRecordIndex());
    		item.setLastIndex(paginationInfo.getLastRecordIndex());
    		item.setRecordCountPerPage(paginationInfo.getRecordCountPerPage());
    //
    		// List
    		List<Item> items = itemService.findAllWithPage(item);
    		List<ItemResDto> itemsResDto = items.stream().map(o -> new ItemResDto(o)).collect(Collectors.toList());
    		int totCnt = itemService.findTotalCount(item);
    //
    		model.addAttribute("itemsResDto", itemsResDto);
    		paginationInfo.setTotalRecordCount(totCnt);
    		// Pagination
    		model.addAttribute("paginationInfo", paginationInfo);
    		log.info("cnt: {}, resultList: {}", totCnt, items);
    //
    		return "jsp/gallery";
    	}
    //
    /**
         * 수정 데이터 작성 -> 정보 기입
         * 특징: 갤러리-아이템에서 이곳으로 접근
         * @param itemId
         * @param model
         * @return
         * @throws Exception 
         */
        @GetMapping("item/{itemId}")
        public String studioCompleteId(@PathVariable Long itemId, Model model) throws Exception {
            Item item = itemService.findById(itemId);
            model.addAttribute("item", item);
            return "/jsp/studio_item"; 
        }
        /***
         * 수정 수행 -> 정보 기입
         * @param form -> 요청 DTO (Valid로 JSP에 출력)
         * @param bindingResult
         * @param itemId
         * @param redirectAttributes
         * @return
         * @throws Exception
         */
        @PostMapping("item/{itemId}")
        public String studioIdUpdate(@Validated @ModelAttribute UpdateItemDto form, BindingResult bindingResult,
                                     @PathVariable Long itemId, RedirectAttributes redirectAttributes, Model model) throws Exception {
            if(bindingResult.hasErrors()) {
                log.info("error={}", bindingResult);
                model.addAttribute("bindingResult", bindingResult);
                return "jsp/studio_item"; //다시 폼으로 이동
                // 어차피 "검증" 에 걸려서 DB 사용안하기에 PRG 패턴 상관없움
            }
            log.info("title테스트={}", form.getTitle());
            itemService.update(form);
    //      int pageId = itemService.findPageId(itemId);
    //      itemService.updateAllWithPage(pageId); // 캐싱
          redirectAttributes.addFlashAttribute("status", "updateON");
          redirectAttributes.addAttribute("itemId", itemId);
          return "redirect:/gallery/itemDetail/{itemId}";        
        }
    //Dto 클래스
    @Data
    public class UpdateItemDto {
    	  @NotNull
    	  private Long id;
    	  @NotNull
    	  private String nickname;
    	  @NotNull
    	  @Pattern(regexp = "^[0-9]+", message = "비밀번호는 숫자로 입력 해주세요.")
    	  private String password;
    	  @NotNull
    	  private String title;
    	  @NotNull
    	  private String content;
    }
    ```
    API) ListsApiController -> findByDateWithMemberTask()
    ```java
    /**
       * 일정 조회(4) - 날짜범위로 Lists(=하루단위 일정모음) 조회 -> 해당 회원꺼만 하루, 한달, 1년 등등 원하는 날짜 범위만큼 사용 가능
       * 요청Dto, 응답Dto
       */
      @PostMapping(value = "/member/date")
      public ResponseEntity<ApiResponse<List<ListsResDto>>> findByDateWithMemberTask(
          @Login Long memberId,
          @RequestBody @Validated CreateListsRequestDto request, BindingResult bindingResult) {
        if (bindingResult.hasErrors()) {
          log.info("검증 오류 발생 errors={}", bindingResult);
          ApiResponse res = ApiResponse.error(HttpStatus.BAD_REQUEST.value(), bindingResult);
          return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(res);
        }
        List<Lists> listsList = listsService.findByDateWithMemberTask(memberId, request.startTime,
            request.endTime);
        if (listsList.isEmpty()) {
          ApiResponse res = ApiResponse.success(HttpStatus.NO_CONTENT.value(), null);
          return ResponseEntity.status(HttpStatus.NO_CONTENT).body(res);
        }
        List<ListsResDto> result = listsList.stream()
            .map(o -> new ListsResDto(o))
            .collect(Collectors.toList());
        ApiResponse res = ApiResponse.success(HttpStatus.OK.value(), result);
        return ResponseEntity.status(HttpStatus.OK).body(res);
      }
    //
    // DTO
      @Getter
      static class ListsResDto {
        private Long listsId;
        private LocalDate listsDate; // 등록 날짜
        private String timerAllUseTime; // 타이머총사용시간
        private List<TaskDto> listsTasks;
        public ListsResDto(Lists lists) { // lazy 강제 초기화
          listsId = lists.getId();
          listsDate = lists.getListsDate();
          Long time = lists.getTimerAllUseTime();
          Long hour = time / (60 * 60 * 1000);
          time %= (60 * 60 * 1000);
          Long minute = time / (60 * 1000);
          time %= (60 * 1000);
          Long second = time / (1000);
          timerAllUseTime = hour + ":" + minute + ":" + second; // 시:분:초 형태로 반환
          listsTasks = lists.getTasks().stream()
              .map(o -> new TaskDto(o))
              .collect(Collectors.toList());
        }
      }
      @Getter
      static class CreateListsRequestDto {
        @NotNull(message = "날짜 범위는 필수입니다.")
        private LocalDateTime startTime;
        @NotNull(message = "날짜 범위는 필수입니다.")
        private LocalDateTime endTime;
      }
    ```
    </div>
    </details>

- **DTO에서 lazy 강제 초기화 하고 있다.** 안하면 `LazyInitializationException` 에러

  - 강제 초기화를 안하면 fetch join으로 모두 조회 해도 해당 엔티티를 찾을 수 없어 null이 응답!!  
    -> "영속성에 등록하지 않아서. 즉, 메모리에 올려두지 않아서."

    <details><summary><b>자세히 (출력사진+코드)</b></summary>
    <div markdown="1"><br>
    **Lazy 강제 초기화 안 할때(null 문제)**<br>
    <img src="https://github.com/user-attachments/assets/093f2678-788f-4ecf-8ade-4790f5cb31e1" alt="image" style="zoom:80%;" /><br>
    <img src="https://github.com/user-attachments/assets/4c512757-f58b-44ba-b89f-4fb9d0706dae" alt="image" style="zoom:80%;" /><br>
    <br>**강제 초기화 할 때(객체를 불러오면 됨)**
    <div markdown="1">
    ```java
    //ListsResDto 생성자 내부
    List<Lists> listsList = listsService...
    List<ListsResDto> result = listsList.stream()
        .map(o -> new ListsResDto(o))
        .collect(Collectors.toList()); //반환 List타입 설정
    this.listsTasks = lists.getTasks().stream()
        .map(o -> new TaskDto(o))
        .collect(Collectors.toList());
    //TaskDto 생성자 내부
    this.completedStatus = task.getTaskStatus().getCompletedStatus();
    this.timerOnOff = task.getTaskStatus().getTimerOnOff();
    ```
    </div>
    <img src="https://github.com/user-attachments/assets/80d56805-64a3-41e9-ba9d-a0cddb027498" alt="image" style="zoom:80%;" /><br>    
    </div>
    </details>

- API JSON직렬화 덕분에 유연 vs JSP는 엄격한 Bean규약: 예로 (private)static class vs public static class

  **API 컨트롤러의 경우:**

  - **주로 해당 컨트롤러 내에서만 DTO를 사용**
  - 응답 데이터는 JSON으로 직렬화되어 전송됨 
    - 이때 **Jackson**같은 라이브러리가 리플렉션 활용하여 private 접근 가능
  - **클라이언트 측에서는 이미 직렬화된 데이터를 받기 때문**에 Java 클래스의 접근성 제한에 영향을 받지 않음

  **JSP(웹)의 경우:**

  - **컨트롤러에서 사용할 뿐만 아니라 JSP 페이지에서도 직접 객체에 접근**
  - JSP 표준 액션 태그나 EL 표현식이 **Java 객체에 직접 접근**해야 함
  - 따라서 Bean 클래스와 그 멤버들이 public으로 선언되어야 함(**ex: 응답DTO가 public**)

- LePl 플젝(Jackson), Swing 플젝(Gson) 사용했으니 참고: [정리한 노션](https://www.notion.so/Java-Spring-7611b33fb665463284dc7a4ffb783c39#19c4a96ae5e341a2b2b628a963bf6d53)

  - JSON 반환시 꼭 마지막에 객체로 감싸서 반환 -> `{ 데이터 }`
  - 배열 JSON이면 꼭 마지막에 배열로 감싸서 반환 -> `[{},{}...]`

  <details><summary><b>Jackson 2.8.0 이전 버전 LocalDateTime 직렬화 오류?</b></summary>
  <div markdown="1">
  ```java
  Map<String, LocalDateTime> map = new HashMap<>();
  map.put("startTime", st);
  map.put("endTime", en);
  ObjectMapper obj = new ObjectMapper();
  String content = obj.registerModule(new JavaTimeModule())
      .writeValueAsString(map); // Jackson 2.8.0 이전 버전에서는 JavaTimeModule 을 써야 에러 해결(직렬화 에러)
  ```
  </div>
  </details>

<br>

<details><summary><b>컨트롤러에서 생각해볼 점:</b></summary>
<div markdown="1"><br>
1. **반환에 대한 생각**: 
   - API는 JSON을 반환해서 반환처리가 "String+여러가지"로 복잡한 반면, 
   - 웹은 "String"만 해도 충분! (String으로 간단히 jsp 반환)
2. **컨트롤러 에서의 JPA vs MyBatis 관점 POINT**: "JSP에서 Item정보를 가져온다 가정"
   - JPA에선 update나 delete할때 항상 findOne으로 해당 Item을 DB에서 찾아와서 "영속성 만들고" update, delete를 적용했다. (특히, "더티체킹" 사용하려면 영속성을 꼭 만들어야 한다.)  
   - MyBatis에선 SQL직접 작성하니 findOne을 따로하지 않고 JSP에서 얻은 Item정보로 바로 update, delete 쿼리를 적용했다. (영속성 필요가 없으니)
   - 따라서 복잡한 도메인(객체) 중심은 JPA, 쿼리 최적화가 중요하면 MyBatis가 좋겠다.<br>JPA가 더티체킹 위해 비즈니스 로직(update)이 엔티티단에 캡슐화 가능한 관점처럼 둘은 차이가 있다.<br>
<div markdown="1"><br>
2번 예시: JPA와 MyBatis의 수정로직 상황 (update, findOne 메소드만 보면 됨.)
```java
    //update 메소드 1개가 끝 (MyBatis)
    @PostMapping("item/{itemId}")
    public String studioIdUpdate(@ModelAttribute UpdateItemDto form, @RequestParam int pageIndex, BindingResult bindingResult,
                                 @PathVariable Long itemId, RedirectAttributes redirectAttributes, Model model) throws Exception {
    	beanValidator.validate(form, bindingResult); //@Validated 사용 안하면 직접 해줘야 함.
    	if(bindingResult.hasErrors()) {
            log.info("error={}", bindingResult);
            model.addAttribute("bindingResult", bindingResult); //서버단 Valid결과 알려주는거 (직접 에러 커스텀도 해가지고 추가했음. form:error만 사용할경우 이 코드 필요없음)
            return this.studioCompleteId(itemId, pageIndex, model); //다시 폼으로 이동 (item/{itemId}로 이동해야해서 내무메소드로 호출하겠음. 애초에 수정폼은 따로 둬서 폼바로 호출해야 했다. 이 방식은 비추다 ㅠ.)
        }
        log.info("title테스트={}", form.getTitle());
        form.setPageIndex(pageIndex);
        itemService.update(form);
      redirectAttributes.addFlashAttribute("status", "updateON");
      redirectAttributes.addAttribute("itemId", itemId);
      return "redirect:/gallery/itemDetail/{itemId}";        
    }
```
```java
  //update, findOneWithMember 메소드가 필요 (JPA)
  @PostMapping(value = "/member/update")
  public ResponseEntity<ApiResponse<String>> update(@Login Long memberId,
      @RequestBody @Validated UpdateTaskRequestDto request, BindingResult bindingResult) {
    if (bindingResult.hasErrors()) {
      log.info("검증 오류 발생 errors={}", bindingResult);
      ApiResponse res = ApiResponse.error(HttpStatus.BAD_REQUEST.value(), bindingResult);
      return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(res);
    }
    Task task = taskService.findOneWithMember(memberId, request.getTaskId());
    if (task == null) {
      ApiResponse res = ApiResponse.success(HttpStatus.ALREADY_REPORTED.value(), VALID_TASK);
      return ResponseEntity.status(HttpStatus.ALREADY_REPORTED).body(res); // 208
    }
    taskService.update(task, request.content, request.startTime, request.endTime); // 변경 감지
    ApiResponse res = ApiResponse.success(HttpStatus.OK.value(), SUCCESS_TASK_UPDATE);
    return ResponseEntity.status(HttpStatus.OK).body(res); // 200
  }
```
</div>
</div>
</details>

<br><br>
