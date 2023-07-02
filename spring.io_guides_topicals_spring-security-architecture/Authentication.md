	인증을 위한 주된 전략 interface는 AuthenticationManager이다. 오직 하나의 method만 가지고 있다.
```java
public interface AuthenticationManager {
	Authentication authenticate(Authentication authentication)
		throws AuthenticationException;
}
```
Authentication은 authenticate() method로 3가지중 하나를 할 수 있다.
- 만약 확인된 주체(principal)를 대표하는 입력을 검증할 수 있다면 Authentication을 반환한다.(보통 authenticated=true다.)
- 만약 검증되지 않은(검증에 실패한에 더 가까운 의미) 주체(principal)를 대표하는 입력을 신뢰한다면 AuthenticationException을 던져라(원문:Throw)
- 만약 결정하지 못했다면 null을 반환하라
AuthenticationException은 Runtime 예외이다. 그건 보통 어플리케이션의 목적이나 스타일에 의존되는 포괄적인 방법에 어플리케이션에 의해 처리된다. 다른 말로, user code는 보통 잡기 위해 기대하지 않고 그걸 처리한다. 예를 들어, 웹 UI는 인증이 실패한 것을 말하는 페이지를 렌더링할 수 있고, backend HTTP Service가 내용에 의존하는 WWW-Authenticate 헤더를 포함하거나 포함되지 않는 401(Unauthentication) 응답을 보낼 수 있다.
대부분 AuthenticationManager의 구현체로 사용되는 것은 ProviderManger다. 어느것이AuthenticationProvider 인스턴스의 체인에 위임할 것인가. AuthenticationProvider는 AuthenticationManager와 조금 닮았지만 Authentication 타입을 지원하는지 아닌지 호출자에 질문하는 것을 허락하는 method를 추가로 가지고 있습니다.
```java
public interface AuthenticationProvider {
	Authentication authenticate(Authentication authentication)
		throws AuthenticationException;
		
	boolean supports(Class<?> authentication);
}
```
supports method의 Class\<?\> 인자는 사실 Class\<? extends Authentication\> 다. (만약 authenticate() method안에 통과되는 것을 지원하면 단 한번뿐만 질문한다.) ProviderManager는 AuthenticationProviders의 체인에 위임해 같은 어플리케이션에서 종류별로 다양한 인증 메커니즘을 지원할 수 있다. 만약 ProviderManager가 특별한 Authentication 인스턴스 형식을 인정하지 않으면, 생략된다.
ProviderManager는 만약 모든 공급자가 null을 반환하면 상의할 수 있는 선택적 부모(부모클래스)를 가진다. 만약 부모를 이용할 수 없다면, null Authentication은 AuthenticationException로 끝난다.
가끔, 어플리케이션은 보호되는 자원(예를 들어, 경로 패턴에 맞는 모든 web 자원, "/api/\*\*와 같은것들")의 논리 그룹을 가지고 있고 각 그룹은 전용 AuthenticationManager를 가진다. 종종, 그것들 각각은 ProviderManager이고 그것들은 부모와 공유한다. 부모는 모든 공급자를 위한 대체 시스템으로써 행동하는 "global" 자원의 일종이다.
![[Pasted image 20230210013013.png]]
그림 1. AuthenticationManager 계층제에서 ProviderManager가 사용되는 것

### Customizing Authentication Managers
Spring Security는 설정 helper가 authentication manager 특징을 너의 어플리케이션 내에 설치하는걸 빠르게 가져오는 기능을 제공한다. 거의 흔하게 사용되는 helper는 AuthenticationBuilder이다. 그건 메모리 내부에 JDBC 혹은 LDAP 사용자 세부사항, 커스텀 UserDetailsService를 추가하기 위해 설치하는 것에 적합하다. 예를들어 상위(global) AuthenticationManager를 설정하는 어플리케이션을보여준다.
```java
@Configuration
public class ApplicationSecurity extends WebSecurityConfigurerAdapter {
	...// 웹 설정을 여기에
	
	@Autowired
		public void initialize(AuthenticationManagerBuilder builder, DataSource dataSource) {
		builder
			.jdbcAuthentication()
			.dataSource(dataSource)
			.withUser("dave")
			.password("secret")
			.roles("USER")
	}
}
```
이 예제는 웹 어플리케이션에 관련이 있다, 하지만 AuthenticationManagerBuilder의 사용은 더 넓게 적용가능하다.(어떻게 웹 어플리케이션 security가 구현되는지에 더 많은 세부사항에 대해서 Web Security를 봐라) AuthenticationManagerBuilder가 @Bean(이를 통해 상위(global) AuthenticationManager를 구축할수있음) 내에 method