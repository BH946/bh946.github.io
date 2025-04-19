---
title:  "MyBatis+Spring+Boot+eGov 리팩토링 모음"
categories : Spring
tag : [eGov프레임워크,검증,예외처리,AOP,Jakarta]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**전자정부 표준프레임워크(eGov)에서 Jakarta Commons Validation을 활용한 검증 구현과 AOP 기반 예외처리 전략, 메시지 국제화 연동 방법을 상세히 설명하는 실무 중심 가이드입니다.**

<br>

<br>

## MyBatis+Spring+Boot+eGov 파트 (리팩토링)

"순수스프링"은 xml이 web.xml > context-servlet.xml > context-common.xml 순으로 크게 나뉜다.  
("스프링, 스프링부트 비교" 파트 참고)

대부분 부트 사용해서 Java Config 방식을 알면 되지만, 레거시한 프로젝트는 xml 많이 사용하므로 알아두자.

<br><br>

### 메시지국제화,외부설정,캐시 - XML

**(1)메시지 국제화 - XML**

context-common.xml에 messageSource빈과 국제화빈 등록,  
context-servlet.xml에 해당 인터셉터를 등록  
message/meesage-common.properties, *\_ko.properties, *\_en.properties 를 생성

**메시지 국제화**는 "핸들러매핑에 인터셉터"를 등록하는 부분이 필요하다.   
"디스패처서블릿->HandlerMapping->인터셉터 거쳐서 컨트롤러 진입"을 해야하기 때문! (Locale 위해)

- Java Config나 \<mvc:interceptors>(xml)패턴으로 인터셉터를 추가하게 되면 부트는 "모든 핸들러매핑"에 인터셉터를 추가해준다!

<details><summary><b>XML 코드: (이거 적용함)</b></summary>
<div markdown="1"><br>
resources/spring/context-common.xml
```xml
<!-- -->
	<!-- 메시지소스와 국제화 -->
	<!-- set message source -->
	<bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
		<property name="basenames">
			<list>
				<value>messages.message-common</value>
			</list>
		</property>
	</bean>
	<!-- setting Locale -->   
	<bean id="localeChangeInterceptor" class="org.springframework.web.servlet.i18n.LocaleChangeInterceptor"
		p:paramName="lang" />
	<bean id="localeResolver" class="org.springframework.web.servlet.i18n.SessionLocaleResolver" />
```
resources/spring/context-servlet.xml
```xml
	<!-- 부트처럼 "모든 핸들러매핑에 인터셉터 등록" 방안 -->
	<mvc:interceptors>
		<mvc:interceptor>
			<mvc:mapping path="/*" />
			<ref bean = "localeChangeInterceptor" /> <!-- 메시지 국제화 등록 -->
		</mvc:interceptor>
	</mvc:interceptors>
```
</div>
</details>


<br>

**(2)외부설정, 캐시는** **JPA 방식꺼 보기. 이건... 비추**

eGov 라이브러리 사용하여 빈 등록해서 XML이나 Java에서 간단히 할 수 있게 지원 중이다.   
=> JAVA ex: EgovPropertyServiceImpl 빈 등록하며 setProperties()로 사용할 변수(Map) 넣으면 됨.  

근데, 위 방식처럼 application.properties에 변수 등록해서 사용하는게 더 좋을것 같음(할 수 있는게 많으니)

<details><summary><b>eGov 사용코드(Java Config):</b></summary>
<div markdown="1"><br>
외부설정 (그냥 따로 클래스 사용하는 느낌인디..)
```java
@Configuration
public class EgovConfigProperties {
	@Bean(destroyMethod="destroy")
	public EgovPropertyServiceImpl propertiesService() {
		Map<String, String> properties = new HashMap<>();
		properties.put("pageUnit", "10");
		properties.put("pageSize", "10");
//
		EgovPropertyServiceImpl egovPropertyServiceImpl = new EgovPropertyServiceImpl();
		egovPropertyServiceImpl.setProperties(properties);
		return egovPropertyServiceImpl;
	}
}
//
//test code: 다양한 get메소드 제공
private final EgovPropertyService propertiesService; //빈 가져옴
propertiesService.getInt("pageUnit"); //10
```
</div>
</details>


<br><br>

### 검증(Validation-Jakarta Commons) - XML

웹(JSP) 방식으로 주로 정리 -> API 방식은 "JPA + Boot 파트" 참고

**Jakarta Commons Validation 방식을 설명한다.** (일반적인 스프링 제공 검증 방식이 아님)    
⇒ JPA 플젝은 다른 방식 (Bean Validation)

