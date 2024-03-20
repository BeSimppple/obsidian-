VMware创建虚拟机后会出现2个网络接口,VMnet1和VMnet8
	vmnet1和vmnet8是两个虚拟网卡，主要作用是让虚拟机可以通过你的宿主机的网线上网。
		vmnet1是为host-only方式服务的，vmnet8是为NAT方式服务的。

**联网模式:**
**host-only模式和nat模式**
	host-only表示仅搭建内部局域网
	nat模式下虚拟机可以通过宿主机
使用桥接模式上网需要网络中存在DHCP服务器，且提供服务



#NAT联网
![[Linux网络_image_1.png|575]]
1：先查看虚拟的交换机的ip地址
2：配置虚拟机的ip地址（默认是DHCP）
3：nat上网模式特点，与宿主机在一个局域网、只要宿主机能连接外网，那么虚拟机就可以连接外网



