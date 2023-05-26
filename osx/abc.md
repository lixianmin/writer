

##### 01 快捷键 

|                             |                  |      |
| --------------------------- | ---------------- | ---- |
| Shift+Command+3             | 截取全屏         |      |
| Shift+Command+4，然后按空格 | 截取一个窗口     |      |
| Shift+Command+5             | 自由选择如何截图 |      |



##### 02 解决软件不可信的问题

软件安装后，打开时报错：xxxx is damaged and can’t be opened. You should move it to the Trash

```shell
// 执行以下命令，允许使用任何来源的软件。然后打印 Settings -> Security & Privacy -> Allow apps downloaded from --> Anywhere
sudo spctl --master-disable 

// 修改应用属性
sudo xattr -rc /Applications/Kaleidoscope.app
```



#### 03 命令行

```shell


# 格式化clipboard中的json串
pbpaste | jq .

# 启动 apache web server
httpd -v
sudo apachectl start	# 根目录在 /Library/WebServer/Documents
sudo apachectl stop		

```



#### 04 开启apache网页服务器

```shell
sudo apachectl start
sudo apachectl stop

文件在目录在： /Library/WebServer/Documents

```



#### 05 在recent文件列表中不显示某个目录的内容

1. System Settings → Siri & Spotlight → Spotlight Privacy → 添加需要隐藏的文件夹
