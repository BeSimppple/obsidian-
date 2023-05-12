**Mybatis的作用和原理:**
	**作用:**
	1.解决原始jdbc开发的问题:
		1.数据库连接创建、释放频繁造成系统资源浪费从而影响系统性能
		2.sql 语句在代码中硬编码，造成代码不易维护，实际应用 sql 变化的可能较大，sql 变动需要改变java代码。
		3.查询操作时，需要手动将结果集中的数据手动封装到实体中。插入操作时，需要手动将实体的数据设置到sql语句的占位符位置
	2.对JDBC进行封装,然后使用ORM(object relation mapping)方式解决了实体和数据库映射,从而不需要在表面使用JDBC的API,让开发人员更加关注sql语句,同时减少耦合
	**原理:**
	1.使用数据库连接池初始化连接资源(优化资源使用)
	2.将sql语句抽取到xml配置文件中(减少硬编码)
	3.使用反射、内省等底层技术，自动将实体与表进行属性与字段的自动映射(减少重复编码)
Mybatis-Plus常用API及注解
	Mybatis-plus常用api和注解在springboot笔记中记载,也可上官网查 @tableId() 和QueryWrapper<>条件构造器和IPage<>分页构造器等常用类和注解
**Mybatis底层架构**
	![[Mybatis(持久层框架)_image_1.jpg|575]]
	**SqlMapConfig.xml**
		该文件作为MyBatis的核心配置文件,配置了MyBatis的运行环境等信息.
	**Mapper.xml**
		sql映射文件，文件中配置了操作数据库的sql语句。此文件需要在SqlMapConfig.xml中加载。
		Mapper.xml中的各项配置的作用
		![[Mybatis(持久层框架)_image_2.jpg]]
	**MyBatis的核心API:**
		**SqlSessionFactoryBuilder**
		获取SqlSessionFactory对象,只需要用一次,所以使用匿名对象
		**SqlSessionFactory**
		通过mybatis配置信息创建,即会话工厂对象,可以进行复用
		**SqlSession**
		是一个执行statement的会话对象,一次数据操作就是一次会话对应一个SqlSession对象
		传统Dao因为 实现子类太多 在映射文件 Dao接口 Dao实现类之间存在耦合
		映射文件的id改变必须要改变实现子类的方法名
		**Executor**,
		mybatis底层自定的接口,用来操作数据库。
		**MappedStatement**
		mybatis的底层封装对象，它包装了mybatis配置信息及sql映射信息等。mapper.xml文件中一个sql对应一个MappedStatement对象。
	**输入映射**
		包括HashMap、基本类型、pojo，Executor通过MappedStatement在执行sql前将输入的java对象映射至sql中，输入映射相当于JDBC编程中对preparedStatement设置参数。
	**输出映射**
		包括HashMap、基本类型、pojo，Executor通过MappedStatement在执行sql后将输出结果映射至java对象中，输出映射相当于JDBC编程中对结果的解析处理过程。
	mapper主要配置的 就是statement执行对象 MapperStatement
	通过SqlSessionFactory的SqlSession的Executor来使用执行对象
	而SqlSessionFactory是通过SqlMapConfig.xml配置文件得来的

Mybatis拦截器
	**作用:** 常用于分页,sql结果集处理执行额外逻辑和过滤敏感信息
	**原理:** mybatis拦截器本质上使用了jdk动态代理
	**实现方法:** 用户自定义拦截器类只需实现Interceptor接口，以及实现intercept方法，plugin和setProperties方法可重写，plugin方法一般不会改动，该方法调用了Plugin的静态方法wrap实现了对目标对象的代理
	拦截器4个对象:
		1.Executor mybatis的内部执行器，作为调度核心负责调用StatementHandler操作数据库，并把结果集通过ResultSetHandler进行自动映射
		2.StatementHandler mybatis的内部执行器，作为调度核心负责调用StatementHandler操作数据库，并把结果集通过ResultSetHandler进行自动映射
		3.ParameterHandler 作为处理sql参数设置的对象，主要实现读取参数和对PreparedStatement的参数进行赋值
		4.ResultSetHandler 处理Statement执行完成后返回结果集的接口对象，mybatis通过它把ResultSet集合映射成实体对象

**MyBatis中增删改默认事务管理是手动提交**


