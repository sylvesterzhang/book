websocket

客服端通信解决方案

1）基于http轮询向服务端异步发请求，获取最新信息

效率低下，服务端压力大

2）基于socket技术

可以直接通信，但如果客户端都保持沉默，链接会保持，但会造成资源浪费

websocket特点

websocket是一种协议，浏览器h5实现了其api来自客户端的握手信息如下：

```
GET ws://localhost/chat HTTP/1.1
Host: localhost
Connection: Upgrade
Upgrade: WebSocket
Sec-WebSocket-Key: sdadasdSsdawefgdgsdsaw==
Sec-WebSocket-Extensions: permessage-deflate
Sec-WebSocket-Version: 13
```

来自服务端的握手信息如下:

```
HTTP/1.1 101 Switching Protocols
Upgrade: WebSocket
Connection: Upgrade
Sec-WebSocket-Key: yuiyuiyurwsdawefgdgsdsaw==
Sec-WebSocket-Extensions: permessage-deflate
```

客户端实现

事件

| 事件    | 事件处理程序            | 描述                       |
| ------- | ----------------------- | -------------------------- |
| open    | websocket对象.onopen    | 链接建立时触发             |
| message | websocket对象.onmessage | 客户端接收服务端数据时触发 |
| error   | websocket对象.onerror   | 通信发生错误时触发         |
| close   | websocket对象.onclose   | 链接关闭时触发             |

方法

send() 使用链接发送数据



```js
let websocket=new WebSocket("ws://localhost:8080/abc")

websocket.onmessage=function(event){//链接建立好后执行
    
    
}
```

 服务端实现

从Tomcat7.0.5开始支持WebSocket，应用由一系列WebSocketEndpoint组成。Endpoint时一个java对象，代表WebSocket链接的一个服务端，我们可以视为处理WebSocket消息的接口，如Serverlet与http请求的关系一样

定义Endpoint的两种方式

编程式：继承javax.websocket.Endpoint并实现其方法（onClose,onOpen,onError）

注解式：定义一个POJO，并添加@ServerEndpoin注解，成员方法用@onClose,@onOpen,@onError标注

接收客户端发送数据

通过为Session添加MessageHandler消息处理器来接收消息，如果使用注解式定义Endpoint，可通过@OnMessage注解指定接收消息的方法

步骤



常见问题

1）测试类运行报错

```
java.lang.IllegalStateException: Failed to load ApplicationContext
……
```

在测试类上修改注解为`@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)`

2）对象无法注入

`@ServerEndpoint`标注的类里，无法通过@Autowired注入对象，需要定义一个工具类，再通过工具类取对象

```java
import java.util.Map;

import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.context.annotation.Lazy;
import org.springframework.stereotype.Service;

/**
 *
 *以静态变量保存Spring ApplicationContext, 可在任何代码任何地方任何时候中取出ApplicaitonContext.
 * @author Bucky
 */
@Service
@Lazy(false)
public class SpringContextHolder implements ApplicationContextAware{

    private static ApplicationContext applicationContext;


    //实现ApplicationContextAware接口的context注入函数, 将其存入静态变量.
    public void setApplicationContext(ApplicationContext applicationContext) {
        SpringContextHolder.applicationContext = applicationContext;
    }


    //取得存储在静态变量中的ApplicationContext.
    public static ApplicationContext getApplicationContext() {
        checkApplicationContext();
        return applicationContext;
    }

    //从静态变量ApplicationContext中取得Bean, 自动转型为所赋值对象的类型.
    @SuppressWarnings("unchecked")
    public static <T> T getBean(String name) {
        checkApplicationContext();
        return (T) applicationContext.getBean(name);
    }


    //从静态变量ApplicationContext中取得Bean, 自动转型为所赋值对象的类型.
    //如果有多个Bean符合Class, 取出第一个.
    @SuppressWarnings("unchecked")
    public static <T> T getBean(Class<T> clazz) {
        checkApplicationContext();
        @SuppressWarnings("rawtypes")
        Map beanMaps = applicationContext.getBeansOfType(clazz);
        if (beanMaps!=null && !beanMaps.isEmpty()) {
            return (T) beanMaps.values().iterator().next();
        } else{
            return null;
        }
    }

    private static void checkApplicationContext() {
        if (applicationContext == null) {
            throw new IllegalStateException("applicaitonContext未注入,请在applicationContext.xml中定义SpringContextHolder");
        }
    }

}
```



