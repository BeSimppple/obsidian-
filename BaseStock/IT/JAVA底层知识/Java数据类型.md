**八大基础数据类型**
	**int**
		1. int常量池中初始化-128~127的范围(所以两个变量指向这个范围的是同一个地址如果超过这个范围则是自定的地址)
		2. int 占据 4 个字节，表示的范围是：-2147483648 ~ 2147483647
	**short**
		1. short 占据 2 个字节，表示的范围是： -32768 ~ 32767
	**long**
		1. 当数据运算超过最大值时会使用补码和反码
			主要出现在数字类型超出规定值时产生,类似long A=long.MaxValue + 1;
			**原码:** 二进制定点表示法即最高位位符号,"0"表示正"1"表示负
			其余位置表示数值的大小
			**反码**:正数的反码与其原码相同:负数的反码是对其原码的逐位取反,但符号位除外
			**补码**:正数补码和反码相同,负数补码在其反码的末位加1
		2. long 占据 8 个字节，表示的范围是：-9223372036854775808 ~ 9223372036854775807
	**float**
		float只要进行运算或其他方法例如Math.floor(float变量)  就会自动转换成double
	**double**
		1. java中默认的小数的类型
		2. Java中带小数定义默认为double,如果要float A =(float)1.0  需要强转
	**byte**
		byte 是存储整数的最小类型，只占 1 个字节，因为还需要表示负数，因此只能保存 -128 ~ 127 范围内的数据
		byte和byte进行运算不论大小都会先自动转换成int进行计算
	**char**
		1. java使用的是Unicode,java8之前String使用的char类型,java9之后使用的byte
		2. char a ='6';  打印(a-'1'); 结果为5   底层会自动转换成ASCII码进行计算6对应54 1对应49结果为5,ASCII中数字只有0到9 依次从0-9为48-57
	**boolean**
		只有ture和false,0是false,1是true
	-----
	1. 类型转换会从小的转换到大的(整数型转换成浮点型)
		1. double -> float -> long -> int -> char -> short -> byte
		2. 强制类型转换可能会导致数据丢失或精度降低，因为目标类型可能无法容纳原始类型的所有可能值。
**基础数据类型什么时候会被自动赋值**
	1. 没有初始值的时候,且非局部变量才会被赋予初始值
**什么时候需要强制转换**
	1. 高精度转低精度应强制转换
	2. 低精度向高精度不需要强制转换
String
	1. String是immutable(不可变的)
	2. String底层使用的是char[]数组,char[]使用的是unicode能一个格子存放一个中文字符,所以长度就是字符串长度
\== 和equal的区别
	\== 在比较基础数据类型的时候比较的是值,引用数据类型比较的是地址值
	equal比较的是值


---
## 常用方法
**常用类**
	Character类
		toString()方法 将char转换成String

String转换成String[]和char[]  (  split()和toCharArray()  )
	1. split是根据正则表达式分割成Sring[]
	2.toCharArray是转换成char[]数组
查看参数类型
	1. Class.instanceof(数据)
	2. instanceof关键字  例子:boolean isInstance = obj instanceof Class









