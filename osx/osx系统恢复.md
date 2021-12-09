

---

#### 01 搭建基础环境

1. bin和gitdir目录太大， 直接使用AirDrop或U盘copy
3. 在~/bin/osx/services下面，有激活terminal等相关的apple script
    * 打开Automator -> Utilities -> Run Apple Script
    * 将activate-terminal.applescript的内容粘贴到里面，选择Workflow receives为no input，选择in any application
    * 设置 -> keyboard -> shortcuts -> Services，设置快捷键
4. 打开App Store，安装Xcode，否则像git这种都用不了。 安装xcode会很慢（3个小时？）
4. 安装一系列命令行工具
``` shell
# 安装homebrew，这里使用国内源
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"

brew install htop
brew install macvim
brew install git  # 默认安装的git版本太低了, 可能不支持git ls-remote -q
brew install tig
brew install wget
brew install graphviz

# 以下是casks
brew install android-platform-tools
brew install sequel-pro
brew install shadowsocksx-ng

```

----
#### 02 software



1. typora网站可能打不开，直接copy
2. [flux](https://justgetflux.com/)
3. youdao note
4. chrome
5. app cleaner

