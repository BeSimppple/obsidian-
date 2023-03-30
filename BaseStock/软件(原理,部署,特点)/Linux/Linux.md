为什么使用linux:

1.模块化程度高,内核设计分成进程管理、内存管理、进程间通信、虚拟文件系统、网络5部分

2.安全稳定。核心防火墙组件性能高、配置简单、比window稳定宕机概率很低

3.多用户，多任务。多用户是指系统资源可以同时被不同的用户使用，每个用户对自己的资源有特定的权限，互不影响。

centOS系统目录详解:[https://blog.csdn.net/Aplumage/article/details/122590105](https://blog.csdn.net/Aplumage/article/details/122590105)

使用VMware生成虚拟机 然后在linux上配置redis和niginx以及nacos等技术性组件

linux命令格式：

    COMMAND [OPTIONS...] [ARGUMENTS...]

    COMMAND：命令，表示可执行的二进制格式文件或者脚本程序文件

    OPTIONS：选项，可以用来调整命令的运行，[]中括号中的内容可以省略，一般命令可以支持一个或多个选项

    ARGUMENTS：参数，表示命令作用的对象，如文件名，用户名、网卡名等，支持多个参数

linux常用命令

man xxx（命令） 可以查看有哪些参数

tab键智能补全

cd命令：用于切换当前目录

ls命令：查看文件与目录的命令，list之意

mkdir命令：创建

rm命令：删除

mv命令：该命令用于移动文件、目录或更名，move之意

cp命令：该命令用于复制文件，copy之意

find命令：find是一个基于查找的功能

ps命令：用于将某个时间点的进程运行情况选取下来并输出，process之意

kill命令：用于向某个工作（%jobnumber）或者是某个PID（数字）传送一个信号，它通常与ps和jobs命令一起使用 杀死进程

tar命令：该命令用于对文件进行打包，默认情况并不会压缩

chmod命令：该命令用于改变文件的权限

nohup命令：不挂断地运行命令。

sudo命令: super do使普通用户也能快速调用管理员权限的命令(使用需要输入管理员账号密码)

nohup command > nohupcmd.out 2>&1 &

cd /

cd ~或者cd

cd bin

cd ..

pwd

ll

mkdir

mkdir -p

rm -rf

cp -r 源目录 目标目录 #复制目录，复制文件可以不用-r命令参数

#查看命令的命令参数

man cp

cp 源文件 目标文件

mv a a1 改名

mv a1 b 剪切

文件命令：

touch

cat 查看文件内容

tail -f 查看滚屏日志

netstat -nltp 查看运行中进程

source命令 source 、etc/profile

sh命令执行shell文件

ssh命令 连接其他端口或主机

临时关闭： systemctl stop firewalld

禁止开机启动 ：systemctl disable firewalld

系统服务指令：

ps -ef命令 ps -aux

top命令查看进程占用cpu情况

lsof -i：8080 通过端口查看进程

hostnamectl 修改主机名（修改完后修改etc/hosts完成主机名映射后可以通过ssh命令加主机名实现主机跳转）

wget与yum的区别

一般使用yum(因为yum是从镜像库中取软件)wget是通过http取任何软件

文件命令：VIM

文件命令vim/vi（linux中通常使用vim/vi来操作文件内容）

yum安装vim

yum -y install vim*

vim的工作模式（命令模式和底线命令模式）

![[Linux_image_1.jpg]]
![[Linux_image_2.jpg]]
  

命令模式：

gg 光标到第一行

G 光标到最后一行

nG 到第n行

dd 删除当前行

10dd 删除n行

yy 复制当前行 p粘贴

nyy复制n行 p粘贴

底线命令模式：
![[Linux_image_3.jpg]]

:set nu 显示行号

:set nonu 不显示行号

:wq保存退出

:q!不保存退出

权限指令：
![[Linux_image_4.jpg]]
chmod授权命令

sudo指令

|grep管道符过滤

linux安装软件方法

源码包（包内有src这种）-》编译（make执行makefile）-》安装（make install）（不常用效率低容易出问题）

二进制包（rpm）-》通过（rpm or yum）安装（常用）

tar.gz解压安装（应用服务安装 常用）tar -zxvf XXXX -C （指令路径）

通过secrueFX本地上传rpm包安装

  

公司实际操作中会配置堡垒机（个人电脑无法通过linux联网下载，只有通过堡垒机下载）

Linux安装各个软件!!!!!!

(几乎所有环境内容都可放到lunix)

安装jdk

yum下载jdk的rpm包(自动配置到/usr/bin/java不需要配置环境变量了)或者gz包解压(需要配置环境变量)

配置/etc/profiles

加上JAVA_HOME=XXX和PATH_HOME=$PATH:$JAVA_HOME/bin

之后使用source /etc/profiles进行刷新配置就可用了

linux安装maven

需要有jdk环境,官网下载maven的gz包,解压到指定目录

/etc/profile配置MAVEN_HOME=XXX和MAVEN_HOME=$PATH:$JAVA_HOME/bin

mvn -v查看版本

linux安装mysql

安装yum-》安装wget-》安装mysql的rpm-》yum install mysql

systemctl start mysqld（默认会去/user/bin目录下寻找启动配置）

linux安装tomcat服务器

找到、var/log/mysqld.log下的密码(grep 'temporary password')

mysql -uroot -p 输入密码进入mysql

进入后SET PASSWORD = PASSWORD('Admin123!');改密码

设置root用户可以在‘%’所有ip访问mysql通过密码。。。

grant all privileges on *.* to 'root'@'%' identified by 'Admin123!';

GRANT ALL PRIVILEGES ON *.* TO 'ROOT'@'%' IDENTIFIED BY

（切换到mysql的databases的user表将user为root的host改为%）

'Admin123!' WITH GRANT OPTION;

找到/etc目录（配置文件目录）下的my.conf文件

使用sql语句将常规设置（例如编码utf-8，时区配置，支持group by语句）

然后设置开机启动mysql

systemctl enable mysqld

systemctl daemon-reload

systemctl disable firewalld禁用防火墙链接navicat

linux安装tomcat服务器

同理mysql获取rpm包或者gz压缩包解压到指定目录

tomcat需要java环境和jdk需要下载配置

如果是rpm通过yum安装能自动配置环境变量，gz解压安装则要自己配置环境变量（vim /etc/profile）

在最后新增行 export JAVA_HOME='JDK路径和jdk文件名'

export PATH=$PATH:$JAVA_HOME/bin

修改完后wq保存退出 在使用source命令使生效source /etc/profile

进入tomcat的bin文件夹使用sh startup.sh启动

可以进入logs文件夹使用tail滚动命令查看catlina.out文件日志看状态

配置进idea并校准db。properties信息

配置完成后clean&install将target包中的war包（编译后class文件包）放到tomcat的webapps文件夹内，并改名设置为root（默认启动，且省略输入项目名）

实现多个tomcat可直接复制tomcat，然后修改conf下的server.xml将端口8080修改为其他，8005端口也修改其他避免冲突

部署WEB项目

WAR包放到tomcat的webapps即可

linux安装sentinel

下载sentinel-dashboard-1.8.0.jar放到server包

jar包通过java命令打开,linux中使用nohup java -Dserver.port=8888 -Dcsp.sentinel.dashboard.server=localhost:8888 -Dproject.name=sentinel-dashboard-1.8.0 -jar sentinel-dashboard-1.8.0.jar &)

