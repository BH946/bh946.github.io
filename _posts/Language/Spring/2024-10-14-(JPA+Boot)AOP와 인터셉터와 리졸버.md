---
title:  "(JPA+Boot)AOP와 인터셉터와 리졸버"
categories : Spring
tag : [스프링AOP,인터셉터,ArgumentResolver,공통관심사,프록시패턴]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**스프링 프레임워크에서 인터셉터, ArgumentResolver, AOP를 활용하여 웹 애플리케이션의 공통 관심사를 효과적으로 분리하고 처리하는 방법과 각 기술의 동작 원리 및 구현 패턴을 상세히 설명하는 가이드입니다.**

<br><br>

### 인터셉터, 리졸버, AOP

요청 흐름: requset(HTTP) > Filter(서블릿필터) > Servlet(디스패처서블릿) > **Interceptor > Argument Resolver > AOP** > Controller

<br>

**AOP(공통 해결 관심사): 관점지향프로그래밍**

- 동작흐름 : **프록시 객체 생성 -> 실제 객체 생성 의 흐름**

- 만들 때 : **@Aspect** 사용 및 "**스프링 빈** 등록 필수"

- 사용할 때 : **@Around**로 원하는 곳에 적용

  - **@Around**는 AOP의 어드바이스 타입 중 하나, **메서드 실행 전후** 로직 실행 지원

  - **proceed()** 를 통해 원하는 시점 때 **"실제 실행"**을 지원 -> AOP 말고 실제 메서드


<details><summary><b>AOP 예시 코드 - 메소드 수행시간 기능</b></summary>
<div markdown="1"><br>
```java
@Aspect // AOP
@Component // "빈" 등록
@Slf4j
public class TimeTraceAop {
  @Around("execution(* com.lepl..*(..))")
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


<br>

"서블릿 필터" 보다는 **“Interceptor” 와 "ArgumentResolver" 활용 권장**

- **ArgumentResolver** 를 통해서 공통 작업할 때 컨트롤러를 더욱 편리하게 사용 가능 (**컨트롤러 메서드의 매개변수에 적절한 값을 바인딩**하는 과정을 담당)
  - 서블릿(=Dispatcher Servlet) 과 컨트롤러(핸들러) 사이에는 사실 핸들러 어댑터가 동작을 하며, 이때 “**ArgumentResolver**” 가 중간에 있어서 이를 거치고 “**컨트롤러(핸들러)**” 가 동작

  - **ArgumentResolver 덕분에 수많은 "애노테이션" 속 데이터들을 컨트롤러(핸들러)로 정상 전달**
    - EX) **@Login** 애노테이션 만들어서 “멤버 정보” 객체 반환 이런게 가능!

    - **기존** : `@SessionAttribute(name = "loginMember", required = false) Member loginMember`

    - **적용** : `@Login Member loginMember`
- “**Interceptor**” 는 “필터” 보다 더 많이 호출 (**컨트롤러 도달 전과 후에 공통 수행**)
  - “로그인 인증” 을 예시로 구현 가능
    - `preHandle` 에 “세션정보(쿠키)” 인증을 시도 및 실패시 다시 “로그인 창” 으로 이동
      - 이를 다양한 **URL 패턴으로 적용 가능** -> 적용URL, 미적용URL 구분도 간편
    - 컨트롤러 도달 후인 postHandle() 도 있다는 것만 기억.

아래 코드들은 `WebMvcConfigurer` 를 구현한 **Java Config 방식** 포함

<details><summary><b>예시코드 - ArgumentResolver</b></summary>
<div markdown="1"><br>
Login.java(인터페이스) -> @Login 애노테이션 생성
```java
@Target(ElementType.PARAMETER) // 파라미터에 사용
@Retention(RetentionPolicy.RUNTIME) // RUNTIME 까지 살아남게 설정
public @interface Login {
}
```
LoginMemberArgumentResolver.java -> 애노테이션이 @Login이고, 타입이 Long인지 체크 후 세션에 있는 memberId 반환해주는 로직
```java
@Slf4j
public class LoginMemberArgumentResolver implements HandlerMethodArgumentResolver {
//
  @Override
  public boolean supportsParameter(MethodParameter parameter) {
    log.info("supportsParameter 실행");
//
    boolean hasLoginAnnotation = parameter.hasParameterAnnotation(Login.class);
    boolean hasLongType = Long.class.isAssignableFrom(parameter.getParameterType());
    // 어노테이션이 @Login 이고, 해당 파라미터 타입이 Long 라면 true 반환
    return hasLoginAnnotation && hasLongType;
  }
//
  // 위 supportsParameter 가 true 면 아래 함수가 실행
  @Override
  public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer,
      NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
    log.info("resolveArgument 실행");
//
    HttpServletRequest request = (HttpServletRequest) webRequest.getNativeRequest();
    HttpSession session = request.getSession(false); // false : 없으면 null
    if (session == null) {
      log.info("null");
      return null;
    }
    Long memberId = Long.valueOf(session.getAttribute(SESSION_NAME_LOGIN).toString());
    return memberId;
  }
}
```
ApiConfig.java -> ArgumentResolver 등록 방법
```java
@Configuration // 설정 파일임을 알림
@Slf4j
public class ApiConfig implements WebMvcConfigurer {
//
  @Override
  public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
    resolvers.add(new LoginMemberArgumentResolver());
  }
}
```
</div>
</details>


<details><summary><b>예시코드 - Interceptor</b></summary>
<div markdown="1"><br>
MemberCheckInterceptor.java -> 로그인 인증 유무 체크 로직
```java
@Slf4j // log
public class MemberCheckInterceptor implements HandlerInterceptor {
//
  @Override
  public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
      throws Exception {
    String requestURI = request.getRequestURI();
    log.info("인증 체크 인터셉터 실행 {}", requestURI);
//
    HttpSession session = request.getSession();
    if (session == null || session.getAttribute(SESSION_NAME_LOGIN) == null) {
      log.info("미인증 사용자 요청");
      // 회원 아님을 알림
      response.setStatus(HttpStatus.UNAUTHORIZED.value()); // status code : 401
      return false;
    }
    return true;
  }
}
```
ApiConfig.java -> Interceptor 적용법 + 인증 URL과 미인증 URL 설정 로직
```java
@Configuration // 설정 파일임을 알림
@Slf4j
public class ApiConfig implements WebMvcConfigurer {
//
  @Override
  public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(new MemberCheckInterceptor())
        .order(2)
        .addPathPatterns("/**") // 모든 경로 접근
        .excludePathPatterns("/", "/api/v1/members/login", "/api/v1/members/register",
            "/api/v1/members/logout", "/api/v1/members/*",
            "/image/**", "/css/**", "/*.ico", "/error"); // 제외 경로!
  }
}
```
</div>
</details>


<br><br>
