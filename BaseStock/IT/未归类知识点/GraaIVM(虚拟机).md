什么是GraaIVM:**结论是GraalVM想成为一统天下的“最终”虚拟机**。
大部分脚本语言或者有动态特效的语言都需要一个语言虚拟机运行，比如CPython，Lua，Erlang，Java，Ruby，R，JS，PHP，Perl，APL等等，但是这些语言的虚拟机水平，对，就是具体的实现，差距很大，比如CPython的VM就不忍直视，JVM的HotSpotVM，C#的CLR和JS的v8却是state of the art级别，那么能不能付出较小努力，用一个state of the art的虚拟机，来运行这些语言，让它们享受该虚拟机的一些工匠特性，比如gc，锁，jit等？
答案基本上是肯定的。<small>首先，对于Java，Scala，Groovy这些本来就是JVM-based的语言，那没有什么压力，直接上JVM即可。对于CPython，R，Ruby，PHP乃至自己写的一门新的语言，回顾一下我们的一般做法：首先解析源代码到AST，然后写一个AST解释器->当有些人用这个语言的时候，语言设计者可能继续迭代，实现一个虚拟机，包括GC'，运行时等，代码执行仍然是AST解释器->用的人多了，继续迭代，将AST转换为字节码，使用字节码解释器->用的人特别多，性能也很关键，如果这个语言社区有足够资金和人力，那么可以写JIT编译器，提升GC性能等，大部分语言都到不了这一步。</small>我们希望一门语言在AST解释器节点性能就足够好了，不用花那么多精力和财力做性能优化等，这就是Truffle语言框架的动机，Truffle是一个Java框架，自然跑在JVM上，在这个框架下，用户只需要实现具体语言的AST解释器，付出的努力比较小，性能也足够好。
为啥呢？因为基于Truffle框架，AST树在解释过程中可以根据type feedback变形，有点Profiling Guided Optimization的意思。除此之外，Truffle还可以在AST解释过程中使用编译器将这个AST树的一部分节点编译为机器代码，后面不解释执行AST节点，直接执行机器代码，这个过程也叫做部分求值（Partial Evaluation），就像下面这样：
![[GraaIVM(虚拟机)_image_1.png]]
之前说AST树可能发生变形，如果这个变形的节点已经被编译过，那么丢弃编译后的机器代码，回退到AST解释执行，这个过程就是退优化，和CLR，JVM的没啥两样：
  ![[GraaIVM(虚拟机)_image_2.png]]
  
  ruffle将AST节点编译为机器代码使用的编译器是Graal，这是一个Java写的即时编译器。我们提到的Truffle是一个Java框架，跑在JVM上，一个Java语言写的即时编译器怎么能编译Java代码呢？答案是通过JEP 243的JVMCI。JVM是C++写的，它内置了两个C++写的即时编译器，C1和C2。一般频繁的代码先用C1编译，这些代码即热点，如果热点继续，那么会使用C2编译。JVMCI相当于把本该交给C2编译的代码交给Graal编译，然后使用编译后的代码（其实也可以替代C1）。用Java写即时编译器看起来很魔幻，其实很正常，因为即时编译说到底就是将一段byte[]代码在运行时转换为另一段byte[]代码，用什么写都可以。
  总之，到目前为止， Java，Scala，Groovy已经可以在JVM运行了。CPython，R，Ruby，JS通过Truffle框架写个AST解释器也可以在JVM上运行了。那么如果是静态语言，像C/C++，Go，Fortran这些呢？可以使用Sulong（顺便一提这个sulong是汉语的速（rapid）龙（dragon））。解决方案是将C/C++这些语言用一些工具（如clang）转换为LLVM IR，然后使用基于Truffle的AST解释LLVM IR，这个解释LLVM IR的东西就是Sulong。到这里大部分语言都可以在JVM上运行了，上面提到的所有技术放到一张图里面，这个整体就叫做GraalVM，其实并不真正存在GraalVM这个语言虚拟机，GraalVM是指以Java虚拟机为基础，以Graal即时编译器为核心，以能运行多种语言为目标，包含一系列框架和技术的大杂烩
![[GraaIVM(虚拟机)_image_3.png]]
GraalVM棒就棒在还没完。。。上面图中所有语言最终都是运行在JVM上，需要机器提前安装JDK环境，而且JVM由于自身原因，启动速度比较慢，内存负载较高。能不能把程序直接打包成平台相关的可执行文件，后面直接执行这个可执行文件，不依赖JVM？答案是jaotc，哦不是，是吊打它几十条街的SubstrateVM。SVM借助Graal编译器，可以将Java程序AOT编译为可执行程序（没错，Graal编译器既可以JIT又可以AOT），它的过程主要是两步：
![[GraaIVM(虚拟机)_image_4.png]]
先通过静态分析找到Java程序用到的所有类，方法和字段以及一个非常小的SVM运行时，然后把这一堆东西通过AOT编译，生成一个可执行文件如elf。SVM的想法很美好，对Java的微服务/FaaS就是福音，只是现实很骨感，因为Java有反射这些动态特效，基本上是无解的，静态分析再厉害也找不到运行时动态加载的类，对于这些类只能手动写配置，或者运行时直接crash，这是个致命问题。生产环境上我知道的有Twitter和阿里尝试过SVM落地，阿里的SVM落地虽然成功了但是付出了巨大努力（狗头，而且是针对特定的app，换个app就需要再次付出努力。。除了这些外，现在SVM的GC是一个比较简单的分代GC，还缺少很多debug工具，编译速度也比较感人（主要是静态分析），这些还好，都在慢慢完善中。。。
[原文](https://www.zhihu.com/question/274042223)
  



