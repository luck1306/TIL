created at : 2025.01.01:16:04:16 ~ 16:04:22

### 의의
- 특정 요청이 컨트롤러에 도달하기 전에, 요청 처리 과정의 중간 단계에서 추가 작업을 수해하거나 요청을 가로채는 데 사용합니다. (_인증_, _로깅_, _데이터 변환_)

### 특징
1. 핸들러(Controller) 호출 전/후 작업
   - Interceptor는 HTTP 요청이 컨트롤러에 도달하기 전과, 응답이 클라이언트로 반환되기 전후에 동작합니다.
2. 중앙 집중 관리
   - 다양한 URL 패턴을 기반으로 Interceptor를 설정하여, 공통 로직을 재사용하고 관리할 수 있습니다.

### 메서드
1. _preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)_
   - 컨트롤러 실행 전에 호출됩니다.
   - 요청을 검증하거나, 인증/권한 체클르 수행할 수 있습니다.
   - true를 반환하면 다음 단계로 진행하고, false를 반환하면 요청을 중단합니다.
1. _postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)_
   - 컨트롤러가 실행된 후, 뷰 렌더링 전에 호출됩니다.
   - 요청 처리 결과를 수정하거나 추가 데이터를 설정할 수 있습니다.
1. _afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)_
   - 뷰가 렌더링된 후 호출됩니다.
   - 예외 로깅이나 리소스 정리와 같은 후처리를 수행합니다.

### 구현 예제
1. Interceptor 구현
```java
import javax.servlet.http.HttpServletRequest; 
import javax.servlet.http.HttpServletResponse; 
import org.springframework.web.servlet.HandlerInterceptor;  

public class LoggingInterceptor implements HandlerInterceptor {      
	@Override     
	public boolean preHandle(
		HttpServletRequest request, 
		HttpServletResponse response, 
		Object handler
	) throws Exception {         
		System.out.println(
			"Incoming request: " + request.getRequestURI()
		);         
		return true; // true면 다음 단계 진행
	}      
	
	@Override     
	public void postHandle(
		HttpServletRequest request, 
		HttpServletResponse response, 
		Object handler, 
		ModelAndView modelAndView
	) throws Exception {         
		System.out.println("Handler executed, now rendering the view.");     
	}      
	
	@Override     
	public void afterCompletion(
		HttpServletRequest request, 
		HttpServletResponse response, 
		Object handler, 
		Exception ex
	) throws Exception {         
		System.out.println("Request completed.");     
	} 
}
```

2. Interceptor 등록
```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoggingInterceptor())
                .addPathPatterns("/**") // 모든 경로에 적용
                .excludePathPatterns("/public/**"); // 특정 경로는 제외
    }
}

```

### 사용 사례
1. 인증 및 권한 확인
   - 로그인 여부 확인 및 역할에 따라 요청 차단 또는 허용.
2. 로깅
   - 요청 및 응답 정보를 기록하여 디버깅 및 모니터링.
3. 데이터 변환
   - 요청 데이터 또는 응답 데이터를 변환하거나 추가 설정.
4. API 호출 제한
   - 특정 클라이언트가 일정 시간 내에 호출할 수 있는 요청 수를 제한.

### Interceptor VS Filter
| 특징             | Interceptor                   | Filter                         |
| -------------- | ----------------------------- | ------------------------------ |
| 위치             | DispatcherServlet 이후 실행       | DispatcherServlet 이전 실행        |
| 목적             | Spring MVC와 연계된 로직 처리         | 낮은 수준에서의 요청 및 응답 가공 처리         |
| 구현 인터페이스       | HandlerInterceptor            | jakarta.servlet.Filter         |
| Spring Context | Spring의 빈 및 컨텍스트를 쉽게 사용할 수 있음 | Spring Context에 접근하려면 추가 작업 필요 |