> **API의 경우 클라쪽 "검증"은 서버가 할 일이 아니다(JS는 프론트쪽 개발진이 해야지!)**  
> **웹의 경우 클라와 서버쪽 둘 다 "검증"**해주는게 좋다.
>
> **API의 "예외"**의 경우 서버는 **JSON으로 변경된 데이터를 “Valid(검증)”**하는거라서 **JSON→DTO매핑될 때 에러나 그 시점에 다양한 에러(주로 서비스로직)들은 “Exception(예외)”**으로 해결!
> **=> 웹은 “검증”만으로 충분하지만 API는 “검증+예외”가 필요!**  
> => 근데, 막상 해보니 웹&API 둘다 "검증+예외"를 적용 했다. eGov에선 웹에 에러페이지만 연동해주는게 아니라 "예외"까지 굳이 하더라?
>
> eGov가이드에선 계속 Jakarta Valid 방식을 사용하더라.
>
> 순수스프링의 Bean Validation 방식이 내가하던 기존 방식이고, 자카트라 방식과는 조금 다름.  
> Bean Vaildation 방식 예시:
>
> 1. DTO 클래스에 검증 어노테이션(`@NotNull` 등) 적용하여 검증기에게 규칙 전달
> 2. 컨트롤러에서 `@Validated` 어노테이션으로 검증 활성화 (스프링프레임워크의 LocalValidatorFactoryBean이 사용됨)
> 3. `BindingResult`로 검증 결과 수집
> 4. 오류출력: 타임리프는 th:error로 자동으로 bindingresult에서 해당필드 검증 오류있나 체크해서 등록해둔 오류메시지-@NotNull("이미지가 없습니다")를 출력  
>    JSP는 form:error로 할 수 있고,  
>    직접 bindingresult를 가져와 사용해도 된다.

자카트라는 JSP에 validator 태그 사용시 "자동으로 JS검증 코드 생성"해줌. 즉, 클라쪽도 검증 자동 지원.  
=> 원래 Bean Validation방식은 컨트롤러 쪽에서 검증(서버단)하고 클라에선 직접 JS검증 코드 만들었었음.

빈은 스프링 모듈의 DefaultBeanValidator, DefaultValidatorFactory 사용

**Jakarta Commons Validation 방식 예시**:

1. 제공된 **validator-rules.xml** 사용 및 기본제공 룰 말고 **커스텀 룰 추가**하는법: [공문](https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:rte:ptl:validation:add_rules_in_commons_validator)

2. **validation.xml, validator.jsp, URL매핑** : 공통 규칙 정의 (서버+클라이언트)

   1. validation.xml로 원하는 규칙 정의 + 원하는 메시지 설정(=5번에서 설명) message-common.properties

   2. context-common.xml에 빈 등록 - DefaultBeanValidator, DefaultValidatorFactory

   3. context-servlet.xml이나 컨트롤러에 validator.jsp URL등록 (태그로 JS검증 코드 자동생성 목적)  
      부트는 그냥 "컨트롤러"에서 해라!! 자동 설정이랑 겹쳐서 머리아프다.

      참고: validator.jsp -> "/validator.do" 와 매핑 (자동으로 룰에 따른 메소드가 생성되는것 같더라)

      ```xml
      <!-- validator.jsp -->
      <%@ page language="java" contentType="javascript/x-javascript" %>
      <%@ taglib prefix="validator" uri="http://www.springmodules.org/tags/commons-validator" %>
      <validator:javascript dynamicJavascript="false" staticJavascript="true"/>
      ```

3. JSP validator 태그 적용해보기 -> **클라단 검증**

   적용 예시: 버튼 type을 submit이 아닌 button으로 하여 js함수(=test) 검증 후 submit하도록 하기!

   ```xml
   <%@ taglib prefix="validator" uri="http://www.springmodules.org/tags/commons-validator" %> 
   <!-- ...등 import -->
   <script type="text/javascript" src="<c:url value="/validator.do"/>"></script>
   <validator:javascript formName="updateItemDto" staticJavascript="false" xhtml="true" cdata="false"/>
   <script type="text/javascript">
   function test(form){
   	if(!validateUpdateItemDto(form)){ //자동 생성된 검증 메소드 사용한 것
   		return;
   	}else{
   		form.submit(); //valid 통과!
   	}
   }
   </script>
   <button class="btn btn-light m-0" type="button" onclick="redirectSavedBgm(), test(this.form)" .../>
   ```

