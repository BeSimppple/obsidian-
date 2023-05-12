**Servlet的作用和原理**
	**作用:**
	使java程序能通过Servlet和和浏览器进行交互
	**概念:**
	Servlet是Java Servlet应用程序的javax.servlet.Servlet接口及相关类和方法的Java 程序,是一个动态资源
	**原理:**
	

处理请求的原理:
Servlet接口下有一个实现类叫做GenericServlet.其有一个子类HttpServlet..
在Servlet接口中定义了一个方法service,它的主要作用是处理来自浏览器的请求操作。在service方法的重载的方法中，对请求方式进行判断，
处理请求流程
* 浏览器发起请求，先交给Servlet的service方法将普通的请求和响应对象强转成针对http协议的请求和响应对象，获取请求方式，根据不同的请求方式调用不同的方法来处理请求：get请求就调用doGet方法、post请求就调用doPost方法、put请求就调用doPut方法、delete请求就调用doDelete方法
* 
创建Servlet的三种方式
	1. implements Servlet接口的实现抽象类
	2. extends GenericServlet类--将Servlet接口方法做了空实现,只将Service()t方法进行抽象的子类
	3. (常用)extends HttpServlet类-针对HTTP协议优化

**Servlet的生命周期:**
	在javax.servlet.Servlet接口中三个方法**init service destroy**就是servlet的生命周期作用方法
	servlet正常情况下不会随着服务器创建而初始化 
	1.第一次访问servlet,servlet会被创建，并将servlet对象常驻内存，调用init方法进行初始化操作，init方法中执行一次。
	2.调用service方法，用于处理来自浏览器端的请求，以后都是开启一个线程来处理浏览器端请求。
	3.随着服务器关闭Servlet销毁  ,  当tomcat服务器正常关闭时，会调用destroy方法将servlet销毁。

load-on-startup
可以让servlet跟随服务器的启动而启动
对于load-on-startup它的可以配置的值有10个，1代表优先级最高，数值越大，优先级越低。

