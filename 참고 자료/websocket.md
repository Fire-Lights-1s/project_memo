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
```


>출처
>https://blog.naver.com/sonmit002/221309102763