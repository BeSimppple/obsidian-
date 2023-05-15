**SpringMVC(模型(Model)、视图(View)、控制器(Controller))作用**
	**概念:** 针对处理javaWeb程序的一个轻量级框架
	**作用:** SpringMVC通过将请求分发给相应的处理程序，使得开发人员能够更加专注于业务逻辑的实现，从而提高了Web应用程序的开发效率和可维护性

**SpringMVC原理和执行流程**
	![[SpringMVC(视图-模型-控制器设计模式)_image_1.jpg|750]]
	**SpringMVC运行过程(原理):**
	1, 用户发送请求至前端控制器DispatcherServlet
	2, DispatcherServlet收到请求调用HandlerMapping处理器映射器。
	3, 返回处理器执行链HandlerExecutionChain。
	4, DispatcherServlet通过HandlerAdapter处理器适配器调用合适的处理器
	5, 执行处理器handler
	6, handler执行完成返回ModelAndView(数据及视图)
	7, HandlerAdapter将果ModelAndView返回给DispatcherServlet
	8, DispatcherServlet将ModelAndView传给ViewReslover视图解析器
	9, ViewReslover解析后返回具体View对象
	10, DispatcherServlet对View进行渲染视图（即将模型数据填充至视图中）。
	11, DispatcherServlet响应用户
**SpringMVC主要角色**
	**DispatcherServlet**：前端控制器前端控制器用户请求到达前端控制器，它就相当于mvc模式中的c，dispatcherServlet是整个流程控制的中心，由它调用其它组件处理用户的请求，dispatcherServlet的存在降低了组件之间的耦合性。
	-
	**HandlerMapping**：处理器映射器处理器映射器HandlerMapping负责根据用户请求找到Handler即处理器，springmvc提供了不同的映射器实现不同的映射方式，由于Handler涉及到具体的用户业务请求，所以一般情况需要程序员根据业务需求开发Handler。
	-
	**HandlerAdapter**：处理器适配器通过HandlerAdapter对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。
	-
	**ViewResolver**：视图解析器View Resolver负责将处理结果生成View视图，View Resolver首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成View视图对象，最后对View进行渲染将处理结果通过页面展示给用户。
	-
	**Handler**：处理器是继DispatcherServlet前端控制器的后端控制器，在DispatcherServlet的控制下Handler对具体的用户请求进行处理。

什么是Applet(小程序)?
	java applet是能够被包含在HTML页面中并且能被启用了java的客户端浏览器执行的程序(默认创建时都是不受信任的)。Applet主要用来创建动态交互的web应用程序。
SpringMVC常用注解(不包含spring)：
	@Transactional 开启事务，注解放在类级别时，表示所有该类的公共方法都配置相同的事务属性信息。
	@EnableWebMvc 在配置类中开启Web MVC的配置支持。
	@Controller Controller层
	@RestController该注解为一个组合注解，相当于@Controller和
	@ResponseBody的组合，注解在类上，意味着，该Controller的所有方法都默认加上了@ResponseBody。
	接口相关：
	@RequestMapping 用于映射web请求，包括访问路径和参数。
	-   @GetMapping：处理get方式请求的映射
	-   @PostMapping：处理post方式请求的映射
	-   @PutMapping：处理put方式请求的映射
	@ResponseBody 支持将返回值放到response内，而不是一个页面，通常用户返回json数据。
	@RequestBody 允许request的参数在request体中，而不是在直接连接的地址后面。（放在参数前）
	@PathVariable
	用于接收路径参数，比如@RequestMapping(“/hello/{name}”)声明的路径，将注解放在参数前，即可获取该值，通常作为Restful的接口实现方法。
	@ControllerAdvice 常用于全局异常处理，全局数据绑定，全局数据预处理
	@ExceptionHandler 用于全局处理控制器里的异常。
	@InitBinder 用来设置WebDataBinder，WebDataBinder用来自动绑定前台请求参数到Model中。
	@ModelAttribute 放在方法上代表的是：该Controller的所有方法在调用前，先执行此@ModelAttribute方法。常用于权限模块

SpringMVC开发步骤
	1.引入依赖
	在spring、mybatis基础上添加依赖
	2.在web.xml中配置DispatcherServlet，并加载spring-mvc.xml配置文件
	3.编写spring-mvc.xml
	扫描注解<context:compenent-scan>
	4.编写UserController类
	编写处理器方法：
	存储数据、跳转页面
	5.编写userList.jsp页面
	注意事项:
		依赖注解驱动支持<mvc:annotation-driven />
		@RequestMapping
		作用:
		1.确定请求URL和处理方法之间的对应关系
		2.窄化请求
		3.请求方法限定
	常用属性
		path : 指定请求路径的url
		value : value属性和path属性是一样的
		method : 指定该方法的请求方式
		params : 指定必须要传递的请求参数
