

---
#### 01 快捷键


| Hotkeys   | Description         |
| ------------- | ------------------- |
| F1 | 显示文档 |
| alt + enter   | import 缺失的类声明 |
| alt + F7      | find usages         |
| alt + F12     | terminal            |
| alt + cmd + b | 转到实现            |
| alt + cmd + L | 格式化              |
| cmd + [ | 回退到上一次编辑的位置 |
| cmd + 1   | Explorer            |
| cmd + 7   | Structure           |
| cmd + o       | open class ，长按cmd**连点两次o可以勾选include non-project items** |
| ctrl + r | 运行程序 |
| shift + F6 | rename              |
| double shift  | search anywhere     |
| ctrl+alt+o | 重新整理import |





---
#### 02 技巧
1. 找不到源代码的话，可以看看是否有对应类的Impl实现，往往可以反编译一份出来

3. git config --global user.email "lixianmin@xatom.com"

     git config --global user.name "lixianmin"

4. http://plugins.jetbrains.com/ 插件下载

----
#### 03 设置

##### 01 基本设置

1. 『 command+, 』 打开Preference，Appearance & Behavior -> Appearance -> Use custom font -> 16；Editor -> Font -> 22
2. Editor -> General -> Auto Import，设置自动导入
3. kotlin compiler中的target VM设置为1.8



##### 02 maven

1.  右键pom.xml --> Maven --> Create/Open "setttings.xml"
2. 加入以下镜像地址：

```xml
    <mirrors>
        <mirror>
            <id>alimaven</id>
            <name>aliyun maven</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
            <mirrorOf>central</mirrorOf>
        </mirror>
        <!-- 中央仓库在中国的镜像 -->
        <mirror>
            <id>maven.net.cn</id>
            <name>oneof the central mirrors in china</name>
            <url>http://maven.net.cn/content/groups/public/</url>
            <mirrorOf>central</mirrorOf>
        </mirror>
    </mirrors>
```





























