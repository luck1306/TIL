수정 : 230905 11:00 ~ 12:25
### Same-origin policy(동일 출처 정책)
- 동일 출처 정책은 자원공유를 같은 origin(protool + domain + port)을 가진 웹사이트로 제한하는 것을 말함
- 어떤 사이트에서 사용하는 api가 있는데 그 api는 비공개 api다.
- 하지만 다른 서비스에서 허락없이 무단으로 사용하는 것을 비공개 api는 원치 않을 것
- 그래서 protocol, domain, port가 모두 같아야 __동일 출처 정책__ 을 따른다고 볼 수 있다.

### CORS(Cross -origin resource sharing)
- 웹 페이지의 제한된 자원을 외부 도메인에서 접근을 허용해줌
- 서로 다른 도메인에서 리소스를 공유 가능케 바꿔줌


### 1. @CrossOrigin
- 스프링 4.2 부터 지원되는 @CrossOrigin은 cors를 설정할 수 있는 annotation이다.
- 이 annotation을 추가 시 "모든 도메인, 모든 요청방식"에 허용한다는 뜻
- 특정 도메인만 허용하고 싶을 때 속성 값으로 origin = "http:// {허용할 도메인 주소}"
```java

@RequestMapping("/")
@RestController
public class Controller {

	@CrossOrigin // 기본 값으로 모든 origin에서의 요청을 허용합니다.
	@GetMapping
	public String getRoot() {
		return "hello!";
	}

}
```

### 2. CorsConfigurationSource
- spring security를 이용하는 방법
- Spring security에서 SecurityFilterChain Bean을 등록하는 과정에 HttpSecurity의 method로 등록 가능
```java
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.CorsConfigurationSource;
import org.springfrmaework.web.cors.UrlBasedCorsConfigurationSource;

@EnableWebSecurity
@RequiredArgsConstructor
@Configuration
public class CustomSecurityConfig {

	@Bean
	public SecurityFilterChain securityFilterChain(HttpSecurity http) {
		return http
			.cors((t)-> corsConfiguragionSource())
			.build();
	}

	@Bean
	public CorsConfigurationSource corsConfigurationSource() {
		CorsConfiguration corsConfiguraion = new CorsConfiguration();
		corsConfiguration.addAllowedMethod("*");
		List<String> originPatterns = new ArrayList<>();
		originPatterns.add("*");
		corsConfiguration.setAllowedOriginPatterns(originPatterns);
		corsConfiguration.setAllowCredentials(true);
		UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
		source.registerCorsConfiguration("/**", corsonfiguration);
		return source;
	}

}
```

### 3. WebMvcConfigurer interface
- WebMvcConfigurer interface의 구현체를 bean으로 등록해 cors를 설정하는 방법
```java
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
import org.springframework.web.servlet.config.annotation.CorsRegistry;

public class WebConfig implements WebMvcConfigurer {
	@Override
	public void addCorsMappings(CorsRegistry corsRegistry) {
		corsRegistry
			.addMappings("/**")
			.allowdOrigins("*")
			.allowdMethods("*");
	}
}
```
