
create 230906 16:24~ 18:00 (@WebMvcTest, @SpringBootTest @DataJpaTest 작성)

**Application의 규모에 따라 테스트 시간의 차이가 극단적으로 변할 가능성으로 인해 아래 Test 중 현재 필요에 맞는 테스트를 진행해야 합니다.**

### @WebMvcTest
	presentation(controller) layer를 test하기 적합한 annotation, controller bean을 load해 test

- @Controller, @RestController, @ControllerAdvice, @RestControllerAdvice, @JsonComponent 등의 annotation이 적용된 bean 등록
- Filter 등록
- WebMvcConfigurer, HandlerMethodArgumentResolver 구현체 등록
-  Controller의 경우 분리된 business logic을 처리하는 Service 객체가 반드시 필요하다.

### @SpringBootTest
	실제 application을 자신의 local network에(localhost=127.0.0.1) 배포해 지정한 port를 점유하고 Database와 통신하는 live test

- spring application 통합 test를 위한 annotation으로 spring에 등록하고 등록된 bean이 모두 application context에 등록되기 때문에 실제 운영 환경과 가장 유사한 test 가능
- 모든 bean을 등록하려다 보니 테스트 시간이 비교적 길다.
- webEnvironment 속성을 지정함으로 테스트할 웹 환경을 선택할 수 있습니다.
	- WebEnvironment.MOCK : mock servlet 환경으로(test에 모든 객체를 생성하기 곤란한 상황에 가짜 객체를 생성) WebApplicationContext를 생성 (@AutoConfigureMockMvc annotation 적용 권유)
	- WebEnvironment.RANDOM_PORT : web application context를 생성하고, 무작위의 port를 점유
	- WebEnvironment.DEFINED_PORT : web application context를 생성하고, 환경변수의 port를 점유
	- WebEnvironment.NONE : ApplicationContext를 생성하고 "SpringApplication.setApplicationType(WebApplicationType)"을 "WebApplicationType.NONE"으로 바꿉니다.

### @DataJpaTest

[@DataJpaTest]((JPA)_@OnDelete_@DataJpaTest.md)

