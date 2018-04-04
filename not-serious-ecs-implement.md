
---

ECS使我们可以像**填配置表一样订制代码**。

懂行的人稍微看一下就会知道，我将介绍的设计并不是正统的ECS实现方案。正统ECS中，Component是纯数据，System是纯函数（无状态）。在我的设计中暂时没有关心这些准则，先围绕自己关心的问题实现。从结果上看，更像是Unity3d中的Component实现方案。

框架基于Unity3d引擎，编码使用C\#，因此下面的示例代码和语法也都是C\#的。

---

#### 0x01. Entity不需要知道正在使用哪些Component

关于ECS框架网上有一些讨论，就目前我了解到的一些方案，Entity都是明确知道自己使用哪些Component的。我认为这跟OO中的组合模式（Composite Pattern）区别不大，我可以接受Component强引用Entity，但不能接受Entity强引用Component。之所以这样设计，有两个比较重要的原因：

1. **越简单越易用**：理想的情况是Entity需要哪些Component的时候随时创建，不需要的时候随时销毁。如果Entity代码中硬编码了Component成员变量，那么就需要手工编码所有其它相关的操作，比如：命名，Init\(\), Dispose\(\)等，删除或重命名时也需要手动调整相关代码。因为这些例行操作在实践中会经常遇到，我认为手工编码的话过于复杂了。参考文献中的Entitas插件通过自动生成代码的方式自动化了这一过程。

2. **代码编译期可以做到Entity与Component解耦**：我们项目有一个需求跟《守望先锋》很像。我们希望部分Logic层的Client代码（比如MoveComponent）可以直接在Server上运行，此时需要完全剥离出View层的代码\(比如RenderComponent\)。这要求Logic层代码不能知道任何View层代码的信息，否则会编译不过。同时因为所有相关代码的生命周期都是一样的，因此ECS是一个favorable的设计方案。具体就是在Client端Entity会动态挂接所有相关Component，而在Server端Entity只需要挂接Logic层的Component。

综合以上原因，相对理想的理想的方案就是类Unity3d中的Component组件方式：

```csharp
    var entity = new GameObjectEntity();
    entity.AddComponent(typeof(MoveComponent));      // Logic层
    entity.AddComponent(typeof(RenderComponent));    // View层
    ...
    entity.RemoveComponent(typeof(RenderComponent));
```

具体到实现，Entity中的Component全部存储在一张中心Hashtable（Type =&gt; Component）中，而为此**付出的代价是：Speed & Memory**：

* Speed：因为对Component的Add/Remove/Get全部通过Hashtable进行，因此与直接访问类成员变量相比速度会更慢。在macOs 10.12.6 + Unity3d 2017.1.0f3下，实测C\#的Hashtable（Type =&gt; Component）与直接访问数组的耗时比大概为10:1，Dictionary&lt;Type, Component&gt;与直接访问数组的耗时比大概为15:1。测试的Hashtable与Dictionary都是使用的默认参数，没有调整loadFactor，据网上说相同的量级下Hashtable的loadFactor会更低，也因此会占用更大的内存。
* Memory：传说，同样的数据，存储在哈希表中比存储在数组中要多占用两倍以上的内存。



---

#### 存疑问题

1. System只有行为，没有状态，是怎么做到的

2. MoveSystem通过某种方式可以得到所有拥有Position和Speed的实体集合， 这是一种怎样的方式？

3. 文章表示可以通过加入EnemyComponent表示这是一个enemy，这意味着EnemyComponent是非常轻量级的，而我现在的实现是相对重量级的，内含的成员太多了

4. 我看Entitas的IComponent就是一个空的接口，它后面是怎么实现的

5. system中遍历组件需要按type进行排序，这样速度才会快

6. typeIndex与partId可以合并为一个int64

7. 有40%的component是singleton，这个应该如何支持？

8. Component需要能订制是否pool

9. System要求无状态，C\#中有几个概念跟这个是相关的：静态类，工具类，纯函数，扩展方法

---

### References

1. [游戏开发中的ECS 架构概述](https://zhuanlan.zhihu.com/p/30538626)
2. [一个无框架的ECS实现（Entity-Component-System](https://zhuanlan.zhihu.com/p/32787878)
3. [浅谈《守望先锋》中的 ECS 构架（云风）](https://blog.codingnow.com/2017/06/overwatch_ecs.html)
4. [Entitas-CSharp](https://github.com/sschmid/Entitas-CSharp)



