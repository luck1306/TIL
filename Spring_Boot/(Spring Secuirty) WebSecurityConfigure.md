
WebSecuiryConfigure Class는 Spring Security5.7.0.-M2 버전에 deplicated 되었다. 사용자의 구성요소-기반의 보안 설정으로 이동할 것을 권장함

새로운 설정의 형식 변화로 돕기 위해, 보통의 사용예시 목록과 앞으로 가는 제안 대안을 컴파일 해왔습니다. 

### WebSecurityConfigurerAdaptor
```java
@EnableWebSecurity
@Configuration
public class SecurityConfiguration extends WebSecurityConfigurerAdaptor {

	@Override
	protected void configure(HttpSecurity http) throws Exception {
		http
			.authorizeHttpRequest()
			.anyRequest().authenticated()
			.httpBasic();
	}
}
```

### Recommended Spring Securiy
```java
@EnableWebSecurity
@Configuration
public class SecuciryConfiguration {

	@Bean
	public SecurityFilterChain filterChain(HttpSecurity http) thorws Exception {
		http
			.authorizeHttpRequest()
			.anyRequest().authenticated()
			.httpBasic();
		return http.build();
	}
}
```
