

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

1. Appearance & Behaviour → Appearance → Use custom font，调整侧边栏字体
1. Go→Go Modules→Enable Go modules integration→解决引用本项目module时，如果未使用全路径url地址，会提示找不到的问题
2. Editor → General → Auto Import，设置自动导入
3. Tools → File Watchers，点击+，加入go fmt，设置文件保存时自动格式化
4. 代码运行有可能需要设置working directory，位置在 Run → Edit Configrations → Go Build → Working Directory
5. 解决`go list -m -json all`超时的问题：设置 Go → Go Mudules(vgo) → Vendoring mode
5. vendor目录可以下断点: 编辑Run/Debug Configuration → Go Build → go tool arguments中加入 `-mod vendor` 🔥
5. 解决把Tools.ts误导入成Tools.js的问题: Editor→Code Style → Javascript → Use File Extension→Never



-------------
#### 03 [WSL调试](https://zhuanlan.zhihu.com/p/378437571)

1. goland安装在windows上，使用的是windows上安装的go的sdk
2. Settings→Build, Execution, Deployment→Run Targets→新建一个WSL的Target：
   1. Go Executable→ /usr/local/go/bin
   2. GOPATH→ /home/xmli/go
3. 正常编辑Go Build的配置→Run On选WSL
   1. 项目本身，需要安装在windows上，而不是在wsl中，否则在Go Build这个配置页面看不到Run on:这个下拉菜单框



---

#### 04 重置VM Options相关的错误

```shell
# 把二进制文件拖到命令行里执行，会打印VM Options相关的信息
/Applications/GoLand.app/Contents/MacOS/goland

# 进入这里，把当前版本相关的内容删除
cd ~/Library/Application Support/JetBrains

# 注意最新安装参数可能需要手动输入
```

