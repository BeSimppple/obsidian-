**泛型的作用和原理**
	**作用**:
	用于传入约束数据类型(提高后续可读和安全性),省去类型强制转换对于性能没有影响
	**特点**:
	1. 泛型约束前后必须一致
	2. <只能是引用数据类型>
	3. jvm在编译期间会擦除所有泛型,反射(运行阶段)是通过Signature属性获取到参数
	**原理**:
	本质是在JVM编译阶段进行强制转换然后将不符合的类型擦除

**泛型是什么时候确定的**
	1. 泛型类被继承时
	2. 当创建泛型类实例对象时
	3. 方法中的泛型是方法被调用的时候决定
	4. 接口中的泛型是被实现的时候确定的
	5. 泛型接口创建匿名内部类对象匿名内部类必须继承一个父类或者实现一个接口
	6. 假如接口或类都设定有泛型,则遵守就近原则
**泛型的生命周期**:
	在源码的时候产生和结束(这才使后面反射越过泛型检查成立)

静态类和方法的泛型:
	1. 静态方法的泛型时,不可以由泛型类来决定
	2. 静态泛型方法的泛型定义在Static后
	3. 静态泛型方法的泛型定义不能由类的泛型类型决定
	不能和类的泛型一致
	因为静态泛型方法能在类对象创建前,类名.调用

**泛型作用范围**:
	java中3个阶段:1.原码2.编译3运行
	**泛型只存在于原码阶段.**

**几种泛型通配符写法**
	\<?>泛型确定类型可以是任意类型
	\<? extends E>泛型确定类型可以是E或者是E的子类
	\<? super E>泛型确定类型可以是E或者是E的父类


---
file更应该叫做一个路径
文件路径或者文件夹路径
File类的创建功能
public boolean createNewFile():创建文件 如果存在这样的文件，就不创建了
public boolean mkdir():创建文件夹 如果存在这样的文件夹，就不创建了
public boolean mkdirs():创建多级文件夹
File类的删除,判断,获取功能
删除功能
public boolean delete():删除文件或者文件夹
注意事项：
Java中的删除不走回收站。
要删除一个文件夹，请注意该文件夹内不能包含文件或者文件夹
判断功能
public boolean isDirectory():判断是否是目录
public boolean isFile():判断是否是文件public boolean exists():判断是否存在
获取功能
public String getAbsolutePath()：获取绝对路径
public String getPath():得到构造file的时候的路径。
public String getName():获取名称
public long length():获取长度。字节数
isHidden方法,判断系统隐藏文件
public String[] list():获取指定目录下的所有文件或者文件夹的名称数组
public File[] listFiles():获取指定目录下的所有文件或者文件夹的File数组
文件名称过滤器FilenameFilter
public String[] list(FilenameFilter filter)
public File[] listFiles(FileFilter filter)
:文件名称过滤器的使用
例如:
File[] files = file.listFiles(new FilenameFilter() {//拿到每一个路径对象,然后加入过滤器
@Override
public boolean accept(File dir, String name) {
if(name.endsWith(".txt")){//加入后缀名为.txt就放行
return true;
}
return false;
}
});
for (File file1 : files) {//遍历数组
System.out.println(file1.getName());
}
IO流概述及分类
概念
IO流用来处理设备之间的数据传输
Java对数据的操作是通过流的方式
Java用于操作流的类都在IO包中
流按流向分为两种：输入流，输出流。
流按操作类型分为两种：
字节流 : 字节流可以操作任何数据,因为在计算机中任何数据都是以字节的形式存储的
字符流 : 字符流只能操作纯字符数据，比较方便。
IO流常用父类
字节流的抽象父类：
InputStream
OutputStream
字符流的抽象父类：
Reader
Writer
IO程序书写
使用前，导入IO包中的类使用时，进行IO异常处理
使用后，释放资源
BufferedInputStream和BufferedOutputStream拷贝
缓冲思想
字节流一次读写一个数组的速度明显比一次读写一个字节的速度快很多
BufferedInputStream:带缓冲区的字节输入流
BufferedInputStream内置了一个缓冲区(数组)
BufferedInputStream会一次性从文件中读取8192个, 存在缓冲区中
程序再次读取时, 就不用找文件读取字节了, 直接从缓冲区中获取直到缓冲区中所有的都被使用过, 才重新从文件中读取8192个.
BufferedOutputStream:带缓冲区的字节输出流
BufferedOutputStream也内置了一个缓冲区(数组)程序向流中写出字节时, 不会直接写到文件, 先写到缓冲区中直到缓冲区写满, BufferedOutputStream才会把缓冲区中的数据一次性写到文件里
int len;
byte[] arr = new byte[1024*8];
while((len = fis.read(arr)) !=‐1) {
fos.write(arr, 0, len);
}
正常复制传输数据流
1024字节文件
需要2048次操作文件
需要2048次操作硬盘
因为操纵硬盘速度非常慢
所以如果用字节数组传入进去
BufferedInputStrem内置一个缓冲区(数组)
冲虚再次读取时就不用从文件中读取字节了,1直接从缓冲区读取
BufferedOutputStrem也是一样设置一个缓冲区
8192java内存 在jvm虚拟机中加载
那么就减少了操作硬盘的次数
大大加快了传输速度
//如果没有释放资源那么----仅仅针对高效流
//如果最后一次未满8kb 也会以8kb的形式传过去.
//所以复制过去的文件可能会稍微大几kb.可以用计算机/1024计算余数就是多的那几kb
StringBuffer不适用于增强for循环
对象操作流
ObjectOutputStream
ObjectIntputStream
序列化:将对象写入到文件中
反序列化:将对象从文件中读取出
为了减少磁盘读取的次数
创建一个集合将要写入的对象存进集合,然后存入集合
同理读取时强转对象成集合,之后遍历集合
字符流
转换流
InputStreamReader
OutputStreamReader
这两个是通向是字节流通向字符流的桥梁
[InputStreamReader](../../java/io/InputStreamReader.html#InputStreamReader(java.io.InputStream, java.nio.charset.Charset))([InputStream](../../java/io/InputStream.html) in, [Charset](../../java/nio/charset/Charset.html) cs)
这个有参构造方法可以放入字节流参数,和字符集设置编码解码格式