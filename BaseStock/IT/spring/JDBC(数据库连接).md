全称:Java DataBase Connectivity standard
**JDBC原理和作用:**
	是一只执行SQL语句的java的API,可以为多种关系型数据库提供统一的访问,**它是由一种使用java语言编写的类或接口组成**
	作用是**链接数据库**
**什么是驱动?**
	java语言要连接数据库必须使用数据库的驱动
	各个数据库的生产商必须提供数据库的驱动,使用java连接各种数据库,需要了解各个数据库的驱动,这样会增加java程序员的压力.
	cun公司与各个数据库生厂商协商,由SUN公司提供一套统一
JDBC创建步骤
	1.搭建环境，导入驱动jar包
	2.加载驱动
	3.获取数据库连接
	4.获取执行sql对象
	5.编写sql,执行sql
	6.释放资源
	方法重载:在同一个类中方法名相同,传入参数不同
JDBC的事务管理
	没有异常--提交数据
	出现异常--事物回滚,提交数据
	现在开头设置:
	//设置不自动提交
	connection.setAutoCommit(false);
	//这样结束程序时就会自动提交false
	然后程序中段部分如果发生异常
	在catch(Exception e){//发现异常后,处理异常
	e.printStackTrace();//这时候发现并且处理异常时加入
	try{//设置回滚
	connection.rollback();//尝试回滚
	}catch(SQLException e1){
	e1.printStackTrace();
	}finally{//最后在finally这里必定执行
	try{
	//手动提交
	connection.commit();//提交
	}catch(SQLException e){
	e.printStackTrace();
	}
	}
现实生活中的事务管理
	在生活有些方法不能绑定执行,如银行的转账
	转出去和存到别的账户不可能绑定在一起,这时候绑定事务管理就无法执行,事务管理不在一起那么其原子性也不能保证
	所以使用了以下的事务管理方法
	首先如果两个执行方法在同一个类的main方法中
	使用ThreadLocal方法(存储一个变量Connection链接)
	ThreadLoal 变量，线程局部变量，同一个 ThreadLocal 所包含的对象，在不同的 Thread 中有不同的副本。这里有几点需要注意：
	-   因为每个 Thread 内有自己的实例副本，且该副本只能由当前 Thread 使用。这是也是 ThreadLocal 命名的由来。
	-   既然每个 Thread 有自己的实例副本，且其它 Thread 不可访问，那就不存在多线程间共享的问题。
	在JDBCutil.getConnection方法中,尝试
	Connection connection = threadLocal.get();
	再判断if(null == connection){如果为null代表原来没有从链接池中获取链接,则dataSource,getConnection
	再使用threadLocal.set(Connection)加入这个链接
	}
	return connection;
	如此改进就保证了同一个线程中使用的是同一个链接
	在JDBCUtil中将setAutoCommit commit rollback 封装进方法中,简化执行页面代码
	另外需要写入方法closeConnection来关闭连接,
	并在其中threadLocal.remove()来移除其中保存的连接
	因为下一次业务不一定是在要在同一个事务中进行
如何防止SQL蛀漏洞
	攻击方式
	例一:在用户名地方输入:aaa'or'1=1
	**怎么预防:**
	使用预编译SQL执行对象prepareStatement代替createStatement
	然后利用prepareStatement的方法setString来传入参数
	其中要注意setString方法(index,String)
	其中index代表的是上面sql语句?的顺序
	因为sql的参数以?代表了,为了防止蛀漏

---
## 链接池
什么是连接池?
	是一个装有很多连接的一个容器.
	使用连接的时候,直接可以从连接池中获取.用完以后归还给连接池.
	**优点:**
	当需要操作数据库时，不再需要每次都创建连接。提升程序的性能.对程序进行优化。
	开源连接池:	c3p0  ,  druid   ,dbcp
自定义链接池:
	**原理:**
	1.提供一个无参构造方法方法中写入一个方法创建一个许多个链接的集合
	2.提供方法能从链接池中获取到链接使用
	3.提供方法将连接池中的链接使用完后不关闭而是返回到连接池中

java代码方式手写链接池
	与c3p0-config.xml文件方式配置c3p0不同的点在于
	DriverClass启动需要写在java代码中
	而c3p0-config是使用properties配置文件读取的方式
	默认配置文件地址在src包下.
	利用代码,也就是c3p0的主要的ComboPooledDateSource功能
	//**创建数据源对象,也就是链接池**
	ComboPooledDataSource dataSource = new ComboPooledDataSource();
	//**配置数据源对象的参数**
	dataSource.setDriverClass("com.mysql.jdbc.Driver");
	dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/day19");
	dataSource.setUser("root");
	dataSource.setPassword("root");
	//**获取链接**
	//**其他和普通的链接形式无二,主要是创建链接池这步**
	//最后的connection.close代表的是归还了,不是原来的结束
	dataSource.getConnection();

c3p0.properties
	//特点是配置文件前面需要加上c3p0. 并且首字母小写
	将此名字的配置文件放到src路径下会被自动读取
	文件名称:c3p0.properties
	其他同理c3p0‐config.xml
	不管什么链接池,底层基本都会对close方法进行增强
	底层的C3P0也是对close方法进行了增强,把关闭变成了归还链接
	第三种链接池方法
	格式:
	c3p0.driverClass=com.mysql.jdbc.Driver
	c3p0.jdbcUrl=jdbc:mysql://localhost:3306/day19
	c3p0.user=root
	c3p0.password=root123
XML永远是最强的配置链接池方式,因为可以设置重复的,可以使用标签设置.
	xml方式载入c3p0‐config.xml
	//直接在创建链接池时候传入xml的配置文件即能自动读取
	c3p0‐config.xml放在src文件夹下
	里面的内容必须按照标准格式输入
	\<c3p0-config>
	\<default-config>
	\<property name="driverClass">com.mysql.jdbc.Driver\</property>
	\<property name="jdbcUrl">jdbc:mysql://localhost:3306/test\</property>
	\<property name="user">root\</property>
	\<property name="password">root123\</property>
	\</default-config>
	</c3p0-config>
开源池Druid
	使用druid打的方式创建链接池(除了创建方法其他等同于c3p0)
	DruidDataSource dataSource = new DruidDataSource();
	druid使用properties方法来调用链接池
	Properties pro = new Properties();
	pro.load(Demo08.class.getClassLoader().getResourceAsStream("druid.properties"));
	DataSource dataSource = DruidDataSourceFactory.createDataSource(pro);
	//使用DruidDataSourceFactory的createDataSource方法(传入pro配置文件)返回载入配置文件的dataSource
DbUtils工具类使用
	作用:将编程时候封装重复部分代码,更加专注于sql语句
	在写增删改类方法时主要不同就是sql语句不同和传入参数个数和类型不同
	改进方法在增删改方法中传入参数(String sql, Object...args)
	将sql语句写入参数列表中,将需要传入的不同类型和不同个数的参数使用Object...args表示
	statement = connection.prepareStatement(sql);
	ParameterMetaData parameterMetaData = statement.getParameterMetaData();//获取sql语句中的参数
	for (int i = 0; i <parameterMetaData.getParameterCount() ; i++) {//循环参数个数次
	//index从1.2开始
	//循环的i从0,1开始
	statement.setObject(i+1,args[i]);//传入参数
	}
	然后在调用方法时候传去sql语句,和参数
查询方法的DButil写法
	定义一个接口处理结果集(返回的值)
	接口定义规范
	//1.接口定义的时候无法确定怎么处理结果集(使用抽象方法);
	//2,.接口定义的时候无法确定返回值类型(使用泛型)
	public interface MyResultsetHandler<T> {
	T handler(ResultSet resultSet) throws Exception;
	}
	//因为返回值类型不能确定所以设置返回值为T
	//处理结果集的方式也不同,先传入结果参数ResultSet
	private static<T> void query(String sql,MyResultsetHandler<T> resultsetHandler,Object... args)
	在创建方法的时候传入结果集处理定义接口MyResultSetHandler定义接口
	T handler = resultsetHandler.handler(resultSet);//使用处理器处理结果集,返回需要的结果T
	System.out.println(handler);
	使用接口中的Handler处理方法处理结果集返回参数T
	打印参数
	query(sql语句,new MyResultsetHandler<需要的参数类型在这时候确定>(){//匿名内部类实现接口方法
	重写handler方法
	写入处理结果集的方法,将查询到的内容传到参数中
	return 参数;
	},参数)
	使用jar包来使用DButil方法
	//1.导入第三方jar包,创建new QueryRunner(传入链接池)对象
	//2.queryRunner中调用方法
	例如:queryRunner.query(sql语句,new BeanHandler<参数类型>(参数的class对象))
	这样返回的一个参数类型的对象
	打印对象
	使用new BeanListHandler则是使用返回lis集合对象
JDBCTenplate
	是基于spring框架对JDBC进行简单的封装
	提供一个JDBCTenplate对象
	简化了JDBC的链接操作,让人更专注于sql语句书写
	单表查询和多表查询,重点
	JDBC重点
	c3p0重点