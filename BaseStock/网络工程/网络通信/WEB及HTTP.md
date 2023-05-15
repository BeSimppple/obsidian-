## web
web简介
	web指的就是网页,我们所指的一般就是internet主机(服务器)上提供的外界访问的资源
	**静态web资源:** 
		**定义:** 不需要与任何程序交互和数据处理,速度快但是修改麻烦,每个都需要代码单独修改
		**特点:** 在服务器执行过程中不会发生改变的资源,例如:html,css,js,图片,文本
	**动态web资源:**
		**定义:** 指web页面中内容是由程序产生的,可以实现人和人之间的交互,用到Servlet和JSP基数,维护方便,可以实时动态更新和存放大量数据(数据库)
		**特点:** 服务期执行过程中,内容可以实时发生或改变的资源,比如:servlet,jsp
web服务器
	两种方式上网:通过browser(浏览器)和client(客户端)上网[[常见架构]]
什么是服务器
	大致2种:
	1.一台电脑(硬件服务器)
	2.在电脑上安装的服务器软件(软件服务器)
常见的javaweb服务器
	1.weblogic
	2.websphere
	3.tomcat
	4.jboss
	5.nginx 主要做载重平衡(多个服务器使用时候会优先匹配空闲的服务器)是一个高性能的 HTTP 和 反向代理服务器
**tomcat目录结构**
| 目录 | 作用 |
| ------- | ----------------------------- |
| bin | 存放tomcat的可执行文件 |
| conf | 存放tomcat的配置文件 |
| lib | 存放tomcat执行所需的jar包 |
| logs | 存放tomcat的日志文件 |
| temp | 存放tomcat的临时文件 |
| webapps | 部署web资源 |
| work | 存放jsp页面转译之后的java文件 |
默认访问页面
	在tomcat/conf/web.xml文件，将这个文件理解成是我们所有的tomcat下的web应用程序中的web.xml文件的父类文件。在 这段声明就规定了web应用中哪些文件是默认被访问的。
	如果我们在自己项目的web.xml文件中将上述内容覆盖了，就可以设置自己项目的默认访问页面。
TOMCAT配置
	配置
	* tomcat依赖java的运行环境，需要用到JAVA_HOME
	* ‘端口配置
	* E:\Program Files\apache-tomcat-8.5.54\conf\server.xml中，添加如下配置：
	```xml
	<Connector port="8181" protocol="HTTP/1.1"
	connectionTimeout="20000"
	redirectPort="8443" />
	```
	* 服务器中的web.xml，相当于全局配置
	* 项目中的web.xml，相当于局部配置
TOMCAT部署web应用程序
	三种方式
	1,直接将一个web应用程序放置在tomcat/webapps目录下。这时web应用程序目录名称就是我们访问tomcat下的这个应用程序的名称
	2.将一个不在tomcat下的web应用程序部署加载。 可以在tomcat/conf/server.xml文件中配置，在server.xml文件中的\<Host>标签中添加一段配置
	3.可以在第二种方案基础上进行增强，在tomcat/conf/Catalina/localhost下创建任意名称的一个xml文件，例如创建一个good.xml文件，在good.xml中书写
	方式二和三都是虚拟目录的方式
在eclipse下发开web应用
	1.配置TOMCAT,2.将web项目部署到TOMCAT
在idea下开发web应用
	//1,.创建javaEE工程并选中web application
	配置TOMCAT进idea.
	启动TOMCAT
	idea下的web项目部署运行原理
	1.针对当前项目对象创建一个tomcat镜像
	CATALINA_HOME是Tomcat的安装目录，CATALINA_BASE是Tomcat的工作目录
	2.在tomcat镜像文件项目下自动设置路径和项目名称
	有没有部署成功得看有没有放到workspace下的out文件夹下
	idea的项目中,只有src和web文件夹中的内容会部署到tomcat服务器
	SRC文件夹中的内容会部署到
	/项目/web-inf/classes文件夹中
	web文件夹中的内容会部署到
	/项目文件夹中

---
## HTTP
**Http定义**
	Hyper Text Transfer Protocol 超文本传输协议 是互联网上应用最为广泛的一种网络协议，所有的www都必须遵守这个标准
	**传输协议:** 定义了,客户端和服务端和服务器端通信时,发送数据的格式
	默认端口号:80(443)
	基于TCP/UDP的高级协议
	基于请求/响应模型的:一次请求对应一次响应
	无状态的:每次请求之间相互独立,不能交互数据
历史版本:
	1.0:每次请求响应都会建立新的链接
	1.1:复用链接(短时间内再传一次数据时会直接使用上一次的链接)

请求中的参数
	**浏览器F12查看**
	General:请求行,响应行
	Request Headers:请求头
	Response Headers:响应头
	响应正文:将显示内容携带到浏览器
	请求正文:用来接收请求的参数
	**总结:**
	**请求**: 请求行,请求头,请求正文
	**响应**: 响应行,响应头,响应正文
HTTP请求的执行过程
	1. 根据windows系统的域名解析文件将域名解析成对应的本地ip,如果没有找到对应的IP,那么就将域名放到DNS服务器上就解析找到互联网上的对应IP
	2. 经过三次握手建立TCP连接
	3. 发起HTTP请求,包含请求行,请求头,请求正文
	4. 服务器返回HTTP响应,包含响应行,响应头,响应正文
	5. 浏览器解析响应正文并展示
**Http请求三部分:**
	1. **请求行:**
		1. 请求方式 GET POST
			1. **GET和POST请求区别**
			1. get只能传递1kb以下数据,POST可以传递大数据。
			1. get请求如果有请求参数，那么，浏览器上会显示请求参数 post请求不会，所以post请求会更安全。
			1. 如果是get请求，有请求参数，请求参数是在http请求行的资源路径上。
			1. 如果是post请求，有请求参数，请求参数是在请求正文中
		1. 请求资源路径
		1. 协议版本
	2. **请求头**
		1. User‐Agent: Mozilla/5.0 (Windows NT 10.0; WOW64)AppleWebKit/537.36 (KHTML, likeGecko) Chrome/75.0.3770.100 Safari/537.36
		1. 请求是在哪种浏览器上发起的
	3. **请求正文**
		1. 只有请求方式是post，才会有请求正文
**Http响应三部分**
	1. **响应行**
		1. 在响应行中最重要的是响应状态码一共分为五种
		1. 1xx 代表浏览器向服务器发送请求，服务器没有做任何操作
		1. 2xx 服务器正确响应，并且响应成功。
		1. 3xx 代表浏览器向服务器发送主求，服务器完成一部分工作，通知浏览器继续执行其它的操作。
		1. 4xx 浏览器错误
		1. 5xx 服务器端错误
		1. 最常见的状态码
		1. 200 响应成功
		1. 302 可以与一个响应头location组合完成重定向。
		1. 304 代表服务器端资源没有改变。
		1. 404 资源访问不到
		1. 500 服务器端错误。
	2. **响应头**
		1. Location它与302状态码组全可以完成重定向功能。（response对象）
		1. Content-type服务器响应回的数据mimeType类型:将音频,图片,文本,视频等等。
		1. refresh:可以实现定时跳转content-disposition
		1. 可以完成文件下载,点击的整个文件是否弹出下载框。
	3. **响应正文**
		1. 对于http响应正文，它是真正被浏览器解析并显示在浏览器上的。