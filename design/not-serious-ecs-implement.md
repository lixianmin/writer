
---

### 非正经ECS实现方案

作者：李现民

github: [https://github.com/lixianmin](https://github.com/lixianmin)

---

#### 0x00. Abstract

本文中的ECS是Entity-Component-System（实体-组件-系统） 的缩写，是一种代码框架设计理念。ECS使我们可以

懂行的人稍微看一下就会知道，我下面介绍的设计并不是正统的ECS实现方案。正统ECS中，要求Component是纯数据，System是纯函数（无状态）。在我们的设计中（暂时maybe -\_\_\_\_- ）没有care这些准则，我们的目的是可以像**填配置一样订制代码**，而从结果上看，更像是Unity3d中的Component实现方案。希望了解正经ECS设计方案的，请移步文末的参考文献区，那里有一些链接也许对你有用。

该方案基于Unity3d引擎，编码使用C\#，因此下面的示例语法也都是C\#的。

---

#### 0x01. Entity不需要知道正在使用哪些Component

关于ECS框架网上有一些讨论，就目前我了解到的一些方案，Entity都是明确知道自己使用哪些Component的。我认为这跟OO中的组合模式（Composite Pattern）区别不大，我可以接受Component强引用Entity，但不能接受Entity强引用Component。之所以这样设计，有两个比较重要的原因：

1. **越简单越易用**：理想的情况下，Entit在需要任意Component时可随时创建，而不需要时可随时销毁。如果在Entity代码中硬编码了Component成员变量，那么就需要同样手工编码所有其它相关的操作，比如：命名，Init\(\), Dispose\(\)等，删除或重命名时也需要手动调整相关代码。这些例行操作在实践中经常遇到，因此我认为手工编码的话过于复杂了。参考文献中的Entitas通过插件自动生成代码的方式自动化了这一过程。

2. **Entity在编译期与Component解耦**：我们项目有一个需求跟《守望先锋》很像。我们希望部分Logic层的Client代码（比如MoveComponent）可以直接在Server上运行，此时需要完全剥离出View层的代码\(比如RenderComponent\)。这要求Logic层代码不能强引用任何View层代码的信息，否则会编译不过。同时因为所有相关代码的生命周期都是一样的，因此动态增删Component是一个favorable的设计方案。具体就是在Client端Entity会动态挂接所有相关Component，而在Server端Entity只需要挂接Logic层的Component。

综合以上原因，相对理想的理想的方案就是类Unity3d中的Component组件方式：

```csharp
var entity = new GameObjectEntity();
entity.AddComponent(typeof(MoveComponent));   // Logic层
entity.AddComponent(typeof(RenderComponent)); // View层

// ...其它代码
entity.RemoveComponent(typeof(RenderComponent));
```

具体实现时，Entity中的Component全部存储在一张中心Hashtable（Type =&gt; Component）中，而为此**付出的代价是：Speed & Memory**：

* Speed：因为对Component的Add/Remove/Get全部通过Hashtable进行，因此与直接访问类成员变量相比速度会更慢。在macOS 10.12.6 + Unity3d 2017.1.0f3下，实测C\#的Hashtable（Type =&gt; Component）与直接访问数组的耗时比大概为10:1，Dictionary&lt;Type, Component&gt;与直接访问数组的耗时比大概为15:1。测试时Hashtable与Dictionary都使用了默认参数，没有调整loadFactor。据网上说相同的量级下Hashtable的loadFactor会更低，也因此会占用更大的内存。

* Memory：传说，同样的数据，存储在哈希表中比存储在数组中要多占用两倍左右的内存。

---

#### 0x02. 缓存友好

从实现效果上，ECS设计倾向于以属性为中心（请参考《游戏引擎架构P655》）。Entity中只需要存储实际用到的Component（即：我们不会有一些对象，内含未使用的Component成员），这对于有效使用内存是有益的，不过，考虑到我们使用哈希表存储Component成员，一正一负，实际内存占用不好说是升了还是降了。

Update Method是游戏设计中的一种常规设计手法，具体方法可能命名为Update\(\)或Tick\(\)，本文中不作区分。在以对象为中心的设计中，很多宿主对象与其属性对象都需要写一个Update\(\)方法，用于在每一帧更新相关数据。实际上，因为Update\(\)调用通常是自上而下逐级进行的，因此只要有一个属性对象需要Update\(\)方法，都会强迫其所在的宿主对象及每一个上游对象都拥有Update\(\)方法。这样，以游戏代码中的初始Update\(\)方法为根节点，自上而下，由外到内，对所有对象Update\(\)方法的调用可以看成是一个树形结构，我们可称之为Update树。以对象为中心的设计模型有一些缺点，其中之一就是Update\(\)树中相邻叶节点的类型通常是不一样的，因此在遍历整个Update树的过程中，缓存命中失败的概率（cache miss rate）比较大。

以属性中心设计则可能更加缓存友好。在我们的ECS实现方案中，我们设计了一个名为ComponentUpdateSystem的类，收集所有包含Update\(\)的Component，将它们**存储在同一个array中并按type排序**。这样，相同type的Component在内存中是连续存储的，数据布局符合**数组之结构（struct of array, SoA）**的要求。在遍历调用所有Component的Update\(\)方法时，能够减少或消除缓命中失败。

具体到ComponentUpdateSystem类的实现细节，由于我们使用array存储Component对象，在添加或删除Component时，不应该立即调整array中的内容，否则会导致频繁移动array中的数据，可能引起不必要的CPU开销。添加Component时，可以先将新的Component对象append到数组尾部，在真正遍历array中的Component之前，将其按type排序（因此在最坏的情况下ComponentUpdateSystem.Update\(\)的时间复杂度为O\(NlogN\)）。删除Component时，也不需要立即从array中移除，只需要在遍历结束后的某个时刻调用一个RemoveAll\(\)方法统一移除即可（类似于List&lt;T&gt;.RemoveAll\(\)，只移动一次内存）。

不同type的Component之间对Update\(\)方法的调用先后顺序可能有要求，Unity3d中专门区分了Update\(\)与LateUpdate\(\)应对这件事情。我们通过以下方式可以控制的更加细致：给每一种type提供一个typeIndex值，并将array中的Component按typeIndex的顺序排序（在C\#中，Array.Sort\(keys, items\)方法可以帮助）。大部分情况下，不同Component之间的Update\(\)调用顺序并无特殊要求，因此只需要在第一次访问这种Component的type时候自动生成一个typeIndex即可。对于少部分需要严格控制Update\(\)调用顺序的Component，只需要在游戏初始化时为它们设置指定的typeIndex值就可以了。

在初版设计中，我们将typeIndex作为property放到Component类中，但经过几周的迭代发现，该变量只在排序Update\(\)的调用顺序时有用，因此将其转移到了ComponentUpdateSystem类中，作为Array.Sort\(keys, items\)的keys参数。这种设计明显违反了ECS中要求System不能含有状态的准则，因此你们现在可以准备批评我了。

---

#### 0x03. Component基类与IComponent接口

Entity类中有一对方法名为AddComponent和RemoveComponent，分别负责创建和销毁Component。这对方法分别会调用Component类中的一对虚方法DoInitialize\(\)和DoDispose\(\)。这样，Component的生命周期就由它所在的Entity全盘接管。

在某些情况下，我们可能不希望或无法使用Component基类。一种情况是，我们需要非常轻量级的组件，它可能只需要包含一个int或bool值，用于存取数值或当做标志位使用，此时使用Component创建一个子类的话显得过于重度了。另一种情况是，目标组件类已经有一个基类了，

---

#### 存疑问题

1. System只有行为，没有状态，是怎么做到的

2. partId到底应该不应该有？

3. MoveSystem通过某种方式可以得到所有拥有Position和Speed的实体集合， 这是一种怎样的方式？

4. 文章表示可以通过加入EnemyComponent表示这是一个enemy，这意味着EnemyComponent是非常轻量级的，而我现在的实现是相对重量级的，内含的成员太多了

5. 我看Entitas的IComponent就是一个空的接口，它后面是怎么实现的

6. system中遍历组件需要按type进行排序，这样速度才会快

7. typeIndex与partId可以合并为一个int64

8. 有40%的component是singleton，这个应该如何支持？

9. Component需要能订制是否pool

10. System要求无状态，C\#中有几个概念跟这个是相关的：静态类，工具类，纯函数，扩展方法

---

### References

1. [游戏开发中的ECS 架构概述](https://zhuanlan.zhihu.com/p/30538626)

2. [一个无框架的ECS实现（Entity-Component-System](https://zhuanlan.zhihu.com/p/32787878)

3. [浅谈《守望先锋》中的 ECS 构架（云风）](https://blog.codingnow.com/2017/06/overwatch_ecs.html)

4. [Entitas-CSharp](https://github.com/sschmid/Entitas-CSharp)

5. [游戏引擎架构](https://www.amazon.cn/dp/B00HY8SIX2/ref=sr_1_1?s=books&ie=UTF8&qid=1522924143&sr=1-1)

6. [Update Method](https://github.com/lixianmin/design-pattern/blob/master/update-method.md)



