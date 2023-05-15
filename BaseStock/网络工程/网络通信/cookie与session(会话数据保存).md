会话概念
	**概念:** 打开浏览器,访问一些服务器的资源,直到关闭浏览器,整个过程称之为会话

## Cookie(浏览器端会话技术)
**Cokie作用和原理**
	**作用:**
	解决session会话中断后丢失的用户信息,导致每次请求访问都需要重新登录
	**原理:**
	1.  当用户首次访问Web应用程序时，服务器会在HTTP响应头中设置一个Set-Cookie头，其中包含一个唯一的标识符和一些数据。
	2.  浏览器接收到响应后，将Cookie保存在本地文件系统中。以后每次访问该Web应用程序时，浏览器都会将Cookie发送回服务器。
	3.  当服务器接收到包含Cookie的HTTP请求时，它可以读取其中的数据，并根据需要更新Cookie的内容。
	**注意:** Cookie是客户端存储的，因此它对于客户端是可见的。为了防止恶意客户端修改Cookie中的数据，服务器会对Cookie进行签名或加密,并且为了保护隐私而设置过期时间.

Cookie的执行流程:
	1.浏览器发起第一次请求，服务中创建Cookie对象，服务通过响应操作响应头"Set-Cookie"将Cookie信息携带到浏览器
	2.浏览器拿到Cookie信息之后保存
	3.浏览器发起第二次请求，会通过请求头"Cookie"携带Cookie信息
	4.服务器通过"Cookie"请求头可以获取对应的Cookie信息
	Cookie cookie1 = new Cookie(cookie名称,cookie值);
	response.addCookie(cookie1);
	Cookie[] cookies = request.getCookies();
	cookie.setMaxAge(7 * 24 * 60 * 60);//设置cookie的生命周期
	cookie.setPath("/day36/demo06");//设置cookie的访问路径

  

  

## Session(服务端会话技术)
Session是一个服务器端的会话技术
创建session对象
request.getSession()

  

执行流程
1. 第一次发起请求时，请求头Cookie中如果没有jsessionid，那么服务器创建session对象
2.当响应产生时，会将session对象的id作为响应头Set-Cookie携带到浏览器保存
3.第二次发起请求时，请求头Cookie中会协议jsessionid
4. 服务器根据该jsessionid在内存中查找是否有对应的session对象，如果有直接使用，否则新建

  
session是一个域对象,他的存在在浏览器初始化的时候初始化,一般在浏览器销毁的时候销毁,所以在同一次使用浏览器的时候可以使用session域对象传输数据,但是 可以使用invalidate方法销毁session对象
如果浏览器关闭了，Cookie会销毁，Cookie中的jsessionid也会销毁，但是不意味着服务器中session对象销毁。
