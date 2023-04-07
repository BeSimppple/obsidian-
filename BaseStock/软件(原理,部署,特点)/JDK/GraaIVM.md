

强答一下。结论是GraalVM想成为一统天下的“最终”虚拟机。
大部分脚本语言或者有动态特效的语言都需要一个语言虚拟机运行，比如CPython，Lua，Erlang，Java，Ruby，R，JS，PHP，Perl，APL等等，但是这些语言的虚拟机水平，对，就是具体的实现，差距很大，比如CPython的VM就不忍直视，JVM的HotSpotVM，C#的CLR和JS的v8却是state of the art级别，那么能不能付出较小努力，用一个state of the art的虚拟机，来运行这些语言，让它们享受该虚拟机的一些工匠特性，比如gc，锁，jit等？
答案基本上是肯定的。<small>首先，对于Java，Scala，Groovy这些本来就是JVM-based的语言，那没有什么压力，直接上JVM即可。对于CPython，R，Ruby，PHP乃至自己写的一门新的语言，回顾一下我们的一般做法：首先解析源代码到AST，然后写一个AST解释器->当有些人用这个语言的时候，语言设计者可能继续迭代，实现一个虚拟机，包括GC'，运行时等，代码执行仍然是AST解释器->用的人多了，继续迭代，将AST转换为字节码，使用字节码解释器->用的人特别多，性能也很关键，如果这个语言社区有足够资金和人力，那么可以写JIT编译器，提升GC性能等，</small>大部分语言都到不了这一步。我们希望一门语言在AST解释器节点性能就足够好了，不用花那么多精力和财力做性能优化等，这就是Truffle语言框架的动机，Truffle是一个Java框架，自然跑在JVM上，在这个框架下，用户只需要实现具体语言的AST解释器，付出的努力比较小，性能也足够好。
为啥呢？因为基于Truffle框架，AST树在解释过程中可以根据type feedback变形，有点Profiling Guided Optimization的意思。除此之外，Truffle还可以在AST解释过程中使用编译器将这个AST树的一部分节点编译为机器代码，后面不解释执行AST节点，直接执行机器代码，这个过程也叫做部分求值（Partial Evaluation），就像下面这样：



  
  
