**- h辅助命令**
	不知道命令就使用 xxx命令 -h查看帮助  或者xxx命令 来查看有哪些命令
	man命令也是linux最常用的帮助命令
	全意是format and display the on-line manual pages
**linux命令**
	systemctl restart network \#重新配置network
	top \#查看内存占用情况
	 systemctl命令兼容了service
	即systemctl也会去/etc/init.d目录下，查看，执行相关程序
	netstat -? #netstat命令用来打印Linux中网络系统的状态信息，可让你得知整个Linux系统的网络情况
	常用netstat -ntlp  #查看tcp类型端口
	lsof -i:???? #通过????端口号查看进程
	tail -100f #查看100行日志
	nmtui #配置
	ip addr #查看ip等配置
	echo #打印
	chod 777 ??? #给???赋予全部权限(777读写和执行)
	-----------------------------
	ps -ef|grep ??? #查询???是否启动
	rm -rf ??? #删除???程序
	cp -r 源目录 目标目录 #复制目录，复制文件可以不用-r命令参数
	cat ??? #查看???文件内容
	pwd #查询当前文件夹的绝对路径
	tar -zxvf ??? -C /export/server/ #解压??? 到 C/ex....
	VI中的命令(需要先通过yum安装vim)
	:%s/?1/?2/g  #替换所有?1 变成 ?2
	/?   #查找? 按N代表next
	set nu #显示行号
	:wq #保存并退出
	:q! #不保存退出
	??dd #删除??行
	??yy   #复制??行     p粘贴
	其他指令
	source /etc/profile  #立即生效
	expire key seconds  #给key设置过期时间
	ttl key    #查看key的剩余时间  -1：表示没有过期时间  >0 表示剩余的时间 -2:过期，数据被回收
	persist key #不设置过期时间，让它常驻内存
	flushdb  #清空数据库
	flushall  #清空所有的数据库
	dbsize  #数据库的数据总条数
	lastsave  #最近一次保存数据的时间戳
	mutli #开启事务
	exec #提交事务
	discard #回滚事务
	auth #输入密码
	info replication $查询主从状态























