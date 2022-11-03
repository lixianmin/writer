

---
#### 0x01 基本常识

```lua
    for i= 10, 1, -1 do
        print(i)
    end
    
    for i, v in ipairs(a) do
        print(v)
    end
    
    for k, v in pairs(a) do
        print(v)
    end
    
    -- 这个写法与pairs等价
    for k, v in next, a do
        print(v)
    end
    
    for i=1, #a do
        print(a[i])
    end
```

---
#### 0x02 内置C函数

push            | 测试          | 转换
---             |---            |-
lua_pushnumber  | lua_isnumber  | lua_tonumber
lua_pushinteger | lua_isnumber  | lua_tointeger
lua_pushstring  | lua_isstring  | lua_tostring