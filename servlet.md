
@ServletComponentScan : 프로젝트 내의 annotation이 선언된 클래스가 포함된 패키지 부터 하위 패키지까지 작성된 서블릿을 등록 및 사용하게 해준다

프로젝트 내에서 servlet 사용조건
- HttpServlet 상속
- @WebServlet annotation 사용
	- property:name, property:urlPatterns 설정
__@WebServlet property 중복 금지__

### servlet containor 동작 방식
1. 스프링 부트 내부에 포함된 내장 톰캣이 가지고 있는 servlet containor로 서블릿을 생성&등록한다.
2. Http Req, Http Res 
![[Pasted image 20221125022006.png]]
	-  Http Request : 웹 브라우저에서 Http 요청 포맷을 만들어 서버에 요청
	- Http Response : 서버에서 로직이 끝나면 Http 응답 메시지를 만들어 웹 브라우저로 전달

3. 웹 어플리케이션 서버의 요청 & 응답 구조
	- Http Request로 request, response 객체 생성
	- 싱글톤으로 등록된 서블릿 중 매핑된 서블릿을 호출해 request, response 객체 전달
	- 서블릿에서 비즈니스 로직수행
	- response 객체를 기반으로 응답 메시지 제작 및 웹 클라이언트로 반환

### HttpServletRequest

![[Pasted image 20221125025452.png]]
	Http 요청 메시지

텍스트 구조의 메시지를 하나하나 파싱한 결과가 HttpServletRequest
	HttpServletRequest의 부가기능

Request message 분석
- Start Line
	- Http method
	- URL
	- Query String
	- scheme __(?)__ , protocol
- Header
	-  read header
- Body
	- form 매개변수 형식 조회
	- message body 데이터 직접 조회

