**Nginx的作用和原理:**
	**作用:**
	1.做**反向代理**服务器对多台服务器进行代理
	2.超强的并发处理能力,便捷的配置
	**原理:**
	
1.能支持50000个并发连接数
2.能支持高性能的Web和反向代理服务器
为什么使用nginx:
主要使用场景:当单个服务器tomcat并不能满足程序的访问量的时候,可以使用nginx代理多台tomcat实现集群模式
nginx可以直接当做静态资源的服务器,当加载nginx时候可以直接加载静态资源
负载均衡:平衡代理的多台服务器之间的执行
正向代理的特点:隐藏了客户端（服务器不知道是哪个客户端发送的请求）
反向代理的特点:隐藏了服务端（客户端不知道是哪个服务端返回的消息）
nginx在linux上的搭建
配置环境，make gcc c++等
yum -y install make gcc-c++ zlib zlib-devel libtool openssl openssl-devel libpcre3 libpcre3-dev
同mysql等，可用rpm包或者tar.gz包解压进行下载
解压后进入nginx文件，发现没有bin文件说明是源码（需要编码安装）此时需要makefile文件但是也没有所以需要输入指令生成
./configure --prefix=安装目标地址 --with-http_stub_status_module --with-http_ssl_module --with-pcre
配置完成后使用make命令编译，编译完成后使用make install安装
安装后到指定目录的nginx 的conf里nginx.conf修改配置文件
到sbin文件夹中启动nginx
systemctl start keepalived启动keepalive
nginx高可用实现配置
1：准备两台服务器（克隆）
2：每台服务器上安装nginx，nginx的代理配置要一样
3.安装keepalive
#yum安装keepalived
yum install -y keepalived
#查看是否已安装keepalived
rpm -q -a keepalived
4.配置keepalive启动(配置代理端口)
# vim /etc/keepalived/keepalived.conf
配置完成后启动keepalive然后就可以使用代理ip访问了
systemctl start keepalived启动keepalive
自此成功完成nginx代理配置和负载均衡和保证nginx高可用
动静分离
nginx可以充当(freeMarker)静态详情页面的服务器
![[Nginx(反向代理服务器)_image_1.jpg]]
配置:
全局块
worker_processes，工作进程数
1.默认：worker_processes: 1
2.调大：worker_processes: CPU核心数，(双核4线程，可以设置为4)
worker_rlimit_nofile
Nginx worker进程最大打开文件数
进程可以开局打开多个进程,根据电脑性能设定,等同cpu数
一般默认设置为1
vent块
worker_connections，单个工作进程可以允许同时建立外部连接的数量
数字越大，能同时处理的连接越多
1.默认：worker_connections: 1024
2.调大：worker_connections: 100000，（调大到10万连接）
connections不是随便设置的，而是与两个指标有重要关联，一是内存，二是操作系统级别的“进程最大可打开文件数”。
最大可打开文件数：进程最大可打开文件数受限于操作系统，可通过 ulimit -n 命令查询
nginx的并发=worker_processes（进程数）*worker_connections（线程数）/(4或者2)
动态资源除以4
静态资源除以2
http块(核心页面配置块)
#nginx日志格式，可以自定义
1----log_format main '$remote_addr - $remote_user [$time_local] "$request" '
2----'$status $body_bytes_sent "$http_referer" '
3----$http_user_agent" "$http_x_forwarded_for"';
#gzip on; #文件特别大可以开启压缩
server {
listen 80; #监听端口
server_name localhost;#监听地址 例如：www.abc.com
#access_log logs/host.access.log main;
location / {
root html;#根目录
index index.html index.htm;#根目录下页面
}
location资源映射 匹配机制
location = / {=是严格匹配
location = /login {=是严格匹配
location ^~ /static/ {^~表以什么开头
location ~*.(gif|jpg|jpeg|png)$ {匹配任何以 gif、jpg 或 jpeg 结尾的请求。
nginx配置虚拟主机
如果端口相同，那么server_name必须不同
server_name相同，端口不同
客户端通过域名访问服务器时会将域名与被解析的ip一同放在请求中。当请求到了nginx中时。nginx会先去匹配ip，如果listen中没有找到对应的ip，就会通过域名进行匹配，匹配成功以后，再匹配端口。当这三步完成，就会找到对应的server的location对应的资源。
如何实现反向代理功能
location / {
#可以实现对外隐藏端口和域名细节，下面的ip地址也可以在/etc/hosts中修改域名映射方便书写 本质是主机通过nginx重定向到tomcat
proxy_pass http://192.168.174.129:8080/;
}
如何实现负载均衡
upstream wfx { #upstream模块实现负载均衡策略·并定义多台服务器
server localhost:8080;
server localhost:8081;
}
权重方式是后方跟 weight= ？；
iphash方式是在最上方加 ip_hash；（根据每个ip的hash结果分配且固定后不改变）
least_conn 最上方加least_conn（将请求分配到链接最少的服务上）
fair同iphash 在最上方加fair；（根据响应时间最短的来返回）
nginx高可用
安装keepalived
准备一台备用nginx防止nginx宕机
![[Nginx(反向代理服务器)_image_2.jpg]]
用户访问虚拟ip192.168.110
但是内部是始终访问主nginx的131ip并持有锁如果主nginx死亡keepalive也会死亡释放锁，因为主nginx死亡备份nginx132ip获得锁并绑定vip（virtual ip）。这样保持高可用用户体验无察觉