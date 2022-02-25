

##### 01 快捷键 

|                             |                            |      |
| --------------------------- | -------------------------- | ---- |
| Shift+Command+3             | 截取全屏                   |      |
| Shift+Command+4，然后按空格 | 截取一个窗口               |      |
| Shift+Command+4             | 截取任意区域，并保存到桌面 |      |



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


```

