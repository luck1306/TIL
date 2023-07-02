
이 섹션에서는 Spring Security의 Servlet 기반 애플리케이션 내 고급 아키텍처에 대해 설명합니다. 우리는 참조의 인증, 권한 부여 및 악용 방지 섹션 내에서 이러한 높은 수준의 이해를 바탕으로 합니다.

### A Review of Filters
스프링 시큐리티의 서블릿 지원은 서블릿 필터를 기반으로 하므로 일반적으로 먼저 필터의 역할을 살펴보는 것이 도움이 됩니다. 다음 이미지는 단일 HTTP 요청에 대한 핸들러의 일반적인 계층화를 보여줍니다.

![[Pasted image 20230109005543.png]]
클라이언트가 응용 프로그램에 요청을 보내고 컨테이너는 요청 URI의 경로를 기반으로 HttpServletRequest를 처리해야 하는 Filter instances와 Servlet을 포함하는 Filter Chain을 만듭니다. 스프링 MVC 응용 프로그램에서 Servlet은 DispatcherServlet의 인스턴스입니다. 최대 하나의 Servlet이 하나의 HttpServletRequest와 HttpServletResponse를 처리할 수 있지만 한 개의 Filter 보다 더 사용될 수 있다:
- 하위 Filter instances나 Servlet에서 호출되지 않게 보호한다. 이 경우에, Filter는 보통 HttpServletResponse를 기록한다.
- HttpServletRequest와 HttpServlerResponse가 하위의 Filter instances와 Servlert에서 사용되게 수정한다.
Filter의 힘은 Filter를 통과시켜준 FilterChain으로 부터 나온다.

```java
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
	chain.doFilter(request, response);
}
```
Filter는 하위 Filter instances와 Servlet에만 영향을 미치기 때문에 각 Filter가 호출되는 순서가 매우 중요합니다.

### DelegatingFilterProxy
Spring은 Servlet 컨테이너의 생애주기와 Spring의 ApplicationContext 사이의 연결을 허락하는DelegatingFilterProxy라는 이름의 Filter 실행을 제공한다. Servlet 컨테이너는 Filter instances의 독자적인 기준을 사용하는 것에 관해서 Filter instances의 등록을 허용하지만, 정의된 스프링 Beans을 알지 못한다. 너는 표준 Servlet 컨테이너의 메커니즘을 통해 DelegatingFilterProxy를 등록할 수 있지만 모든 일을 Filter를 구현한 Spring Bean에 위임할 수 있다.

이건 어떻게 DelegatingFilterProxy가 Filter instances와 FilterChain에 맞는지의 사진이다.
![[Pasted image 20230109020120.png]]
DelegatingFilterProxy는 ApplicationContext로 부터 Bean Filter(0)를 찾아보고 그리고나서 Bean Filter(0)를 실행시킨다. 아래의 목록은 DelegatingFilterProxy의 의사 코드를 보여준다:

```java
public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) { 
// 여유롭게 Spring Bean으로써 등록된 Filter를 가져온다.
// 예를 들어 DelegatingFilterProxy 
// 아래에 기술된 delegate는 Bean Filter(0)의 instance다. 
	Filter delegate = getFilterBean("{someBeanName:BeanFilter_0}"); 
// delegate는 Spring Bean을 위해 일한다. 
	delegate.doFilter(req, res); 
}
```

DelegatingFilterProxy의 또 다른 장점으로는 Filter bean instances의 탐색 지연을 허락하는 것이다.컨테이너는 컨테이너 시작 전에 Filter instances를 등록하는 것이 필요하기 때문에 중요하다. 그러나 Spring은 보통 Filter instances를 등록해야 할 때 까지 미완성인 Spring Beans를 로드하기 위해 ContextLoaderListener를 사용한다.

### FilterChainProxy
Spring Security의 Servlet 지원은 FilterChainProxy와 함께 포함된다. FilterChainProxy는 특별한 Filter가 SecurityFilterChain을 통해 많은 Filter instances를 위임하는 것을 허락한 Spring Security에 의해 공급된다. FilterChainProxy가 Bean이었을때 부터 그것은 보통 DelegatingFilterProxy에게 감싸진다.

아래 사진은 FilterChainProxy의 역할을 보여준다.
![[Pasted image 20230109190531.png]]

### SecurityFilterChain
SecurityFilterChain은 Spring Security Filter instances가 현재 요청을 위해 실행되야 하는 것을 결정하는 FilterChainProxy에 의해 사용된다.

아래 사진은 SecurityFilterChain의 역할을 보여준다.
![[Pasted image 20230109191215.png]]

SecurityFilterChain의 Security Filters는 보통 Bean이지만 Security Filters는 DelegatingFilterProxy 대신에 FilterChainProxy와 함께 등록된다. FilterChainProxy는 Servlet 컨테이너나 DelegatingFilterProxy로 직접적으로 등록하는것에 많은 이점을 제공한다. 첫째로, FilterChainProxy는 Spring Security의 Servlet 지원의 모든 것을 위해 시작 지점을 제공한다. 그런 이유로 만약 네가 Spring Security의 Servlet 지원 문제를 해결하기 위해 노력한다면, FilterChainProxy에 debug point를 추가하는 것이 시작하기 좋은 지점이다.

둘째로, FilterChainProxy는 Spring Security 사용의 중심이기 때문에 선택 사항으로 간주되지 않는 작업을 수행할 수 있습니다. 예를 들어, 메모리 누수를 방지하기 위해 SecurityContext를 처리할 수 있다. 또한 응용 프로그램들이 특정 유형의 공격으로부터 지키기 위해 Spring Security의 Http방화벽을 적용한다.

