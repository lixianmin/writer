

---

1. java有静态初始化器，C#有静态构造方法，写法不同但概念类似，都是用于初始化类的，或者说是**在class加载之后执行**以配置class行为的。
2. 类中定义的static变量时可以直接赋值，但这些值可以在static initializer中覆盖；这跟类中定义类成员变量时可以直接赋值，但这些值可以在构造方法中覆盖的策略是一样的。



---

#### References

1. [Java提高篇——静态代码块、构造代码块、构造函数以及Java类初始化顺序](https://www.cnblogs.com/Qian123/p/5713440.html)