nohup命令启动后按crit+c退出他也会无视关闭命令在后台运行

然后便可在网页中输入linux的ip:8888/打开sentinel输入账号密码sentinel即可

修改配置在源码的application.properties中

linux安装zipkin(同windows java-jar运行即可)

nohup command > nohupcmd.out 2>&1 &格式书写后台启动

nohup java -jar zipkin-server-2.12.9-exec.jar --server.port=9999>zipkin.log 2>&1 &

linux安装elasitcsearch和kibana(同windows java-jar运行即可)

linux安装redis(详情见redis记录)

linux安装nginx(详情见nginx记录)

linux安装nacos(详情见nacos记录)

  

  

云主机（腾讯云或阿里云等购买使用）

没有设置开机启动的需要手动启动，因此写个shell脚本去帮助一键启动会更方便

  

ip配置

ip:192.168.2.254

mask(掩码):255.255.255.0

为什么是255(因为能匹配所有段落)

因为255拆成2进制为:11111111

1& 1或0 都是原数

0& 1或0 都是0

吧192转成2进制:11000000

两者一&就是192为同一个网段

#是root用户 $是普通用户

发行版本选择centOS或 RHEL 因为比较稳定

Ubuntu带桌面系统 Gentoo自由探索
![[Linux_image_5.jpg]]
dns：192.168.174.2 网上的服务器帮助找其他网站的主机 国内的114.114.114.114 和google的8.8.8.8.8

