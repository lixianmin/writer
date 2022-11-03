

| Command 命令 | Normal   常规模式 | Visual 可视化模式 | Operator Pending 运算符模式 | Insert Only 插入模式 | Command Line 命令行模式 |
| ------------ | ----------------- | ----------------- | --------------------------- | -------------------- | ----------------------- |
| `:map`       | y                 | y                 | y                           |                      |                         |
| `:nmap`      | y                 |                   |                             |                      |                         |
| `:vmap`      |                   | y                 |                             |                      |                         |
| `:omap`      |                   |                   | y                           |                      |                         |
| `:map!`      |                   |                   |                             | y                    | y                       |
| `:imap`      |                   |                   |                             | y                    |                         |
| `:cmap`      |                   |                   |                             |                      | y                       |



```shell
" <F5> 一键编译和运行golang
map <F5> :call CompileRunGolang() <CR>
func! CompileRunGolang()
exec "w"
exec "!go build %"
exec "!%<"
endfunc
```



---

#### References

1. [VIM键盘映射](http://www.pythonclub.org/linux/vim/map)
2. [Vim一键编译运行](https://blog.csdn.net/zijin0802034/article/details/77709465)
3. [vim 一键编译运行c++,c,java,python, shell](http://www.edbiji.com/doccenter/showdoc/24/nav/284.html)