对于servlet,我们需要在web.xml文件中对其进行配置
在web.xml中声明Servlet
在web.xml中给Servlet映射访问路径
一个aervlet可以有多条路径访问,一个<servlet>可以多个<servlet-mapping>对应
url-pattem的书写规则
完全匹配:以/开始
目录匹配:以/开始,然后以*结束
比如/a/b/c* 代表只要前面是/a/b/c后面是都无所谓可以访问到servlet
扩展名匹配:不能以/开始,必须以.XXX结束
比如*.abc
不管前面怎么写必须是.abc结尾才能访问到servlet
缺省servlet
缺省:默认
创建servlet时,如果他的url-pattern的配置为"/"这时这个servlet就是一个缺省servlet,tomcat服务器中默认有缺省servlet
作用:凡是在web..xml中找不到匹配的<servlet-mapping>元素的URL,他们的请求都将交给缺省servlet处理,
当访问tomcat服务器中的静态资源时,实际上是在访问这个缺省servlet
访问的服务器路径的书写
最上面一层就是工程的名字
然后下面分文件夹或者内容,一级的可以直接相对路径打名字
而如果是上一级则需要../来返回上一级
如果是上一级下面的另一个包中,则需要先../返回在输入相应的路径
---
request和response对象
request与response是什么?
Web服务器接收到客户端的请求，会针对于每一次请求创建
一个用于封装我们http请求信息的对象request,也会创建一个response对象，它对应着http响应。
request对象就可以操作http请求信息
response对象就可以操作http响应信息
response操作响应行和响应头
响应行
setStatus(int status)//操作正常响应码.对应的反馈
sendError(int status)//操作异常响应码,
响应头
//操作响应头，在原有基础上添加新值
public void addHeader(String name, String value):
/操作响应头，直接赋值
public void setHeader(String name, String value):
response的重定向的使用
//首先要操作响应行让系统去跳转
response.setStatus(302);
//定位响应头去定位跳转位置
response.setHeader("location","/fuck/demo01");
第二种:定时重定向跳转
response.setHeader("refresh"."5;url=http://localhost:8080/demo111.html")
//其中refersh相当与302 5代表5秒 ;隔开 url绝对地址
response操作响应正文
//返回一个可将字符文本发送到客户端的的对象writer
public java.io.PrintWriter getWriter()
//设定浏览器编解码的格式
public void setCharacterEncoding(String charset)
//设定浏览器字符编码,同时设置浏览器解析字符串的编码格式
public void setContentType(String type)
Request对象
1.操作请求行
//获取请求方式
public String getMethod()
//获取请求路径
public String getRequestURI()
//获取请求路径上的参数,仅限于get请求方式
public String getQueryString()
2.操作请求头
//根据请求头名获取请求头值
public String getHeader(String name)
3.操作请求正文
//获取指定参数的参数值
public String getParameter(String name)
//获取到所有[name]元素的一组值(一个元素可以定义多个值)
public String[] getParameterValues(String name)
//使用hashMap的方法来编写元素,对应元素名:键 值对应值
//返回此请求的参数的
public java.util.Map<K, V>getParameterMap()
//使用枚举的方法来获取所有参数名称
public java.util.Enumeration<E>getParameterNames()
* HttpServletRequest和HttpServletResponse针对http协议
* ServletRequest和ServletResponse不针对http协议
编码,解码乱码的问题
浏览器将请求正文编码UTF-8成字节数组,然后服务器以iso8859-1进行解码,之后再以iso8859-1进行编码然后再发送给浏览器,浏览器以UTF-8进行解码就乱码了!!!!!
Request请求转发
request对象是在浏览器向服务器发送请求时创建
request对象是在服务器响应时候销毁的
请求转发,就会形成一个请求链,他们共享一个request和response对象
request域对象只能在同一个request中传递数据,因此重定向的时候request对象会变化,而请求转发传递的是同一个request
在同一服务器上不同的request请求是会得到唯一的session
实现方法:
request.getRequestDispathcher("路径").forword(req.resp)
//从项目开始定位,所以绝对路径从资源名开始
Ajax
AJAX即“Asynchronous Javascript And XML”
（异步JavaScript和XML）
作用:
可以局部刷新页面(当有很多请求需要一起刷新时局部刷新要更改的页面信息)
可以发送异步请求
异步请求:可以局部的改变网页上的内容,当正在发生改变时,其他的模块的内容也可以发出请求
.XMLHttpRequest对象详细介绍
Ajax异步请求对象
属性:
1.onreadystatechange
用于指定XMLHttpRequest对象状态改变时的事件处理函数
2.readystate
0 :XMLHttpRequest对象还没有完成初始化
1 :XMLHttpRequest对象开始发送请求
2 :XMLHttpRequest对象的请求发送完成
3 :XMLHttpRequest对象开始读取服务器的响应
4 :XMLHttpRequest对象读取服务器响应完成
**10. forward(请求转发)和redirect(重定向)的区别?**
forward是服务端发送的额外请求且只能跳转本站资源点,所有请求一次完成可以携带request资源,客户端并不知道请求的地址
redirect是客户端行为,可以请求到非本站资源.请求分二次完成第一次返回200状态码后请求第二个资源,无法携带request对象,会显示第二次请求的地址
---
过滤器Filter
概述:用以在请求之前处理资源的组件
生命周期
1.随着服务器的启动而初始化
2.随着请求的发生而过滤
3.随着服务器的关闭而销毁
执行流程:
1.浏览器发起请求
2.服务器会根据这个请求，创建request对象及response对象
3.过滤器会持有request对象及response对象
4.只有当过滤器放行之后，request对象及response对象才会传给Serlve
执行顺序:
先过滤,后放行
过滤器相关配置
初始化参数,
过滤路径:根据访问路径过滤
1.完全匹配 : 以"/"开头
2.目录匹配 : 以"/"开头，以"*"结尾
3.后缀名匹配 : 以"*"开头，以后缀名结尾
也可以根据过滤的servlet名称进行过滤
过滤器的注解开发
WebInitParam[] initParams() default {};//初始化参数
等
可以使用过滤器的特性,进行编解码格式的设置
监听器
概述:
1.事件源：事件发生的源头
2.监听器：事件发生后被触发的组件
3.事件：能够触发监听器的事
4.绑定：将事件源和监听器关联
servlet监听器(具体)
事件源：三大域对象
监听器：三类监听器
事件：域对象发生改变
绑定：在web.xml中配置监听器
监听器分类
一类监听器
-   监听域对象的创建、销毁
二类监听器
-   监听域对象中的属性的变更（属性添加、属性修改、属性移除）
三类监听器
-   监听域对象中的对象状态改变（对象绑定、对象解绑）
三类监听器很特殊,可以直接在对象中实现三类监听器(然后重写方法完善解绑和绑定状态时的方法)
过滤器链和拦截器的区别
![[Servlet(服务器端小型通信)_image_1.jpg]]
1、实现原理不同 过滤器和拦截器底层实现方式大不相同，过滤器 是基于函数回调的，拦截器 则是基于Java的反射机制（动态代理）实现的。
2、使用范围不同 我们看到过滤器 实现的是 javax.servlet.Filter 接口，而这个接口是在Servlet规范中定义的，也就是说过滤器Filter 的使用要依赖于Tomcat等容器，导致它只能在web程序中使用。 而拦截器(Interceptor) 它是一个Spring组件，并由Spring容器管理，并不依赖Tomcat等容器，是可以单独使用的。不仅能应用在web程序中，也可以用于Application、Swing等程序中。
3、触发时机不同 过滤器Filter是在请求进入容器后，但在进入servlet之前进行预处理，请求结束是在servlet处理完以后。拦截器 Interceptor 是在请求进入servlet后，在进入Controller之前进行预处理的，Controller 中渲染了对应的视图之后请求结束。
4、拦截的请求范围不同 过滤器Filter执行了两次，拦截器Interceptor只执行了一次。这是因为过滤器几乎可以对所有进入容器的请求起作用，而拦截器只会对Controller中请求或访问static目录下的资源请求起作用。
5、注入Bean情况不同 这是因为加载顺序导致的问题，拦截器加载的时间点在springcontext之前，而Bean又是由spring进行管理。
6、控制执行顺序不同 过滤器用@Order注解控制执行顺序，通过@Order控制过滤器的级别，值越小级别越高越先执行。 拦截器默认的执行顺序，就是它的注册顺序，也可以通过Order手动设置控制，值越小越先执行