**SpringMVC拦截器**
	**概念:** [[过滤器,拦截器,监听器]]
	**开发步骤**:
	**1.自定义类实现HandlerInterceptor接口**
	实现3个方法preHandle postHandle afterCompletion
	**2.在spring-mvc.xml中配置拦截器**
	<mvc:interceptors>
	<mvc:interceptor>
	<mvc:mapping path="/**"/>
	<mvc:exclude-mapping path="/testException"/>
	\<bean class="com.qfedu.interceptor.MyInterceptor02">\</bean>
	</mvc:interceptor>
	<mvc:interceptor>
	<!--拦截路径-->
	<mvc:mapping path="/**"/>
	<mvc:exclude-mapping path="/testException"/>
	\<bean class="com.qfedu.interceptor.MyInterceptor01">\</bean>
	</mvc:interceptor>
	</mvc:interceptors>
**SpringMVC异常处理**
	在SpringMVC中，统一将异常使用throw抛出给异常机制处理，
	项目中异常过多后，不便管理，所以将异常分为三类。
	**1.业务异常**
	发送对应消息给用户，提醒规范操作
	**2.系统异常**
	1.发送固定消息传递给用户，安抚用户；2.发送特定消息给运维人员，提醒维护；3.记录日志
	**3.突发异常**
	1.发送固定消息传递给用户，安抚用户；2.发送特定消息给编程人员，提醒维护；3.纳入预期处理范围；4.记录日志
	public class BusinessException extends RuntimeException {
	public class SystemException extends RuntimeException {
	-
	**自定义异常处理器**
	![[SpringMVC(视图-模型-控制器设计模式)_image_2.jpg]]
	**1. SpringMVC中ioc全局异常处理器,在web.xml中配置并屏蔽自动注册异常处理器
	2. implements HandlerExceptionResolver
	3. 实现处理方法**
	**---------------------------例子:**
	@ControllerAdvice
	public class ExceptionAdvice {
	@ExceptionHandler(BusinessException.class)
	public String handleBusinessException(Model model){
	model.addAttribute("errorMsg","请规范操作");
	return "error";
	}
	@ExceptionHandler(SystemException.class)
	public String handleSystemException(Model model){
	model.addAttribute("errorMsg","操作用户过多，请稍后重试");
	return "error";
	}
	@ExceptionHandler(Exception.class)
	public String handleOtherException(Model model){
	model.addAttribute("errorMsg","系统正在维护，请联系管理员");
	return "error";
	}
	}
	**Controller层操作**
	@RequestMapping("/testException")
	public void testException(int num){
	if (num == 1) {
	throw new BusinessException("年龄不能大于88岁");
	} else if (num == 2) {
	throw new SystemException("手机号错误");
	} else {
	throw new RuntimeException();
	}
	}


请求参数绑定
	**绑定机制**
	表单提交的数据都是k=v格式的 username=haha&password=123
	SpringMVC的参数绑定过程是把表单提交的请求参数，作为控制器中方法的参数进行绑定的
	将传入的参数以键值对的方式输入,键为变量名,传到MVC中也为形参名
	**支持的数据类型**
	简单类型(提交表单的变量名和参数的名称是相同的)
	基本数据类型和字符串类型
	实体类型（JavaBean）
	数组类型
	**注解说明**
		@RequestMapping 通用注解，窄化请求
		@PutMapping 针对put方式，修改操作
		@DeleteMapping 针对delete方式，删除操作
	**请求参数绑定之javaBean**
	提交表单的变量名和JavaBean中的属性名称需要一致
	当不一样的时候可以使用@params注解进行说明
	**请求参数绑定之javaBean包装类**
	如果一个JavaBean类(对象A)中包含另外一个JavaBean类(对象B)，
	表单的name属性需要编写成：对象B.属性来对应参数的名称
	**请求参数绑定之数组,集合**
	数组可以直接提取,并且遍历
	集合需要添加@RequestParam("集合名") 注解
	(@RequestParam("ids") List\<Integer> ids)
	**请求参数绑定之日期类型格式转换**
	参数需要添加注解 书写格式:
	(@DateTimeFormat(pattern = "yyyy‐MM‐dd") Date date)
	**请求参数中文乱码**
	在web.xml中配置CharacterEncodingFilter过滤器
	<filter>
	<filter‐name>EncodingFilter</filter‐name>
	<filter‐class>org.springframework.web.filter.CharacterEncodingFilter</filter‐class>
	<init‐param>
	<param‐name>encoding</param‐name>
	<param‐value>UTF‐8</param‐value>
	</init‐param>
	</filter>
	<filter‐mapping>
	<filter‐name>EncodingFilter</filter‐name>
	<url‐pattern>/*</url‐pattern>
	</filter‐mapping>
	处理器方法中使用原生ServletAPI对象
	springmvc支持传入的Sevlet常用原生API:
	HttpServletRequest
	HttpServletResponse
	HttpSession
	PrintWriter

@RequestParam注解
	作用
	把请求中的指定名称的参数值传递给控制器中的形参赋值
	属性
	value：请求参数中的名称
	required：请求参数中是否必须提供此参数，默认值是true，必须提供
@RequestBody注解
	作用
	用于获取请求正文的内容（注意：get方法不可以）
	属性
	required：是否必须有请求体，默认值是true
	default: 请求参数默认值
Controller方法返回值
	Controller处理器常用返回值:
	String:书写地址
	**ModelAndView对象**:返回该对象,该对象可以获取和输入值和选定跳转地址..等
	返回值:ModelAndView
	ModelAndView对象是Spring提供的一个对象
	可以用来调整具体的JSP视图,也可以存储数据
	返回值:void
	如果控制器的方法返回值编写成void,可以使用请求转发或者重定向跳转到指定的页面
	返回值:String
	Controller方法返回字符串可以指定逻辑视图的名称，根据视图解析器为物理视图的地址。
	需求
	使用返回字符串的方式替换上一个章节的入门案例,可以实现重定向和请求转发 "redirect:/jsp/index.jsp"; "forward:/jsp/index.jsp";
	}
	//RestController和Controller的区别在于前者可以直接返回对象到页面,而后者只能跳转到jsp,html等\ @RestController相当于@ResponseBody + @Controller。
静态资源访问配置
	**概述**
	当web.xml中将DispatcherServlet的url-parttern设置为”/”，意味着所有的请求都会由SpringMvc容器进行拦截处理，也包括静态资源(js,图片等等)，会导致无法正常访问静态资源,需要放行静态资源
	**解决方案:**
	在spring-mvc.xml中配置DefaultServletHttpRequestHandler,
	像一个检查员，对进入DispatcherServlet的URL进行筛查，如果发现是静态资源的请求，就将该请求转由Web应用服务器默认的Servlet处理，如果不是静态资源的请求，才由DispatcherServlet继续处理。
	<mvc:default‐servlet‐handler />
	---
	@ResponseBody响应json数据
	校验用户名是否存在
	请求参数为json字符串
	**开发步骤**
	1.引入依赖
	2.编写异步请求代码
	3.编写后台Controller代码
	SpringMVC可以自动解析请求参数中的json字符串
	,依赖于@RequestBody
	SpringMVC可以自动根据对象生成json字符串,
	依赖于@ResponseBody
	SpringMVC可以自动解决响应中文乱码,
	依赖于@ResponseBody

SpringMVC上传
	导入commons-fileupload-1.4.jar包
	commons-io-2.6.jar包
	ioc解析器org.springframework.web.multipart.commons.CommonsMultipartResolver
	id必须为multipartResolver
	-
	**jsp页面**
	\<form method="post" enctype="multipart/form‐data"action="${pageContext.request.contextPath}/upload.do">
	文件:\<input type="file" name="uploadFile"/>\<br>
	\<button type="submit">上传\</button>
	\</form>
	**Controller层**
	注意:这里的pic必须要和页面上的文件项的name属性一致
	@RequestMapping("/upload.do")
	public ModelAndView checkUsername(HttpServletRequest request , MultipartFile
	uploadFile) throws Exception {
	String dirPath = request.getServletContext().getRealPath("upload");
	File dirFile = new File(dirPath);
	//upload文件如果不存在就创建
	if (!dirFile.exists()){
	dirFile.mkdir();
	}
	//在服务器上的真实路径
	File desFile = new File(dirFile,uploadFile.getOriginalFilename());
	uploadFile.transferTo(desFile);
	ModelAndView modelAndView = new ModelAndView();
	modelAndView.setViewName("index");
	return modelAndView;
	}

SSM整合之非聚合项目
	-   开发流程
	-   1.整合spring、mybatis
	-   mapper代理方式
	-   2.配置springmvc框架
	-   3.整合ssm
	-   在web.xml中配置ContextLoaderListener监听器加载spring-core.xml
SpringMVC组件目录
	默认加载的组件在org.springframework.web.servlet目录下DispatcherServlet.properties文件中有设置SpringMVC的默认组件.设置的常用组件有:HandlerMapping(处理器映射器),HandlerAdapter(处理器适配器),ViewResolver(视图解析器).