host里面是域名和ip的映射关系

dns是找到网络上对应的host文件去根据网址和ip找主机地址（例如百度网址找百度主机）

NAT链接虚拟局域网
![[Linux_image_6.jpg]]
1：先查看虚拟的交换机的ip地址

2：配置虚拟机的ip地址（默认是DHCP动态ip）

3：nat上网模式特点，与宿主机在一个局域网、只要宿主机能连接外网，那么虚拟机就可以连接外网

桥接上网模式

使用真实交换机要求虚拟机配置静态的ip、且必须和真实交换机在同一网段，弊端电脑换地交换机更换ip网段变更需要变更虚拟机网段设置

  

使用shell终端SecureCRTPortable（或者Xshell）去更快捷的敲命令

SecureFX可视化操作文件夹等·

Redis中的事务跟mysql不一样，Redis单条指令可以保证原执行，但是多条不能保证原子性

  

#编译时异常,回滚

#运行时异常，不回滚

  

注意：

  

1：修改bind

  

bind ip错误理解：不是说bind哪个ip ，就只能哪个ip来访问redis

  

bind ip正确理解： bind本机的网卡对应的ip地址，只有通过指定网卡进来的主机才能访问redis

  

bind 127.0.0.1（默认），本地回环地址。那么访问redis服务只能通过本机的客户端连接，而无法通过远程连接

  

bind 192.168.234.131 通过本机ens33网卡进来的主机都可以访问redis，这样设置后基本所有的主机都可以访问

  

如果要限制只允许某些host访问，那么可以通过配置安全组实现
![[Linux_image_7.jpg]]
2：redis设置密码

  

Redis6之前Redis就只有一个用户(default)权限最高，通过配置文件的requirepass配置

  

Redis6版本推出了**ACL(Access Control List)访问控制权限**的功能，基于此功能，我们可以设置多个用户，为了保证**向下兼容**，Redis6保留了default用户和使用requirepass的方式给default用户设置密码，默认情况下default用户拥有Redis最大权限，我们使用redis-cli连接时如果没有指定用户名，用户也是默认default

  

3:ACL常用命令

ACL whoami

ACL list

ACL setuser allen on >123456 +@all ~*

AUTH allen mypasswd

  

#只能创建以lakers为前缀的key

ACL setuser james on >123456 +@all ~lakers*

#不拥有set权限

ACL setuser james -SET

  

ACL DELUSER james

  

  

从外部导依赖到mvn中 在cmd执行

mvn install:install-file -Dfile=taobao-sdk-java-auto_1455552377940-20160607.jar -DgroupId=com.alimama -DartifactId=sms -Dversion=1.0 -Dpackaging=jar

  

Linuix常见知识点

文件句柄0,1,2分别代表(标准输入,标准输出,标准错误)