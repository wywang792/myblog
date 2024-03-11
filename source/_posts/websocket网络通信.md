---
title: websocket网络通信
categories:
  - 网络通信
tags:
  - java
  - websocket
abbrlink: 368e27d4
date: 2024-03-10 00:00:00
cover:
---



### 一、定义handle

​      以下的handle只是做简单演示，可以根据需要做更多功能的扩展，比如根据session的具体属性，接收指定session的信息，给指定的session发送信息等等。

```java
import org.springframework.web.socket.CloseStatus;
import org.springframework.web.socket.TextMessage;
import org.springframework.web.socket.WebSocketSession;
import org.springframework.web.socket.handler.TextWebSocketHandler;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

public class MyWebSocketHandler extends TextWebSocketHandler {

    private List<WebSocketSession> sessions = new ArrayList<>();

    @Override
    public void afterConnectionEstablished(WebSocketSession session) throws Exception {
        System.out.println("接收到新的链接");
        sessions.add(session);
    }

    @Override
    protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
        // 处理接收到的消息
        String receivedMessage = message.getPayload();
        System.out.println("接收到新的消息：" + receivedMessage);
    }

    @Override
    public void afterConnectionClosed(WebSocketSession session, CloseStatus status) throws Exception {
        System.out.println("断开链接");
        sessions.remove(session);
    }

    public void sendMessageToAllSessions(String message) throws IOException {
        TextMessage textMessage = new TextMessage(message);
        for (WebSocketSession session : sessions) {
            System.out.println("发送消息");
            session.sendMessage(textMessage);
        }
    }
}
```



### 二、spring配置类

```java
import com.flux.handler.MyWebSocketHandler;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.WebSocketHandler;
import org.springframework.web.socket.config.annotation.EnableWebSocket;
import org.springframework.web.socket.config.annotation.WebSocketConfigurer;
import org.springframework.web.socket.config.annotation.WebSocketHandlerRegistry;

@Configuration
@EnableWebSocket
public class WebsocketConfig implements WebSocketConfigurer {

    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        registry.addHandler(myWebSocketHandler(), "/websocket").setAllowedOrigins("*");
    }

    @Bean
    public WebSocketHandler myWebSocketHandler() {
        return new MyWebSocketHandler();
    }
}

```



### 三、后端使用

```java
import com.flux.handler.MyWebSocketHandler;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.annotation.Resource;
import java.io.IOException;

@Controller
public class MyController {

    @Resource
    private MyWebSocketHandler myWebSocketHandler;

    @PostMapping("/sendMessage")
    @ResponseBody
    public void sendMessage(String message) throws IOException {
        myWebSocketHandler.sendMessageToAllSessions(message);
    }
}

```



### 四、前端使用

```javascript
const socket = new WebSocket('ws://127.0.0.1:18080/websocket')

// 监听连接成功事件
socket.onopen = function() {
  console.log('WebSocket连接已打开')
}

// 接收消息
socket.onmessage = function(event) {
  console.log('接收到消息:', event.data)
}

// 监听连接关闭事件
socket.onclose = function() {
  console.log('WebSocket连接已关闭')
}

// 监听连接错误事件
socket.onerror = function(error) {
  console.error('WebSocket连接发生错误:', error)
}

// 准备要发送的数据
const message = 'Hello, WebSocket Server!'

// 发送数据
socket.send(message)
console.log('消息已发送')
```

