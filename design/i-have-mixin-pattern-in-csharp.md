
---

### I have mixin模式（C\#）

文：普通熊猫

源：[https://github.com/lixianmin/writer/blob/master/design/i-have-mixin-pattern-in-csharp.md](https://github.com/lixianmin/writer/blob/master/design/i-have-mixin-pattern-in-csharp.md)

---

C\#使用扩展方法实现mixin，这点毋庸置疑，但对如何在项目中推广mixin这件事，一直一来并没有找到合适的切入点。昨天突发奇想，终于找到一种还算合适的方案，我暂且称之为I have mixin模式。也许别人知道但我不知道，也许我接下来要讲的方案在业界已有定论，只是我孤陋寡闻没听过，也许有更好的方案-- 无论哪种情况，请您告诉我。

该模式涉及到四个概念，分别是：宿主类，属性类，接口类和扩展类。在常规设计方案中，我们只会使用前两种概念（宿主类和属性类）。

举个栗子，我们现在已经有一个Wallet类负责处理money相关的管理，如果要新建一个Person类需要进行money相关的操作的话，它可能需要组合一个Wallet属性对象。常规的设计方案可能是如下样子，其中Person是宿主类，Wallet是属性类：

```csharp
    public class Person
    {
        public void AddMoney(int count)
        {
            var balance = _wallet.GetMoney() + count;
            _wallet.SetMoney(balance);
        }

        public void SpendMoney(int count)
        {
            var balance = _wallet.GetMoney() - count;
            _wallet.SetMoney(balance);
        }

        public int GetBalance()
        {
            return _wallet.GetMoney();
        }

        private readonly Wallet _wallet = new Wallet();
    }
```

而使用I have mixin模式的写法是如下样子：

```csharp
    public interface IHaveWallet
    {
        Wallet GetWallet();
    }

    public class Person : IHaveWallet
    {
        public Wallet GetWallet ()
        {
        return _wallet;
        }

        private readonly Wallet _wallet = new Wallet();
    }

    public static class IHaveWalletEx
    {
        public static void AddMoneyEx(this IHaveWallet self, int count)
        {
            if (null != self)
            {
                var wallet = self.GetWallet();
                if (null != wallet)
                {
                    var total = wallet.GetMoney();
                    total += count;
                    wallet.SetMoney(total);
                }
            }
        }

        public static bool SpendMoneyEx(this IHaveWallet self, int count)
        {
            if (null != self && count > 0)
            {
                var wallet = self.GetWallet();
                if (null != wallet)
                {
                    var total = wallet.GetMoney();
                    if (total > count)
                    {
                        total -= count;
                        wallet.SetMoney(total);
                        return true;
                    }
                }
            }

            return false;
        }

        public static int GetBalanceEx(this IHaveWallet self)
        {
            if (null != self)
            {
                var wallet = self.GetWallet();
                if (null != wallet)
                {
                    var total = wallet.GetMoney();
                    return total;
                }
            }

            return 0;
        }
    }

    public class Game
    {
        // 测试代码  
        public void TestMixin ()
        {
            var person = new Person();
            person.AddMoneyEx(300);
            person.SpendMoneyEx(20);
            var balance = person.GetBalanceEx();
        }
    }
```

使用I have mixin模式的写法看起来要复杂得多，但是注意，宿主类Person中只编写了一个接口方法GetWallet\(\)，却自动拥有了IHaveWalletEx扩展类中的所有方法。因此只要Wallet类足够通用，这个方案就是很便宜的。比如，拥有Wallet属性对象的可能不止是Person类，也许Mobile类也需要。

再举个例子：Unity3d中有一个广泛应用的类名为Transform，它实现了很多3d坐标相关的操作。我们可以定义一个接口类IHaveTransform和一个扩展类IHaveTransformEx，从此以后，只要实现了IHaveTransform接口的类都将因此收益。

你可能已经注意到我在对扩展方法命名时都使用了以Ex结尾方式，目的是为了让程序员明确的知道自己调用是扩展方法而不是成员方法，这在很多情况下是有益的。比如对IHaveTransformEx中的SetPositionEx\(\)方法，宿主类可能需要定义一个同名方法SetPosition\(\)（注意没有Ex）用于处理一些特殊情况。如果扩展类中的方法也命名为SetPosition\(\)的话，两个方法的区分度就很低了，程序员使用时很容易混淆。

想想我们自己的项目中定义过多少名为GetXXX\(\)的方法，如果有一小半能用上I have mixin模式的话，生活会变得多么美好~

后来我想了一下，System.Linq.Enumerable就是这样实现mixin的，我一直知道这件事情，但竟然没有重视起来。

参考测试代码地址：[https://github.com/lixianmin/cloud/tree/master/projects/IHaveMixin](https://github.com/lixianmin/cloud/tree/master/projects/IHaveMixin)

