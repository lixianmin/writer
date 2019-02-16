----
#### Item 1: Consider static factory methods instead of constructors 

static factory的优点有：

1. 不需要每次都new一个新的对象
2. 可以返回subtype，可以返回private subtype，可以在版本迭代过程中使用全新的class

static factory的缺点有：

1. static factory with only private constructors can not be subclassed.

----
#### Item 2: Consider a builder when faced with many constructor parameters

1. Telescoping constructor pattern: 会创建很多很多的构造函数
2. JavaBean pattern: 创建的过程中对象可能会处于inconsistent的状态
3. Builder pattern: 模拟以Python中的named optional parameters，但可以使用多个varargs

----
#### Item 5: Avoid creating unnecessary objects

1. 如果adapter不含有backing object的状态的话，根本没有必要创建多个adapter对象
2. 除非你的object非常heavyweight，否则object pool可能还没有GC来得快

----
#### Item 6: Eliminate obsolete object references

obsolete references导致内存泄漏，常见的几种原因包括：

1. 自己管理内存的class：容器，各种manager，它们实际上额外增加了一个对object的引用，因此free对象时切记主动nulling out
2. cache：
	1. 弱引用(WeakHashMap)，如果对象生命周期恰好与cache差不多长的话
	2. background thread
	3. LinkedHashMap : as a side effect of adding new entries to the cache
3. listeners and callbacks：store only weak references，比如作为WeakHashMap的key

最小化的使用variable的scope

----
#### Item 7: Avoid finalizers

除以下两种情形外，不要使用finalizer:

1. safety net: 防止资源泄漏，better late than never，此时在finalizer中应该加入warning日志
2. terminate noncritical native peers：本地对等体，JNI

另外，“终结方法链（finalizer chaining）”并不会被自动执行，因此需要考虑try{...} finally { super.finalize(); } ，以及**finalizer guardian**

----
#### Item 8: Obey the general contract when overriding equals

什么时候应该覆盖`Object.equals`呢？如果类具有自己特有的“逻辑相等”概念（不同于对象等同的概念），而且超类还没有覆盖`equals`以实现期望的行为，这时我们就需要覆盖`equals`方法。这通常属于**值类（value class）** 的情形。值类仅仅是一个表示值的类，例如String、Integer或者Date。这样做使得这个类的实例可以被用作映射表（map）的键（key），或者集合（set）的元素，使映射或者集合表现出预期的行为。

1. a==b 永远是判断两个对象的引用是否是一致的，而equals()因为是方法，可以被子类override，因此
2. **Composition中不要试图兼容组合类**: 比如你自己写了一个LocaleText类，写equals()方法的时候，你想“我必须兼容string类，在它们text值相等的时候返回true”，然而这可能只是一厢情愿，因为你兼容了string，但string类并不会兼容你，单相思了啊
3. **Inheritance中不试图兼容基类，除非你的基类都是abstract的**：原因跟前面一条类似，单相思，不对等
4. equals方法是确定的、一致的、稳定的，不能抛exception，不能依赖hardware, network