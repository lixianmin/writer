



---
#### [Java并发编程：volatile关键字解析](http://www.cnblogs.com/dolphin0520/p/3920373.html)

1. 原子性：就是指的事务的原子性，在java中，对**基本数据类型变量的读取和赋值是具有原子性的**，然而：

```java
    x = 10;     // 原子操作
    y = x;      // 包含2个原子操作，一个读取x，一个对y赋值
    x++;        // 包含3个原子操作，一个读取x，一个对x加1，一个写回
    x = x + 1;  // 同样包含3个原子操作
```

2. 可见性：指当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值

3. 有序性：（intra-thread semantics）当发生指令重排序（Instruction Reorder）时，程序的执行结果与不重排的时候一样。JVM对代码执行顺序会有优化，保证单线程的情况下，代码的执行结果是对的



一旦一个共享变量（类的成员变量、类的静态成员变量）被volatile修饰之后，那么就具备了两层语义：

1. 保证了不同线程对这个变量进行操作时的可见性，即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的。

2. 禁止进行指令重排序。



**也就是说，volatile(1)解决了可见性，(2)部分解决了有序性，(3)然而并不care原子性**

##### 使用volatile关键字的场景

　　synchronized关键字是防止多个线程同时执行一段代码，那么就会很影响程序执行效率，而volatile关键字在某些情况下性能要优于synchronized，但是要注意volatile关键字是无法替代synchronized关键字的，因为volatile关键字无法保证操作的原子性。通常来说，使用volatile必须具备以下2个条件：

　　1）对变量的写操作不依赖于当前值

　　2）该变量没有包含在具有其他变量的不变式中

　　实际上，这些条件表明，可以被写入volatile变量的这些有效值**独立于任何程序的状态，包括变量的当前状态**。

　　事实上，我的理解就是上面的2个条件需要保证操作是原子性操作，才能保证使用volatile关键字的程序在并发时能够正确执行。

```java
volatile boolean inited = false;
//线程1:
context = loadContext();  
inited = true;            
 
//线程2:
while(!inited ){
    sleep()
}

doSomethingwithconfig(context);

```



---
#### [双重检查锁定与延迟初始化](http://www.infoq.com/cn/articles/double-checked-locking-with-delay-initialization)

```java
public class DoubleCheckedLocking {                      //1
    private static Instance instance;                    //2

    public static Instance getInstance() {               //3
        if (instance == null) {                          //4:第一次检查
            synchronized (DoubleCheckedLocking.class) {  //5:加锁
                if (instance == null)                    //6:第二次检查
                    instance = new Instance();           //7:问题的根源出在这里
            }                                            //8
        }                                                //9
        return instance;                                 //10
    }                                                    //11
}                                                        //12
```



double check需要使用volatile的根本原因是为了保证有序性，`instance= new Instance()`这一句并不是一个操作，而是从逻辑上就可以拆为三部分伪代码：

```java
memory = allocate();   //1：分配对象的内存空间
ctorInstance(memory);  //2：初始化对象
instance = memory;     //3：设置instance指向刚分配的内存地址
```

JIT进行代码重排之后就可能变成了下面的样子：

```java
memory = allocate();   //1：分配对象的内存空间
instance = memory;     //3：设置instance指向刚分配的内存地址
                       //注意，此时对象还没有被初始化！
ctorInstance(memory);  //2：初始化对象
```

所以问题来了：线程2在第4行进行instance == null判空检查时，虽然instance不为null，但拿到的有可能是一个分配了内存，但未初始化的对象。

----
#### Reference
