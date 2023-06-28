**ServletConfig对象**
	通过getServletConfig获取ServletConfig对象
	这是javax,servlet包下的一个接口.ServletConfig他是Servlet的一个配置对象
	ServletConfig是由服务器创建的,由Servlet中的init方法传递到Servlet中
	方法:getServletName()
	作用:获取Servlet名称.是指单的Servlet的名称
	方法:getInitParmeter(Name)
	作用:根据参数的名称来获取参数的内容
	方法:getInitParameterNames()
	作用:获取到所有参数的名称
	其中ServletContext对象就是通过ServletConfig对象获取的
**ServletContext对象**
	ServletContext它是javax.servlet包下的一个接口。
	当服务器启动时，会为服务器中的每一个web应用程序创建
	一个ServletContext对象，一个ServletContext对象对应的就是一个web应用程序。
	对于ServletContext,我们叫它上下文对象，ServletConfig对象中维护了ServletContext对象，也就是说，我们可以通过ServletConfig对象来获取ServletContext对象。
	在web应用中的servlet要想实现资源的共享，可以通过ServletContext来完成，
	ServletContext也叫做域对象。
	**作用1**:
	实现资源共享:
	ServletContext也叫做域对象，可以将它想像成一个Map<String,Object>，可以通过它实现Servlet资源共享
	ServletContext作用范围是整个当前项目工作目录
	方法:
	//获取属性值.对应setAttribute()中创建的名字
	public Object getAttribute(String name)
	//移除属性.根据名字
	public void removeAttribute(String name)
	//设置新的属性:名字,值
	public void setAttribute(String name, Object object)
	**作用2**:
	获取全局初始化参数(与ServletConfig获取的对象不同,是创建在xml中配置的全局都可以使用的参数)
	在web.xml中配置的全局初始化参数，可以通过ServletContext对象获取
	//根据参数名获取参数值
	public String getInitParameter(String name)
	//获取所有参数,并生成一个枚举enums
	public java.util.Enumeration\<E>getInitParameterNames()
	**作用3**:
	获取资源在磁盘上的真实路径
	public String getRealPath(String path)
	//传入的参数是从 当前servlet 部署在tomcat中的文件夹算起的相对路径
	.
**从Servlet3.0开始支持注解开发**
	@Target({ElementType.TYPE})
	@Retention(RetentionPolicy.RUNTIME)
	@Documented
	这是三个元注解,在其中还包括多个属性可以设置
	@WebServlet可以设置Servlet中的许多属性替代xml简化设置
**JAVA中的4大域对象**
	域对象的定义:可以在不同的Servlet中进行数据传递的对象就称为域对象
	1.  page(jsp有效)–>page域指的是pageContext.(前后端分离模式下基本不用了,本文也就不讲了)
	2.  request(一次请求)–>request域指的是HttpServletContext（需要掌握）
	3.  session(一次会话)–>session域指的是HttpSession
	4.  application(当前web应用)–>application域指的是application ServletContext；
	之所以他们是域对象，原因是他们都内置了map集合，都有setAttribute和getAttribute方法。
**request的生命周期**
	1.  当请求来时创建request对象
	2.  当请求结束时，request就销毁。
	3.  每次请求都是一个新的request对象。
	request域对象，是建议使用，并被频繁使用的域对象，因为它声明周期比较短，也就代表着，它效率比较高，释放资源比较及时。
**Session的生命周期**
	在第一次调用request.getSession()方法时，服务器会检查是否已经有对应的session，如果没有就在内存中创建一个session并返回。
	（1）当一段时间内session没有被使用（默认为30分钟），则服务器会销毁该session。
	（2）如果服务器非正常关闭，没有到期的session也会跟着销毁。
	（3）如果调用session提供的invalidate()，可以立即销毁session。
	用户打开浏览器访问，创建session（开始），session超时或者被声明失效，该对象生命周期结束；
	Session并不是一打开网页就创建了session对象。对于Servlet的请求中，只有当Servlet内部调用了如下代码，才会生成session
	监听Session的利器HttpSessionListener
	在同一服务器上不同的request请求是会得到唯一的session