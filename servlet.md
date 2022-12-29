
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
	- Http Request : 웹 브라우저에서 Http 요청 포맷을 만들어 서버에 요청
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
		1. 임시 저장소 기능
		2. 세션관리 기능

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

## Http 요청 데이터
	Http 요청 메시지를 보낼 때 클라이언트에서 서버로 데이터를 전단하는 방법

### Query Parameter

url의 query param에 데이터를 넣어 전달하는 방식

예시) http://localhost:8080/a?userName=woonil&age=18&userName=cumulus
_위의 query parameter에는 userName이 두 번 들어가는데 request.getParamgerValues() method를 사용해서 String 배열로 반환받을 수 있다._
```java
System.out.println("[복수 파라미터 조회]"); 
String[] userNames = request.getParameterValues("userName"); 
for (String name : userNames) { System.out.println("userName="+name); }
```
실행 결과
\[복수 피라미터 조회\]
userName=woonil
userName=cumulus

### Html form
- content-type:application/x-www-form-urlencoded
- 메시지 바디에 쿼리 파라미터 형식으로 데이터를 전달함
- url query parameter와 같은 쿼리스트링 형태이기 때문에 request.getParameter() method로 추출가능하다

### Http message body에 데이터를 담아 요청

#### 단순 텍스트
```java
// servlet의 service method
ServletInputStream inputStream = request.getInputStream(); 
String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8); System.out.println("messageBody = " + messageBody); 
response.getWriter().write("ok")
```
- 단순 텍스트는 Http message body를 직접 읽을 수 있는 getInputStream() method를 통해 body 전체를 가져온다.
- StreamUtils로 Http message Body를 StandardCharsets.UTF_8을 이용해 String type으로 변환함

__실행결과__       (보낸 내용이 hello일 때)
messageBody = hello

#### JSON
```java
//serblet의 service method
ServletInputStream inputStream = request.getInputStream(); 
String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8); System.out.println("messageBody = " + messageBody); 
//=================================
HelloData helloData = objectMapper.readValue(messageBody, HelloData.class); System.out.println("helloData = " + helloData);


@Getter 
@Setter 
@ToString 
public class HelloData { 
private String userName; 
private int age; 
}
```
- 절취선(?) 위의 내용은 단순텍스트를 String type으로 만드는 것과 다를 바 없다
- 하지만 만들어 놓은 HelloData 클래스 형으로 변환하기 위해서는 jackson 라이브러리를 사용해야 함(objectMapper는 Jackson 라이브러리 내에 있는 class)

실행결과       (보낸 내용이 {"userName":"woonil","age":18}일 때)
messageBody = {
	"userName" : "woonil",
	"age" : 18
}
helloData = HelloData(userName=woonil, age=18)

## Http 응답 데이터

Response 객체를 생성하기 위해 지정해야 하는 것
- Http 응답 코드
- Header
- Body
- Content-Type
- Cookie
- Redirect

refrences : https://catsbi.oopy.io/e10ed90b-95dd-435a-873b-40183f93635c