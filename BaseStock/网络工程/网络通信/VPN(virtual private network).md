[VPN文章分析](https://www.anquanke.com/post/id/248136)
由于 VPN 在不同系统下的实现方式不同，为了便于学习和理解，这里我们选择 `Linux` 环境，我本地测试环境使用的是 `Ubuntu 18.04 x64`。
本文从 TUN/TAP 出发，逐步理解 VPN 中的技术细节；并结合 [simpletun](https://github.com/gregnietsky/simpletun) 源码，进行 VPN 的原理验证。
实现原理:利用隧道协议（Tunneling Protocol）来达到发送端认证、消息保密与准确性等功能。
**高匿名,匿名和透明代理**
	**高匿名**:服务器不知道你使用了代理.也不知道你的真实ip
	**匿名**:服务器知道你使用了代理,但不知道你的真实ip
	**透明代理**:服务器知道你使用了代理,也知道你的真实ip

以C/S架构(client/server架构)运行工作流程如下:
	![[VPN(virtual private network)_image_1.png|600]]

**工作原理:**
	在外网的用户可以使用 `vpn client` 连接组织搭建的 `vpn server` 以建立通信隧道，随后便建立了虚拟的私人网络，处于外网的 `worker` 和内网中的 `server` 可以相互通信。
	那么我们可以简单理解 VPN，由 `VPN client` 捕获用户发出的报文，封装报文后通过物理网络通信链路将报文发给 `VPN server`，`VPN server` 接收到报文后进行解包，再将其转发给实际的目标，反之同理； VPN 在逻辑层面构建了虚拟网络。
**代码层面具体实现:**
	TUN/TAP 是操作系统内核中的虚拟网络设备，由软件进行实现，向操作系统和应用程序提供与硬件网络设备完全相同的功能。其中 TAP 是以太网设备(二层设备)，操作和封装以太网数据帧，TUN 则是网络层设备(三层设备)，操作和封装网络层数据帧。
	当应用程序发出报文后，报文将通过操作系统协议栈处理，到达网络设备，硬件网络设备将收到的报文转化为电信号发出，而虚拟网络设备(TUN/TAP)不具备实际的物理功能，报文需要上层应用进行处理，如下：
	![[VPN(virtual private network)_image_2.png|600]]
	2.硬件/虚拟网络设备
	我们直接使用命令创建 TUN/TAP 设备，并进行测试：
	```csharp
	# ip tuntap 创建名为 tun0 的 tun 设备
	sudo ip tuntap add dev tun0 mod tun
	# 为 tun0 配置 ip
	sudo ifconfig tun0 192.168.0.10 netmask 255.255.255.0
	# 查看 tun0 网卡
	ifconfig tun0
	```
	![[VPN(virtual private network)_image_3.png|600]]
	3.通过命令创建TUN设备
	在 VPN 中我们可以借助 TUN/TAP 来捕获用户发出的报文。
	## 0x03 虚拟通信链路
	按照 TUN/TAP 的工作特性，我们可以编写程序直接读写虚拟网卡(也就是物理网卡实际收发报文的过程)，来实现捕获用户数据以及传递用户数据。(TUN 和 TAP 有不同的应用场景，下文我们将以更简单的 TUN 作为例子)
	随后，位于不同主机上的程序通过 socket 进行通信，将从虚拟网卡的接收的数据通过 socket 发送给对端，这就是一个 VPN 的雏形了，如下：
	![[VPN(virtual private network)_image_4.png|600]]
	4.虚拟通信链路工作流程
	`simpletun` 是这种方案的最小实现(源码仅 300+ 行，感兴趣的小伙伴可以自行学习)，在源码中实现了创建虚拟网络设备以及 socket 通信，借助 `simpletun` 可以帮助我们快速进行验证。
	需要注意一点，`simpletun` 启动后需要我们手动配置虚拟网卡的 ip 地址，当 ip 地址未配置时，两端相互发送数据(部分操作系统会自动发送)会造成程序异常退出，所以在代码中添加一个 `sleep(30)` 便于我们配置 ip 地址：
	![[VPN(virtual private network)_image_5.png|600]].在simpletun中添加sleep
	在两台 `Ubuntu` 测试环境下配置并进行验证：
	```bash
	# A主机
	# 编译 simpletun
	gcc simpletun.c -Wall -o vpn
	# 作为 vpn server 启动，并开启 debug，默认监听 55555
	sudo ./vpn -i tun0 -s -d
	# 配置 tun 网卡地址
	sudo ifconfig tun0 192.168.0.10 netmask 255.255.255.0
	# B主机
	# 编译 simpletun
	gcc simpletun.c -Wall -o vpn
	# 作为 vpn client 启动，连接 server，并开启 debug
	sudo ./vpn -i tun0 -c 10.11.33.50 -d
	# 配置 tun 网卡地址
	sudo ifconfig tun0 192.168.0.11 netmask 255.255.255.0
	```
	此时两台主机位于 `192.168.0.0/24` 虚拟网络网段下，可以相互通信，如下：
	![[VPN(virtual private network)_image_6.png|600]]两端通信
	## 0x04 访问内网网段
	在上文的验证中，我们可以实现两端的虚拟网络搭建和通信，但实际 VPN 的使用场景是需要通过 VPN 访问整个内网网段，在这种使用场景下，VPN server 至少配置有两张物理网卡，其中一张接入内网网段，另一张则连接到互联网。
	按照 `0x03 虚拟通信链路` 的链路，VPN client 发送报文到内网主机，VPN server 接收到该报文后，将其写入到虚拟网卡中，随后报文进入 TCP/IP 协议栈，但是由于 IP 地址不是 VPN server 自己，该报文会被丢弃，无法正常进行通信；这里我们需要借助「报文转发」，将内网报文从虚拟网卡转发到内网网卡上。其新的工作流程如下：
	![[VPN(virtual private network)_image_7.png|600]]一般会作为内网网关，内网主机无需任何额外配置就可以在虚拟网段下正常工作。
	我们按照该流程配置测试环境，复用 `0x03 虚拟通信链路` 中的环境，在 VPN server 上我们使用 docker 模拟内网网段和主机，其环境搭建如下：
	![[VPN(virtual private network)_image_8.png|600]]
	8.VPN测试环境搭建
	然后按照 `0x03 虚拟通信链路` 中的方式，启动 `simpletun` 并使用 `ifconfig` 配置 ip 地址，创建虚拟通信链路；使用如下命令开启报文转发：
	```bash
	# 临时开启报文转发
	echo "1" > /proc/sys/net/ipv4/ip_forward
	```
	实际上在该测试环境下，docker 会自动开启报文转发
	再通过 `iptables` 配置转发策略，如下：
	```css
	# 将入口网卡、来源ip为 192.168.0.0/24 转发至 docker0
	sudo iptables -A FORWARD -i tun0 -s 192.168.0.0/24 -o docker0 -j ACCEPT
	# 将入口网卡、目的ip为 192.168.0.0/24 转发至 tun0
	sudo iptables -A FORWARD -i docker0 -d 192.168.0.0/24 -o tun0 -j ACCEPT
	```
	实际上在该测试环境下，第二条可以不用配置，因为 docker 会自动配置转发策略，会覆盖这条策略
	除此之外，为了在 VPN client 可以访问到内网主机，需要手动添加路由：
	```csharp
	# VPN client 添加内网网段路由，设置为虚拟网络设备 tun0
	sudo route add -net 172.17.0.0/24 tun0
	```
	此时 VPN 配置完成，内网主机和 VPN client 相互连通：
	![[VPN(virtual private network)_image_9.png|600]]
	9.内网主机访问以及验证
