[VPN文章分析](https://www.anquanke.com/post/id/248136)
由于 VPN 在不同系统下的实现方式不同，为了便于学习和理解，这里我们选择 `Linux` 环境，我本地测试环境使用的是 `Ubuntu 18.04 x64`。

本文从 TUN/TAP 出发，逐步理解 VPN 中的技术细节；并结合 [simpletun](https://github.com/gregnietsky/simpletun) 源码，进行 VPN 的原理验证。
实现原理:利用隧道协议（Tunneling Protocol）来达到发送端认证、消息保密与准确性等功能。
##### 以C/S架构(client/server架构)运行工作流程如下:
![[VPN(virtual private network)_image_1.png]]

##### 工作原理:
在外网的用户可以使用 `vpn client` 连接组织搭建的 `vpn server` 以建立通信隧道，随后便建立了虚拟的私人网络，处于外网的 `worker` 和内网中的 `server` 可以相互通信。

那么我们可以简单理解 VPN，由 `VPN client` 捕获用户发出的报文，封装报文后通过物理网络通信链路将报文发给 `VPN server`，`VPN server` 接收到报文后进行解包，再将其转发给实际的目标，反之同理； VPN 在逻辑层面构建了虚拟网络。

##### 代码层面具体实现:









![[VPN(virtual private network)_image_2.png|600]]






