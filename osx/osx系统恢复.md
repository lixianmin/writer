

---

1. git clone https://github.com/lixianmin/bin.git ，建立~/bin目录

2. 建立~/gitdir，在其中
``` shell
git clone https://github.com/lixianmin/cloud.git
git clone https://github.com/lixianmin/writer.git
```

3. 在~/bin/osx/services下面，有激活terminal等相关的apple script
    * 打开Automator -> Utilities -> Run Apple Script
    * 将activate-terminal.applescript的内容粘贴到里面，选择Workflow receives为no input，选择in any application
    * 设置 -> keyboard -> shortcuts -> Services，设置快捷键

4. 安装一系列命令行工具
``` shell
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

git clone git://github.com/altercation/solarized.git

```

5. 通过输入lixianmin@live.cn 来恢复蓝灯 lantern

----
#### software

1. [flux](https://justgetflux.com/)
2. lantern
3. youdao note
4. chrome
5. app cleaner
6. typora