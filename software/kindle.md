



----

#### 01 怎么转换pdf的格式到kindle上，使得字体更合适一些

1. 使用过calibre，转出mobi格式，发现字是好了，但是图丢失
2. 发邮件到lixianmin56@kindle.cn，带convert标题，自己转mobi，发现图丢失
3. 使用mac的preview打开pdf，直接再导出为pdf，然后把新导出的pdf再导出，此时导出框中选detail，然后page size选ROC 16，这时导出的可能最适合kindle看；如果选择了A5，发现导出的格式有可能丢失一行；如果选择了envolop系列，则导出的pdf内容又细又长，边距太大了没法看



---
#### 02 怎样将web clip发送到kindle

试了各种chrome的插件，格式都会丢失，最终还是放弃吧

1. 新的方案是使用chrome打印pdf的功能，command + P
1. 在打印预览页，在more settings里，把scale设置为200（这是上限了）
2. 然后点击Open PDF in Preview
3. 然后点击分享到邮件
4. 直接发送到自己的lixianmin56@kindle.cn 邮件地址

因为是pdf，所以  格式保持的很完整



----

#### 03 使用calibre脱壳DRM导出到Boox中

1. 安装calibre与plugin参考： https://github.com/apprenticeharper/DeDRM_tools/wiki/Exactly-how-to-remove-DRM#2-install-calibre
   1. https://calibre-ebook.com/download 下载安装calibre
   2. https://github.com/apprenticeharper/DeDRM_tools/releases 下载 DeDRM
   3. Load plugin from file -> 加载DeDRM_Plugin.zip文件 -> File type下面, 选中DeDRM -> Customize plugin -> eInk Kindle ebooks -> 输入:  901309077372027T
2. 转到z.cn，登录，然后管理我的[内容与设备](https://www.amazon.cn/hz/mycd/myx?ref_=ya_d_l_manage_kindle#/home/content/booksAll/dateDsc/)
3. 找到要下载的书，点击『操作』按钮，在弹出的菜单中选择『通过电脑下载USB传输』，然后选择设备『KindleVoyage』
4. 将下载的图书（*.azw3），拖入到calibre中
5. 双击图书，查看是否能正常打开
6. 选中图书，按下s键，导出图书到U盘，调整一下目录，使用书名作为目录
7. 将U盘插入到BOOX中，把U盘上的图书目录复制到『内部存储』的Books目录下
8. 点击BOOX上的图书目录，进入目录，点击AZW3文件，打开图书查看