추가로, SecurityFilterChain이 실행되야 할 때 더 유연한 결정을 제공한다. Servlet 컨테이너에서, Filter instances는 URI에 기반을 둬 혼자 호출된다. 그러나 FilterChainProxy는 RequestMatcher interface를 사용하는 것에 의해 HttpServletRequest의 어떤 것에 기반을 둬서 호출을 결정할 수 있다.

아래의 사진이 다중 SecurityFilterChain instances를 보여준다:
![[Pasted image 20230109221732.png]]
위의 그림에서, FilterChainProxy는 어느 SecurityFilterChain이 사용되야 하는지 결정해야한다. 오직 매치가 발동되는 첫번째 SecurityFilterChain이다. 만약 "/api/messages"의 URL이 요청되면 그게 만약 먼저 /api/\*\*의 SecurityFilterChain(0) 패턴과 일치하면 따라서 SecurityFilterChain(0)만 호출되며 SecurityFilterChain(n)에서도 일치합니다. 만약 "/messages/"의 URL이 요청되면, "/api/\*\*"의 패턴 SecurityFilterChain(0)에 일치하지 않습니다, 그래서 FilterChainProxy는 각각의 SecurityFilterChain을 시도하는 것을 번복합니다. 다른 SecurityFilterChain의 instance들이 일치하지 않음을 가정하면, SecurityFilterChain(n)이 호출된다.

오직 세 개의 Security Filter instance로 구성되고 SecurityFilterChain(0)을 가진다. 그러나 네 개의 Security Filter instances로 구성된 SecurityFilterChain(n)을 가진다. 각각의 SecurityFilterChain이 고유할 수 있고 별도로 구성될 수 있는 점이 중요하다. 사실 SecurityFilterChain은 만약 응용 프로그램이 Spring Security가 특정 요청을 무시하기를 바란다면 Security Filter instance가 있어서는 안된다.

### Security Filters
Security Filter들은 SecurityFilterChain API와 함께 FilterChainProxy안에 삽입된다. Filter instances의 순서가 중요하다. 보통 Spring Security의 Filter instances의 순서를 아는 것이 중요하지 않다. 그러나 순서를 아는 장점이 몇번 있다.(알고있으면 좋다.)

### Handing Security Exceotions
ExceptionTranslationFilter가 Http 응답의 AuthenticationException과 AccessDeniedException의 번역을 허락한다. ExceptionTranslationFilter는 Security Filters 중에 하나로써 FilterChainProxy안에 삽입된다.
아래 사진은 다른 요소에 ExceptionTranslationFilter의 관계를 보여준다.
![[Pasted image 20230110023429.png]]

- ① 첫 번째, ExceptonTranslationFilter는 나머지 응용 프로그램을 호출하는 FilterChain.doFilter(req, res)를 호출한다.
- ② 만약 사용자가 인증되지 않았거나 AuthenticationException일 때, 인증을 시작한다.
	- SecurityContextHolder가 비워진다.
	- 인증이 성공하면 원래 요청을 재생하는 데 사용할 수 있도록 HttpServletRequest가 저장됨
- AuthenticationEntryPoint는 클라이언트로 부터 자격증명 요청을 하는데 시용된다.
	- ③그렇지 안으면, 만약 이게 AccessDeniedException이라면, 거부된 권한이다. AccessDeniedHandler는 거부된 권한을 처리하기 위해 호출된다.

__만약 응용프로그램이 AccessDeniedException이나 AuthenticationException을 던지지 않으면, ExceptionTranslationFilter가 아무것도 하지 않는다.__

ExceptionTranslationFilter를 위한 의사코드는 다음과 같다:
```java
try {
	filterChain.doFilter(request, response); // ①
} catch (AccessDeniedException | AuthenticationException ex) {
	if(!authenticated || ex instanceof AuthenticationException) startAuthentication(); // ②
	else accessDenied(); // ③
}
```

1. A Review of Filters에서 기술한 바와 같이, FilterChain.doFilter(request, response)를 호출하는 것은 나머지 응용 프로그램을 호출하는 것의 환경이다.
2. 만약 사용자가 인증되지 않거나 AuthenticationException이면, 인증을 시작한다.
3. 그렇지 않으면, 거부된 권한

### Saving Requests Between Authentication
Handling Security Exceptions에 실증된 바와 같이 요청이 인증을 가지고있지 않고 인증을 필요로하는 자원을 위할 때, 인증이 성공적이게 된 후에 인증된 자원에 재요청에 대한 요청을 저장하는 것이 필요하다. Spring Security에서 HttpServletRequest에게 RequestCache 구현체를 사용하기를 저장하는 것으로 인해 완료된다.

#### RequestCache
HttpServletRequest는 RequestCache에 저장된다. 사용자가 성공적으로 인증될 때, RequestCache는 원본의 요청을 재전송하기 위해 사용된다. RequestCacheAwareFilter는 HttpServletRequest를 저장하는 RequestCache를 사용하는 것이다.

기본적으로, HttpSessionRequestCache가 사용된다. 아래 코드에서 만약 매개변수 현재인 continue라 불리면 요청을 저장하기 위해서 HttpServlet을 확인하기 위해 사용되는 RequestCache의 구현체를 주문 제작하는 방법을 입증한다
```java
@Bean
DefaultSecurityFilterChain springSecurity(HttpSecurity http) throws Exception {
	HttpSessionRequestCache requestCache = new HttpSessionRequestCache();
	requestCache.setMatchingRequestParameterName("continue");
	http
		// ...
		.requestCache((cache) -> cache
			.requestCache(requestCache)
		);
	return http.build();
}
```

#### RequestCacheAwareFilter
ReqeustCahceAwareFilter는 HttpServletRequest를 저장하는 RequestCahe를 사용한다.

_본 문서는 spring 공식문서를 참조합니다._