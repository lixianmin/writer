

----
#### 成员变量

1. 基类Parent, 子类Child， 实例变量instance分别是三张不同的table

2. Parent.release()方法与Child.release()方法是两个不同的方法，因为它们属于不同的table

3. 所有在类中定义的变量都是**类变量，它们被所有的类成员共享**：
    1. 类的function被共享似乎是理所当然的事情，但其实想一想类成员变量也是一样的
    2. 它们都满足一个特性：**readonly**，也就是说当他们是只读的时候，大家（所有类实例）用的是同一份
    3. 如果Child类中定义了一个成员变量_isReleased，如果instance不对它赋值，而是只读取的时候，那么它读取的是Child._isReleased，此时instance表中没有_isReleased变量；如果设置instance._isReleased= true，此时设置的就是instance表中的变量，而Child._isReleased仍然是false

4. 在面向对象编程时，self往往指的是instance，而不是Child类

5. 如果一个table计划作为基类使用，那么它的**成员变量（含方法）必须尽可能的控制数量**，以防止被子类override掉，这个在lua中是没有类型检查的 


```lua
local Parent = { }
Parent.__index = Parent

function Parent.new (klass)
    local instance = setmetatable({}, klass)
    return instance
end

function Parent.subclass (superclass, prototype)
    local klass = setmetatable(prototype or {}, superclass)
    klass.__index = klass
    return klass
end

local Child = Parent : subclass
{
    --- 这里的_isReleased是类成员变量，如果没设置过，则是只有在类里有一份，
    --- 如果设置了，则是instance成员变量，与原始的类成员变量是相互独立的
    _isReleased = false,
}

function Child.release (self)
    self._isReleased = true
end

local instance = Child:new()

print ('Child._isReleased = '..tostring(Child._isReleased)..', instance._isReleased='..tostring(instance._isReleased))
instance:release()
print ('Child._isReleased = '..tostring(Child._isReleased)..', instance._isReleased='..tostring(instance._isReleased))

--- 输出为:
---Child._isReleased = false, instance._isReleased=false
---Child._isReleased = false, instance._isReleased=true


```