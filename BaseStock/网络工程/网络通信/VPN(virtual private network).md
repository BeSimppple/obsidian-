[VPN文章分析](https://www.anquanke.com/post/id/248136)
由于 VPN 在不同系统下的实现方式不同，为了便于学习和理解，这里我们选择 `Linux` 环境，我本地测试环境使用的是 `Ubuntu 18.04 x64`。

本文从 TUN/TAP 出发，逐步理解 VPN 中的技术细节；并结合 [simpletun](https://github.com/gregnietsky/simpletun) 源码，进行 VPN 的原理验证。
实现原理:利用隧道协议（Tunneling Protocol）来达到发送端认证、消息保密与准确性等功能。
以C/S架构(client/server架构)运行工作流程如下:
![[VPN(virtual private network)_image_1.png]]













![[VPN(virtual private network)_image_2.png|600]]






