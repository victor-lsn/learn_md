# WebSocket

1. 与springboot整合

   ~~~java
   @Configuration
   public class WebsocketConfig {
   
       @Bean
       public ServerEndpointExporter serverEndpointExporter(){
           return new ServerEndpointExporter();
       }
   }
   ~~~

2. 主要业务代码

   ~~~java
   package com.victor.websocket;
   
   import com.alibaba.fastjson.JSON;
   import com.alibaba.fastjson.JSONObject;
   import com.fasterxml.jackson.databind.JsonNode;
   import com.fasterxml.jackson.databind.ObjectMapper;
   import org.springframework.stereotype.Component;
   
   import javax.websocket.*;
   import javax.websocket.server.PathParam;
   import javax.websocket.server.ServerEndpoint;
   import java.io.IOException;
   import java.util.*;
   import java.util.concurrent.ConcurrentHashMap;
   import java.util.concurrent.CopyOnWriteArraySet;
   
   /**
    * @author Victor
    * @version 1.0
    * @date 20-8-24 上午10:09
    */
   @Component
   @ServerEndpoint("/WebSocketLink/{username}")
   public class WebSocketServer {
       //静态变量，用来记录当前在线连接数。应该把它设计成线程安全的。
       private static int onlineCount = 0;
       //concurrent包的线程安全Set，用来存放每个客户端对应的CumWebSocket对象。
       private static CopyOnWriteArraySet<WebSocketServer> webSocketSet = new CopyOnWriteArraySet<WebSocketServer>();
       //与某个客户端的连接会话，需要通过它来给客户端发送数据
       private Session session;
       private String username;
   
       //存储用户的Map
       private static ConcurrentHashMap userMap = new ConcurrentHashMap<>();
   
   
       /**
        * 连接建立成功调用的方法
        * @param session
        */
       @OnOpen
       public void onOpen(Session session, @PathParam("username") String username) throws IOException, EncodeException {
           this.session = session;
           System.out.println(username);
           session.getBasicRemote().sendText(username + "已上线");
   
           //加入set中
           webSocketSet.add(this);
   
           this.username = username;
           userMap.put(username, session);
   
           JSONObject jsonObject = new JSONObject();
   
           jsonObject.put("users", userMap.keySet());
           System.out.println(jsonObject);
   
           //获取所有用户的session，利用session发送用户列表
           for (Object s : userMap.keySet()) {
               Session o = (Session) userMap.get(s);
               o.getBasicRemote().sendText(jsonObject.toJSONString());
           }
   
   
           //添加在线人数
           addOnlineCount();
           System.out.println("新连接接入。当前在线人数为：" + getOnlineCount());
       }
   
       /**
        * 连接关闭调用的方法
        */
       @OnClose
       public void onClose() {
           //从set，map中删除
           webSocketSet.remove(this);
           userMap.remove(this.username);
   
           //在线数减1
           subOnlineCount();
           System.out.println("有连接关闭。当前在线人数为：" + getOnlineCount());
       }
   
       private static List list = new ArrayList();
   
       /**
        * 收到客户端消息后调用
        * @param message
        * @param session
        */
       @OnMessage
       public void onMessage(String message, Session session) throws IOException {
           //把String转换成json字符串
           JSONObject jsonObject = JSON.parseObject(message);
           //获取发送对象
           String toName = jsonObject.getString("toName");
           System.out.println(toName);
           //获取消息
           String textareaData = jsonObject.getString("textareaData");
           System.out.println(username + "发送的消息：" + message);
   
   
           Map map2 = new HashMap();
           map2.put("name", username);
           map2.put("message", textareaData);
           list.add(map2);
   
           JSONObject jsonObject1 = new JSONObject();
           jsonObject1.put("list", list);
           System.out.println(jsonObject1.toJSONString());
   
   
           Session rita = (Session) userMap.get(username);
           rita.getBasicRemote().sendText(jsonObject1.toJSONString());
   
   
           Session victor = (Session) userMap.get(toName);
           victor.getBasicRemote().sendText(jsonObject1.toJSONString());
   
       }
   
       /**
        * 暴露给外部的群发
        * @param message
        * @throws IOException
        */
       public static void sendInfo(String message) throws IOException {
           sendAll(message);
       }
   
       /**
        * 群发
        * @param message
        */
       private static void sendAll(String message) {
           Arrays.asList(webSocketSet.toArray()).forEach(item -> {
               WebSocketServer customWebSocket = (WebSocketServer) item;
               //群发
               try {
                   customWebSocket.sendMessage(message);
               } catch (IOException e) {
                   e.printStackTrace();
               }
           });
       }
   
   
       /**
        * 发生错误时调用
        * @param session
        * @param error
        */
       @OnError
       public void onError(Session session, Throwable error) {
           System.out.println("----websocket-------有异常啦");
           error.printStackTrace();
       }
   
       /**
        * 减少在线人数
        */
       private void subOnlineCount() {
           WebSocketServer.onlineCount--;
       }
   
       /**
        * 添加在线人数
        */
       private void addOnlineCount() {
           WebSocketServer.onlineCount++;
       }
   
       /**
        * 当前在线人数
        * @return
        */
       public static synchronized int getOnlineCount() {
           return onlineCount;
    }
   
       /**
        * 发送信息
        * @param message
        * @throws IOException
        */
       public void sendMessage(String message) throws IOException {
           //获取session远程基本连接发送文本消息
           this.session.getBasicRemote().sendText(message);
           //this.session.getAsyncRemote().sendText(message);
       }
   
   }
   ~~~
   
3. pom.xml

   ~~~xml
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-websocket</artifactId>
   </dependency>
   ~~~

   