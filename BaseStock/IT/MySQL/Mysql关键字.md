**Mysql关键字执行顺序**
	 （8）SELECT（9）DISTINCT <select_list>
	 （1）FROM <left_table>
	 （3）<join_type> JOIN <right_table>
	 （2）ON <join_condition>
	 （4）WHERE <where_condition>
	（5）GROUP BY <grout_by_list>
	 （6）WITH {CUTE|ROLLUP}
	 （7）HAVING <having_condition>
	 （10）ORDER BY <order_by_list>
	（11）LIMIT <limit_number>
**group by**
	**作用**:常用语聚合函数(例如sum)将结果通过合并后再呈现
**join on**
	分作letf join on 和 right join on左右链接
	join后面跟表名
	on后面跟条件(condition)
	尽量避免使用左右链接会降低效率,使用inner或nature join
	一定要使用时尽量使用小表做驱动表更高效(因为驱动表要做全表扫描)
**innner**
	和join on相同,但是是取两表的交集
	不同点是MySQL 会根据优化器的算法自动选择一个表作为驱动表
	几个选择关键条件(表大小,索引,链接条件)
distinct
	去重
UCASE/LCASE
	大写/小写  字段中的值
	例子:SELECT LCASE(name) AS names FROM user;
grand
	授权语句
	GRANT privilege [, ...] ON object [, ...]  
	TO { PUBLIC | GROUP group | username }
	赋值对象:table  
view  
sequence
	**所有权限**：  
		SELECT  
		访问声明的表/视图的所有列/字段．  
		INSERT  
		向声明的表中插入所有列字段．  
		UPDATE  
		更新声明的表所有列/字段．  
		DELETE  
		从声明的表中删除所有行．  
		RULE  
		在表/视图上定义规则 （参见 CREATE RULE 语句）．  
		ALL  
		赋予所有权限．  
		object
## sql函数:
**sleep()和benchmark()**
	用来延时执行语句
	例如:sleep(5)   benchmark(100000000,md5(1))
len(字段)
	**作用**:字符长度
	不存在  显示0
	存在则  显示长度   范围从(1-len)
stuff()
	**作用**: 删除指定位置字符并在指定位置加入字符
	STUFF ( 原字符 , 开始下标 , 删除字符数 , 新增字符 )
	例子: select stuff('lo ina',3, 1, 've ch')  返回love china