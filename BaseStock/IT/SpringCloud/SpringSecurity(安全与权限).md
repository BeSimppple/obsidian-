SpringSecurity(安全框架)
	springSecurity+jwt+Oauth2
	同类型安全框架有Apache的shiro(独立框架不基于spring)
**为什么要用springSecurity?**
	1.**认证**和**授权**功能并且**模块化(集成简单)**
	2.@hasAnyAuthority(XXX)方法级别权限注解(**细粒度高**)
	3.web站点的XXS(跨站点脚本)和CRSF(跨站点请求伪造)**攻击防护**以及头部管理和会话管理
		后端向前端发送
**SpringSecurity本质(过滤器链)**
	[SpringSecurity-W3C讲解](https://www.w3cschool.cn/springsecurity/ted11ii1.html)
	过滤器三个方法:init(),destory(),doFilter()
	![[SpringSecurity(安全与权限)_image_1.jpg]]
	绿色部分,过滤器都是可以高度定制修改的
	UsernamePasswordAuthenticationFilter(重要)
	作用:拦截匹配URL为/login且必须为POST请求,实现登录JWT令牌生成,校验功能
微服务整合springSecurity
	1.创建一个springSecurity项目,导入pom依赖
	spring-boot-starter-web
	spring-boot-starter-security
	(security包内自己设计了一套登录验证规则和页面)
	但是满足不了生产功能,因此需要自己定制
**三个模块概念:**
	主体：principal( 使用系统的用户或设备或从其他系统远程登录的用户等等。简单说就是谁使用系统谁就是主体。)
	认证：authentication( 主体是否完成登录)
	授权：authorization(主体是否拥有访问目标资源的权利)
**2.Springsecurity登录流程**
	![[SpringSecurity(安全与权限)_image_2.jpg]]
	其中AuthenticationProvider直接认证方式是基于内存存储主体信息进行认证,可以跳过UserDetailService的认证
**自定义主体规则(认证规则)**
	1.**基于内存验证(不常用)**:
		自定义一个配置类@EnableWebSecurity MyWebSecurityConfigurerAdapter
		然后extends WebSecurityConfigurerAdapter
		重写其中configure(AuthenticationManagerBuilder auth)方法
		auth.inMemoryAuthentication().passwordEncoder(NoOpPasswordEncoder.getInstance())
		.withUser("root").password("123").authorities("admin")
		定义密码加密方式为不加密,然后输入想要配置的用户名密码以及权限,全局使用链式代码方式书写
	2.**基于数据库(常用):**
		在MyWebSecurityConfigurerAdapter重写configure()方法auth.userDetailsService(传入自定义MyUserserviceDetail)
		表示使用我们自定义的处理方式去校验
		自定义一个MyUserserviceDetail类,实现userserviceDetail接口(校验账号密码并返回权限主体到上下文),实现loadUserByUsername()方法,在这个方法中通过传入的用户名到数据库通过sql语句拿到用户的主体(账号秘密权限)
		将获取到的权限内容遍历放到list集合然后通过StringUtils工具类将list集合内容通过指定格式分割传入grantedAuthorities
		信息与用户输入的账号密码进行比对,如果正确则
		return User(用户主体信息,grantedAuthorities类格式要求a,b,c形式)
		将这些信息放到上下文(SecurityContext)之后访问其他权限内容则会去查询上下文
		SecurityContext中存储了当前用户的认证以及权限信息。
		所有过滤器共享这个上下文
		如果不正确则写其他逻辑例如弹出提示不正确...
**自定义http资源访问规则(访问规则)**
	默认:所有请求都需要认证(表单格式认证json方式)
	**1.基于方法自定义http访问规则与跳转页面:**
		首先MyWebSecurityConfigurerAdapter中去重写configure(HttpSecurity http)方法
		http.authorizeRequests()
		.antMatchers("/order/\*\*").hasAnyAuthority("order","admin")
		http.formLogin()
		.loginPage("/login.html")
		.loginProcessingUrl("/doLogin")
		.defaultSuccessUrl("/index.html")(html页面放在resource下的static包下表示静态资源绝对路径第一层便可访问到)
		.failureForwardUrl("/login.html");
		http.csrf().disable();
		http.addFilter()//加入指定过滤器,如果是继承某过滤器则覆盖原版过滤器
		定义访问/order开头的页面资源需要order或admin权限
		formLogin表示开启表单提交功能,同时可以使用不同方法指定登录页面,登录成功和失败的跳转页面
		crsf().disable()表示关闭crsf防护(csrf详见笔记顶部)
	**2.基于注解自定义http资源访问规则**
		放到启动类上代表开启方法级别权限控
		@EnableGlobalMethodSecurity(prePostEnable = true)
		@PreAuthorize("hasAnyAuthority('XXX','XX')")写在指定接口方法上代表需要指定XXX和XX权限
		底层也是基于方法
**密码的常见加密方式**
	**MD5(不可逆)**
		一种被广泛使用的[密码散列函数](https://baike.baidu.com/item/%E5%AF%86%E7%A0%81%E6%95%A3%E5%88%97%E5%87%BD%E6%95%B0/14937715)，可以产生出一个32字符的散列值（hash value）
		可能会被暴力破解(所以需要加salt)
	**Bcrypt(不可逆)->项目使用**
		springSecurity提供
		加slat且每次同样salt生成的密码不同
		BCrypt有四个变量：
			1.  saltRounds: 正数，代表hash杂凑次数，数值越高越安全，默认10次。
			2.  myPassword: 明文密码字符串。
			3.  salt: 盐，一个128bits随机字符串，22字符
			4.  myHash: 经过明文密码password和盐salt进行hash，默认10次下 ，循环加盐hash10次，得到myHash
		![[SpringSecurity(安全与权限)_image_3.jpg]]

---
## 单点登录SSO功能实现:
**单点登录意义是什么?**
	单点登录(Single Sign On)
	**是一种身份认证方法，用户一次可通过一组登录凭证登入会话，在该次会话期间无需再次登录，即可安全访问多个相关的应用和服务**
单点登录需要实现三个功能(顺序):登录--认证--授权
**基于JWT(JSON Web Token)(本地存储->登录校验)令牌登录校验:**
**为什么使用JWT令牌**
	1.JWT信息可以存在客户端减少内存压力(JWT可以设置过期时间,且如果有需要可以在每次访问后刷新过期时间)
	2.JWT安全不容易被破解
	3.JWT同时还可以存放用户其他信息减少再次调用获取信息的请求从而减少开销
	4.成功让每个服务之间保持无状态的请求
Session和Redis登录校验缺点
	**基于session的登录校验:**
		1.session的占用服务器内存问题
		2.session的不同服务之间共享问题(可能需要多次登录)
		3.前后端分离跨域写jessionid的问题(前后端域不同)
	**基于redis的登录校验(每次请求将验证信息token放到请求头):**
		1.每次请求都需要经过redis验证效率低服务开销大
jwt实现登录校验流程
	![[SpringSecurity(安全与权限)_image_4.jpg]]
	单独的登录模块将校验成功的验证信息生成JWT放到客户端本地,后续每次请求携带JWT遇到需要校验的请求时拦截器会校验JWT的数据正确则执行业务逻辑
**JWT令牌组成部分:**
	1.**头部(head) (不常修改)**
		默认如下由 令牌类型和 所使用的签名算法组成一般
		{
		'alg':'HS256'
		'typ':'JWT'
		}
		进行base64编码（不是加密）==> 'xxxxxxx'
	2.**载荷(payload)**
		token中存放有效信息的部分，比如用户名，用户角色，过期时间等，但是不要放密码，因为载荷可以被解析！
		{
		account:'admin',
		auth:['admin','seller']
		}
		进行base64编码（不是加密）==> 'yyyyyy'
	3.**签名(signatrue)(主要修改部分**)
		将头部与载荷分别采用 base64编码后，用“.”相连，再加入盐，最后使用头部声明的编码类型进行加密，就得到了签名。
		签名=HS256(xxxxxx.yyyyyyy,salt) ==> 'ZZZZZZ'
		盐是用于校验的,即使获取了JWT令牌不知道盐,也不能校验成功(但是salt是用于校验的意味着每个服务都需要拿到salt那就不安全可能会被泄露,所以采用RSA非对称加密)
[[RSA非对称加密]]
	(salt可以使用RAS非对称加密 将盐分类为公钥和私钥 公钥用于不同资源之间的解析 私钥用于第一次的加密)
	私钥:唯一生成令牌钥匙,只放在单点登录模块
	公钥:多用的校验钥匙,放在所有需要校验的模块各一个(无法生成令牌,只能做校验)
**java项目整合JWT**
	1.导入pom依赖
	com.auth0.java-jwt
	使用JWT类中方法即可实现生成,校验等操作
	2.从网上复制RSA非对称加密工具类的代码放到工具类
	2.5从网上复制JJWT工具类的代码(JWT结合RSA生成公钥和私钥)
	3.使用rsa工具类将私钥和公钥生成到本地后复制到对应项目的Resource包中,实际使用要拿RSA私钥可以直接使用ResourceUtils.getFile("classpath:rsa").getPath()拿到私钥的地址然后使用RSA工具类的RsaUtils.generatePrivateKey(私钥地址)构建私钥对象PrivateKey
	然后使用JJWT工具类的JwtUtils.generateTokenExpireInMinutes(用户信息,PrivateKey,过期时间)生成JWT令牌
	4.使用公钥获取JWT令牌中用户信息,同理使用ResourceUtils.getFile("classpath:rsa.pub").getPath()获取到
	公钥地址,然后通过RsaUtils.generatePublicKey(公钥地址)获取到公钥对象,然后使用JJWT工具类的
	JwtUtils.getInfoFromToken(JWT令牌,公钥,需要生成的格式例如map.class);
**Secutiry整合JWT令牌(修改过滤器)**
	![[SpringSecurity(安全与权限)_image_5.jpg|300]]
	自定义类继承UsernamePasswordAuthenticationFilter过滤器,
		**主要作用登录校验然后颁发JWT令牌**
		写一个属性private AuthenticationManager authenticationManager(认证管理器,需要通过他讲认证信息传递到UserDetaiServicel中)后定义有参构造函数
		**重写其中(登录处理逻辑)**
		attemptAuthentication(HttpServletRequest request, HttpServletResponse response)方法
		因为账号密码可能是json提交格式而不是表单提交格式没法用getParameter()所以
		通过request.getInputstream()获取用户名和密码的字节流然后new ObjectMapper().readVlaue(字节流,map.class)获取登录的用户名和密码
		然后return this.getAuthenticationManager.authenticate(new UsernamePasswordAuthenticationToken(账号,密码)) 传递给UserDetailService
		全程使用try/catch包围,如果发生异常不抛出,使用printWriter返回ResultVo错误信息给页面然后return null
		然后到SecurityConfig中,configure方法中使用http.addfilter(MyUsernamePasswordAuthenticationFilter)
		加入自定义过滤器如果是继承某过滤器则覆盖完成
		和 **(认证成功回调方法)**
		successfulAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authResult) 方法
		回调方法主要作用是生成JWT令牌
		final AuthBean authBean = (AuthBean)authResult.getPrincipal();获取用户主体信息
		通过ResourceUtils.getFile("classpath:rsa").getPath()拿到私钥的地址然后使用RSA工具类的RsaUtils.generatePrivateKey(私钥地址)构建私钥对象PrivateKey
		然后使用JJWT工具类的JwtUtils.generateTokenExpireInMinutes(用户主体信息,PrivateKey,过期时间)生成JWT令牌
		全程使用try/catch包围,如果发生异常不抛出,使用printWriter返回ResultVo错误信息给页面,没有异常则正常prinWiriter打印正确信息和JWT令牌到本地
	自定义类继承BasicAuthenticationFilter
		**主要作用校验JWT令牌**
		重写doFilterInternal(HttpServletRequest request, HttpServletResponse response,FilterChain chain)方法
		首先通过request.getHeader("token");获取到JWT令牌,
		然后JwtUtils.getInfoFromToken(token, RsaUtils.getPublicKey(ResourceUtils.getFile("classpath:rsa.pub").getPath()),Map.class)根据JWT令牌和公钥获取到载荷信息(用户信息) 然后遍历用户的权限信息存到list(a,b.c)格式放到grantAuthority放到UsernamePasswordAuthenticationToken
		最后通过SecurityContextHolder.getContext().setAuthentication(UsernamePasswordAuthenticationToken);将用户权限信息放到Security上下文中,最后chain.doFilter(request, response);放行
		然后到SecurityConfig中的Configure()方法中使用http.addFilter(自定义BasicAuthenticationFilter)
自定义JWT中的用户主体信息
	JWT主体信息默认使用的Security中的User只有账号密码和权限信息属性,
	自定义主要解决如果其他类需要使用用户id或商户id来操作会导致代码臃肿的问题,不如在一开始便提供好可能需要的属性
	首先创建一个自定义类extends User(Springframework.Security)
	创建你需要的属性例如:
	private Stiring userId;
	在UserDetailService返回AuthBean返回到SecurityContext上下文中
	在UsernamePasswordAuthenticationFilter中登录回调生成JWT令牌时放入的用户主体也改为AuthBean
	在BasicAuthenticationFilter的doFilterInternal()校验方法解析JWT令牌获取其中主体信息然后new一个AuthBean放入UsernamePasswordAuthenticationToken最后返回到SecurityContext上下文中
网关和所有项目整合SpringSecurity
	第一种是全权由springGateway进行校验获取权限信息,并控制权限给下属项目
	**第二种方式:**
	是springGatewat只负责校验,然后每个项目都依赖SpringSecurity自行二次校验和获取主体中的权限信息
	此处采用的是第二种模式:(因为第二种简单,且每个模块能拿到JWT令牌并分析载荷将权限放到SecurityContext方便权限控制)
	1:创建一个sso登录模块(将security中创建JWT令牌和回调函数的代码复制进去)只负责拿到账号密码去数据库校验的登录逻辑和颁发JWT令牌只使用私钥,无法验证并放行
	2.gateway设置全局过滤器
	前置环境操作:
	1.将rsa.pub放到对应项目resource包下
	2.整合SpringSecurity依赖
	3.定制SecurityConfig的configure()方法,使用http.addFilter(new CustomBasicAuthenticationFilter())引入自定义验证JWT过滤器
	4.将CustomBasicAuthenticationFilter过滤器(校验过滤器)放到项目filter包下
	5.将AuthBean放到项目Bean包下
	创建自定义类实现GlobalFilter和Ordered接口,实现其中Filter(ServerWebExchange exchange, GatewayFilterChain chain)方法
	exchange.getRequest().getURI().getPath()拿到传入的URL路径,对路径做if判断,如果是/login则放行,然后通过request获取head中的token判断token是否为空如果为空则返回错误信息,如果不为空则继续通过ResourceUtils.getFile("classpath:rsa.pub").getPath()拿到放在resource中的公钥地址,通过JWT工具类的getInfoFromToken(JWT令牌,RsaUtils.getPublicKey(公钥地址),Map.class)获取到map形式的载荷中的用户的信息最后return chanin.Filter(exchange)放行(如果是方案1则可以将解析出的内容传给其他模块然后通过内容做限制)
	最后重写getOrder()返回值改为-100提高过滤器优先级
	网关并没有做JWT令牌校验不需要依赖security包(如果依赖还需要重写basic的校验Filter),只是用工具类对JWT进行解析如果报错则返回登录失败,如果正确则放行给其他模块进行JWT校验
	3.在gateway的yaml文件中写上sso项目监视/login地址
	4.在其他项目中引入springSecurity和Utils包依赖和rsa.pub公钥,并将修改好的basic的filter过滤器复制过去做登录校验,然后将basic过滤器放到securityConfig的configure(HttpSecurity http)方法中http.addFilter()进行配置实现全项目整合springSecurity
	自此实现每个项目自行验证并获取JWT令牌中的主体信息并放到SecurityContext中,从而实现基于注解的权限控制

---
vue某些问题
	因为令牌具有时效性,所以需要去判断令牌是否有效,这时候在SpringSecurity模块控制器层写一个校验路径,在前端发起请求,被permission拦截判断是否有TOKEN的时候可以请求该方法进行校验TOKEN有效性
**springSecurity过滤器详解**
	1 . org.springframework.security.web.context.SecurityContextPersistenceFilter
	首当其冲的一个过滤器，作用之重要，自不必多言。
	SecurityContextPersistenceFilter主要是使用SecurityContextRepository在session中保存或更新一个
	SecurityContext，SecurityContext中存储了当前用户的认证以及权限信息。
	所有过滤器共享这个上下文
	2 . org.springframework.security.web.context.request.async.WebAsyncManagerIntegrationFilter
	此过滤器用于集成SecurityContext到Spring异步执行机制中的WebAsyncManager
	3 . org.springframework.security.web.header.HeaderWriterFilter
	向请求的Header中添加相应的信息,可在http标签内部使用security:headers来控制
	4 . org.springframework.security.web.csrf.CsrfFilter
	csrf又称跨域请求伪造，CSRF攻击：攻击者盗用了你的身份，以你的名义发送恶意请求
	该过滤器拦截所有post请求，验证是否包含系统生成的csrf的token信息，
	如果不包含，则报错。起到防止csrf攻击的效果。
	5.org.springframework.security.web.authentication.logout.LogoutFilter
	拦截匹配 URL为/logout的请求，实现用户退出,清除认证信息。
	6.org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter
	拦截匹配URL为/login且必须为POST请求,实现登录校验功能
	7.org.springframework.security.web.authentication.ui.DefaultLoginPageGeneratingFilter
	如果没有在配置认证(登录)页面，则由该过滤器生成一个默认认证(登录)页面。
	8.org.springframework.security.web.authentication.ui.DefaultLogoutPageGeneratingFilter
	由此过滤器可以生产一个默认的退出登录页面
	9.org.springframework.security.web.authentication.www.BasicAuthenticationFilter
	此过滤器会自动解析HTTP请求中头部名字为Authentication，且以Basic开头的头信息。
	10.org.springframework.security.web.savedrequest.RequestCacheAwareFilter
	通过HttpSessionRequestCache内部维护了一个RequestCache，用于缓存HttpServletRequest
	11.org.springframework.security.web.servletapi.SecurityContextHolderAwareRequestFilter
	针对ServletRequest进行了一次包装，使得request具有更加丰富的API
	12.org.springframework.security.web.authentication.AnonymousAuthenticationFilter
	当SecurityContextHolder中认证信息为空,则会创建一个匿名用户存入到SecurityContextHolder中。
	spring security为了兼容未登录的访问，也走了一套认证流程，只不过是一个匿名的身份。
	13.org.springframework.security.web.session.SessionManagementFilter
	SecurityContextRepository限制同一用户开启多个会话的数量
	14.org.springframework.security.web.access.ExceptionTranslationFilter
	异常转换过滤器位于整个springSecurityFilterChain的后方，用来转换整个链路中出现的异常
	15.org.springframework.security.web.access.intercept.FilterSecurityInterceptor
	获取所配置资源访问的授权信息，根据SecurityContextHolder中存储的用户信息来决定其是否有权限。
**什么是crsf防护:**
	crsf防护(使用账号登录后会有一个jessionid存在域中如果该id被其他机器盗用则会存在账号泄露,crsf的作用是根据本机信息组成加密成一串符号同时上传,如果没有crsf的加密符则无法登录从而保证安全性)