4. Spring MVC Controller 적용해보기 -> **서버단 검증**

   적용 예시: form taglib를 활용하자 + modelAttribute필수 + form:error필수

   ```java
   private final DefaultBeanValidator beanValidator; //빈 주입
   @PostMapping("item/{itemId}")
   public String studioIdUpdate(@ModelAttribute UpdateItemDto form, Model model) throws Exception {
       beanValidator.validate(form, bindingResult); //@Validated 미사용은 직접 해줘야 함.
       if(bindingResult.hasErrors()) return 수정폼;
   ```

   ```xml
   <%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
   <form:form action=""
              method="post" modelAttribute="updateItemDto">
   <input type="password" class="no-spin" id="password" name="password" value="${item.password}" ...생략 />
   <form:errors id="password" name="password" path="password" />
   ```

   +번외) 직접 bindingResult가져와 jsp에서 사용하고 싶은경우?

   ```java
   @PostMapping("item/{itemId}")
   public String studioIdUpdate(@ModelAttribute UpdateItemDto form, Model model) throws Exception {
       beanValidator.validate(form, bindingResult); //@Validated 미사용은 직접 해줘야 함.
       if(bindingResult.hasErrors()) {
           model.addAttribute("bindingResult", bindingResult); //서버단 Valid결과 알려주는것 (직접 에러 커스텀 하려고 추가했음. form:error만 사용할경우 이 코드 필요없음)
   ... 생략
   ```

   ```xml
   <div class="field-error">
   	<%-- <c:if test="${not empty bindingResult.fieldErrors['password']}"> --%>
       <c:if test="${not empty bindingResult.fieldErrors}">
           비밀번호 오류
           <%-- 비밀번호 오류: <c:out value="${bindingResult.fieldErrors}" /> --%>
       </c:if>
   </div>
   ```

5. validator 태그 검증한 클라 에러 메시지와 controller에 bindingresult에 검증한 서버 에러 메시지

   **클라단 검증**의 경우 validater-rule.xml 를 보면 msg="errors.required" 이런 코드 체크!  
   message.properties에서 errors.required 관련 메시지로 검증 메시지(alert)를 출력한다.

   **서버단 검증**의 경우 타임리프로 했던 th:error와 유사하게 form:error으로 가능  
   둘 다 bindingResult 결과로 검증결과를 얻어서 message.properties의 메시지로 폼에 출력해주고  
   Bean Valid 방식으로 @NotNull("메시지")로 했다면 해당 메시지를 출력해준다.   

   **직접** 모든걸 하고 싶으면 model로 bindingResult를 넘겨서 jsp에서 가공하여 출력해도 된다.

   여기선 message-common.properties를 활용했다.  
   연동은 validator.xml에서 설정 가능하고, validator-rule.xml의 msg에서도 설정 가능하다.

   ```properties
   #validator.xml에 연동한 메시지
   updateItemDto.password=비밀번호는 필수 입력 항목입니다.~!
   #validator-rule.xml에 msg필드에 자동으로 연동된 메시지
   errors.required={0} 은 필수 입력값입니다.~!~!
   ```

   - 클라단 메시지든 서버단 메시지든 두 메시지가 전부 출력된다. 하나로 통일해서 사용하자.  
     예로 updateItemDto.password를 지우고 errors.required만 사용

**클라 검증 메시지, 서버 검증 메시지 예시:**

<img src="/images/2024-10-17-MyBatis+Spring+Boot+eGov 파트 리팩토링/image-20250406231747392.png" alt="image-20250406231747392" style="zoom:80%;" /> 

<img src="/images/2024-10-17-MyBatis+Spring+Boot+eGov 파트 리팩토링/image-20250406231709336.png" alt="image-20250406231709336" style="zoom:80%;" />

**번외인 직접 bindingresult가져와 jsp에서 커스텀한 방식 예시(코드는 앞에서 언급한것 동일):**

<img src="/images/2024-10-17-MyBatis+Spring+Boot+eGov 파트 리팩토링/image-20250406231558527.png" alt="image-20250406231558527" style="zoom: 80%;" /> 

<details><summary><b>xml 설정 코드와 message 예시:</b></summary>
<div markdown="1"><br>
**validator-rule.xml**
```xml
<form-validation>
   <global>
      <validator name="required"
            classname="org.springmodules.validation.commons.FieldChecks"
               method="validateRequired"
         methodParams="java.lang.Object,
                       org.apache.commons.validator.ValidatorAction,
                       org.apache.commons.validator.Field,
                       org.springframework.validation.Errors"
                  msg="errors.required">
          ...
```
**validator.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE form-validation PUBLIC 
    "-//Apache Software Foundation//DTD Commons Validator Rules Configuration 1.1//EN" 
    "http://jakarta.apache.org/commons/dtds/validator_1_1.dtd">
<form-validation>
    <formset>
        <form name="updateItemDto">
            <!-- id 필드: 필수 값 -->
            <field property="id" depends="required">
                <arg0 key="updateItemDto.id" />
            </field>
            <!-- nickname 필드: 필수 값 -->
            <field property="nickname" depends="required">
                <arg0 key="updateItemDto.nickname" />
            </field>
            <!-- password 필드: 숫자만 허용 -->
            <field property="password" depends="required,integer">
                <arg0 key="updateItemDto.password" />
                <arg1 key="updateItemDto.password.integer" />
            </field>
            <!-- title 필드: 필수 값 -->
            <field property="title" depends="required">
                <arg0 key="updateItemDto.title" />
            </field>
            <!-- content 필드: 필수 값 -->
            <field property="content" depends="required">
                <arg0 key="updateItemDto.content" />
            </field>
            <!-- pageIndex 필드 -->
            <field property="pageIndex" >
            </field>
        </form>
    </formset>
