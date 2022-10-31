

---

#### 01 搭建基础环境

1. bin和gitdir目录太大， 直接使用AirDrop或U盘copy
3. 在~/bin/osx/services下面，有激活terminal等相关的apple script
    * 打开Automator → 选择齿轮图标: 可能叫 Quick Action (旧版叫Service), 双击打开
    * 左侧导航栏 → Utilities → Run Apple Script
    * 将activate-terminal.applescript的内容粘贴到Run AppleScript的窗口中，选择Workflow receives为no input，选择in any application. 保存, 起个名字
    * 设置 → keyboard → shortcuts → Services，设置快捷键
4. 打开App Store，**安装Xcode**，否则像git这种都用不了。 安装xcode会很慢（3个小时？）
4. 把默认shell调整为bash: System Preferences → Users & Groups → 先开锁 → 按住ctrl点击用户名 → advanced options→ 找到login shell，改为 /bin/bash



安装一系列命令行工具：

``` shell
# 安装homebrew，这里使用国内源
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"

# 如果使用brew命令出现错误，请使用brew doctor命令解决
brew install htop
brew install macvim
brew install git  # 默认安装的git版本太低了, 可能不支持git ls-remote -q
brew install tig
brew install wget
brew install graphviz

softwareupdate --all --install --force
brew install fzf 			# https://github.com/junegunn/fzf
brew install fd	
brew install bat			# https://github.com/sharkdp/bat
brew install ripgrep	# https://github.com/BurntSushi/ripgrep


# 以下是cask
brew install android-platform-tools
brew install sequel-ace
brew install obsidian

# 还是使用破解版的kaleidoscope吧，其它的在git difftool两个分支的时候，都只能按每次弹出单个文件来比较，很麻烦
#brew install p4v   # 代替收费的kaleidoscope
#git config --global diff.tool p4merge
#git config --global merge.tool p4merge

#brew install shadowsocksx-ng

# 在Finder中显示目录路径
defaults write com.apple.finder _FXShowPosixPathInTitle -bool TRUE;killall Finder
```

----
#### 02 software



1. typora网站可能打不开，直接copy
2. [flux](https://justgetflux.com/)
3. youdao note
4. chrome
5. [app cleaner](http://freemacsoft.net/appcleaner/)

