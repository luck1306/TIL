
230905 09:00 ~ 10:53

### 스프링 공식문서 직역
- 한 개 이상의 component class를 가져오도록 지시하는 것 - 일반적으로 @Configuragion class
- Spring xml의 \<import /\>와 같은 기능을 제공함. 
- @Configuration class나 ImportSelector interface, ImportBeanDefinitionRegistrar interface의 구현체 게다가 정규 component class(4.2버전 부터; AnnotationConfigApplicationContext.register(java.lang.Class\<?\>)과 유사함)의 추가를 허용함
- 추가한 @Configuration class에서 선언된 @Bean에 대한 정의는 @Autowired 주입을 통해 접근되어야 함
- Bean 스스로 주입되거나(autowired), bean이 선언된 configuration class instance가 주입된다.
- 나중에 접근하는건 명시적으로 @Configuration class 메서드 사이에 