</form-validation>
```
**context-common.xml**
```xml
<!-- jakarta common validation 빈 등록 -->
<!-- Integration Apache Commons Validator by Spring Modules -->		
<bean id="beanValidator" class="org.springmodules.validation.commons.DefaultBeanValidator">
    <property name="validatorFactory" ref="validatorFactory"/>
</bean>
<bean id="validatorFactory" class="org.springmodules.validation.commons.DefaultValidatorFactory">
    <property name="validationConfigLocations">
        <list>
            <!-- 공통기술 -->
            <value>classpath:/validator/validator-rules.xml</value>
            <value>classpath:/validator/**/*.xml</value>  
        </list>
    </property>
</bean>
```
**validator.jsp**
```jsp
<%@ page language="java" contentType="javascript/x-javascript" %>
<%@ taglib prefix="validator" uri="http://www.springmodules.org/tags/commons-validator" %>
<validator:javascript dynamicJavascript="false" staticJavascript="true"/>
```
**controller.java**
```java
@GetMapping("/validator.do")
public String validator() {
    return "jsp/validator"; // Jakarta common validation 활용 위해
}
```
**message-common.properties**
```properties
# -- validator errors -- #
updateItemDto.id=ID는 필수 입력 항목입니다.
updateItemDto.nickname=닉네임은 필수 입력 항목입니다.
updateItemDto.password=비밀번호는 필수 입력 항목입니다.~!
updateItemDto.password.integer=비밀번호는 숫자로 입력해주세요.테스트임!!
updateItemDto.title=제목은 필수 입력 항목입니다.
updateItemDto.content=내용은 필수 입력 항목입니다.
#
fail.common.msg=에러가 발생했습니다!
fail.common.sql=sql 에러가 발생했습니다! error code: {0}, error msg: {1}
info.nodata.msg=해당 데이터가 없습니다.
errors.prefix=<div class="error"> 
errors.suffix=</div><br/>
errors.required={0} 은 필수 입력값입니다.~!~!
errors.minlength={0} 은 {1}자 이상 입력해야 합니다.
errors.maxlength={0} 은 {1}자 이상 입력할수 없습니다.
errors.invalid={0} 은 유효하지 않은 값입니다.
errors.byte={0} 은 byte 타입이어야 합니다.
errors.short={0} 은 short 타입이어야 합니다.
errors.integer={0} 은 integer 타입이어야 합니다.
errors.long={0} 은 long 타입이어야 합니다.
errors.float={0} 은 float 타입이어야 합니다.
errors.double={0} 은 double 타입이어야 합니다.
errors.date={0} 은 날짜 유형이 아닙니다.
errors.range={0} 은 {1} 과 {2} 사이의 값이어야 합니다.
errors.creditcard={0} 은 유효하지 않은 신용카드 번호입니다.
errors.email={0} 은 유효하지 않은 이메일 주소입니다.
errors.ihidnum=유효하지 않은 주민등록번호입니다.
errors.korean={0}은 한글을 입력하셔야 합니다.
```
</div>
</details>



<details><summary><b>Bean 방식 vs Jakarta 방식 코드 예시(+JS직접,자동): th:error vs form:errors</b></summary>
<div markdown="1"><br>
th:error, form:errors는 유사하다. 오히려 Bean, Jakarta 방식의 다른점을 비교해야 한다.<br>
"클라단 검증" 과 "메시지 설정" 및 xml설정은 위 정리글 보고,<br>
**"서버단 검증"을 중점적으로 비교**
```java
/*
차이점:
@Validated 유무로 인해 beanValidator.validate(form, bindingResult); 생략 유무
@Pattern(regexp = "^[0-9]+", message = "비밀번호는 숫자로 입력 해주세요.") 처럼 DTO에 바로 적용 메시지 유무
*/
//Bean Validation
@PostMapping("item/{itemId}")
    public String studioIdUpdate(@Validated @ModelAttribute UpdateItemDto form, @RequestParam int pageIndex, BindingResult bindingResult,
                                 @PathVariable Long itemId, RedirectAttributes redirectAttributes, Model model) throws Exception {
        if(bindingResult.hasErrors()) {
            log.info("error={}", bindingResult);
            model.addAttribute("bindingResult", bindingResult); //서버단 Valid결과 알려주는거
            return "jsp/studio_item"; //다시 폼으로 이동
            // 어차피 "검증" 에 걸려서 DB 사용안하기에 PRG 패턴 상관없움
        }
        form.setPageIndex(pageIndex);
        itemService.update(form);
      redirectAttributes.addFlashAttribute("status", "updateON");
      redirectAttributes.addAttribute("itemId", itemId);
      return "redirect:/gallery/itemDetail/{itemId}";   
//DTO
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
	  private int pageIndex;
}
//
//
//Jakarta Validation
private final DefaultBeanValidator beanValidator;
@PostMapping("item/{itemId}")
public String studioIdUpdate(@ModelAttribute UpdateItemDto form, @RequestParam int pageIndex, BindingResult bindingResult,
                             @PathVariable Long itemId, RedirectAttributes redirectAttributes, Model model) throws Exception {
	beanValidator.validate(form, bindingResult); //@Validated 사용 안하면 직접 해줘야 함.
	if(bindingResult.hasErrors()) {
        log.info("error={}", bindingResult);
        model.addAttribute("bindingResult", bindingResult); //서버단 Valid결과 알려주는거 (직접 에러 커스텀도 해가지고 추가했음. form:error만 사용할경우 이 코드 필요없음)
        return this.studioCompleteId(itemId, pageIndex, model); //다시 폼으로 이동 (item/{itemId}로 이동해야해서 내무메소드로 호출하겠음. 애초에 수정폼은 따로 둬서 폼바로 호출해야 했다. 이 방식은 비추다 ㅠ.)
    }
    form.setPageIndex(pageIndex);
    itemService.update(form);
  redirectAttributes.addFlashAttribute("status", "updateON");
  redirectAttributes.addAttribute("itemId", itemId);
  return "redirect:/gallery/itemDetail/{itemId}";  
}
//DTO
@Data
public class UpdateItemDto {
	  private Long id;
	  private String nickname;
	  private String password;
	  private String title;
	  private String content;
	  private int pageIndex;
}
```
```xml
<!-- form:errors 출력! th:error는 방식 유사하여 생략 -->
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
<form:form action=""
           method="post" modelAttribute="updateItemDto">
