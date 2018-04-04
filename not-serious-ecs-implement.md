

---

通过使用ECS，我们可以像**填配置表一样订制代码**。

懂行的人稍微看一下就会知道，我的设计并不是一个正统的ECS实现方案。正统的ECS中Component是纯数据的，而System则是纯函数无状态，我的设计暂时没有关心这些准则，先围绕自己关心的问题实现。从结果上看，更像是Unity3d中的Component实现方案。


1. 简单
2. Enitity与Component解耦


---
#### 1. Entity不需要知道正在使用哪些Component

关于ECS框架网上有一些讨论，就目前我了解到的一些方案，Entity都是明确知道自己使用哪些Component的。我认为这跟OO中的组合模式（Composite Pattern）区别不大，我可以接受Component强引用Entity，但不能接受Entity强引用Component。之所以这样设计，有两个比较重要的原因：

1. **越简单越易用**：理想的情况是Entity需要哪些Component的时候随时创建，不需要的时候随时销毁。如果Entity代码中硬编码了Component成员变量，那么就需要手工编码所有其它相关的操作，比如：命名，Init(), Dispose()等，删除或重命名时也需要手动调整相关代码。因为这些例行操作在实践中会经常遇到，我认为手工编码的话过于复杂了。参考文献中的Entitas插件通过自动生成代码的方式自动化了这一过程。

2. **代码编译期可以做到Entity与Component解耦**：我们项目有一个需求跟《守望先锋》很像。我们希望部分Logic层的Client代码（比如MoveComponent）可以直接在Server上运行，此时需要完全剥离出View层的代码(比如RenderComponent)。这要求Logic层代码不能知道任何View层代码的信息，否则会编译不过。同时因为所有相关代码的生命周期都是一样的，因此ECS是一个favorable的设计方案。具体就是在Client端Entity会动态挂接所有相关Component，而在Server端Entity只需要挂接Logic层的Component。

综合以上原因，相对理想的理想的方案就是类Unity3d中的Component组件方式：

```csharp
    var entity = new GameObjectEntity();
    entity.AddComponent(typeof(MoveComponent));      // Logic层
    entity.AddComponent(typeof(RenderComponent));    // View层
    ...
    entity.RemoveComponent(typeof(RenderComponent));
```







---
#### 存疑问题


1. System只有行为，没有状态，是怎么做到的

2. MoveSystem通过某种方式可以得到所有拥有Position和Speed的实体集合， 这是一种怎样的方式？

3. 文章表示可以通过加入EnemyComponent表示这是一个enemy，这意味着EnemyComponent是非常轻量级的，而我现在的实现是相对重量级的，内含的成员太多了

4. 我看Entitas的IComponent就是一个空的接口，它后面是怎么实现的

5. system中遍历组件需要按type进行排序，这样速度才会快

6. typeIndex与partId可以合并为一个int64

7. 有40%的component是singleton，这个应该如何支持？

2. Component需要能订制是否pool

3. System要求无状态，C\#中有几个概念跟这个是相关的：静态类，工具类，纯函数，扩展方法



---

### References


1. [游戏开发中的ECS 架构概述](https://zhuanlan.zhihu.com/p/30538626)
2. [一个无框架的ECS实现（Entity-Component-System](https://zhuanlan.zhihu.com/p/32787878)
3. [浅谈《守望先锋》中的 ECS 构架（云风）](https://blog.codingnow.com/2017/06/overwatch_ecs.html)
4. [Entitas-CSharp](https://github.com/sschmid/Entitas-CSharp)

