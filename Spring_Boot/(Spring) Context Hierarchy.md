
230830 17:0X~18:09
reference : https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/context-hierarchy.html

context : application context, 그러니까 등록한 bean이 저장되는 집합(저장소)

대부분의 응용 프로그램은 하나의 context로 충분합니다. 하지만 필요에 따라 context를 계층화 할 수 있습니다. 대표적인 예로 **Root WebApplicationContext**에는 middle tier service _(데이터베이스 연결 설정, 데이터베이스 트랜잭션 설정, 유효성 검사, 로깅 등 응용프로그램 기능의 주가 되어 비즈니스 로직을 처리하지 않지만, 특정 기능 혹은 응용 프로그램 전체에서 사용 되는 기능의 설정을 위한 Class)_ 에 대한 Bean을 등록하고 **Servlet WebApplicationContext**에는 Controller Bean, Business-Layer Bean, 다른 web 관련 Bean들이 등록됩니다. 
위와 같은 계층화로 **Root WebApplicationContext**에서 등록한 Bean을 하위 WebApplicationContext에서 활용하거나 재선언(Root에서 등록한 Bean 대신 내가 설정한 Bean으로 등록) 가능합니다. 

하지만 굳이 context hierarchy를 사용할 필요가 없다면 다음과 같이 context를 구현할 수 있습니다.

```java
public class myWebInitalizer extends AbstractAnnotationConfigDispatcherServletInitializer {
	@Override
	protected Class<?>[] getRootConfigClasses() {
		return new Class<?>[] {RootConfg.class, ServletConfig.class};
	}

	@Override
	protected Class<?> [] getServletConfigClasses() {
		return null;
	}

	@Override
	protected String[] getServletMappings() {
		return new String[] { "/api/**" };
	}
	...
}
```
