**匿名函数(lambda)**
	**lambda 属性 : 表达式**
	将: 后面的**表达式**返回值赋值给**属性**
基础系统方法
	详情查看python官方帮助文档,基础类型的方法基本类似JAVA
	**变量相关**
	type(变量)  查看变量类型
	int(变量)  类型转换
		String如果不是数字或包含小数点则无法转换成int
	float(变量)  类型转换
	str(变量)   类型转换
	bool(变量)  类型转换
		非0整数转换为boolean都为true
		只要其中有内容则均为true
	**输入输出**
		print()
			age = 18
			name = "老王"
			print("他的名字是%s,年龄是%d"%(name,age))
			类似sql预编译,%s代表填入字符串 %d代表填入数字类型
		input()
			类似Java的System.in()
文件相关
	open()函数打开文件
	write()写文件
	read(),readlines()读文件
序列化
	由JSON包提供 需要import json
	dumps() dump()  将对象序列化
	load()数据反序列化成对象
filter()、map()、reduce() 的作用
	1. filter 函数用于过滤序列，它接收一个函数和一个序列，把函数作用在序列的每个元素上，然后根据返回值是True还是False决定保留还是丢弃该元素。
		1. mylist = list(range(10))
		2. list(filter(lambda x: x % 2 == 1, mylist))
		3. 返回值:[1, 3, 5, 7, 9]    
	2. map函数传入一个函数和一个序列，并把函数作用到序列的每个元素上，返回一个可迭代对象。
		1. 例子: list(map(lambda x: x % 2, mylist))   将mylist中所有元素计算后返回新的对象
	3. reduce函数用于递归计算，同样需要传入一个函数和一个序列，并把函数和序列元素的计算结果与下一个元素进行计算
		1. 例子: reduce(lambda x, y: x * y, [1, 2, 3, 4])     \[1,2,3,4]所有元素的乘积