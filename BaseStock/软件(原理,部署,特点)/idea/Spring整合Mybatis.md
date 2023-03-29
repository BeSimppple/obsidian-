Spring和Mybatis整合

开发步骤

UserDaoImpl操作

-   将UserService对象IoC到Spring容器中

-   对userDao进行DI

-   将UserDao接口的实现子类对象IoC到Spring容器中

-   对sqlSessionFactory进行DI

-   将SqlSessionFactoryBean对象IoC到Spring容器中

-   对dataSource进行DI
-   对configLocation进行DI

-   将DataSource对象IoC到Spring容器中

-   对driverClass、jdbcUrl、user、password进行DI

  

  

开发步骤

mapper映射式操作

-   1.将UserService对象IoC到Spring容器中

-   对userDao进行DI

-   2.将UserDao的代理对象IoC到Spring容器中

-   使用MapperScannerConfigurer

-   3.将MapperScannerConfigurer对象IoC到Spring容器

-   对basePackage进行DI

-   4.将SqlSessionFactoryBean对象IoC到Spring容器中

-   对dataSource进行DI
-   对configLocation进行DI

-   5.将DataSource对象IoC到Spring容器中

-   对driverClass、jdbcUrl、user、password进行DI