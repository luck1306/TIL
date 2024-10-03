### 다음 문서는 특정 end-point에 대한 Spring Security의 검증을 무시하는 방법을 기술하고 있습니다. 알잘딱하시면 될 듯

- 해당 클래스는 HttpSecurity보다 위에 적용됩니다.
- 해당 클래스의 가장 큰 특징으로 지정한 endpoint를 무시할 수 있습니다.
	- 위에서 기술한 "무시"는 지정한 endpoint가 FilterChain을 더 이상 지나지 않음을 의미합니다.
	- HttpSecurity의 __antMatcher("/end-point").permitAll()__ 과 같습니다.
	- 하지만 HttpSecurity의 무시방법은 FilteChain을 모두 지나기 때문에 WebSecurity에서 지정하는 방법이 훨씬 overhead가 적습니다.

```java
@EnableWebSecurity
@Configuration
public class SecurityConfig {
	@Bean  
	public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {  
	    return http  
	        .csrf().disable()  
	        .formLogin().disable()  
	        .sessionManagement()  
	        .sessionCreationPolicy(SessionCreationPolicy.STATELESS)  
	        .and()  
	        .authorizeHttpRequests()  
	  
	        // "/auth"  
	        .antMatchers(HttpMethod.DELETE, "/auth/logout").authenticated()  
	  
	        // "/user"  
	        .antMatchers(HttpMethod.GET, "/user/my-page").authenticated()  
	        .antMatchers(HttpMethod.GET, "/user/mail/password").authenticated()  
	        .antMatchers(HttpMethod.GET, "/user/password").authenticated()  
	        .antMatchers(HttpMethod.PUT, "/user/password").authenticated()  
	        .antMatchers(HttpMethod.PUT, "/user/reissue").authenticated()  
	  
	        // "/post/anonymous"  
	        .antMatchers(HttpMethod.POST, "/post/anonymous/posting").permitAll() // must change hasAuthority()  
	  
	        // "/post/auth"        .antMatchers(HttpMethod.POST, "/post/auth/posting").authenticated()  
	  
	        .anyRequest().denyAll()  
	        .and()  
	        .addFilterBefore(new JwtFilter(jwtProvider), UsernamePasswordAuthenticationFilter.class)  
	        .build();  
}  
  
	@Bean  
	public WebSecurityCustomizer webSecurityCustomizer() {  
	    // if you implement permitAll(), this method better than configure in HttpSecurity  
	    return web -> web.ignoring()  
	            // when get cors header to preflight request, avoid the request  
	            .requestMatchers(CorsUtils::isPreFlightRequest)  
	  
	            // "/auth"  
	            .antMatchers(HttpMethod.GET, "/auth/email/verification")  
	            .antMatchers(HttpMethod.GET, "/auth/email/transmission")  
	            .antMatchers(HttpMethod.GET, "/auth/nickname/{nickname}")  
	            .antMatchers(HttpMethod.POST, "/auth/sign-up")  
	            .antMatchers(HttpMethod.POST, "/auth/login")  
	  
	            // "/post/anonymous"  
	            .antMatchers(HttpMethod.GET, "/post/anonymous")  
	            .antMatchers(HttpMethod.GET, "/post/anonymous/{id}")  
	  
	            // "/post/auth"  
	            .antMatchers(HttpMethod.GET, "/post/auth")  
	            .antMatchers(HttpMethod.GET, "/post/auth/{id}");  
	}
}
```
