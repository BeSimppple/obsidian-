***网络编程三要素(IP地址,端口,协议)***

三次握手
-   seq：（sequence number）序号
-   ack：（acknowledgement number）确认号
-   标志位：

-   SYN ：(SYNchronization）同步
-   ACK ：(ACKnowlegment）确认
-   FIN :（FINish）终止
![[TCP协议三次握手四次挥手_image_1.jpeg|600]]
初始状态：

client主动打开 server被动打开

第一次握手：client向server发送syn同步确认以及seq请求序号（X） 到此client进入SYN_SENT状态
![[TCP协议三次握手四次挥手_image_2.jpeg|600]]
第二次握手：server接收到seq请求序号（X）并加1变成ack确定号（X+1）
![[TCP协议三次握手四次挥手_image_3.jpeg|600]]
TCP因为是”全双工协议“所以server端也需要向client发送seq请求序号（Y）和ack确认号（X+1）以及SYN同步确认和和ACK链接确认

保证server端能向client端发送信息

Server进入SYN_RCVD状态
![[TCP协议三次握手四次挥手_image_4.jpeg|600]]

第三次握手：此时client已经收到并链接server了，但server还没收到client提供的通过确认，所以需要第三次握手

同理度二次握手传入ACK链接确认和ack确认号（Y+1）以及seq请求序号（X+1）

  

Client和Server进入ESTABLISHED状态，完成三次握手，随后Client与Server之间可以开始传输数据了。

1、客户端主机C说：“我可以给你发送数据吗？”

2、服务器S说：“可以的，不过我可能也会给你发送数据。”

3、C：“好，那我开始互相发送数据吧。”

  

**四次挥手为什么比三次握手多一次**
所谓四次挥手（Four-Way Wavehand）即终止TCP连接，就是指断开一个TCP连接时，需要客户端和服务端相互总共发送4个包以确认连接的断开。在socket编程中，这一过程由客户端或服务端任一方执行close来触发。

  ![[TCP协议三次握手四次挥手_image_5.jpeg|600]]

由于TCP连接是全双工的，因此，每个方向都必须要单独进行关闭。这一原则是当一方完成数据发送任务后，发送一个FIN来终止这一方向的连接，收到一个FIN只是意味着这一方向上没有数据流动了，即不会再收到数据了。但是在这个TCP连接上仍然能够发送数据，直到另一方向也发送了FIN。首先进行关闭的一方将执行主动关闭，而另一方则执行被动关闭，上图描述的即是如此。
![[TCP协议三次握手四次挥手_image_6.jpeg|600]]
  

第一次挥手：Client发送一个FIN，以及选择号seq=u（表示：u之前的数据已经全部发送，并且数据发到u就可以截止了，就不再有数据了），用来关闭Client到Server的数据传送。Client进入FIN_WAIT_1状态。
![[TCP协议三次握手四次挥手_image_7.jpeg|600]]


  

第二次挥手：Server收到FIN后，发送一个请求号seq=v和确认序号ack=u+1给Client。Server进入CLOSE_WAIT状态。（此时并不能立即会FIN状态还需要关闭socket）

第三次挥手：Server发送一个FIN，请求号为最新的seq=w和确认序号ack=u+1，用来关闭Server到Client的数据传送。Server进入LAST_ACK状态。

![[TCP协议三次握手四次挥手_image_8.jpeg|600]]
  

第四次挥手：Client收到FIN后，Client进入TIME_WAIT状态，接着发送一个ACK给Server，确认序号为w+1。Server进入CLOSED状态，完成四次挥手。



**四次挥手的情景大致是这样的：**
1、客户端主机C说：“我没有数据了，断开连接吧。 ”
2、服务器S说：“好，但是我还有数据（不断给C发送数据，此时C已经不能给S发送数据了，但是必须要就收S发来的数据）。”
3、（当S给C发完数据后）S说：“我发完了，断开连接吧。”
4、C说：“好，断开连接吧。”
## 常见问题:
为什么TCP不能两次握手
	假设是两次握手，若客户端发起的连接请求阻塞在网络中，会造成该报文的重传，这时服务收到连接请求后会立刻进入连接状态，当双方传输完数据结束连接后，第一次阻塞的请求突然又到达了服务端，此时服务端又进入连接状态，而客户端不会响应服务端的连接确认报文
为什么要进入时间等待状态？
	若客户端发送确认释放包后直接关闭，而服务端因为某种原因没有收到客户端的确认释放包，就会一直发送确认请求，而客户端永远不会再响应该请求。
TCP重传机制和滑动窗口,流量控制:
	[重传机制和滑动窗口,流量控制](https://www.cnblogs.com/xiaolincoding/p/12732052.html)
	
	

**TCP的滑动窗口:**
	为什么需要滑动窗口:
	![[TCP协议三次握手四次挥手_image_9.png|220]]![[TCP协议三次握手四次挥手_image_10.png|370]]
	信息之间传送的缓冲(一次最多能接收到多少信息而不需要回传)
	TCP 头里有一个字段叫 `Window`，也就是窗口大小(这个字段是接收端告诉发送端自己还有多少缓冲区可以接收数据。于是发送端就可以根据这个接收端的处理能力来发送数据，而不会导致接收端处理不过来)大小由接收端决定
	滑动窗口有4个字段1.发送并收到ack确认2.发送但未收到ack确认3.未发送但总大小在接收方处理范围内4.未发送但总大小超过接收方处理范围
	发送方的滑动窗口:
	![[TCP协议三次握手四次挥手_image_11.png]]
	接收方的滑动窗口:
	![[TCP协议三次握手四次挥手_image_12.png]]
	
 接收窗口和发送窗口的大小是相等的吗？
	并不是完全相等，接收窗口的大小是**约等于**发送窗口的大小的。
	因为滑动窗口并不是一成不变的。比如，当接收方的应用进程读取数据的速度非常快的话，这样的话接收窗口可以很快的就空缺出来。那么新的接收窗口大小，是通过 TCP 报文中的 Windows 字段来告诉发送方。那么这个传输过程是存在时延的，所以接收窗口和发送窗口是约等于的关系。

TCP报头信息
	![[TCP协议三次握手四次挥手_image_13.png]]

UDP协议:
	面向无连接，数据不安全，速度快。不区分客户端与服务端。
	不用确认是否建立链接便开始通信,即无链接通信协议
	UDP协议消耗资源小,通信效率搞,偶尔会应用于音频,视频传输,但是会偶尔丢失一两个数据包
	qq.飞秋集邮使用UDP协议.也有TCP协议
	特点:数据被限制在64KB以内,超出范围不能发送
TCP协议:
	面向连接(三次握手),数据安全,速度略低,分为客户端和服务端
	三次握手:客户端先向服务端发起请求,服务端响应请求,传输数据
**Socket通信原理**
	Socket套接字概述：
	网络上具有唯一标识的IP地址和端口号组合在一起才能构成唯一能识别的标识符套接字。
	通信的两端都有Socket。
	**网络通信其实就是Socket间的通信。
	而Socket之间的通信本质上是IO流之间的通信**
	数据在两个Socket间通过IO流传输。
	Socket在应用程序中创建，通过一种绑定机制与驱动程序建立关系，告诉自己所对应的IP和
	port。
**socket通信流程:**
	（1）服务端创建socket并调用bind()方法绑定ip和端口号
	（2）服务端调用listen()方法建立监听，此时服务的scoket还没有打开
	（3）客户端创建socket并调用connect()方法像服务端请求连接
	（4）服务端socket监听到客户端请求后，被动打开，调用accept()方法接收客户端连接请求，当accept()方法接收到客户端connect()方法返回的响应成功的信息后，连接成功
	（5）客户端向socket写入(send)请求信息，服务端读取(receive)信息
	（6）客户端调用close()结束链接，服务端监听到释放连接请求后，也结束链接
	![[TCP协议三次握手四次挥手_image_14.png|600]]
UDP传输JAVA写法
	1.发送Send
	创建DatagramSocket, 随机端口号
	此类用来发送和接受数据报包的套接字
	创建DatagramPacket, 指定数据, 长度, 地址, 端口
	此类表示数据报包
	buf - 包数据。
	length - 包长度。
	address - 目的地址。
	port - 目的端口号。
	使用DatagramSocket发送DatagramPacket
	关闭DatagramSocket
	关流只需要关闭socket对象就行,因为输入或者输出流都是从Socket中获取的
	2.接收Receive
	创建DatagramSocket, 指定端口号
	创建DatagramPacket, 指定数组, 长度
	使用DatagramSocket接收DatagramPacket
	从DatagramPacket中获取数据
	关闭DatagramSocket
	改进------------------------------------------
		在初版基础上改进的需求:
		1,发送端和接收端功能在一个窗口完成.
		2,发送端可以多次发送键盘录入的消息,接收端可以多次接收消息.如果录入的是"quit"那么退
		出发送端,接收端根据发送端的"quit"也退出.
		3,解决接收端使用字节流接收数据,数据后面有多余空格的BUG.
		4,在接收端加入发送者信息的显示(ip+端口)
		5.创建多线程的线程池完成接收和传输
TCP协议传输JAVA写法
	1.客户端
	创建Socket连接服务端(指定ip地址,端口号)通过ip地址找到对应的服务器
	调用Socket的getInputStream()和getOutputStream()方法获取和服务端相连的io流
	输入流可以读取服务端写入的数据
	输出流可以写入数据到服务端的输入流
	2.服务端
	创建ServerSocket(需要指定端口号)
	调用ServerSocket的accept()方法接收一个客户端请求,得到一个Socket
	Socket中的accept方法:侦听并获取到Sockt的链接
	就是创建一个Socket然后接受,其余都是IO的过程
	调用Socket的getInputStream()和getOutputStream()方法获取和服务端相连的io流
	输入流可以读取客户端写入的数据
	输出流可以写入数据到客户端端的输入流
	服务器端是没有io流的,它是由accpet方法接收到的客户端提供的Socket对象调用方法来使用io流的
	因此每个不同客户端提供的不同的socket对象,所以能11对应
	优化------------------
	文件名字写死
	如果名字重复则会覆盖第一个文件所以改进
	名字用System.CurrentTimeMillis()+".jpg"方式命名
	服务端循环接收
	whle(true){
	Socket accept = serverSocket.accept();
	......
	}无限循环
	-
	效率问题:
	利用多线程技术优化
	while（true）{
	Socket accept = serverSocket.accept();
	// accept 交给子线程处理.
	new Thread(() ‐> {
	......
	InputStream bis = accept.getInputStream();
	......
	}).start();
	}