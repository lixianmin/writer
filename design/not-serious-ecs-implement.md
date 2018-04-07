
---

### 非正经ECS实现方案

作者：李现民

原文地址：[https://github.com/lixianmin/writer/blob/master/design/not-serious-ecs-implement.md](https://github.com/lixianmin/writer/blob/master/design/not-serious-ecs-implement.md)

---

#### 0x00. Abstract

ECS是Entity-Component-System（实体-组件-系统） 的缩写，是一种代码框架设计理念。但我要讲的ECS并不是正经的ECS实现方案，而是歪理邪说，如果您已经感到不适，应立即移步。

正经ECS中，Component是pure data，System是pure function（无状态）。在我们的设计中（暂时maybe -\_\_\_\_- ）没有care这些准则，我们的目的是可以像**填配置一样订制代码。**从实现效果上看，更像Unity3d中的Component实现方案。希望了解正经ECS设计方案的，请移步文末的参考文献区，那里有一些链接也许对你有用。

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

创建和删除组件分别由Entity类中的一对名为AddComponent\(\)/RemoveComponent\(\)的方法负责。代码实现大概如下：

```csharp
public class Entity
{
    public IComponent AddComponent(Type type)
    {
        if (null != type)
        {
            var component = Activator.CreateInstance(type) as IComponent;
            if (null != component)
            {
                var hasEntity = component as IHaveEntity;
                if (null != hasEntity)
                {
                    hasEntity.SetEntity(this);
                }

                var initializable = component as IInitalizable;
                if (null != initializable)
                {
                    initializable.Initalize();
                }

                _components.Add(type, component);
                return component;
            }
        }

        return null;
    }

    private readonly Hashtable _components = new Hashtable();
}

public class Component : IInitalizable, IDisposable, IIsDisposed, IHaveEntity
{
    ...
}
```

框架实现了一个Component基类和以IComponent为代表的系列接口。

多数比较复杂的组件类应该通过继承Component基类实现。它包含一个对宿主Entity的引用，并默认实现了IInitalizable（组件创建回调）、IDisposable（组件释放回调）和IIsDisposed（查询组件是否已经被释放）接口，这些接口的方法对应着组件对象的完整生命周期。

在有些情况下，我们可能不希望或无法使用Component基类。一种可能的情况是，我们有时需要非常轻量级的组件，它可能只需要包含一个int值，此时基于创建一个Component的子类会显得过于重度。另一种可能的情况是，目标组件类已经预定了一个基类了，但在C\#中我们无法使用多重继承。

使用IComponent系列接口可以创建**与Component子类等价能力**的组件对象。从前面的示例代码可以看到，AddComponent\(\)方法完全基于接口编程，它可以创建任何实现了IComponent接口（特别注意到**IComponent是一个空接口**）的类对象。如果需要其它IInitalizable, IDisposable等能力的话，只要实现对应的接口就可以。

完整的代码地址请参考：[https://github.com/lixianmin/cloud/tree/master/projects/ecs](https://github.com/lixianmin/cloud/tree/master/projects/ecs)

---

#### 0x04. 设计权衡

1. 为什么没有遵循Component是pure data，System是pure function的ECS规范？

> 框架并未否定正经的ECS实现方案，如前所述，只要实现了IComponent空接口的类都可以作为组件被Entity使用---这已经是理论上能做到的最小的约束了。我们完全可以使用纯数据的Component类，同时在设计System的时候拒绝包含任何状态。  
> 只所以没有强制要求Component是pure data，是因为很多组件的专用性太强，它就只能是为某些Entity服务的，如果再把行为拆出来，目前感觉有些过渡设计了。好吧，其实作者受OO思想影响多年，暂时无法脱身也是一个~~最~~重要的原因。

1. Component是否应该有一个id标识符？

> 初版设计时Component基类的确有一个全局唯一的id标识符，后来移除了。这个全局唯一id通过一个static的int变量自加得来，通过它我们可以跟踪到所有处于alive状态的组件对象。一开始我觉得这会很有用，但经过几个星期的迭代，我发现实际上应用不是很广泛，就移除了。  
> 唯一的一次应用是将某个组件id传递给lua脚本作为查询id使用，后来被我使用宿主Entity的id代替了。这个解决方案可能具备一定程度上的普适性，因为目前框架中每个Entity上相同类型的组件同时只能有一个，这样宿主id+组件类型就可以唯一确定是哪一个组件了。

1. 为什么每个Entity上同种类型的Component只能有一个？

> 是的，Unity3d在同一个gameObject上就可以同时有多个相同类型的Component。正是因为基于这个考虑，最初设计的时候，每个Entity上是可以同时有多个同种类型的Component的。这样定位一个组件需要两个数据：type+id。这给接下来的一系列组件相关的操作都带来了设计复杂度，包括存储、查询、排序、遍历等等。经过几周的代码迭代，我们发现似乎没有哪个需求是需要在同一个Entity上同时包含一个以上的相同类型的组件的。另外，调研了一下业界道友的一个实现（包括Entitas），发现他们也没有支持这种需求，于是后来在重构代码的时候把这个特性移除了。这大大简化了很多方法的设计，并减少了代码量，简直是普天同庆。

1. 为什么没有使用AddComponen&lt;T&gt;\(\)这种泛型接口，而是使用了AddComponent\(Type type\)？

> 在定义了AddComponent\(Type type\)后，泛型版本的方法可以使用扩展方法实现，即：AddComponent\(typeof\(T\)\) as T;

1. Activator.CreateInstance\(type\)比起泛型版的new T\(\)会不会慢？

> 我反编译了Mono的实现，泛型版的new T\(\)最后就是使用了Activator.CreateInstance\(typeof\(T\)\);实现的，dotnet的实现手法没有查过，不清楚。

1. 根据《守望先锋》的经验，它们最终有大约40%的Component是Singleton，在框架中如何支持？另外，某些Component可能需要频繁的创建和销毁，是否应该考虑加入Pool的方案？

> 目前框架中的对象都是直接CreateInstance\(\)创建出来的，对于Singleton和Pool还没有想好解决方案。，以实现Singleton为例，有几种参考方案：  
> 方案一：使用Attribute属性或ISingleton接口来标记一个组件是一个Singleton类，并在AddComponent\(\)的时候取得这些信息。Attribute属性可能稍好一些，因为它可以带一些控制参数，比如控制Pool的大小。该方案的问题是：每次调用AddComponent\(\)的时候都需要查询这些标记信息，这是一笔额外开销，特别是对原来不需要这些信息的普通组件来说。即使，我们使用一张Hashtable缓存这些信息，也会多一次Hashtable的查询，这个开销是否能被接受还需要斟酌。  
> 方案二：加一个新的AddSingletonComponent\(\)方法，这样可以避免方案一的性能问题，对原先已经在运行的代码也没有任何影响。该方案的问题是：组件是否是Singleton应该由设计组件的人决定，而不是由使用组件的人决定。  
> 方案三：扩展AddComponent\(\)方法，加入一个flags参数，用这个参数区分组件对象是否为Singleton。这个方案的优点跟方案二相同，并给未来扩展flags留下了余地。该方案的问题跟方案二是一样的：组件是否是Singleton应该由设计组件的人决定，而不是由使用组件的人决定。

1. 无状态System应该如何实现？

> 无状态是为了无副作用，跟函数式编程的理念相同，有几个跟此相关的概念可以参考：静态类，工具类，纯函数，扩展方法。


---

#### 0x05. Summary 

有任何关的疑问或建议，请留言。

---

#### 0x06. References

1. [游戏开发中的ECS 架构概述](https://zhuanlan.zhihu.com/p/30538626)

2. [一个无框架的ECS实现（Entity-Component-System](https://zhuanlan.zhihu.com/p/32787878)

3. [浅谈《守望先锋》中的 ECS 构架（云风）](https://blog.codingnow.com/2017/06/overwatch_ecs.html)

4. [Entitas-CSharp](https://github.com/sschmid/Entitas-CSharp)

5. [游戏引擎架构](https://www.amazon.cn/dp/B00HY8SIX2/ref=sr_1_1?s=books&ie=UTF8&qid=1522924143&sr=1-1)

6. [Update Method](https://github.com/lixianmin/design-pattern/blob/master/update-method.md)

7. [Implementing Component-Entity-Systems](https://www.gamedev.net/articles/programming/general-and-gameplay-programming/implementing-component-entity-systems-r3382/)



