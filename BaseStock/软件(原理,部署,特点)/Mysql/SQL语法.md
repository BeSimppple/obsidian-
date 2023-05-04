##### 系统语法
1. show variables like "%char%";  查看本地地址环境配置
2. 隔离级别设置语法:
	1.set global trandaction isolation level 隔离级别
	--设置全局隔离级别,需要重新连接数据库
	2.set session transaction isolation level 隔离级别;
	--设置会话隔离级别,不需要重新连接数据库
	3.select @@tx_isolation;
	--查看隔离级别
3. 设置慢查询
	set global slow_query_log=1; 设置开启全局慢查询日志
	show variables like '%slow_query_log%';  查询慢查询变量是否开启
	




















#mysql