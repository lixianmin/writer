





#### 1 Basics



|          |                   |      |
| -------- | ----------------- | ---- |
| win+箭头 | 窗口最大化， 分屏 |      |
|          |                   |      |
|          |                   |      |



#### 2 apt

1. 使用`apt install ./abc.deb`而不是`dpkg -i ./abc.deb`， 安装软折的时候， 可以自动安装依赖



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

1. 这个玩意去cisco官网下载不靠谱，需要登录，但我无法正常注册，废了两个邮箱了
2. 从这里下载5.13版本：https://blog.1e3.ru/cisco-anyconnect-vpn-installation-for-windows-linux-mac-os/
3. 从这里下载4.10版本：https://vpn.nic.in/
4. 解压后进入anyconnect-linux64-4.10.07061/vpn目录， 执行 `bash vpn_install.sh`
5. 安装成功扗， 从系统菜单中启动， 正常使用



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



##### 4 unity hub

以下是修正unity hub安装后打不开界面的问题

If you installed using the `.deb` distribution, your Unity Hub binary file is probably at `/opt/unityhub/unityhub-bin`, so you can just copy-paste the snippet to `/etc/apparmor.d/unityhub`

You should open it as root/with sudo and your text editor of preference:
`sudo vim /etc/apparmor.d/unityhub`
or
`sudo nano /etc/apparmor.d/unityhub`
or
`sudo gedit /etc/apparmor.d/unityhub`
and so on...

Write the following (assuming your Unity Hub binary path is `/opt/unityhub/unityhub-bin` which is the default, otherwise change it to match your binary path):

```
abi <abi/4.0>,
include <tunables/global>

profile unityhub /opt/unityhub/unityhub-bin flags=(unconfined) {
  userns,

  # Site-specific additions and overrides. See local/README for details.
  include if exists <local/unityhub>
}
```

Now restart apparmor
`sudo systemctl restart apparmor.service`
Check if everything is ok
`systemctl status apparmor.service`
If so then you are good to go!



以下是修正打开了界面，但界面是空的问题

1. ubuntu 22.04/24.04上面， 因为缺少libssl的旧版本库一直下载不到license, [解决方案](https://gist.github.com/joulgs/c8a85bb462f48ffc2044dd878ecaa786)  ： `
   wget http://archive.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.0g-2ubuntu4_amd64.deb `
   `sudo apt install libssl1.1_1.1.0g-2ubuntu4_amd64.deb` 
2. 然后重启ubuntu



```shell
# 增加adb的访问权限
chmod +x ~/Unity/Hub/Editor/2022.3.30f1c1/Editor/Data/PlaybackEngines/AndroidPlayer/SDK/platform-tools/adb
```





