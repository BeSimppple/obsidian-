CSS

<!--如果要将表单标签的内容提交到服务器,name需要给表单标签设置name属性-->

<!--

让单选框的两个name属性相同,就达成了互斥的效果

-->

CSS:层叠样式表

cascading(层叠)style(样式)sheets(表格)

作用:通过css定义html元素如何显示.

优点:提高工作效率,html代码与样式代码分离

  

书写格式:

选择器{

属性:属性值;

属性:属性值;

}

选择器的优先级

A.

总结

内联结合方式 > ID选择器 > 类选择器 > 标签选择器

内联结合方式因为就近才最高级,其他是因为本身的优先级

  

工程项目结构的解释

1.css:用来存放css样式文件

2.img:用来存放资源文件,比如图片,音频,视频...

3.js:用来存放js(javascript)脚本文件

4.index.html:网页文件(这个index文件是默认的网页文件如果你没有设置其他的网页文件)

  

CSSS与HTML结合之内联结合

<div style="font-size:20pxcolor:red;">这是一个div</div>

1.有点简单方便

2.复用性差

  

CSS和HTML结合之内部结合

<style>

div{

font-size:20px;

}

  

</style>

  

CSS之外部结合

新建一个CSS文件.使用link标签引入外部样式文件

  

CSS之id选择器

#(设定的id){

CSS语句

}

  

CSS之类选择器

<style>

.(设定的类名){

CSS语句

}

</style>

  

CSS之标签选择器

<style>

(标签名){

CSS语句

}

  

</style>

  

CSS选择器之分组

概念:多个选择器使用同一个段的CSS,name就可以使用分组,选择器之间使用逗号隔开.

例如:

#div1,.c1{

CSS语句

}

  

CSS之派生选择器

概念:依据元素在其位置的上下文关系来定义,可以使标签更加简洁,也称为上下文选择器

  

父标标签(父id,父类名) 子标标签(子id,子类名){

CSS语句

}

  

CSS伪类

a:link{color:XXX}未访问本来的颜色

a:visited{color:XXX}已经连接的颜色

a:hover{color:XXX}鼠标悬停时的颜色

a:active{color}鼠标点击时的颜色

注意事项:

hover必须在link和visited后

active必须在hover后

  

CSS字体属性

  

font 简写属性

font-family 设置字体系列

font-size 设置字体尺寸

font-size-adjust 当首选字体不可用时,对替换字体智能缩放

font-stretch(伸展延伸) 设置进行水平拉伸

font-style 设置字体书写格式

font-variant 以小型字体显示打字体,或者大字体显示正常字体

font-weight 设置字体粗细

  

CSS文本属性

  

color 设置文本颜色

direction 设置文本的开始方向默认ltr(left ro right)

line-height 设置行高

letter-spacing 字符间隔

text-align 文本位置

text-decoration 向文本添加修饰

text-indent 首行缩进

text-shadow 文本阴影

text-transfrom 控制元素中的字母

unicode-bidi 文本的方向

white-space 文本空白的处理方式

word-spacing 字间距

  

CSS背景属性

  

background-color 背景颜色

background-attachment 背景是否固定随页面其余部分滚动

background--image 背景图片

background-position 背景图像的启示位置

background-repeat

background

  

CSS尺寸属性

height

line-height

max-height

max-width

min-height

min-width

width

  

CSS列表属性

list-style

list-style-image

list-style-position

list-style-type

marker-offset

  

CSS边框属性

  

border

border-style

border-color

border-bottom

border-bootom-color

(其他几个边框格式书写类似)

  

盒子模型

从外到内

margin ---border ---- padding ---element

外边框 --- 边框 ---- 内边框 -- 元素

css定位,浮动

分类

固定定位 fixed

相对于整体界面边框位置进行设定固定

相对定位 relative

相对于原先的位置的右下角进行调整

并且保留原先的占位空间

绝对定位 absolute

相对于父级元素的位置进行调整,

并且不保留原先的占位空间

position

top

right

(上下左右)

overflow

clip

vertical-align

z-index

  

块级元素和行内元素

块级元素:包含一整行,前后带有换行符

行内元素:包含内容,前后没有换行符

display转换: