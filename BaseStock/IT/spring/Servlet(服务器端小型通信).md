Servlet入门

servlet的作用

原来的我javase程序无法在服务器中正常运行

Servlet是一个运算在服务器上的一个java小程序,就是一个java类使用时要先导入其Servlet的api

主要功能在于能够和浏览器进行交互,是一个动态资源

使java程序能通过Servlet和服务器进行交互

定义:就是一个javax.servlet.Servlet接口

处理请求的原理:

Servlet接口下有一个实现类叫做GenericServlet.其有一个子类HttpServlet..

在Servlet接口中定义了一个方法service,它的主要作用是处理来自浏览器的请求操作。在service方法的重载的方法中，对请求方式进行判断，

处理请求流程

* 浏览器发起请求，先交给Servlet的service方法将普通的请求和响应对象强转成针对http协议的请求和响应对象，获取请求方式，根据不同的请求方式调用不同的方法来处理请求：get请求就调用doGet方法、post请求就调用doPost方法、put请求就调用doPut方法、delete请求就调用doDelete方法

创建Servlet的三种方式

implements Servlet接口

的实现抽象类

extends GenericServlet类--将Servlet接口方法做了空实现,只将Service()t方法进行抽象

的子类

extends HttpServlet类-针对HTTP协议优化

一般为extends HttpServlet'

  

在javax.servlet.Servlet接口中定义了三个方法init service destroy它们就是servlet的生命周期方法

  

总结:.servlet正常情况下不会随着服务器创建而初始化 第一次访问servlet,servlet会被创建，并将servlet对象常驻内存，调用init方法进行初始化操作，init方法中执行一次。

  

调用service方法，用于处理来自浏览器端的请求，以后都是开启一个线程来处理浏览器端请求。

  

随着服务器关闭Servlet销毁

当tomcat服务器正常关闭时，会调用destroy方法将servlet销毁。

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