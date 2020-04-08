

----

GCRoot

1. 静态对象
2. 栈对象
3. jni对象





----

基本的GC算法就三种：

1. 引用计数（reference counting）：
2. 标记清除（mark & sweep）：需要STW（stop the world）。会产生heap碎片
3. 复制收集（copy & collection）：浪费一半内存，如果只有小部分需要存活的话，速度会很快



java的GC综合了上述几种方案的优缺点，是一种分代算法：新生代，老生代，永久代。

1. 新生代的GC叫Minor GC：新生对象的特点是绝大部分都可以被立即回收，比如90%。那么每次GC只需要找到需要活下来的那10%，拎到Survivor区，然后直接把Eden区内存reset就OK了。新生代的内存分为三部分：
   1. Eden 80%，伊甸园，就是亚当、夏娃最初居住的地方。这喻意也够明显了，新生成的对象都放到Eden区
   2. Survivor1, Sruviror2，分别占10%，交替使用，实际上难民营。每次Minor GC活下来的对象，只要没进入老生代，就从一个Survivor搬到另一个Survivor，同时把搬离的那一家直接推倒重建。





<img src="https://pic4.zhimg.com/80/v2-c71d5bf56a068a8bf3075b11ea05eae7_1440w.jpg" style="zoom:80%" />

上图对应的JVM的参数：

“-Xms3072M -Xmx3072M -Xmn2048M -Xss1M -XX:PermSize=256M -XX:MaxPermSize=256M -XX:SurvivorRatio=8”



----

#### 0x09 References

1. [JVM内存管理，Minor GC和Full GC触发机制总结](https://zhuanlan.zhihu.com/p/82186499)
2. [案例实战：每日上亿请求量的电商系统，JVM年轻代垃圾回收参数如何优化？](https://zhuanlan.zhihu.com/p/76203311)
3. 

