

将所有的GetComponentEx<T>(); 替换为GetComponentEx(typeof(T)) as T;

1. Find为        GetComponentEx\s*<(\w+)>\s*\(\)
2. Replace串为   GetComponentEx(typeof($1)) as $1