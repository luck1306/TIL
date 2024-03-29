servlet
```
서버에서 웹페이지 등을 동적으로 생성하거나 데이터 처리를 수행하기 위해 자바로 작성된 프로그램
```

원래 서버에서 처리해야 하는 로직
1. 서버에는 TCP/IP 연결을 대각하고 있도록 한다.
2. 웹 브라우저에서 전송한 메시지를 분석해서 파싱한다.
3. 분석한 데이터를 토대로 무슨 방식인지 파악한다. (Http method, url)
4. Content-Type을 분석해 메시지 바디를 파싱해서 읽는다.
5. 요청한 경로가 매핑된 프로세스가 실행된다.
6. 의미있는 비즈니스 로직을 수행한다.
7. 로직이 끝난 다음 Http 응답 메시지 생성 시작
8. TCP/IP에 응답을 전달하고 소켓 종료

**서블릿은 이 번거로운 작업을 모두 자동화해서 제공해 준다.**

서블릿 사용
```java
@WebServlet(name = "helloServlet", urlPattern = "/hello")
public class HelloServlet extends HttpServlet {
	@Override
	protected void service(HttpServletRequest request, HttpServletResponse response) {
		// application logic
	}
}
```
HttpServletRequest : Http 요청을 편리하게 사용가능하게 함
HttpServletResponse : Http 응답을 편리하게 사용가능하게 함

서블릿이 사용되는 서비스의 로직
1. 브라우저에서 특정경로로 요청한다.
2. WAS는 Request, Response 객체를 새로 만들어 서블릿 객체를 서블릿 컨테이너에서 호출한다.
3. 개발자는 각각 Request, Response 객체에서 Http 정보를 편리하게 작업한다.
4. WAS는 Response 객체에서 Http 응답 정보를 생성해 반환한다.

서블릿 컨테이너 
- 서블릿의 생명주기를 관리해준다. (tomcat, netty 등등)
- 서블릿 객체는 싱글톤으로 관리함(한번에 두 쓰레드의 동시 접근 불가)
- 동시 요청을 위한 멀티 쓰레드 처리가 지원된다.

쓰레드
- 서블릿 객체를 호출함
- 쓰레드가 있어야 자바 어플리케이션 실행 가능
- 메인 메서드 실행 시 main이라는 쓰레드 실행
- 동시 처리 필요시 쓰레드 추가생성

단일 쓰레드
- 쓰레드를 하나만 사용하는 경우
- 만약 요청1의 요청이 끝나기 전에 요청2가 들어왔을 때 요청2는 요청1이 끝나기 전까지 대기해야 한다.

멀티 쓰레드
- 요청마다 쓰레드를 생성하는 경우
- 장점
	- 동시 요청을 처리할 수 있다.
	- 리소스(cpu, 메모리)가 허용할 때까지 처리가능
	- 하나의 쓰레드가 지연되도 나머지에는 영향이 가지않는다.
- 단점
	- 쓰레드는 생성비용이 높다
	- 쓰레드는 컨텍스트 스위칭이 발생한다.
		- 컨텍스트 스위칭 : 프로세스 실행 중 인터럽트에 의해 다음 우선순위 프로세스가 들어오면 현재 작동중인 프로세스를 저장하고 다음 프로세스로 교체하는 작업
	- 쓰레드 생성제한이 없다

쓰레드 풀
- 일정 갯수의 쓰레드를 미리 생성해두고 필요할때 쓰고 다 쓰면 반납하는 식의 시스템
- 이미 존재하는 쓰레드를 사용하는 것
	- 무한히 증가하는 쓰레드 걱정 해소
	- 쓰레드 생성시 생기는 비용 부담 해소
- 최대 쓰레드는 너무 적게 설정하면 클라이언트 응답이 지연될 가능성이 있고, 너무 많게 설정하면 cpu, 메모리 자원의 임계점 초과로 서버가 다운될 수 있다.
- 따라서 쓰레드풀의 최대 쓰레드는 적정 숫자를 찾아야한다.

Http Api
- 주고 받는 데이터
- 보통 json 많이 사용

SSR - Server Side Rendering
- 서버에서 최종적으로 생성한 HTML을 브라우저에 반환
- JSP, 타임리프 등(백엔드)

CSR - Client Side Rendering
- 먼저 html반환하고 js반환, js실행 data 서버로 부터 받고, content확인 가능
- react vue



refrences : https://catsbi.oopy.io/defe6c4d-1d74-4a5e-8349-ff9077dda184