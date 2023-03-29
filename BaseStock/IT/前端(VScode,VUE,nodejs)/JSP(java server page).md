
概述:

目前Servlet要处理的事情

1.请求处理 ： Servlet本职工作Controller层

2.业务处理 ： 后续交给业务类Service层

3.操作数据库 ： 后续交给持久类Dao层

4.显示数据 ： 本应该是交给html，但是html无法获取

java数据，所以不行。急切地需要一个可以获取java数据的页面技术。所以诞生了jsp

  

java server page : 可以理解为是一个可以获取java数据的html

本质是一个servlet

因为,jsp会转译成一个java类，而该类继承于HttpJspBase，而HttpJspBase是Servlet一个子类

  

执行流程

1.会将jsp转译成java类，并存放到tomcat安装目录中“\work\Catalina\localhost\day38\org\apache\jsp”，本质上就是一个servlet

2.该Servlet会将jsp中编写的html标签作为响应正文写回到浏览器进行显示

  

jsp脚本

分类:

1.声明脚本 : 声明成员变量<%! %>

2.片段脚本 : 在jspService方法中处理代码<% %>

3.输出脚本 : 向浏览器输出内容,<%= %>

相当于response.getWrite().write()

jsp页面中可以使用的注释有三类：html注释、java注释、jsp注释

其中jsp注释在所有代码中都适用

  

jsp指令:

概念:用于指定jsp完成一些特点功能和效果

1.taglib指令

在当前jsp页面中引入第三方标签库,比如：jstl标签库

<%@taglibprefix="c"

uri="http://java.sun.com/jsp/jstl/core" %>

2.page指令

contentType="text/html;charset=UTF-8"

-   告诉服务器和浏览器的字符集是utf-8

isELIgnored="false"

-   是否忽略el表达式

errorPage="error.jsp"

-   当前页面发生异常后跳转的页面路径

isErrorPage="true"

-   当前页面是否可以使用异常对象（exception）, 默认是false

import="java.util.List"

-   在当前页面中导入相关java类

3.include指令

<%@ include file="资源路径" %>作用:在jsp中引入外部资源

  

  

jsp9大内置对象

| 对象名 | 含义 |

| ----------- | ------------------------------ |

| page | 是jsp页面对象 |

| pageContext | jsp页面上下文环境对象，域对象 |

| request | HttpServletRequest对象，域对象 |

| response | HttpServletResponse对象 |

| session | HttpSession对象，域对象 |

| out | JspWriter对象 |

| exception | Throwable对象 |

| application | ServletContext对象，域对象 |

| config | ServvletConfig对象 |

  

  

jsp四大域对象

-   pageContext
-   request
-   session
-   application

pageContext可以获取所有域中的对象,并且可以调用其他域,或者直接写对象设置名然后挨个搜索

Servlet三大域对象

-   HttpServletRequest
-   HttpSession
-   ServletContext

  

el表达式

概述:

是jsp内置一个表达式

用来替代jsp脚本

  

语法格式: ${表达式}

  

常用web对象

pageScope、requestScope、sessionScope、applicationScope、pageContext

  

pageScope：获取pageContext域属性

requestScope：获取request域属性

sessionScope：获取session域属性

applicationScope：获取application域属性

pageContext：可以获取JSP九大内置对象

cookie：用于获取cookie，Map<String,Cookie>，其中key是cookie的name，value是cookie对象

  

jstl:

概述

1.jsp standard tag library : jsp标准标签库

2.和el表达式结合使用，可以完成更好的效果

导入方式:使用jar包,然后使用taglib指定在指定页面引入jstl标签库

  

<c:set var="msg" value="hello" scope="request"></c:set>

  

<c:remove var="msg"></c:remove>

  

<c:catch var="e"> </c:catch>

  

<c:if test="${num == 1}"> </c:if>

  

<c:if test="${num != 1}"> </c:if>

  

<c:forEach begin="1" end="100" var="num" step="10">

</c:forEach>

  

<c:forEach items="${myList}" var="num" varStatus="stat">

</c:forEach>

  

<c:forTokens items="${str}" delims="-" var="myStr">

</c:forTokens>

  

  

BeanUtil框架使用,

servlet中多次req.getP获取数据时

可以直接根据获取的数据写一个对应的对象

对象 对象名= new 对象();

BeanUtils.populate(对象名,req.getParameterMap())

将传来的数据封装到对象中

  

  

分页查询,

创建一个pageBean对象,其中包括

当前页数(currentPage)

总页数(totalPage)

总记录数(totalSize)

每页记录数(pageSize)

当前页数据(list)

将他作为一个整体的原因是因为其中大多数的属性不会变化,而且如果为整体那么数据的传输将更加便利

  

以上5个参数是在service层确定,