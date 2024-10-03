### Authentication
Spring Security는 인증을 위한 종합적인 지원을 제공한다. 우리는 Servlet Authentication Architecture를 전반적으로 의논하기 시작할 것이다. 네가 예상할 수 있는것 처럼, 이 섹션은 흐름의 구체화를 적용하는 방법에 많은 논의 없이 더 추상적으로 묘사되는 아키텍쳐다.

만약 네가 원하면, 너는 사용자가 인증할 수 있는 어느 방법과 구체적인 Authentication Mechanisms을 참조할 수 있다. 이 섹션들은 네가 인증을 원하는 것과 특정한 작업 흐름의 방법을 묘사하는 아키텍쳐 섹션을 가리키는 것에 집중한다.

### Servlet Authentication Architecture
이 논점을 확대한다 Servlet Security: Spring Security의 주요 아키텍쳐 컴포넌트를 묘사하는 큰 그림이 Servlet 인증에 사용된다. 만약 네가 이 조각들이 서로 맞는 방법을 설명하는 구체적인 흐름이 필요하다면, Authentication Mechanism이라는 특별한 섹션을 확인해라.

- SecurityContextHolder - 어디에 Spring Security가 인증된 사람의 세부사항을 저장하는지
- SecurityContext - SecurityContextHolder로 부터 받고 현재 인증된 사용자의 Authentication을 내포한다
- Authentication - 인증이나 SecurityContext로 부터의 현재사용자의 자격증명을 제공하는 AuthenticationManager 안에 입력될 수 있음
- GrantedAuthority - Authentication의 주체에 허가받은 인가
- AuthenticationManager - 어떻게 Spring Security의 필터가 인증을 수행하는지 정의된 API
- ProviderManager - 거의 보통 AuthenticationManager의 구현체
- AuthenticationProvider - authentication의 특정한 자료형을 수행하는 ProviderManager로 사용됨
- Request Credentials with AuthenticationEntryPoint - 클라이언트로 부터 자격증명 요청을 위해 사용됨
- AbstractAuthenticationProcessingFilter - 인증을 위해 사용되는 표준 Filter. 또한 인증의 고급 흐름의 좋은 아이디어와 작업을 한데 모으는 방법을 준다.