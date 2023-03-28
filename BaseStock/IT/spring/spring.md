Spring程序开发步骤

  

使用此方法

UserDao userDao = Spring客户端.getBean(id标识);

代替UserDao userDao = new UserDaoLmpl();

然后通过Spring框架读取xml配置文件

通过id标识获取到Bean全限定名

通过反射newInstance创建Bean对象 返回对象

1.导入Spring开发的基本包坐标

2.编写Dao接口和实现类

3.创建Spring核心配置文件

4.在Spring配置文件中配置UserDaoLmpl

5.使用Spring的API获得Bean实例