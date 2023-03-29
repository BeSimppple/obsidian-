SDK包含了工具和API

微信支付(第三方支付接口开发)

微信支付申请(native支付(扫码支付))

我们负责商户后台系统

![](C:\Users\YuLi\AppData\Local\YNote\data\qq3AE958F571E4572DE9CC711939D04018\ea9bb20fe252480daa964d5d7c670797\bd886399102d4e3197ed53c2b6282858.jpg)

微信公众号注册:

微信支付功能需要注册公众号(服务号)才能使用

第一步：注册公众号（类型须为：服务号）

注册地址：[https://mp.weixin.qq.com/cgi-bin/registermidpage?action=index&lang=zh_CN&token=](https://mp.weixin.qq.com/cgi-bin/registermidpage?action=index&lang=zh_CN&token=)

第二步：认证公众号

公众号认证后才可申请微信支付，认证费：300元/次。

第三步：提交资料申请微信支付

第四步：开户成功，登录商户平台进行验证

第五步：在线签署协议

第六步：申请成功获取秘钥

例如:

appid：微信公众账号或开放平台APP的唯一标识 wx3963c4b88e10b849

mch_id：商户号 1535235291

key：商户密钥 f63be239c43f16bd52a3cf8a2fada0ea

千峰

- 商户编号：1497984412

- 商户账号AppID：wx632c8f211f8122c6

- 商户Key：sbNCm1JnevqI36LrEaxFwcaT0hkGxFnC

在线微信支付开发文档：[https://pay.weixin.qq.com/wiki/doc/api/index.html](https://pay.weixin.qq.com/wiki/doc/api/index.html)

支付流程图:

![](C:\Users\YuLi\AppData\Local\YNote\data\qq3AE958F571E4572DE9CC711939D04018\8ac5691d63894fe593d82868a173ca21\fc3c3b37944f4d809875213ee8b35798.jpg)

微信支付结合IDEA

主要接口(统一下单,查询订单,关闭订单)

1.引用pom依赖或下载官方JAVA版SDK

com.github.tedzhdz.wxpay-sdk 3.0.10

commons-logging.commons-logging 1.2

2.创建config类继承抽象类WXPayConfig

重写get方法并返回对应的商户id和钥匙等...

3.根据接口文档需求编写接口

例如:统一下单接口

new HashMap()将文档中需要的内容put进去->文档需要传入xml格式文件所以将map转为xml串格式(WXPayUtil.generateSignedXml(HashMap.config.getKey()))

->发送支付请求(底层是通过HttpClient发送请求)

request = new WXPayRequest(config);

request.requestWithoutCert(指定接口的路径后缀,UUID, xml格式的参数, 是否自动报告)

->发送成功返回一个xml结果集 通过WXPayUtil.xmlToMap(resultXml);转译成map后可打印

同理配置查询订单和关闭订单接口

  

整合MQ当库存改变索引库也需要改变

配置FeignConfig完成TOKEN在服务之间的传递

订单已被支付反馈逻辑,

方案一:ajax轮循请求(时效性差且加大了后台访问压力)

方案2:webScoket

webSocket(双向通信)

作用:客户端和服务端建立长链接,当支付成功或错误后立即返回前端

![](C:\Users\YuLi\AppData\Local\YNote\data\qq3AE958F571E4572DE9CC711939D04018\3b456df069524013b3fa55b88dc3fd8f\ce521491cc094d9c9017982f0601c97e.jpg)

WebSocket 是 HTML5 中一种新的通信协议，能够实现浏览器与服务器之间全双工通信(例如打电话持久性链接,双向数据传输)。

webSoket应用:

1.服务端导入websocket的pom依赖\

org.springframework.boot.spring-boot-starter-websocket

客户端写相应的前端websocket代码(网上可查)

2.配置websocketConfig类开启websocket

@Bean

public ServerEndpointExporter serverEndpointExporter() {

return new ServerEndpointExporter();

}

3.自定义类开启监听连接、消息、关闭连接

@ConditionalOnClass(value = WebSocketConfig.class)

@Component

@ServerEndpoint("路径") (设置双向端点.表示客户端必须通过该路径双向链接)

  

写一个线程安全(会有多个流调用)的hashmap存放session信息方便二次登录后载入原session(KEY是路径中传入的id信息,value是session信息做一个绑定)

private static ConcurrentHashMap<String,Session> sessions = new ConcurrentHashMap<String, Session>();

写接口方法

@OnOpen

onOpen(Session session., @PathParam("orderId") String orderId){}当客户端与服务端建立连接调用的方法,

@OnMessage

onMessage(String message,Session session){}当客户端传输数据调用的方法,

@OnClose

onClose(@PathParam("orderId") String orderId){}当客户端断开连接调用的方法

写静态方法(发送消息)

public static void sendMsg(String Id)

Session session = sessions.get(ID信息); (获取session)

session.getBasicRemote().sendText("消息") (发消息)

4.前端页面书写

项目集成websocker思路:

1：微信支付成功后调用下单模块的websocket的通知接口

所以要在下单模块微服务集成websocket

2：自媒体用户系统，支付页面集成websocket

支付页面与wfx-order建立长连接，一旦支付成功，立马通知浏览器客户端，进行页面跳转

注意security验证,设置对websocket放行

解决超时未支付订单问题思路:

使用延时消息解决订单超时未支付的问题

思路：

1：在wfx-order生成订单后发送延时消息（15分钟） topic（order-for-pay）

2：wfx-pay监听 topic（order-for-pay） 一旦有消息，获取orderId

通过orderid调用微信支付的查询订单状态接口

如果状态为未支付，退回库存，调用微信的关闭订单接口，关闭订单

如果状态为已支付，可以不用处理

  

qrcode.js二维码生成工具

<div id="qrcode"></div>

<script>

var qrcode = new QRCode("test", {

text: "http://www.runoob.com", //地址

width: 128,

height: 128,

colorDark : "#000000",

colorLight : "#ffffff",

correctLevel : QRCode.CorrectLevel.H //二维码级别

});

</script>

内网穿透工具:

ngrock内网穿透

natapp内网穿透