# dependency
```xml
		<!-- websocket -->
		<dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-websocket</artifactId>
           <version>${org.springframework-version}</version>
		</dependency>
		<!-- sockejs-client & stomp-websocket -->
		<!-- stomp-websocket은 spring-websocket에 포함 되어있다고 한다 -->
		<dependency>
		   <groupId>org.webjars</groupId>
		   <artifactId>sockjs-client</artifactId>
		   <version>1.1.2</version>
		</dependency>
		<dependency>
		   <groupId>org.webjars</groupId>
		   <artifactId>stomp-websocket</artifactId>
		   <version>2.3.3</version>
		</dependency>
		
		<!--  JSON라이브러리 -->
		<dependency>
		    <groupId>com.fasterxml.jackson.core</groupId>
		    <artifactId>jackson-databind</artifactId>
		    <version>2.11.3</version>
		</dependency>
	    <!-- GSON 라이브러리 -->
		<!-- https://mvnrepository.com/artifact/com.google.code.gson/gson -->
		<dependency>
		    <groupId>com.google.code.gson</groupId>
		    <artifactId>gson</artifactId>
		    <version>2.9.0</version>
		</dependency>
		<!-- JSON -->
		<dependency>
			<groupId>org.json</groupId>
			<artifactId>json</artifactId>
			<version>20180813</version>
		</dependency>
```
# 1:1 채팅에 실제 사용한 코드

WebSocketConfig.java
```java
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {
	
    @Override
	public void registerStompEndpoints(StompEndpointRegistry registry) {
    	registry.addEndpoint("/chatting")
		.setAllowedOrigins("*")
        .withSockJS();
	}

	@Override
	public void configureMessageBroker(MessageBrokerRegistry config) {
		config.setApplicationDestinationPrefixes("/send"); 
        config.enableSimpleBroker("/topic");
	}

}

```

chatController.java
```java
	@MessageMapping("/{roomId}")
	@SendTo("/topic/{roomId}")
    public ChatMessageDTO chat(@DestinationVariable("roomId") String roomId, 
    		ChatMessageDTO message){
//		System.out.println(message);
		//클라이언트에서 받아야하는 DTO 변수 chat_room_id, message_type, user_id, message
		ChatMessageDTO chat = chatService.createChatMessage(roomId ,message);
		return chat;
    }

```


chatScript.js
```js
// 채팅방 연결
function connect(chatRoom_json){
	
	chatRoomGlobal = JSON.parse(chatRoom_json.replaceAll("&#034;", "\"").replaceAll("\n","<br>"));
    let socket = new SockJS('./chatting');
    stompClient = Stomp.over(socket);
    stompClient.connect({}, function(frame){
    
        // roomID를 구독 /send/roomID로 보내진 메세지를 받음
        stompClient.subscribe('/topic/' + chatRoomGlobal.chat_room_id, function (chatMessage){
        	//console.log('chatMessage: ', chatMessage);
            showChat(JSON.parse(chatMessage.body));
        });
        
        $('#inputMessage').css('visibility', 'visible');
    });
}

//채팅 보내기
function sendChat() {
	let sendMessage = $("#message").val();
    if (sendMessage != "" && sendMessage.replaceAll("\n","") != "" ) {
    	let data = {
    	"message_type":"MESSAGE",
    	"user_id": userId,
    	"message":sendMessage,
    	}
    	
        stompClient.send("/send/"+chatRoomGlobal.chat_room_id, {},
            JSON.stringify(data));
        $("#message").val('');
    }
}
```


>출처
>https://blog.naver.com/sonmit002/221309102763