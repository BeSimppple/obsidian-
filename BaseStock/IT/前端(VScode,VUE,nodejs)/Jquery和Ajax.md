Jquery

概念:Jquery是一个快速,简洁的javaScript框架,倡导写更少的代码,做更多的事情.它封装JavaScript常用的功能代码,提供一种简便的javaScript设计模式,优化HTML文档操作,事件处理,动画设计,和Ajax交互

  

书写格式:$(方法体);

其中$代表Jquery的简写

document.getElementById("main");

等同于$("#main");

  

Jquery实现动画效果

通过show方法

$("标签").show(毫秒数,function(){

方法体

例如:

show():隐藏

hide():显示

slideDown():滑下显示

slideUp():滑上隐藏

fadeIn():淡入

fadeOut()淡出

})

自定义动画animate

$("标签").animate({

//animate:只支持数值变化

width: "400px",

height: "400px",

fontSize: "50px",

backgroundColor:"red"//不生效

},3000)

  

Jquery实现淡入淡出,轮播案例


  

  

  

#### Ajax

概念:

Ajax即: "Asynchronous javascipt And XML"(异步js和xml),是指一种创建交互式网页应用的网页开发技术.

  

特点:

1.  服务器获取数据
2.  更新部分网页
3.  局部刷新页面

  

使用场景:

例如:淘宝输入框输入关键字,还没点击提交按钮的时候,,就自动显示根据输入关键字返回的下拉菜单结果

例如:注册账户等表单效验时候并没有完全输入完成也能成功提示该项输入是否正确

  

异步同步的区分：

1.  同步：客户端发起第一个请求后，需要等待服务器响应结束后，才可以发第二个请求
2.  异步：客户端发起第一个请求后，不需要等待服务器响应结束后，就可以发第二个请求

  

使用Ajax基本步骤`

1.创建XmlHttpRequest对象

var xhr = new XMLHttpRequest();

2.打开链接

//参数：

请求类型

后端url请求接口

true:使用异步请求

//语法：

xhr.open("get或者post请求","后端servlet接口(url)",true);

3.发送请求

xhr.send(String类型参数)

  

  

xhr对象与响应状态判断

-   status:请求状态码 200：ok 404:未找到
-   readyState:xhr的状态，取值0-4

-   0: 请求未初始化
-   1: 服务器连接已建立
-   2: 请求已接收
-   3: 请求处理中
-   4: 请求已完成，且响应已就绪

-   onreadystatechange：监听readyState ，每当readyState 改变，就会触发




#### Jquery的Ajax使用

参数含义：

1.  dataType:服务器返回的数据类型
2.  Type:请求类型(get post)
3.  url:请求地址
4.  contentType:默认application/x-www-form-urlencoded 发送信息至服务器时内容编码类型。
5.  success:请求成功执行的回调函数

-   回调方法中的第一个参数，是后端返回的结果

6.  error：请求失败执行的回调函数，可以打印具体的错误信息
7.  data:发送到服务器的数据,可以是数字、字符串、json对象

  

  

1.  传递参数较少时可以使用get请求直接在url后拼接
2.  传递参数较多时，使用post请求直接传递整个json对象，后端使用request.getParameter("json的键名")接收

  

  

  

ajax封装之后的get或者post请求,只有四个参数，并且参数依次是

-   url:待载入页面的URL地址
-   data:待发送 Key/value 参数。
-   callback:载入成功时回调函数
-   type:返回内容格式，xml, html, script, json, text, _default