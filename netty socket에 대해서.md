### 기본 설정

WebSocketConfig

```java
package com.example.netty_socket.config;

import com.corundumstudio.socketio.SocketIOServer;
import com.corundumstudio.socketio.annotation.SpringAnnotationScanner;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class WebSocketConfig {


    @Value("${socket-server.port}")
    private Integer port;

    @Value("${socket-server.host}")
    private String host;

    @Bean
    public SocketIOServer socketIOServer() {
        com.corundumstudio.socketio.Configuration config = new com.corundumstudio.socketio.Configuration();
        config.setHostname(host);
        config.setPort(port);
//        config.setPingTimeout(60000); // if cannot receive message, send time out event
        return new SocketIOServer(config);
    }

    @Bean // for using @Onconnect, @OnDisConnect annotation
    public SpringAnnotationScanner springAnnotationScanner() {
        return new SpringAnnotationScanner(socketIOServer());
    }
}
```

SocketRunner
```java
package com.example.netty_socket.config;

import com.corundumstudio.socketio.SocketIOServer;
import lombok.RequiredArgsConstructor;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@RequiredArgsConstructor
@Component
public class SocketRunner implements CommandLineRunner {

    private final SocketIOServer socketIOServer;

    @Override
    public void run(String... args) throws Exception {
        socketIOServer.start();
    }
}
```

SocketModule
```java
package com.example.netty_socket.service;

import com.corundumstudio.socketio.AckRequest;
import com.corundumstudio.socketio.SocketIOClient;
import com.corundumstudio.socketio.SocketIOServer;
import com.corundumstudio.socketio.annotation.OnConnect;
import com.corundumstudio.socketio.annotation.OnDisconnect;
import com.corundumstudio.socketio.annotation.OnEvent;
import com.example.netty_socket.entity.Message;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;

import java.util.List;
import java.util.Map;


@Slf4j
@Component
public class SocketModule {
    private final SocketIOServer server;;

    public SocketModule(SocketIOServer server, SocketService socketService) {
        this.server = server;
        this.socketService = socketService;
        // "server.addEventListener..." can handle corresponding eventName : ("send_message")
    }

    @OnEvent(value = "send_message")
    private void onChatReceived(SocketIOClient client, Message data, AckRequest ack) {
//            log.info(data.getUserName() + " : " + data.getContent());
            sendMessage(data.getRoom(), "read_message", client, data);
//            ack.sendAckData(data); // use for ack data to server
//           client.getNamespace().getBroadcastOperations().sendEvent("get message", data.getMessage());
//           "client.getNamespace().getBroadCastOperations..." send all user data include me
        }


    @OnConnect
    private void onConnected(SocketIOClient client) {
        Map<String, List<String>> params = client.getHandshakeData().getUrlParams();
        String room = String.valueOf(params.get("room").toArray()[0]);
        String userName = String.valueOf(params.get("userName").toArray()[0]);
        client.joinRoom(room);
        Message message = Message.builder()
                .content(String.format("welcome %s", userName))
                .messageType(Message.MessageType.SERVER)
                .room(room)
                .userName(userName)
                .build();
        sendMessage(room, "read_message", client, message);
//        socketService.saveInfoMessage(client, String.format("welcome %s", userName),room);
        log.info("Socket Id[{}] Connected room - [{}] user_name - [{}]]"
                , client.getSessionId().toString(), room, userName);
    }

    @OnDisconnect
    private void onDisconnected(SocketIOClient client) {
        Map<String, List<String>> params = client.getHandshakeData().getUrlParams();
        String room = String.valueOf(params.get("room").toArray()[0]);
        String userName = String.valueOf(params.get("userName").toArray()[0]);
        client.leaveRoom(room);
        Message message = Message.builder()
                .userName(userName)
                .room(room)
                .messageType(Message.MessageType.SERVER)
                .content(String.format("good bye %s", userName))
                .build();
        sendMessage(room, "read_message", client, message);
//        socketService.saveInfoMessage(client, String.format("good bye %s", userName), room);
        log.info("Client[{}] - Disconnected from socket", client.getSessionId().toString());
    }
    
    private void sendMessage(
	    String room, 
	    String event,
	    SocketIOClient senderClient,
	    Message message
    ) {
		for (SocketIOClient client : sendereClient.getNameSpace().getRoomOperations(room).getClients()) {
			if(!client.getSessionId.equals(senderClient.getSessionId)) client.sendEvent(event, message);
		}
    }
}
```

application.yml
```yml
socket-server:
	port: 8085 # 8080 말고 아무거나
	host: ${IP_ADDRESS} # 현재 연결되어 있는 무선 LAN adaptor wifi ip address

spring:
	jpa:
		database: mysql
		database-platform: org.hibernate.dialect.MySQL5InnoDBDialect
		generate-ddl: true
		hibernate:
			ddl-auto: update
	datasource:
		username: ${USERNAME}
		password: ${PASSWORD}
		url: ${URL}
```

url 예시 : ws://${IP_ADDRESS}:8085?room=a&userName=luck1306

refrences : https://github.com/mrniko/netty-socketio/blob/master/src/main/java/com/corundumstudio/socketio/handler/SocketIOException.java