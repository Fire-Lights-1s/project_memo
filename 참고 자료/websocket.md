실제 사용한 코드

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

>출처
>https://blog.naver.com/sonmit002/221309102763