会话概念
	**概念:** 打开浏览器,访问一些服务器的资源,直到关闭浏览器,整个过程称之为会话
## Session(服务端会话技术)
**Session的作用和原理**
	**作用:**
	解决session会话中浏览同页面刷新后重复信息丢失导致登录问题
	**原理:**
	1.  当用户首次访问Web应用程序时，服务器会创建一个唯一的Session标识符，并将其存储在请求头(Cookie中)或者在URL中传递给客户端。
	2.  在后续的请求中，客户端会将Session标识符发送回服务器。
	3.  服务器接收到包含Session标识符的请求后，会根据标识符从存储介质中读取对应的Session数据。
	4.  服务器可以在Session中存储一些数据，例如用户的登录状态、购物车中的商品等。
	5.  当用户关闭浏览器或者Session超时时，服务器会删除Session数据。

Session执行流程
	1. 第一次发起请求时，请求头Cookie中如果没有jsessionid，那么服务器创建session对象
	2.当响应产生时，会将session对象的id作为响应头Set-Cookie携带到浏览器保存
	3.第二次发起请求时，请求头Cookie中会协议jsessionid
	4. 服务器根据该jsessionid在内存中查找是否有对应的session对象，如果有直接使用，否则新建
Session生命周期
	是一个域对象,他的存在在浏览器初始化的时候初始化,一般在浏览器销毁的时候销毁,所以在同一次使用浏览器的时候可以使用session域对象传输数据,但是 可以使用invalidate方法销毁session对象
	如果浏览器关闭了，Cookie会销毁，Cookie中的jsessionid也会销毁，但是不意味着服务器中session对象销毁。

**请求头和请求体**
	HTTP请求通常由请求头和请求体两部分组成。
	**请求头**包含一些元信息，例如Cookie,请求方法、URL、请求头字段等。
	**请求体**则包含一些请求参数或者请求数据，例如表单数据、JSON数据等

* ServletContext存在数据交叉的问题解决(他人也可以轻松看到你的数据)
	**原因:** 当多个线程同时访问ServletContext中的数据时，可能会出现数据交叉的问题
	**解决方案:**
	1. 使用同步机制
	2. 使用线程安全的数据结构
	3. 仅一条线路可写,其他均为可读
* request存在丢数据的问题(不同地址请求不同数据无法传递)
	**原因:** 请求处理过程中发生了异常或者错误导致的或者重定向无法传递request,以及请求方式(get,post)不同导致的
	**解决方案:**
	1. 使用Session
	2. 使用隐藏表单域
	3. 使用URL传递参数
**Servlet之间的关系图**
	![[cookie与session(会话数据保存)_image_1.png]]


---
## Cookie(浏览器端会话技术)
**Cokie作用和原理**
	**作用:**
	解决session会话结束后丢失的用户信息,导致每次请求访问都需要重新登录
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
