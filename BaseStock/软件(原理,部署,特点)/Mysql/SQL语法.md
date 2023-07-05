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
	1. 数据定义语句DDL:  
    create、alter、drop、truncate（表结构）
	2. 数据操作语句DML:  
    insert、delete、update、select（数据）
	3. 数据控制语句DCL:  
    授权grant  
    收回权限：revoke
	4. 事务控制语句TCL:  
    开启事务：begin  
    提交：commit  
    回滚：rollback
**数据库操作**
	**创建数据库**
	create database stu_b default charset='utf8';
	**显示数据库结构**
	show create database 数据库名;
	**删除数据库**
	drop database 数据库名;
**表操作**
	**选择数据库**
	Use 数据库名;
	**创建表**
	create table 表名(id int,name varchar(20)...表属性)
	**显示表结构**
	desc 表名
	show create table 表名
	**删除表**
	drop table 表名;
	**删除字段**
	alter table 表名 drop 字段名;
	**添加新字段**
	alter table 表名 add 新字段名 新数据类型\[新约束条件];
	**修改字段名**
	alter table 表名 change 旧字段名 新字段名 新数据类型
	**修改数据类型**
	alter table 表名 modify 字段名 新数据类型
	**修改表明**
	renanme table 旧表名 to 新表名
**数据增删改查**
	**插入**
	insert into 表名(字段列表) values(值列表);
	insert 表名 values(值列表) --该方式仅当值个数和字段个数相同时使用
	insert into 表名(字段列表) values(值列表1)(值列表2)..(值列表n); ---插入多条
	insert into  表名(字段列表1) select(字段列表2) form 表名2 where 条件表达式 --将select的结果插入
	create table 新表 select * from 旧表 --赋值表结构及数据到新表
	**修改**
	update 表名 set 字段名1 = 值1 , 字段名2 = 值2...where\[条件表达式]
	**删除**
	delete from 表名 where \[条件表达式];
	truncate table 表名 ---该方式直接清空表记录
	**查询**
	select 字段列表 from 表名 where \[条件表达式]
	



delete和truncate的区别
	Delete不加WHERE条件是删除所有数据
	Truncate不能够加WHERE条件
	Delete可以加WHERE条件
	Truncate会重置AUTO_INCREMENT
	Delete可以进⾏回滚操作














#mysql