

---
#### 01 快捷键


| Hotkeys           | Description            |
| ----------------- | ---------------------- |
| F1                | 显示文档               |
| alt + F7          | find usages            |
| alt + F12         | terminal               |
| alt + cmd + b     | 转到实现               |
| alt + cmd + L     | 格式化                 |
| cmd + [           | 回退到上一次编辑的位置 |
| cmd + 1           | Explorer               |
| cmd + 6           | 打开todo list          |
| cmd + 7           | Structure              |
| cmd + o           | open class             |
| cmd + shift + F12 | 切换主编辑区域全屏显示 |
| cmd + 鼠标单击    | 转到reference          |
| shift + F6        | rename                 |
| double shift      | search anywhere        |

----
#### 02 设置

1. Appearance & Behaviour -> Appearance -> Use custom font，调整侧边栏字体
2. Editor -> General -> Auto Import，设置自动导入
3. Tools -> File Watchers，点击+，加入go fmt，设置文件保存时自动格式化
4. 代码运行有可能需要设置working directory，位置在 Run -> Edit Configrations -> Go Build -> Working Directory
5. 解决`go list -m -json all`超时的问题：设置 Go -> Go Mudules(vgo) -> Vendoring mode
5. vendor目录可以下断点: 在编译选项的go tool arguments中加入 `-mod vendor` 🔥



-------------
#### 03 重置VM Options相关的错误

```shell
# 把二进制文件拖到命令行里执行，会打印VM Options相关的信息
/Applications/GoLand.app/Contents/MacOS/goland

# 进入这里，把当前版本相关的内容删除
cd ~/Library/Application Support/JetBrains

# 注意最新安装参数可能需要手动输入
```

