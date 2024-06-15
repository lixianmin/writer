





#### 1 Basics



#### 2 apt

1. 使用`apt install abc.deb`而不是`dpkg -i abc.deb`， 安装软折的时候， 可以自动安装信赖



```shell

# 查看安装的deb软件
apt list --installed

# 移除软件
apt purge xxx.deb

```







#### 3 安装ubuntu系统

##### １ 安装ubuntu 24.04

```shell

### 以下参考bilibili的视频：　NVIDIA显卡的Ubuntu驱动程序安装方法　https://www.bilibili.com/video/BV1wY411p7mU

1. 使用U盘安装的时候， 因为nouveau驱动的问题， 会导致ubuntu安装过程中进入黑屏，解决方案是加 nomodeset
2. 在引导菜单中， 按下e键， 找到quiet splash， 在后面加上 nomodeset, 按下F10保存，然后正常安装
3. 安装完成后， 拔出U盘，新系统在启动的时候也会使用nouveau驱动，导致进入黑屏，解决方案仍然是加nomodeset
4. 在启动系统的过程中，按下shift键，显示启动菜单，按下e键，编辑启动项，定位到linux开头的行，找到最后的quie splash, 加入nomodeset
5. sudo vi /etc/modprobe.d/blacklist.conf
6. 在文件最后添加：blacklist nouveau
7. 让黑名单起作用：sudo update-initramfs -u
```



##### 2 cisco anyconnect

参考： [在linux上下载使用cisco anyconnect Software](https://blog.csdn.net/weixin_45765073/article/details/128329898)

1. 这个玩意去cisco官网下载不靠谱，需要登录，但我无法正常注册，废了两个邮箱了
2. 从这里下载：https://uci.service-now.com/sys_attachment.do?sys_id=3e869ef2db082b0054e7f236bf961900
3. 解压后进入 anyconnect-linux64-4.6.02074/vpn目录， 执行 `bash vpn_install.sh`
4. 安装成功扗， 从系统菜单中启动， 正常使用



##### 3 rime五笔输入法



参考：[fcitx-rime添加五笔/五笔拼音 ](https://www.cnblogs.com/weishuan/p/4402731.html)



```
 sudo apt-get install fcitx-rime #Fcitx 用户
 sudo apt-get install ibus-rime  #IBus 用户 （ibus不作介绍，因为很繁琐）
```

默认情况下安装的fcitx-rime中是没有五笔的。

```shell
sudo apt-get install librime-data-wubi
cp /usr/share/rime-data/wubi86.schema.yaml ~/.config/fcitx/rime/
cp /usr/share/rime-data/wubi_pinyin.schema.yaml ~/.config/fcitx/rime/
ln -s /usr/share/rime-data/wubi86.dict.yaml ~/.config/fcitx/rime/
ln -s /usr/share/rime-data/wubi_pinyin.prism.bin ~/.config/fcitx/rime/
ln -s /usr/share/rime-data/pinyin_simp.dict.yaml ~/.config/fcitx/rime/
ln -s /usr/share/rime-data/pinyin_simp.* ~/.config/fcitx/rime/
ln -s /usr/share/rime-data/wubi86.* ~/.config/fcitx/rime/
```



编辑~/.config/fcitx/rime/build/default.yaml，在schema_list下方加一行：

\- schema: wubi_pinyin
\- schema: wubi86

重启fcitx，F4选择五笔/五笔拼音，OK。



