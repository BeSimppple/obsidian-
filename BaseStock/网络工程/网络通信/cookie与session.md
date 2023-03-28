会话技术
概念:打开浏览器,访问一些服务器的资源,直到关闭浏览器,整个过程称之为会话
会话技术只要是解决整个会话过程中产生的数据的保存问题
分类
* #Cookie ： 浏览器端的会话技术
* #Session ： 服务器端的会话技术
* 作用

* 解决业务执行过程中，数据保存的问题

* ServlketContext存在数据交叉的问题(他人也可以轻松看到你的数据)

* request存在丢数据的问题(不同地址请求不同数据无法传递)

  

  

Cookie

cookie是由服务器端创建发送给浏览器,浏览器可以将cookie中的键值对存储在浏览器端,用于下一次请求同一个网站的时候发送给服务器,最经典的保存账户密码就是通过cookie

  

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

  

  

Session

Session是一个服务器端的会话技术

创建session对象

request.getSession()

  

执行流程

1. 第一次发起请求时，请求头Cookie中如果没有jsessionid，那么服务器创建session对象

2.当响应产生时，会将session对象的id作为响应头Set-Cookie携带到浏览器保存

3.第二次发起请求时，请求头Cookie中会协议jsessionid

4. 服务器根据该jsessionid在内存中查找是否有对应的session对象，如果有直接使用，否则新建

  

session是一个域对象,他的存在在浏览器初始化的时候初始化,一般在浏览器销毁的时候销毁,所以在同一次使用浏览器的时候可以使用session域对象传输数据

,但是 可以使用invalidate方法销毁session对象

如果浏览器关闭了，Cookie会销毁，Cookie中的jsessionid也会销毁，但是不意味着服务器中session对象销毁。