#关键字native
	用来声明本地方法的。 本地方法是指那些直接用非Java 语言编写的方法，例如C 或C++ 通过使用native 关键字，可以在Java 代码中调用本地方法，从而利用本地代码来实现某些功能，例如与操作系统的交互、访问底层硬件等
#关键字transient
	 修饰的字段不会被序列化
#关键字volatile
	 保证不同线程对它修饰的变量进行操作时的**可见性**，即一个线程修改了某个变量的值，新值对其他线程来说是立即可见的
	 volatile如何保证**有序性**：通过设置 #内存屏障 -(不同硬件实现内存屏障的方式不同)
	1.在每个volatile写操作的前面插入一个StoreStore屏障
    2.在每个volatile写操作的后面插入一个StoreLoad屏障
    3.在每个volatile读操作的前面插入一个LoadLoad屏障
     4.在每个volatile读操作的后面插入一个LoadStore屏障
     LoadLoad	Load1; LoadLoad; Load2	保证load1的读操作先于load2执行
	StoreStore	Store1; StoreStore; Store2	保证store1的写操作先于store2执行，并刷新到主内存
	LoadStore	Load1; LoadStore; Store2	保证load1的读操作结束先于load2的写操作执行
	StoreLoad	Store1; StoreLoad; Load2	保证store1的写操作已刷新到主内存之后，load2及其后的读操作才能执行
    （1）对于写操作：对变量更改完之后，要立刻写回到主存中。
    （2）对于读操作：对变量读取的时候，要从主存中读，而不是缓存。
    屏障类型	指令示例	说明
    **注意**:不能使用Volatile替代多线程计数器(因为部分情况下volatile操作不会被记录)

#关键字synchronized
	用于指定多线程代码中的同步方法、变量或者代码块
#关键字static
	静态修饰
#关键字abstract
	抽象化
#关键字final
	 **作用**:
	 用于表示某个变量、方法或类是最终的，**不能被修改或继承**
	**特点**:
	1. final定义的属性必须被初始化(可以在定义时初始化或构造函数时初始化)
	2. final修饰的成员变量为基本数据类型是，在赋值之后无法改变。
	3. final修饰的成员变量为引用数据类型时，在赋值后其指向地址无法改变，但是对象内容还是可以改变的。
#关键字insteadof
	用于判断对象是否属于某个类型（class）

#关键字this
	可用于在方法或构造方法中引用当前对象
#关键字throw #关键字throws
	throw为主动抛出异常
	throws用于声明异常
#关键字assert
	可以判断布尔值的结果是否和预期的一样，如果一样就正常执行，否则会抛出AssertionError
	主要在测试类中使用
#保留字goto #保留字const
	两个目前都还没有具体含义