Mybatis的代理开发模式
需求条件:
1.mapper映射文件的nameSpace必须要和dao接口的全限定类名一致
2.statement的id值必须和dao接口的方法名一致
3.statemtn的输入映射类型和dao接口方法的形参类型一致
4.statement的输出映射类型和dao接口的方法的返回值类型一致
在SqlMapConfig中加载maper映射文件,然后mapper会通过nameSpace中的全限定类名找到接口
代理模式使用原理:
省略了UserDaoImpl这个层面的处理
直接利用mapper获取全限定类名反射,获取UserDao接口对象
去实现UserDao接口方法
MyBatis的核心配置文件设置
configuration(配置)
1.[properties（属性）](https://mybatis.org/mybatis-3/zh/configuration.html#properties)
//可以选择resource引入外部标签,也可以自己定义变量再使用
-   [settings（设置）](https://mybatis.org/mybatis-3/zh/configuration.html#settings)
[typeAliases（类型别名）](https://mybatis.org/mybatis-3/zh/configuration.html#typeAliases)
//给一个对象起别名,方便查看和书写
-   [typeHandlers（类型处理器）](https://mybatis.org/mybatis-3/zh/configuration.html#typeHandlers)
-   [objectFactory（对象工厂）](https://mybatis.org/mybatis-3/zh/configuration.html#objectFactory)
[plugins（插件）](https://mybatis.org/mybatis-3/zh/configuration.html#plugins)
MyBatis可以使用第三方的插件对功能进行扩展，分页助手PageHelper是将分页的复杂操作进行封装，使用简单的方式即可获得分页的相关数据
[environments（环境配置）](https://mybatis.org/mybatis-3/zh/configuration.html#environments)
environment（环境变量）
transactionManager（事务管理器）
dataSource（数据源）
-   [databaseIdProvider（数据库厂商标识）](https://mybatis.org/mybatis-3/zh/configuration.html#databaseIdProvider)
[mappers（映射器）](https://mybatis.org/mybatis-3/zh/configuration.html#mappers)
//resource="mapper01.xml"在classpath下加载mapper01.xml映射文件
//class 是扫描UserDao2接口对应的映射文件,映射文件必须和接口名一直,而且必须存放在和接口同一目录下
//name 扫描指定包下所有接口对应的映射文件,映射文件必须和接口名一直,且必须存放在和接口同目录下
MyBatis的映射配置文件设置
parameterType属性 设置传入的参数类型
包括简单数据类型(int,String等) 这类只能传入一个参数
也包括复杂数据类型 (javaBean实体类,Map等)这类可以传入多个参数
resultType属性设置返回值的参数类型 同上
resultMap属性
resultType可以将查询结果映射为pojo,但需要pojo类的属性名和sql查询的字段名称一致方可映射成功。
如果sql查询字段名和pojo类的属性名不一致，可以通过resultMap将字段名和属性名作一个对应关系.
resultMap可以实现将查询结果映射为复杂类型的pojo，比如实现一对一查询和一对多查询。 例如:
<id property="eid" javaType="int" column="eid"></id>
<result column="ename" property="ename" javaType="String"></result>
<!-- 映射Dept属性 property叫做dept 他的属性类型为简写的全类名dept-->
<association property="dept" javaType="dept">
<id property="did" javaType="int" column="did"></id>
<result property="dname" column="dname" javaType="String"></result>
</association>
</resultMap>
MyBatis的多表查询
其中association 代表着可以将对象中的对象进行分析成我们需要的简单数据类型 用于多表查询
sql属性 可以简化sql语句书写
<sql id="queryByParam">
id,username,password
</sql>
引入方式: <include refid="片段名"></include>
动态sql之 if标签 和where标签 通过if标签完成sql的动态语句
使用where可以省略where1=1 和第一个and
select <include refid="queryByParam"></include> from tb_user
<where>
<if test="null != username and '' != username">
username = #{username}
</if>
<if test="null != password and '' != password">
and password = #{password}
</if>
</where>
动态sql之 foreach标签 通过foreach标签完成sql的动态语句
select <include refid="queryByParam"></include> from tb_user
<where>
<foreach collection="list" separator="," open="id in (" close=")" item="id">
#{id}
</foreach>
</where>
select * from tb_user where id in (1,2,3,4,5,6) 通过id 查询id为这些里面相同的
select * from tb_user where id = 1 or id = 2 or id =3 or id =4 or id =5 or id = 6
collection代表类型:list是集合 array是数组
open开始 close结束
separator是间隔
item是中间内容 foreach也是为了输入中间内容
通过拼接的方式来变化语句\
wherer的作用就是保证后面能接语句
一对多查询
<resultMap>
其中List对象
//这里使用ofType代表这是一个List集合包括其中泛型对象
<collectioin property="employeeList" ofType="employee"></collection>
</resultMap>
MyBatis的延迟加载
概念:
例如多表查询根据部门查员工,但是默认不需要员工信息时候不加载,只有需要的时候才加载显示
本质:将一次多表查询拆分成两次单表查询
好处:提交了数据库的操作效率
一对一查询一般不需要做延迟加载
,一对多可以做延迟加载


**MyBatis缓存**
	**作用:** Mysql数据库是一个文件存储系统,放在磁盘上,而磁盘的读写性是有限制的,缓存就是为了减少和数据库的交互次数,提高执行效率
	主要适用于
	1.经常查询并且不经常改变的
	2.数据的正确与否对最终结果影响不大
	**一级缓存**
	在一次会话中的缓存,默认设置
	仅在一次会话中存在
	可以使用sqlSession的方法clearCache()清除缓存
	也可以因为第二次查询前内容发生更改,mybatis会自动帮你修改缓存
	就像是session
	二级缓存
	1.开启缓存setting
	2.在影射文件中开启二级缓存 <cache></cache>
	3.在具体的statemetn中使用userCash = true(可能需要在返回类中实现序列化serialize)
	4.代码测试
	第一次查询出来的对象,将其中的值存放到二级缓存中,在下一次创建的时候赋值使用
	就像是Cookie







数据库代码生成(数据库表生成java中对应类)
	可以生成对应类,xml配置,接口等
	导入需要的几个pom包(官网找引导有详细教程)
	codeGender类(mybatis-plus提供)
	修改必要的参数(数据库地址等信息)
	网上也有很多同类型的类方法(了解原理直接复制就行)



