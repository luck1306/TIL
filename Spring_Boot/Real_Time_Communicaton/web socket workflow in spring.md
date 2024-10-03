
Web Socket('org.springframework.boot:spring-boot-starter-websocket')의 대표적인 대표적인 클래스
- Message : 헤더와 payload를 포함한 메시지를 위한 간단한 표현
- MessageHandler : 메시지를 처리하기 위한 계약
- MessageChannel : 공급자와 소비자 사이의 느슨한 결합을 가능하도록 도우며 메시지를 보내기 위한 계약
- SubscribableChannel : MessageHadler 구독자들의 MesssageChannel
- ExecutorSubscribableChannel : 메시지를 운반하기 위한 실행기를 이용한 SubscribableChannel

@EnableWebSocketMessageBroker와 <websocket:message-broker>는 메시지 작업흐름을 모으는 최전방 요소로 사용된다. 아래 도표는 간단한 내장 메시지 브로커가 사용되는 요소를 보여준다.
![[Pasted image 20230708165219.png]]
앞의 도표는 3개의 메시지 채널을 보여준다.
- clientInboundChannel : WebSocket client로 부터 받은 메시지를 통과시키기 위함
- clientOutboundChannel : WebSocket client에 서버 메시지를 보내기 위함
- brokerChannel : 서버사이드 어플리케이션 코드 내에서 메시지 브로커에게 메시지를 보내기 위함

다음 도표는 외부 브로커(RabbitMQ 같은) 요소를 사용할 때를 보여주는 구독자 관리 및 메시지 방송을 위해 설정되었습니다.

![[Pasted image 20230708173736.png]]
앞의 두 도표 사이에 주된 차이점은 TCP를 통한 외부 STOMP 브로커에 메시지를 전달시키기 위함과 브로커로부터 구독된 클라이언트로 메시지를 전달하기 위한 "broker relay"를 사용한다는 것이다.
Web Socket 연결에서 message를 수신하고, 그 메시지들은 STOMP 프레임으로 디코딩되며 Spirng Message 표현으로 변환된 후, 추가 처리를 위해 clientInboundChannel로 전송된다. 예를 들어 "/app"으로 시작하는 도착 헤더를 지닌 STOMP Message는 어노테이션이 적용된(annotated) controller 내의 @MessageMappping 메서드로 경로 설정 될 수 있고 "/topic", "/queue" 메시지는 메시지 브로커로 바로 경로 설정 가능하다. 

client로 부터 STOMP 메시지를 처리하는 어노테이션이 적용된(annotated) @Controller는 brokerChannel를 통해 메시지 브로커로 메시지를 전달할수 있다 그리고 브로커는 clientOutboundChannel을 통해 구독자들에 대응되는 메시지를 전파한다 같은 controlle는 Http 요청으로 응답 또한 같이 할 수 있다 그래서 client는 Http POST를 수행 가능하다, 그리고 나서 @PostMapping 메서드는 구독된 client에게 전파하기 위해 메시지 브로거에게 메시지를 보낼 수 있다.

간단한 예제를 통해 흐름을 추적할 수 있다. 다음의 예제를 서버에 적용할지말지 고려할 수 있다.
```java
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {

	@Override
	public void registerStompEndpoints(StompEndpointRegistry registry) {
		registry.addEndpoint("/portfolio");
	}

	@Override
	public void configureMessageBroker(MessageBrokerRegistry registry) {
		registry.setApplicationDestinationPrefixes("/app");
		registry.enableSimpleBroker("/topic");
	}
}

@Controller
public class GreetingController {

	@MessageMapping("/greeting")
	public String handle(String greeting) {
		return "[" + getTimestamp() + ": " + greeting;
	}
}
```
앞의 예제는 다음 흐름을 따릅니다.

1. client는 locahost:8080/portfolio에 연결하고 일단 WebSocket의 연결을 성립시킨다. STOMP 프레임은 그 위에서 흐르기 시작한다.
2. client는 "/topic/greeting"의 목적지 헤더인 SUBSCRIBE 프레임을 보낸다. 일단 수신되고 디코딩되면 메시지는 clientInboundChannel로 전송하고 그 후 client 구독을 저장하는 메시지 브로커에 라우팅됩니다.
3. client는 "/app/greeting"으로 SEND 프레임을 보낸다. "/app" 접두사는 annotation이 적용된 controller로 대응되도록 돕는다. "/app" 접두사를 없엔 후에 목적지의 "/greeting" 부분을 남기는 것은 GreetingController 내의 @MessageMapping 메서드로 대응된다.
4. GreetingControler에서 반환된 값은 반환값에 기반을 둔 payload와 "/topic/greeting"의 기본 목적지 헤더가 포함된 Spring Message로 전환된다. (입력 대상에서 파생되어 "/app"이 "/topic"으로 대채됨) 결과 메시지는 "brokerChannel"로 전송되고 메시지 브로커를 통해 처리된다.
5. 메시지 브로커는 대응되는 모든 구독자들을 찾고 clientOutBoundChannel을 통해 각자에게 MESSAGE 프레임을 전송한다. 여기서 메시지는 STOMP로 인코딩되고 WebSocket 연결로 전송된다.


### referenced by https://docs.spring.io/spring-framework/reference/web/websocket/stomp/message-flow.html
