# Spring Boot 로그인 & 회원가입 복습

### 매 요청 마다 실행되는 로직
spring boot application을 가동하면 여러 요청을 받을 수 있다.
하지만 바로 요청을 받는 것이 아닌 여러 단계에 걸쳐 요청을 받을 수 있는데
그 중 Filter라는 것도 거쳐 지나가게 된다.

로그인 회원가입을 하기 위한 Filter를 만들 때 매 요청마다 거치는 Filter를 만들 것이다.
```java
// JwtFilter
@RequiredArgsConstructor
public class JwtFilter extends OncePerRequestFilter {

    public static final String AUTHORIZATION_HEADER = "Authorization";
    public static final String BEARER_PREFIX = "Bearer ";

    private final TokenProvider tokenProvider;

    // 실제 필터링 로직은 doFilterInternal 에 들어감
    // JWT 토큰의 인증 정보를 현재 쓰레드의 SecurityContext 에 저장하는 역할 수행
    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws IOException, ServletException {

        // 1. Request Header 에서 토큰을 꺼냄
        String jwt = resolveToken(request);

        // 2. validateToken 으로 토큰 유효성 검사
        // 정상 토큰이면 해당 토큰으로 Authentication 을 가져와서 SecurityContext 에 저장
        if (StringUtils.hasText(jwt) && tokenProvider.validateToken(jwt)) {
            Authentication authentication = tokenProvider.getAuthentication(jwt);
            SecurityContextHolder.getContext().setAuthentication(authentication);
        }

        filterChain.doFilter(request, response);
    }

    // Request Header 에서 토큰 정보를 꺼내오기
    private String resolveToken(HttpServletRequest request) {
        String bearerToken = request.getHeader(AUTHORIZATION_HEADER);
        if (StringUtils.hasText(bearerToken) && bearerToken.startsWith(BEARER_PREFIX)) {
            return bearerToken.substring(7);
        }
        return null;
    }
}
```
위 코드중 doFilterInternal method는 매 요청마다 실행되게 될 것이다.
위 코드를 간단히 설명하면 로그인 되었을 시 매 요청마다 header에 token을 보내는데
그 토큰을 SecurityContext라는 곳에 저장하기 위한 Filter이다.

이 클래스를 적용시켜 실제 매 요청 마다 SecurityContext에 정보를 저장하기 위해서는 WebSecurityConfigurerAdapter라는 class를 상속받은 SecurityConfig class에 명시적으로 적용을 해줘야 한다.

하지만 SecurityConfig에서 바로 Filter를 설정할 수 있는 것이 아니라 Filter를 등록할 수 있게 하는 또 다른 설정 class가 있어야 한다.
class의 이름은 상관 없지만 SecurityConfigAdapter를 상속받고 만든 Filter를 UsernamePasswordAuthenticationFilter 앞에 등록시키는 기능이 있게 만든다.

**여기 까지가 특수한 기능이 아닌 요청 마다 확인해야 하는 기능에 관해 기술하였다.**

---

지금 부터는 회원가입, 로그인, 토큰 재발급, 로그아웃에 대해 기술할 것이다.

### 회원가입 기능


**회원가입은 뒤에 나올 기능보다는 상대적으로 익숙한 로직으로 진행된다.**
- Post 요청으로 회원가입에 필요한 정보들을 받아오고 DB에 입력받은 정보와 같은 칼럼을 찾는다.
- 같은 칼럼이 있다면 이미 가입되어 있는 사용자임을 알리고 예외를 발생시킨다.
- 같은 칼럼이 없다면 엔티티 객체로 만들어 저장한다.

### 로그인 기능

*UserDetailsService*

- 아이디와 비밀번호를 입력받는다.
- 입력받은 값을 기반으로 UsernamePasswordAuthentication 객체를 만든다.
- authenticationManagerBuilder의 authenticate method를 사용해 사용자의 비밀번호를 검증한다.
검증과정
    - 우선 UserDetailsService를 구현한 CustomUserDetailsService를 만든다
    - loadUserByUsername을 Override 한다.
    - UserDetailsService를 구현해 loadUserByUsername method를 재정의 한다.(이 method는 여러 곳에서 호출된다.)
    - 호출되는 곳 중 DaoAuthenticationProvider를 확인한다.
    - DaoAuthenticationProvider의 method : retrieveUser에서 loadUserByUsername을 사용한다. 그리고 이 retrieveUser method는 DaoAuthenticationProvider의 부모 클래스인 AbstractUserDetailsAuthenticationProvider에서 사용되고 retrivevUser 뿐만 아니라 additionalAuthenticationChecks라는 method도 사용된다.
    - additionalAuthenticationChecks method는 입력받은 password와 UserDetails에 저장된 password를 비교하는 method이고 retrieveUser는 username에 맞는 객체를 찾고 UserDetails로 반환한다.
    - AbstractUserDetailsAuthenticationProvider의 authentication은 인터페이스인 AuthenticationProvider에서 호출된다.
    - ProviderManger의 authenticate method에서 AuthenticationProvider의 authenticate가 호출된다.
    - 마지막으로 ProviderManger가 구현하는 인터페이스는 AuthenticationManger다.
**정리**
**로그인 로직에 있는 autheticationMangerBuild에 authenticate method를 사용할 때 ProviderManger->AuthenicationProvider->AbstractUserDetailsAuthenticationProvider->retrieveUser(method : DaoAuthenticationProvider), additionalAuthenticationChecks(method : DaoAuthenticationProvider)의 로직을 수행하고 그 중 UserDetailsService의 loadUserByUsername method를 사용하기 때문에 authenticationMangerBuild를 사용하는 로그인 로직에서는 UserDetailsService의 loadUserByUsername을 반드시 구현해야 한다.**
- 그 후 인증된 객체를 바탕으로 token을 생성한다.
- refresh token을 저장한다.
- 마지막으로 token을 반환하면 로그인 로직은 끝난다.

### 토큰 재발급

- 받아온 refresh token의 유효성 검사
- refresh token이 유효하다면 access token에서 entity의 기본 키를 가져온다.
- refresh token repository에서 access token에서 가져온 entity의 기본 키를 기반으로 repository token 값을 가져온다.
- 받아온 refresh token과 refresh token repositoty에서 받아온 refresh token이 같은지 비교한다.
- generateToken method를 사용해서 새로 토큰을 발급받고 토큰을 반환한다.

### 로그아웃

- SecurityContextHolder에서 Authentication을 가져와서 getName method로 entity의 식별자를 받아온다 (예시 : 기본 키, account id, email, name 등등)
- refresh token repository에서 entity의 식별자로 refresh token을 찾는다.
- 찾은 refrsh token을 refresh token repositort에서 지운다.