<input type="password" class="no-spin" id="password" name="password" value="${item.password}" ...생략 />
<form:errors id="password" name="password" path="password" />
<!-- -->
<!-- -->
<!-- 번외로 소개한 bindingResult model에 담아 가져와 직접 커스텀 방식 -->
<div class="field-error">
	<%-- <c:if test="${not empty bindingResult.fieldErrors['password']}"> --%>
    <c:if test="${not empty bindingResult.fieldErrors}">
        비밀번호 오류
        <%-- 비밀번호 오류: <c:out value="${bindingResult.fieldErrors}" /> --%>
    </c:if>
</div>
```
</div>
</details>


<br><br>

### 예외처리와 AOP - XML

XML 방식으로 주로 정리 -> Java Config 방식은 "JPA + Boot 파트" 참고

> **API의 경우 클라쪽 "검증"은 서버가 할 일이 아니다(JS는 프론트쪽 개발진이 해야지!)**  
> **웹의 경우 클라와 서버쪽 둘 다 "검증"**해주는게 좋다.
>
> **API의 "예외"**의 경우 서버는 **JSON으로 변경된 데이터를 “Valid(검증)”**하는거라서 **JSON→DTO매핑될 때 에러나 그 시점에 다양한 에러(주로 서비스로직)들은 “Exception(예외)”**으로 해결!
> **=> 웹은 “검증”만으로 충분하지만 API는 “검증+예외”가 필요!**  
> => 근데, 막상 해보니 웹&API 둘다 "검증+예외"를 적용 했다. eGov에선 웹에 에러페이지만 연동해주는게 아니라 "예외"까지 굳이 하더라?
>
> **예외를 처리하는 방법 크게 2가지**
>
> 1. 예외를 잡아서 정상화 하는 방법 ⇒ 예로 try, catch
>
> 2. 예외를 해결할 수 없는 문제로 인정하고 공통 처리하는 방법(사용자에게 죄송합니다. 같은 화면을 보여주는 방법) ⇒ 예로 @ExceptionHandler + @ControllerAdvice
>
>    특히, API(JSON)의 경우 대부분 2번으로 해결 됨. 직접 예외를 throw로 던져서 공통 관리해도 되니까.
>
> 예외를 처리하는 계층의 흐름 이해:
>
> 1. 서비스계층의 비즈니스 로직의 예외 발생하면 정상화하거나 공통 처리위해 던지기
> 2. 컨트롤러에서 웹이면 서비스의 Exception을 JSP 뷰로 매핑, API면 JSON으로 응답

**서비스계층의 예외 처리:**

- eGov에선 서비스계층의 비즈니스 로직 예외처리를 위해 **EgovAbstractServiceImpl** 를 사용하며,  
  EgovBizException 발생 메소드(**processException**) 와 Exception 발생없이 후처리 로그 메소드(**leaveaTrace**)를 제공한다.

  - **다국어 지원 메시지, 확장성, 표준화 로직** 때문에 사용!

- **leaveaTrace -> EgovBizException 발생 없이 로그 생성(메시지소스를 사용하는게 특징)**

  - 메소드 원리: traceHandler빈을 참조한 leaveaTrace빈을 xml이나 java로 등록 후 leaveaTrace빈을 주입해 사용하여 핸들링한다.

  - MessageSource 빈에 등록된 메시지를 로그(Info)로 제공

  - <details><summary><b>xml 설정 코드 예시:</b></summary>
    <div markdown="1"><br>
    **context-common.xml (비즈니스단 계층용 설정파일)**
    ```xml
    <!-- EgovAbstractServiceImpl가 제공하는 leaveaTrace(예외처리 관련) 사용 위해 빈 등록 필수! -->
    <bean id="leaveaTrace"
    	class="org.egovframe.rte.fdl.cmmn.trace.LeaveaTrace">
    	<property name="traceHandlerServices">
    		<list>
    			<ref bean="traceHandlerService" />
    		</list>
    	</property>
    </bean>
    <bean id="traceHandlerService"
    	class="org.egovframe.rte.fdl.cmmn.trace.manager.DefaultTraceHandleManager">
    	<property name="reqExpMatcher">
    		<ref bean="antPathMater" />
    	</property>
    	<property name="patterns">
    		<list>
    			<value>*</value>
    		</list>
    	</property>
    	<property name="handlers">
    		<list>
    			<ref bean="defaultTraceHandler" />
    		</list>
    	</property>
    </bean>
    <bean id="antPathMater"
    	class="org.springframework.util.AntPathMatcher" />
    <bean id="defaultTraceHandler"
    	class="org.egovframe.rte.fdl.cmmn.trace.handler.DefaultTraceHandler" />
    ```
    </div>
    </details>

  - **leaveaTrace 메소드 적용 예시:**

  - ```java
    //EgovAbstractServiceImpl를 상속한 서비스로직이라 가정
    try{
        //비즈니스 로직
    }catch {
        //fail.common.msg=에러가 발생했습니다! 라고 message.properties에 있음
        leaveaTrace("fail.common.msg"); 
    }
    ```

- **processException -> 예외들을 EgovBizException 예외로 바꿈 + 로그 생성(메시지소스를 사용)**

  - 메소드 원리: 발생한 예외를 EgovBizException 로 감싸는 방식

  - MessageSource 빈에 등록된 메시지를 로그(Info)로 제공

  - ```java
    //EgovAbstractServiceImpl를 상속한 서비스로직이라 가정
    try{
        int i = 1/0;
    }catch(ArithmeticException ae){
        throw processException("fail.common.msg");
    }
    ```

- **예시 결과:**

  <img src="/images/2024-10-17-MyBatis+Spring+Boot+eGov 파트 리팩토링/image-20250406231902976.png" alt="image-20250406231902976" style="zoom:80%;" />

**컨트롤러계층(+AOP)의 예외 처리:**

> 웹 플젝 스프링 부트는 ErrorPage, BasicErrorController를 자동등록하여 예외 핸들링 에러페이지 자동
>
> API는 부트든 아니든 @ExceptionHandelr + @ControllerAdvice로 예외 공통 관리 및 JSON 응답 젤 편함!

- 웹 플젝 eGov에서는 부트가 한 **에러페이지** 자동등록 설정을 SimpleMappingExceptionResolver빈 등록으로 직접 하는편. (리졸버뷰를 생각하자)

  - **jsp에도 MessageSource 빈의 메시지가 출력이 됨**.(exception.message로 서버상에서 MessageSource 빈 메시지를 기록해둔다고 예상)

  - <details><summary><b>xml 설정과 error-page(jsp) 코드 예시:</b></summary>
    <div markdown="1"><br>
    **context-servlet.xml (컨트롤러단 계층용 설정파일)**
    ```xml
    <!-- 직접 원하는 Exception과 에러뷰를 매핑 확장 가능 -->
    <bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
        <property name="defaultErrorView" value="jsp/cmmn/genneralException" />
        <property name="exceptionMappings">
            <props>
                <prop key="org.egovframe.rte.fdl.cmmn.exception.EgovBizException">jsp/cmmn/egovBizException</prop>
                <prop key="org.springframework.dao.DataAccessException">jsp/cmmn/egovBizException</prop>
                <prop key="org.springframework.transaction.TransactionException">jsp/cmmn/egovBizException</prop>
            </props>
        </property>
    </bean>
    ```
    **egovBizException.jsp**
    ```jsp
    <%@ page language="java" contentType="text/html; charset=UTF-8" %>
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>에러가 발생하였습니다.종류는 EgovBizException 입니다.</title>
    </head>
    <body>
    에러가 발생하였습니다.종류는 EgovBizException 입니다. 메세지는${exception.message} 입니다.
    </body>
    </html>
    ```
    **genneralException.jsp**
    ```jsp
    <%@ page language="java" contentType="text/html; charset=UTF-8" %>
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>일반적인 에러가 발생하였습니다.</title>
    </head>
    <body>
    일반적인 에러가 발생하였습니다. 메세지는${exception.message} 입니다.
    </body>
    </html>
    ```
    </div>
    </details>

- @ExceptionHandelr 로 예외 공통 관리하던걸 **"eGov의 ExceptionHandler인터페이스"**를 구현하여 **AOP를 직접 설정**하자. (예시 부분의 콘솔을 봐라)

  - **주의:** "부트"(순수스프링 아님)에선 자동으로 예외처리기를 제공하다보니 아래 AOP적용한 예외처리기와 중복 사용될 수 있다. (로그상에서 예외가 1번더 출력됨을 확인)

  - <details><summary><b>xml 설정과 ExceptionHandler 구현 코드 예시:</b></summary>
    <div markdown="1"><br>
    **ExceptionHandler 구현 코드 java**
    ```java
    @Slf4j
    public class SecretGalleryExceptionHandler implements ExceptionHandler {
        public void occur(Exception exception, String packageName) {
        	log.debug(" EasyCompanyExceptionHandler run...............{}", ((EgovBizException) exception).getWrappedException());
            try {
                if (exception instanceof EgovBizException) {
                    Exception exx = (Exception) ((EgovBizException) exception).getWrappedException();
                    if (exx != null) {
                    	log.debug(" sending a alert mail  is completed ");
                        exx.printStackTrace();
                    }
                }
            } catch (Exception e) { //일반 예외
                e.printStackTrace();
            }
        }
    }
    ```
    **context-aspect.xml**
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    	xmlns:aop="http://www.springframework.org/schema/aop"
    	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
    		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd">
    <!-- -->
    	<aop:config>
    		<aop:pointcut id="serviceMethod" expression="execution(* com.secretgallery..impl.*Impl.*(..))" />
    		<aop:aspect ref="exceptionTransfer">
    			<aop:after-throwing throwing="exception" pointcut-ref="serviceMethod" method="transfer" />
    		</aop:aspect>
    	</aop:config>
    <!-- -->
    	<bean id="exceptionTransfer" class="org.egovframe.rte.fdl.cmmn.aspect.ExceptionTransfer">
    		<property name="exceptionHandlerService">
    			<list>
    				<ref bean="defaultExceptionHandleManager" />
    			</list>
    		</property>
    	</bean>
    <!-- -->
    	<bean id="defaultExceptionHandleManager" class="org.egovframe.rte.fdl.cmmn.exception.manager.DefaultExceptionHandleManager">
    		<property name="reqExpMatcher">
    			<ref bean="antPathMater" />
    		</property>
    		<property name="patterns">
    			<list>
    				<value>**service.impl.**</value>
    			</list>
    		</property>
    		<property name="handlers">
    			<list>
    				<ref bean="exceptionHandler" />
    			</list>
    		</property>
    	</bean>
    <!-- -->
    	<bean id="exceptionHandler" class="com.secretgallery.exception.SecretGalleryExceptionHandler" />
    </beans>
    ```
    </div>
    </details>

  - **사용예시 코드:**

    등록한 EasyCompanyExceptionHandler빈을 통해 공통으로 예외처리 관리 가능

    ```java
    //EgovBizException 관련 공통예외처리 로직 수행 (ExceptionHandler 구현로직 참고)
    throw processException("msg.exception.case1", new RuntimeException("실제 원인"));
    //일반 공통예외처리 로직 수행 (ExceptionHandler 구현로직 참고)
    throw new Exception(); 
    ...
    ```

    - 특히, "msg.exception.case1=에러테스트를 위한 에러를 발생시킵니다." 으로 기록된 메시지를 JSP에 출력

