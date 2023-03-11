

---

#### 1 快捷键

##### 1 普通快捷键

| Hotkeys        | Description |
| -------------- | ----------- |
| cmd + ctrl + 1 | outline大纲 |
| cmd + ctrl + 3 | Files列表   |
|                |             |



##### 2 table操作快捷键

| Hotkeys              | Description                   |
| -------------------- | ----------------------------- |
| alt + cmd + t        | 在当前位置插入table           |
| cmd + enter          | 插入一行                      |
| cmd + shift + delete | 删除当前行                    |
| shift + enter        | 当前单元格中加入<br \/>换行符 |



#### 2 自定义Typora

##### 1 增大File Tree字体

1. 打开Settings→Appearance→Open Theme Folder
2. 找到你要修改的那个样式的xxx.css文件
3. 打开它, 在里面加入如下css, 你就发现sidebar的字体变大了. 这个css来自于Pixyll.css

```css
#typora-sidebar {
	font-size:1rem !important;
}

.html-for-mac #typora-sidebar {
	background-color:white;
}
```

