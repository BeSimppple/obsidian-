**XML作用和原理**
	可扩展:X 标记语言:ML
	XML是标准通用标记语言 (SGML) 的子集
	**作用:**
	1.作为配置文件
	2.存储数据在网络中传输
	**原理:**
	XML是一种通用的标记语言，XML的实现原理是基于文本的标记语言，
	1.  **定义数据模型：** XML使用文档类型定义（DTD）或XML模式定义（XSD）来定义数据模型，以确保数据的结构和内容符合预期。
	2.  **编写XML文档**：XML文档由标签、属性和文本组成，标签用于表示数据的结构，属性用于表示数据的特性，而文本则用于表示数据的内容。
	3.  **解析XML文档**：解析XML文档是将XML文档转换为计算机可以处理的数据结构的过程。XML解析器可以将XML文档解析为DOM（文档对象模型）或SAX（简单API for XML）等数据结构。
	4.  **处理XML数据**：一旦XML文档被解析为计算机可以处理的数据结构，就可以对其进行各种操作，如读取、修改和删除等。
XML和HTML的标签由谁定义的
	W3C（万维网联盟）是一个国际标准组织，致力于开发Web标准，以确保Web的互操作性和可访问性。W3C负责制定并维护XML,HTML规范，以确保XML,HTML的标准化和互操作性。
**xml与Html的区别**
	1.xml标签可以扩展是自定义的,而html都是预定义的
	2.xml的语法严格,html语法松散
	3,xml是存储数据的,html是展示数据
	语法介绍:
	//第一行必须定义为文档说明
	//有且只有一个根标签
	//属性值必须使用引号(单双都可)
	//标签必须正确关闭
	//特性:标签区分大小写
**XML和DSL的区别**
	XML是标记语言,DSL是特定领域语言,XML通用性更强且表示的内容更多
	但是DSL能满足特定领域内容,且能动态配置减少配置项便于阅读

---
**XML解析**
	**实质:** 对XML文件进行读写操作
	主要解析方式分两种
	Dom方式(document object model)
		dom解析方式它是将整个xml文件载入内存，以树型结构来存储
		dom支持回写 可以对xml文件中内容进行crud操作
		crud操作:增删改查
		如果文件内容比较大，不建议使用dom解析。
		优势:操作方便,可以对文档进行CRUD的所有操作
		缺点:占内存
	Sax方式(simple api for xml)
		相比dom，sax是一种更为轻量级解决方案
		采用串行读取方式，逐行读取，读取一行解析一行释放一行。是基于事件的驱动,因为每一行开始都会释放
		编程比较复杂
		sax只能读取，不能对xml进行修改。
		优点:不占内存
		缺点:只能读取,不能增删改
xml常见解析器:
	Jaxp(sun标准)性能差,支持dom和sax
	Jdom
	Dom4j(重点)优秀解析器,基于dom
	jsoup 优秀的html解析器,可以直接解析某个URL地址,HTML文本内容,提供了一套JQuery的操作方法来去除和操作数据.
	pull: 安卓操作系统内置解析器 sax方式
XML中关键字
	**1.文档声明**
	<?xml version='1,0' ?>
	//属性有version:版本号
	,encoding:编码方式:告知解析引擎当前文档使用的字符集
	standalone:是否独立(yes:不依赖于其他文件)
	<根节点>
	</根节点>
	**2.指令(了解):**
	结合Css的
	<?xml stylesheet type = "text/css" herf ="a.css"?>
	**3.标签:规则**
	**4.属性**
	id属性值唯一
	**5.文本**
	特殊字符得用转义号转义
	但是有:CDATA区可以按照原义展示
	书写格式: \\<![CDATA[数据]]>
	XML约束介绍:
	因为xml书写太随意了,所以必须要有约束定义规范
**约束分类:**
	**DTD约束**
	文档类型定义,可定义合法的XML文档构建模块dtd文件后缀dtd
	内部关联//直接在指定XML文件中定义
	<!DOCTYPE 根节点名称(例如:students) [
	DTD约束代码
	]>
	<根节点>
	∙∙∙∙∙∙
	</根节点>
	外部关联//在外部写好DTD约束,引用DTD文件
	<!DOCTYPE 根节点名称 SYSTEM “DTD文件的位置”>
	//如果dtd文件在本地
	<!DOCTYPE 根节点名称 PUBLIC "dtd文件名字" “DTD文件的位置URL”>
	//如果dtd文件在网络
	<根节点>
	∙∙∙∙∙∙
	</根节点
	**Schema**
	是dtd的替代者,比dtd详细,文件后缀名是xsd.
schema和dtd的区别
	dtd不遵守xml语法，而schema它遵守xml语法，它本质上就是一个xml
	dtd数据类型不完善，而schema它有丰富数据类型
	dtd只能有一个应用于xml文件，使用schema可以多个schema约束xml文件。
Schema约束引入步骤
	1.填写nml文档的根元素
	2.引入xsi前缀
	3.引入xsd文件命名空间(ns = nameSpace )
	4.为每一个xsd约束声明一个前缀,作为标识
	\<students
	//第一步引入xsi前缀
	xmins:xsi="\http://www.w3.org/2001/XMLSchema‐instance"
	//第二部设置xsd命名空间
	//如果有多个按照下面格式区分,并且在第三步声明两个不同的前缀在定义元素的时候可以区分使用了哪种约束
	xsi:schemaLocation="
	\http://www.example.org/students students.xsd
	\http://www.example.org/students2 students2.xsd
	"
	//第三步声明一个前缀作为标识,第一种是a:第二种b:
	使用哪种约束就使用哪种标签
	xmlns:a="\http://www.example.org/students"
	xmlns:b="\http://www.example.org/students2">
	
	\<students
	xmlns:xsi="\http://www.w3.org/2001/XMLSchema‐instance"
	//声明我要用xsi空间,给一个XML的约束的实例
	xmlns="\http://www.example.org/students"
	//引用Schema命名空间
	xsi:schemaLocation="\http://www.example.org/students students.xsd">
	\</students>
复杂元素和简单元素
	元素下还有子元素则为复杂元素