常用插件:
idea右键项目local history(选中某个节点)revert可以本地回滚(以后项目中使用gitte云项目回滚)
最左下边的structure（结构）可以帮忙查看类中属性和方法（查看lombok注解是否生效）
idea中target包的作用（存放编译后的class文件）
（可通过show excluded files开启隐藏关闭）
target包中war包作用
默认打jar包
在pom.xml中设置
<packing>war<packing>修改
jar包和war包的区别？
jar包可打包多个文件，以便用于库、插件或任何类型的应用程序，war包仅用于web应用程序，即web项目；jar包里没有静态资源的文件，war包里有。
jar包是类的归档文件，jar文件格式以流行的zip文件格式为基础。jar文件允许我们打包多个文件，以便将其用作库、插件或任何类型的应用程序。
war包是JavaWeb程序打的包，war包里面包括写的代码编译成的class文件，依赖的包，配置文件，所有的网站页面，包括html，jsp等等。一个war包可以理解为一个web项目，里面是项目的所有东西。

  

pom.xml配置mvaen环境
spring项目中properties和application.xml和yaml
.properties文件是spring加载时可以读取的文件类型，一般在该文件中写对应工具的参数例如redis的最大活动数等
application.xml是java项目读取配置文件，一般通过xml文件读取.properties文件中的参数使用
springcloud使用yaml和注释和@configuration类简化了配置

  
mvn install:install-file -Dfile=XXX.jar -DgroupId=com.alimama -DartifactId=sms -Dversion=1.0 -Dpackaging=jar
通过maven install命令来安装一些maven内部没有映射的jar包
  

  

idea中常用方法:
StringUtils类的字符串切割等方法
Cleander类的时间方法