- **전체적 흐름 이해하기:** 

  1. 서비스계층의 leaveaTrace, processException 를 적절히 활용하여 예외 처리하고 싶은부분에 사용(leaveaTrace빈이 잘 동작)

     예외 정상으로? leaveaTrace  
     예외 EgovBizException 으로 바꾸려면? processException   
     예외 일반은? 그냥 원하는 예외로 throw 하거나 그냥 둬도 AOP에서 잘 처리

  2. 컨트롤러,AOP단에서 exceptionTransfer빈이 동작하여 예외를 가로채고 EasyCompanyExceptionHandler빈으로 공통으로 예외처리(보통 콘솔에 메시지 출력)

  3. 컨트롤러,AOP단에서 SimpleMappingExceptionResolver빈이 동작하여 예외일때 리졸버뷰로써 잘 동작

     예외 EgovBizException은? 등록한 egovBizException.jsp 출력  
     예외 DataAccessException은? 등록한 egovBizException.jsp 출력   
     예외 TransactionException은? 등록한 egovBizException.jsp 출력  
     예외 일반은? 등록한 genneralException.jsp 출력

- **예시 결과:**

  홈페이지

  <img src="/images/2024-10-17-MyBatis+Spring+Boot+eGov 파트 리팩토링/image-20250407001802824.png" alt="image-20250407001802824" style="zoom:80%;" />

  콘솔

  <img src="/images/2024-10-17-MyBatis+Spring+Boot+eGov 파트 리팩토링/image-20250407001849320.png" alt="image-20250407001849320" style="zoom:80%;" />

