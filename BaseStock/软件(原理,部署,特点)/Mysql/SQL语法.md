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
4. SQL索引语法
	-创建单列索引、符合索引
	CREATE INDEX idx_name on table_name(column_list)
	alter table table_name add index index_name (column_list) ;
	-唯一索引(字段值可以为null)
	CREATE UNIQUE INDEX index_name ON table_name (column_name) ;
	alter table table_name add unique (column_list) ;
	-主键索引(字段值不能为null)
	CREATE PRIMARY KEY INDEX index_name ON table_name (column_list) ;
	alter table table_name add primary key (column_list) ;
	-查看索引
	show index from table_name
	-删除索引
	DROP index index_name on table_name
5. 账号密码
	//查看账号信息不过密码是md5加密的
	select * from mysql.user;







**基础语法**
	











#mysql