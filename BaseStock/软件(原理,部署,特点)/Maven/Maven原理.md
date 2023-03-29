maven
概念:项目管理工具
作用以及优势:
项目的生命周期进行管理(编译,测试,运行,部署)

1.Maven依赖管理(Maven仓库)

将原有的项目的jar包统一放在了Maven仓库中,其他项目的调用只需要引用Maven仓库就行

原理:

而是通过在 pom.xml 文件中添加所需 jar包的坐标，在需要用到 jar包的时候，只要查找pom.xml文件，再通过pom.xml文件中的坐标，到一个专门用于”存放jar包的仓库”(maven仓库)中根据坐标从而找到这些jar包，再把这些 jar 包拿去运行。

1.2.Maven仓库

Maven仓库分为1.本地仓库2.远程仓库3.中央仓库

  

会依次从本地仓库中搜寻是否有需要的jar包,如果没有再去远程仓库 再去中央仓库

远程仓库可以是局域网寻找,也可以是互联网

中央仓库就是Maven软件中内置的一个远程仓库地址,由Maven团队自己维护的仓库

  

可以将本地仓库理解为缓存,如果有了本地仓库就不用每次从远程仓库下载 ,从而统一管理jar包

  

2.项目的一键构建

指的是项目从编译、测试、运行、打包、安装，部署整个过程都交给maven进行管理，这个过程称为构建。 一键构建指的是整个构建过程，使用maven一个命令可以轻松完成整个工作

  ![[Maven原理_image_1.jpg]]

  

  

  

maven的conf文件夹中的setting.xml配置文件

配置本地maven仓库路径

<localRepository>F:\MavenRepository</localRepository>

配置远程仓库镜像--使用阿里云镜像仓库

<mirror>

<id>alimaven</id>

<mirrorOf>central</mirrorOf>

<name>aliyun maven</name>

<url>https://maven.aliyun.com/repository/central</url>

</mirror>

<mirror>

<id>nexus-aliyun</id>

<mirrorOf>central</mirrorOf>

<name>Nexus aliyun</name>

<url>[http://maven.aliyun.com/nexus/content/groups/public](http://maven.aliyun.com/nexus/content/groups/public)</url>

</mirror>

全局配置jdk版本号

<profile>

<id>jdk18</id>

<activation>

<activeByDefault>true</activeByDefault>

<jdk>1.8</jdk>

</activation>

<properties>

<maven.compiler.source>1.8</maven.compiler.source> <maven.compiler.target>1.8</maven.compiler.target> <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>

</properties>

</profile>

maven项目中的pom.xml配置文件

<project ...>

<!--公司名称-->

<groupId>com.qfedu</groupId>

<!--项目名称-->

<artifactId>day50</artifactId>

<!--版本号-->

<version>1.0.0-SNAPSHOT</version>

  

<!--声明变量-->

<properties> </properties>

<!--依赖管理-->

<dependencies> </dependencies>

<!--插件管理-->

<build> </build>

  

</project>

  

常用maven命令

compile

compile是maven工程的编译命令，作用是将src/main/java下的文件编译为class文件输出到 target目录.

clean

clean是 maven工程的清理命令，执行clean会清空target目录.

test

test是maven工程的测试命,会执行src/test/java下的单元测试类

package

package是maven工程的打包命令，对于java工程执行 package 打成 jar 包，对于 web 工程打成 war包。

install

install是maven 工程的安装命令，执行 install 将 maven 打成 jar 包或 war 包发布到本地仓库。在idea中执行maven命令

通过cmd窗口执行来安装一些maven仓库没有映射的jar包

mvn install:install-file -Dfile=XXX.jar -DgroupId=com.alimama -DartifactId=sms -Dversion=1.0 -Dpackaging=jar

  

依赖范围scope

概念:程序依赖于构件,程序运行时,构件的范围
![[Maven原理_image_2.jpg]]
常用的前四种

compile

构件在编译,测试,运行时都存在,会跟随到打包中 默认

test

构件仅在测试时存在,不会跟随到打包中 .比如:junit

provided

构件在编译,测试时存在,不会跟随到打包中

.比如:servlet-api、jsp-api

runtime

构件在测试,运行时存在,会跟随到打包中.

比如:c3p0连接池,一般都是在spring配置文件中进行配置，配置文件不会参与java类的编译过程，但是会参与到测试以及运行。

  

依赖传递

  

构建A依赖构建B,构建B依赖构建C,那么就构成了构建A传递依赖构建C

传递依赖并不一定完全成立
![[E08DA31E152D400DBB09AA58AE5D7187.jpg]]
  

依赖版本冲突

概念:同一个项目中,如果同时依赖了不同版本的构建,那么就会造成版本冲突

  

版本锁定中只进行版本管理,并没有依赖产生

解决方案:使用dependencyManagement进行版本锁定管理

  

  

Maven分模块构建工程

概述:

解决软件的复杂性问题,降低软件的复杂性,随着文件整体的变大,而不可控,为了使其可控可维护可扩展,

模块化是以分治法为依据。简单说就是把软件整体划分，划分后的块组成了软件。

这些块都相对独立，之间用接口（协议）通信，每个块完成一个功能，多个块组合可以完成一系列功能。

  

根据业务分类,并且将公共的业务变成module模块

父工程project管理主要的包的版本,在子模块导入包

  

根据需求可将复用性强的功能分类到一个module中

需要使用的时候从xml中依赖

  

注意要使用父工程内容时候,最好先将父工程install安装,然后再将子工程install

  

继承和聚合

何为继承？

继承是为了消除重复，如果将 dao、 service、 web 分开创建独立的工程则每个工程的pom.xml文件中的内容存在重复，比如：设置编译版本、锁定spring的版本的等，可以

将这些重复的配置提取出来在父工程的pom.xml中定义

何为聚合？

项目开发通常是分组分模块开发， 每个模块开发完成要运行整个工程需要将每个模块聚合在一起运行，比如：dao、service、web三个工程最终会打一个独立的 war 运行。