**번외) 메소드 수행시간 측정 AOP는 Java Config로 해보겠다.**

- <details><summary><b>TimeTraceAop.java 코드 예시:</b></summary>
  <div markdown="1"><br>
  ```java
  @Aspect // AOP
  @Component // "빈" 등록
  @Slf4j
  public class TimeTraceAop {
    @Around("execution(* com.secretgallery..*(..)) && !within(com.secretgallery.security..*)")
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
      // 프록시 실행
      long start = System.currentTimeMillis();
      log.debug("START: {}", joinPoint.toString());
      try {
        return joinPoint.proceed(); // 실제 실행
      } finally {
        long finish = System.currentTimeMillis();
        long timeMs = finish - start;
        log.debug("END: {} {}ms", joinPoint.toString(), timeMs);
      }
    }
  }
  ```
  </div>
  </details>

<br><br>

### "공통컴포넌트", "DBIO Editor", "운영환경", "배치"

<details><summary><b>표준프레임워크는 "실행,개발,관리,운영" 4개의 환경과 "모바일, 공통컴포넌트"로 구성</b></summary>
<div markdown="1"><br>
<img src="https://github.com/user-attachments/assets/4ac6060d-de89-438b-9005-b55a31ff681a" alt="Image" style="zoom:80%;" /><br> **표준프레임워크 실행환경**<br>
<img src="https://github.com/user-attachments/assets/18e66285-25bd-446a-b204-fb3ab69d4b45" alt="Image" style="zoom:80%;" /><br>
**표준프레임워크 개발환경**<br>
<img src="https://github.com/user-attachments/assets/da0e76ab-8bc2-4869-b5c6-db45164bac52" alt="Image" style="zoom:80%;" /><br>
**표준프레임워크 관리환경**<br>
<img src="https://github.com/user-attachments/assets/7e167e44-8cfa-42ab-af5c-8f4b92c70638" alt="Image" style="zoom:80%;" /><br>
**표준프레임워크 운영환경**<br>
<img src="https://github.com/user-attachments/assets/b9aca047-073b-40ae-91a4-eb990daa61fe" alt="Image" style="zoom:80%;" /><br>
**공통컴포넌트**<br>
<img src="https://github.com/user-attachments/assets/25aa899f-6100-48a7-a58f-3f154e0678ba" alt="Image" style="zoom:80%;" /><br>
**모바일 표준프레임워크**<br>
<img src="https://github.com/user-attachments/assets/9feaf183-2d6a-43bc-b4bc-023f851b0c6d" alt="Image" style="zoom:80%;" />
</div>
</details>


