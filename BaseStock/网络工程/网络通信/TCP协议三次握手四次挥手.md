三次握手

-   seq：（sequence number）序号
-   ack：（acknowledgement number）确认号
-   标志位：

-   SYN ：(SYNchronization）同步
-   ACK ：(ACKnowlegment）确认
-   FIN :（FINish）终止
![[TCP协议三次握手四次挥手_image_1.jpeg]]
初始状态：

client主动打开 server被动打开

第一次握手：client向server发送syn同步确认以及seq请求序号（X） 到此client进入SYN_SENT状态
![[TCP协议三次握手四次挥手_image_2.jpeg]]
第二次握手：server接收到seq请求序号（X）并加1变成ack确定号（X+1）
![[TCP协议三次握手四次挥手_image_3.jpeg]]
TCP因为是”全双工协议“所以server端也需要向client发送seq请求序号（Y）和ack确认号（X+1）以及SYN同步确认和和ACK链接确认

保证server端能向client端发送信息

Server进入SYN_RCVD状态
![[TCP协议三次握手四次挥手_image_4.jpeg]]

第三次握手：此时client已经收到并链接server了，但server还没收到client提供的通过确认，所以需要第三次握手

同理度二次握手传入ACK链接确认和ack确认号（Y+1）以及seq请求序号（X+1）

  

Client和Server进入ESTABLISHED状态，完成三次握手，随后Client与Server之间可以开始传输数据了。

1、客户端主机C说：“我可以给你发送数据吗？”

2、服务器S说：“可以的，不过我可能也会给你发送数据。”

3、C：“好，那我开始互相发送数据吧。”

  

四次挥手为什么比三次握手多一次

所谓四次挥手（Four-Way Wavehand）即终止TCP连接，就是指断开一个TCP连接时，需要客户端和服务端相互总共发送4个包以确认连接的断开。在socket编程中，这一过程由客户端或服务端任一方执行close来触发。

  ![[TCP协议三次握手四次挥手_image_5.jpeg]]

由于TCP连接是全双工的，因此，每个方向都必须要单独进行关闭。这一原则是当一方完成数据发送任务后，发送一个FIN来终止这一方向的连接，收到一个FIN只是意味着这一方向上没有数据流动了，即不会再收到数据了。但是在这个TCP连接上仍然能够发送数据，直到另一方向也发送了FIN。首先进行关闭的一方将执行主动关闭，而另一方则执行被动关闭，上图描述的即是如此。
![[TCP协议三次握手四次挥手_image_6.jpeg]]
  

第一次挥手：Client发送一个FIN，以及选择号seq=u（表示：u之前的数据已经全部发送，并且数据发到u就可以截止了，就不再有数据了），用来关闭Client到Server的数据传送。Client进入FIN_WAIT_1状态。
![[TCP协议三次握手四次挥手_image_7.jpeg]]


  

第二次挥手：Server收到FIN后，发送一个请求号seq=v和确认序号ack=u+1给Client。Server进入CLOSE_WAIT状态。（此时并不能立即会FIN状态还需要关闭socket）

第三次挥手：Server发送一个FIN，请求号为最新的seq=w和确认序号ack=u+1，用来关闭Server到Client的数据传送。Server进入LAST_ACK状态。

![[TCP协议三次握手四次挥手_image_8.jpeg]]
  

第四次挥手：Client收到FIN后，Client进入TIME_WAIT状态，接着发送一个ACK给Server，确认序号为w+1。Server进入CLOSED状态，完成四次挥手。

  

  

  

四次挥手的情景大致是这样的：

1、客户端主机C说：“我没有数据了，断开连接吧。 ”

2、服务器S说：“好，但是我还有数据（不断给C发送数据，此时C已经不能给S发送数据了，但是必须要就收S发来的数据）。”

3、（当S给C发完数据后）S说：“我发完了，断开连接吧。”

4、C说：“好，断开连接吧。”