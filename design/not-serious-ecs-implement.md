
---

### 非正经ECS实现方案

文：普通熊猫

源：[https://github.com/lixianmin/writer/blob/master/design/not-serious-ecs-implement.md](https://github.com/lixianmin/writer/blob/master/design/not-serious-ecs-implement.md)

---

#### 0x00. 引言

ECS是Entity-Component-System（实体-组件-系统） 的缩写，是一种框架设计模式，多用于游戏开发。但我下面要讲的ECS并不是正经的ECS实现方案，只是借了ECS的壳。

正经ECS可简述为："Entities as ID's", "Components as raw Data", and "Code stored in Systems, not in Components or Entities"。意译为：Entity就是一个ID，Component是纯数据，System是纯逻辑。在我的设计中（暂时 -\_\_\_\_- ）没有恪守这些准则，我的目的是可以像**填配置一样订制代码。**从实现效果上看，更像Unity3d中的Component实现方案。希望研究正经ECS设计方案的同学，请移步文末的参考文献区，那里有一些链接也许对你有用。

方案基于Unity3d引擎，使用C\#编码，示例语法也都使用C\#。框架代码以及下文中我使用Part一词指代Component，有两个原因：一是Component这个单词已经被Unity3d占了，二是我觉得Component这个单词太长了。

---

#### 0x01. Entity不需要知道正在使用哪些Part

就目前我了解到的一些实现方案中，Entity都明确知道自己会使用哪些组件。我认为这跟OO中的组合模式（Composite Pattern）区别不大。我可以接受Part强引用Entity，但不能接受Entity强引用Part。之所以这样，有两个比较重要的原因：

1. **越简单越易用**：理想的情况下，Entity在任意时刻需要任意Part时可随时添加，不需要某个Part时则随时删除。如果Entity代码中硬编码了Part成员变量，那么就需要同样手工编码所有其它相关的操作，比如：命名，Initialize\(\), Dispose\(\)等，删除或重命名相关代码时也需要手动调整。这些属于常见操作，在编写代码时经常遇到，我认为手工编码的话过于复杂了。参考文献中有一个Unity3d的插件叫Entitas，它通过自动生成代码的方式简化了这个过程。

2. **Entity在编译期与Part解耦**：我们项目有一个跟《守望先锋》类似的需求。我们希望部分Logic层的Client代码（比如MovePart）可以直接在Server上运行，此时需要完全剥离出View层的代码\(比如RenderPart\)。这要求Logic层代码不能强引用任何View层代码的信息，否则会编译不过。同时因为所有相关代码的生命周期都是一样的，因此动态增删Part是一个favorable的设计方案。具体就是在Client端Entity会动态挂接所有相关Part，而在Server端Entity只需要挂接Logic层的Part。

综合以上原因，相对理想的理想的方案就是类Unity3d中的组件使用方式：

```csharp
    var entity = new GameObjectEntity();
    entity.AddPart(typeof(MovePart));   // Logic层
    entity.AddPart(typeof(RenderPart)); // View层
    ...
    entity.RemovePart(typeof(RenderPart));
```

具体实现时，Entity中的Part全部存储在一张中心Hashtable（Type =&gt; Part）中，但也因此**付出了代价：Speed & Memory**，详见第0x04小节：设计缺陷。

---

#### 0x02. 缓存友好

从实现效果上，ECS设计倾向于以属性为中心（可参考《游戏引擎架构P655》）。Entity中只需要存储实际用到的Part（即：我们不会有一些Entity，内含未使用的Part成员），这对于有效使用内存是有益的。不过，考虑到我们使用哈希表存储Part成员，一正一负，实际内存占用不好说是升了还是降了。

Update Method是游戏设计中的一种常规设计手法，具体方法可能命名为Update\(\)或Tick\(\)，其含义在此不作区分，框架中使用Tick\(\)。在以对象为中心的设计中，很多宿主对象与其属性对象都需要写一个Tick\(\)方法，用于在每一帧更新相关数据。实际上，因为Tick\(\)调用通常是自上而下逐级进行的，因此只要有一个属性对象需要Tick\(\)方法，都会强迫其所在的宿主对象及每一个上游对象都拥有Tick\(\)方法。这样，以游戏代码中的初始Tick\(\)方法为根节点，自上而下，由外到内，对所有对象Tick\(\)方法的调用可以看成是一个树形结构，我们可称之为Tick树。以对象为中心的设计模型有一些缺点，其中之一便是Tick树中相邻叶节点的类型通常是不一样的，因此在遍历整个Tick树的过程中，缓存命中失败的概率（cache miss rate）比较大。

以属性中心设计则可能更加缓存友好。在我们的ECS实现方案中，我们设计了一个名为PartTickSystem的类，收集所有包含Tick\(\)的Part，将它们**存储在同一个array中并按type排序**。这样，相同type的Part在内存中是连续存储的，数据布局符合**数组之结构\(struct of array, SoA\)**的要求。在遍历调用所有Part的Tick\(\)方法时，能够减少或消除缓命中失败。

具体到PartTickSystem类的实现细节，由于我们使用array存储Part对象，在添加或删除Part时，不需要立即调整array中的内容，否则会导致频繁移动array中的数据，可能引起不必要的CPU开销。添加Part时，可以先将新的Part对象append到数组尾部，在真正遍历array中的Part之前，将其按type排序（因此在最坏的情况下PartTickSystem.Tick\(\)的时间复杂度为O\(NlogN\)）。删除Part时，也不需要立即从array中移除，只需要在遍历结束后的某个时刻调用一个RemoveAll\(\)方法统一移除即可（类似于List&lt;T&gt;.RemoveAll\(\)，只移动一次内存）。

不同type的Part之间对Tick\(\)方法的调用先后顺序可能有要求，Unity3d中专门区分了Update\(\)与LateUpdate\(\)应对这件事情。我们通过以下方式可以控制的更加细致：给每一种type提供一个typeIndex值，并将array中的Part按typeIndex的顺序排序（在C\#中，Array.Sort\(keys, items\)方法可以帮忙）。很多情况下，不同Part之间的Tick\(\)调用顺序并无特殊要求，因此只需要在第一次访问这种Part的type时候自动生成一个typeIndex即可。对于需要严格控制Tick\(\)调用顺序的Part，则需要在系统初始化时为它们设置指定的typeIndex值。

在初版设计中，我们将typeIndex作为property放到Part类中，但经过几周的迭代发现，该变量只在排序Tick\(\)的调用顺序时有用，因此将其转移到了PartTickSystem类中，作为Array.Sort\(keys, items\)的keys参数。细心的读者已经注意到，这种设计也是违反ECS中System不能含有状态的准则的。

---

#### 0x03. Part基类与IPart接口

创建和删除组件分别由Entity类中一对名为AddPart\(\)/RemovePart\(\)的方法负责。代码如下：

```csharp
public class Entity
{
    public IPart AddPart(Type type)
    {
        if (null != type)
        {
            var part = Activator.CreateInstance(type) as IPart;
            if (null != part)
            {
                var initPart = part as IInitPart;
                if (null != initPart)
                {
                    initPart.InitPart(this);
                }

                _parts.Add(type, part);
                if (null != OnPartCreated)
                {
                    OnPartCreated(part);
                }
                return part;
            }
        }

        return null;
    }

    public bool RemovePart(Type type)
    {
        if (null != type)
        {
            var part = _parts[type];
            if (null != part)
            {
                var disposable = part as IDisposable;
                if (null != disposable)
                {
                    disposable.Dispose();
                }

                _parts.Remove(type);
                return true;
            }
        }

        return false;
    }

    public static event Action<IPart> OnPartCreated;
    private readonly Hashtable _parts = new Hashtable();
}

public interface IPart
{

}
public class Part : IPart, IInitPart, IDisposable, IIsDisposed
{
...
}
```

框架实现了一个Part基类和IPart等一系列接口。

多数逻辑比较复杂的组件类应该通过继承Part基类实现。它默认实现了IPart（组件标志）、IInitPart（组件创建回调）、IDisposable（组件释放回调）和IIsDisposed（查询组件是否已经被释放）接口，这些接口背后的方法对应着组件对象的完整生命周期。

在有些情况下，我们可能不希望或无法使用Part基类。一种情况是，我们有时需要非常轻量级的组件，它可能只需要包含一个int值，此时创建一个Part的子类会显得过于重度。另一种情况是，目标组件类已经拥有一个基类了，但在C\#中我们无法使用多重继承。

使用IPart系列接口可以创建**与Part子类等价能力**的组件对象。从前面的示例代码可以看到，AddPart\(\)方法完全基于接口编程，它可以创建任何实现了IPart接口的类对象（特别注意到**IPart是一个空接口**）。如果需要其它IInitPart, IDisposable等能力的话，只要实现对应的接口就可以。

完整的代码地址请参考：[https://github.com/lixianmin/cloud/tree/master/projects/ecs](https://github.com/lixianmin/cloud/tree/master/projects/ecs)

---

#### 0x04. 设计缺陷

为了降低Entity与Part之间的耦合度，实现机制上我们使用Hashtable存储Part组件，也因此需要注意潜在的**速度与内存**开销。

首先是速度。因为对Part的Add/Remove/Get全部通过Hashtable进行，因此速度比直接访问类成员变量慢很多。如果把class想像成一个存储类成员变量的容器，那么数组是速度最快的容器实现方式。在测试中，我假设获取类成员变量的速度与从数组中按下标获取数组元素的速度相仿 --- 并没有证据，但我认为这是一个相对合理的假设。另外，测试时Hashtable与Dictionary都使用了默认参数，没有调整loadFactor。测试情况如下：

测试代码：[MBHashtableSpeedTest](https://github.com/lixianmin/cloud/tree/master/projects/SortedTableTests)
测试目标：测试各类容器与数组相比获取元素的耗时比。
测试环境：MacBook Pro(13-inch, 2016) + macOS 10.12.5 + Unity2017.1.1f1 + C#
测试方案：容器大小50，按type获取数据10000次的总时间相除

| 容器 | 耗时比 |
|--|--|
| Array | 1: 1 |
| Hashtable（Type => Part）| 13 : 1 |
| Dictionary<Type,  Part> | 25 : 1 |

从表里可以看到，从Hashtable和数组中获取相同元素的耗时比大概为13:1，因此可以大致认为GetPart()的耗时是获取普通类成员变量的13倍。这种速度落差对偶发的组件访问可能影响不大，但需要警惕在Tick()/Update()中反复调用GetPart()的情况。

其次是内存。同样的数据，存储在Hashtable中比存储在数组中要多占用更多的内存。测试内存的方案与测试速度的方案类似，仍然使用Array, Hashtable, Dictionary三种容器对比，容器仍然使用默认参数，未调整loadFactor。测试情况如下：

测试代码：[MBHashtableMemoryTest](https://github.com/lixianmin/cloud/tree/master/projects/SortedTableTests)
测试目标：测试各类容器的内存占用情况。
测试环境：MacBook Pro(13-inch, 2016) + macOS 10.12.5 + Unity2017.1.1f1 + C#
测试方案：容器大小50，每种类型生成10000份，取平均容器的大小

| 容器 | 平均大小 |
|--|--|
| Array | 1.9KB |
| Hashtable（Type => Part）| 2.6KB |
| Dictionary<Type,  Part> | 4.4KB |


---

#### 0x05. 设计权衡

1. 为什么没有使用Component是纯数据，System是纯逻辑的实现方案？

   框架并未否定正经的ECS实现方案。如前所述，只要实现了IPart空接口的类都可以作为组件被Entity使用---这对组件类几乎没有增加额外数据，可能是理论上能做到的最小的约束了。我们完全可以使用纯数据的Part和无状态的System。

   只所以没有强制要求Part是raw data，是因为很多组件的专用性太强，它们就只能是为某些Entity服务，如果再把行为拆出来，感觉有些设计过度了。

   在正经的System实现中，Entity或Part通常集中存储在某个地方。由于每个System只处理某些特定类型的Entity/Part，因此需要在每次访问前先按预定义的条件过滤一遍。在我们的应用中，Entity与Part的创建频率不是特别频繁，我认为使用每次过滤的方式是一种CPU浪费，更倾向于使用在System中做缓存的方式，于是System就包含了状态。

   好吧，其实作者受OO思想影响多年，暂时无法转变思想也是一个~~次~~重要的原因。

2. Entity是否可以同时是一个IPart  
    可以，完全可以，我已经在项目中这么用了。

3. Part是否可以是struct？

   可以但不能这么使用。理论上只要实现了IPart空接口的struct就可以作为组件被Entity使用，但因为我们使用了Hashtable存储Part对象，如果使用struct的话，会导致装箱拆箱问题，所以不建议使用。

4. 为什么要IInitPart接口初始化组件对象，直接使用构造方法不更直接嘛？

   在初始化时我们可能需要Entity对象，无参默认构造方法里无法找到Entity对象。

5. Part是否应该有一个id标识符？

   初版设计时Part的确有一个全局唯一的id标识符，后来移除了。这个全局唯一id是通过一个static的int变量自加得来，通过它我们可以跟踪到所有处于alive状态的组件对象。一开始我觉得这会很有用，但经过几个星期的迭代，我发现实际上用途不是很广泛，就移除了。

   唯一的一次应用是将某个组件id传递给lua脚本作为查询id使用，后来被我使用宿主Entity的id替代了。这个替代方案可能具备一定程度上的普适性，因为目前框架中每个Entity上相同类型的组件同时只能有一个，这样“宿主id+组件类型”就可以唯一确定是哪一个组件了。

6. 为什么单个Entity上同种类型的Part只支持一个？

   的确，Unity3d在同一个gameObject上可以同时拥有多个相同类型的Component。正是因为参考了Unity3d，最初设计的时候，每个Entity上是可以同时有多个同种类型的Part的。这样定位一个组件就需要两个数据：type+id。这个方案给接下来的一系列组件相关的操作都带来了一些设计复杂度，包括存储、查询、排序、遍历等等。经过几周的代码迭代，我们发现似乎没有哪个需求是需要在同一个Entity上同时包含一个以上的相同类型的组件的。另外，调研了一下业界道友的一些实现方案（包括Entitas），发现他们也没有支持这个特性，这说明在实践中至少可以绕过这个特性，于是后来在重构代码的时候把这个特性移除了。这大大简化了很多方法的设计，并减少了代码量，简直是普天同庆。

7. 为什么没有使用AddPart&lt;T&gt;\(\)这种泛型接口，而是使用了AddPart\(Type type\)？

   在定义了AddPart\(Type type\)后，泛型版本的方法可以使用扩展方法实现，即：AddPart\(typeof\(T\)\) as T;

8. Activator.CreateInstance\(type\)比起泛型版的new T\(\)会不会慢？

   我反编译了Mono的实现，泛型版的new T\(\)最后就是使用了Activator.CreateInstance\(typeof\(T\)\);实现的，dotnet的实现手法没有查过，不清楚。

9. 根据《守望先锋》的经验，它们最终有大约40%的组件是Singleton，在框架中如何支持？另外，某些组件可能需要频繁的创建和销毁，是否应该考虑加入Pool的方案？

   目前框架中的Part对象都是直接new出来的，对于Singleton和Pool还没有想好解决方案。以实现Singleton为例，有几种参考方案：

   方案一：使用Attribute属性或ISingleton接口来标记Part是一个Singleton类，并在AddPart\(\)的时候获取这些信息。Attribute属性可能更友好一些，因为它可以带一些控制参数，比如用于控制Pool的大小。该方案的问题是：每次调用AddPart\(\)的时候都需要查询这些标记信息，这是一笔额外开销，特别是对于那些原本不需要这些信息的普通组件来说。即使我们使用一张Hashtable缓存这些信息，也会多一次Hashtable的查询，这个开销是否能被接受还需要斟酌。

   方案二：加一个新的AddSingletonPart\(\)方法，这样可以避免方案一的性能问题，对原先已经在运行的代码也没有任何影响。该方案的问题是：组件是否是Singleton应该由设计组件的人决定，而不是由使用它的人决定。

   方案三：扩展AddPart\(\)方法，加入一个flags参数，用这个参数区分组件对象是否为Singleton。这个方案的优点跟方案二相同，并且给未来扩展flags留下了余地。该方案的问题跟方案二是一样的：组件是否是Singleton应该由设计组件的人决定，而不是由使用它的人决定。

10. 无状态System应该如何实现？

    无状态是为了无副作用，跟函数式编程的理念相同，有几个跟此相关的概念可以参考：静态类，工具类，纯函数，扩展方法。

---

#### 0x06. 收尾

有任何关的疑问或建议，欢迎留言探讨。

---

#### 0x07. 参考文献

1. [wiki: Entity–component–system](https://en.wikipedia.org/wiki/Entity–component–system)

2. [游戏开发中的ECS 架构概述](https://zhuanlan.zhihu.com/p/30538626)

3. [一个无框架的ECS实现（Entity-Component-System](https://zhuanlan.zhihu.com/p/32787878)

4. [浅谈《守望先锋》中的 ECS 构架（云风）](https://blog.codingnow.com/2017/06/overwatch_ecs.html)

5. [Entitas-CSharp](https://github.com/sschmid/Entitas-CSharp)

6. [游戏引擎架构](https://www.amazon.cn/dp/B00HY8SIX2/ref=sr_1_1?s=books&ie=UTF8&qid=1522924143&sr=1-1)

7. [Update Method](https://github.com/lixianmin/design-pattern/blob/master/update-method.md)

8. [Implementing Component-Entity-Systems](https://www.gamedev.net/articles/programming/general-and-gameplay-programming/implementing-component-entity-systems-r3382/)

9. [Game Programming Patterns: Component](http://gameprogrammingpatterns.com/component.html)

10. [http://entity-systems-wiki.t-machine.org](http://entity-systems-wiki.t-machine.org)

11. [Entity Systems are the future of MMOG development – Part 2](http://t-machine.org/index.php/2007/11/11/entity-systems-are-the-future-of-mmog-development-part-2/)