<br>

이클립스의 기능에 로그인쪽(게시판 등?) **"공통컴포넌트" 이용 + Spring Security(공통기능)**도 표준프레임워크로 [security](https://arckwon.tistory.com/entry/%EC%A0%84%EC%9E%90%EC%A0%95%EB%B6%80%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%AC-%EC%8A%A4%ED%94%84%EB%A7%81%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0-%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EC%A0%81%EC%9A%A912), [암호화](https://arckwon.tistory.com/entry/%EC%A0%84%EC%9E%90%EC%A0%95%EB%B6%80%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%AC-DB%EC%A0%91%EC%86%8D%EC%A0%95%EB%B3%B4-%EC%95%94%ED%98%B8%ED%99%94-crypto-%EC%84%9C%EB%B9%84%EC%8A%A4), [공문](https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:rte4.0:fdl:server_security) 참고!  

- 프로젝트 우클릭> New > eGovFrame Common Component > 에 위치
- 자동 코드 예시를 제공

<br>

<details><summary><b>DBIO Editor 예시:</b></summary>
<div markdown="1"><br>
1. DB실행
2. DBIO 실습(자세히는 PDF)
   - Mapper Configuration 파일 생성(sample_config.xml): 프로젝트 우클릭 > New > mapperConfiguration
   - Mapper 파일 생성(sample_map.xml):  Mapper Configuration Editor > New
   - Mapper 파일 편집 -> mapper 에디터를 활용!<br>
     **에디터로 간단히 설정하는데 "xml코드가 자동 생성되는 편리!!"**
     1. Result Map 작성: Mapper Editor> ResultMap 우클릭> Add resultMap
     2. Query 작성: Mapper Editor> Query 우클릭> Add Select Query
   - 자동생성 코드 예시(sample_map.xml):
     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
     <mapper ><resultMap id="resultMap" type="java.lang.String"><result property="deptName" column="DEPT_NAME"/>
     </resultMap>
     <select id="selectDept" parameterType="java.lang.String" resultMap="resultMap">
     SELECT DEPT_NAME
       FROM PUBLIC.DEPT
       WHERE DEPT_NO = #{deptNo}
     </select>
     </mapper>
     ```
3. Query  테스트
   <img src="https://github.com/user-attachments/assets/66c16c69-5daa-4854-9fc4-4df12ad1254e" alt="Image" style="zoom:80%;" />
</div>
</details>


<br>

이클립스의 기능에 01.개발환경_교육교재.pdf에 **Jenkins(CI)** 이거 pdf 참고하기

<br>

**Spring Batch**는 "eGov 가이드 학습하기" 게시물이랑 "프로젝트 코드" 참고

<br>

<br>
