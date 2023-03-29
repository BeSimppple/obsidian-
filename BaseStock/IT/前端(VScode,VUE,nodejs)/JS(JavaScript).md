JavaScript

概述:

被客户端(浏览器)识别的脚本语言

不需要编译,直接可以被浏览器解析执行

作用:

结合html页面,实现动态交互效果,增强用户和页面的交互过程,增强用户体验

特性:

1.srcipt可以写在网页的任意一个位置

2可以直接引入srcipt标签,内部编写js

3.外部编写js文件,html页面引入js文件

  

与java的对比(变量方面)

变量:用来存储数值的内存空间

java是强类型语言, js是弱类型语言;java声明变量时已经确定了变量要赋值的固定数据类型,而js变量赋值数据类型是不确定的,通过浏览器的解析自动识别

  

js中的方法定义:

第一种:

var fn1 = new Function("arg1,arg2", "console.log(arg1);console.log(arg2)");

第二种:

var fn2 = function(name, age) {

console.log(name + "---" + age);

}

第三种:

function fn3(name, age) {

console.log(name + "---" + age);

}

  

Bom

全称:browser object model

浏览器的内置对象,本来就有的,能直接拿来使用的

bom的最上层对象是window,通过window可以获取很多子对象

1.location:当前页面的url

2.document:文档对象(当前html页面这个文档对象)

3.history:历史记录对象

  

Dom对象

全称:document object model

dom操作对页面中的节点(标签)实现增删改查

  

事件

鼠标事件

onclick:点击事件(单机)

ondblclick:双击事件

onmousedown:鼠标按住不动

onmouseup:鼠标按下弹起触发

onmousemove:鼠标移动触发

onmouseover:鼠标移入

onmouseout:鼠标移出

onmouseenter:鼠标移入

onmouseleave:鼠标移出

  

键盘事件

onkeydown:键盘按下触发

onkeyup:键盘弹起触发

onkeyPress:键盘按下触发(ctrl alt shift esc 四个键不会触发)

  

表单事件

1. label标签：本身没有特殊的显示效果，for的值是对应表单控件的id，当鼠标选中label时，能够将焦点聚集到表单控件上

2. 页面上写多少个空格都只会显示一个空格

3. &nbsp; 空格表示使用

&ensp; 表示半角(半个中文宽度)

&emsp; 表示全角(一个中文宽度)

  

使用js实现商城放大镜案例

  

  

页面定位设置:

1. 问：当使用绝对定位时，偏移量的参照是谁？

答：首先查看上级容器中是否存在position属性，如果存在，以上级容器为参照进行偏移

如果不存在，以浏览器为参照进行偏移

2. 相对定位：相对原来的位置的偏移,如果标签处于循环往复的不规律的移动中，原来的位置可能就不是很好找,所以我们在实际绘制页面中，一般不会将相对定位配合偏移量使用

3. 网页布局使用定位的结论：

a. 绝对定位配合偏移量使用布局页面

b. 相对定位不会改变本身的位置，并且可以给子容器的绝对定位偏移提供参照

  

  

  

  

文件上传

前端代码

请求方式post

并且设置enctype=multipart/form-data

  

后端代码

开发步骤

1.导入jar包

2.创建磁盘文件项工厂对象

DiskFileItemFactory diskFileItemFactory = new DiskFileItemFactory();

3.获取核心解析对象

ServletFileUpload servletFileUpload = new ServletFileUpload(diskFileItemFactory);

4.解析请求，获取所有文件项对象

List<FileItem> fileItems = servletFileUpload.parseRequest(req);

-   如果是普通文本，直接打印
-   如果是文件，进行文件上传