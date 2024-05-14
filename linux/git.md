[toc]	

---
#### 01 基础命令

##### 01 基础命令

1. HEAD表示当前版本， HEAD^是前一个版本，HEAD^^是前前一个版本, 或者用 HEAD@{5}这样的格式
2. git config --global pull.rebase true 设置[pull下的rebase=true



```shell 
git config --global user.email "lixianmin@live.cn"
git config --global user.name "lixianmin"
git config --global branch.autosetuprebase always

git config --global credential.helper store			# 每次上传都需要输入用户名密码的问题
git config --global core.autocrlf true				# mac与windows共享代码库时, 忽略因\r\n改变导致文件需要重新上传的问题
git config --add core.filemode false				# mac与windows共享代码库时，因为filemode改变导致文件需要重新上传的问题, 不支持global?

git config --global core.editor "vim"				# 设置vim为编辑器
```



命令 | 详解
---|---
git cherry-pick commit-id | 将commit-id提交到当前分支 
git clean -xdf | 清除所有不被跟踪的文件和目录 
git clone http://.. | clone一个git目录到本地
git commit --amend | 修改最后一次提交的comment 
git config --global credential.helper store | 解决**每次都需要输入用户名密码**的问题 
git gc              | 清空无用文件，解决格式错误
git merge master    | 开发时我们往往从master分支切一个dev.risk出来，开发完成的时候往往master已经更新了，此时该命令将master分支上的改动重新合并到dev.risk中，然后dev.risk就可以考虑发版了 
git reset --hard HEAD   | 把你工作目录中所有未提交的内容清空
git rm --cached filename | 删除远程的文件，但是保存本地的 



##### 02 git branch



| 命令                    | 详解                                               |
| ----------------------- | -------------------------------------------------- |
| git branch -va          | 显示所有的分支信息                                 |
| git branch -d hotfix    | 删除hotfix分支                                     |
| git branch -D hotfix    | 强制删除hotfix分支                                 |
| git branch -m newName   | 将当前分支改名为newName                            |
| git push origin :review | 删除远程的名字为review的分支                       |
| git fetch -p            | 有时本地会显示已经删除的远程分支，拉取新fetch head |



##### 03 git checkout

| 命令                                                         | 详解                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| git checkout master                                          | 切换到master分支                                             |
| git checkout -b hotfix                                       | 新建一个hotfix分支并checkout                                 |
| git checkout -b hotfix remotes/personal/hotfix 或<br />git checkout -t personal/hotfix | 1. 拉取personal这个remote上的hotfix分支<br />2. -t 是 --track 的简写 |
| git fetch origin<br>git checkout hotfix                      | 拉取远程的hotfix分支                                         |
| git checkout -- .                                            | discard本地所有unstagged files                               |



##### 04 git diff

| 命令                     | 详解                                   |
| ------------------------ | -------------------------------------- |
| git diff dev ^master     | 查询dev有，而mater没有的修改           |
| git diff HEAD  ^efe03951 | 查询当前分支从efe03951开始的改动       |
| git difftool master dev  | 使用difftool查看master如何转成dev      |
| git difftool master      | 使用difftool查看master如何转成当前分支 |



##### 05 git log




| 命令              | 详解                    |
| ----------------- | ----------------------- |
| git log --oneline | 在同一行内打印日志      |
| git log -p        | 打印详细日志            |
| git log --stat    | 打印统计信息            |
| git reflog        | 对git误操作进行数据恢复 |



##### 06 git merge

```shell
git checkout dev
git merge test
git mergetool # 通过kaleidoscope解决冲突

git commit -m "解决完冲突"
git push
git checkout test
git merge dev
```



##### 07 git pull



| 命令                   | 详解                     |
| ---------------------- | ------------------------ |
| git pull origin hotfix | 从指定的远程分支拉取数据 |



##### 08 git push



| 命令                                      | 详解                                                                               |
| --------------------------------------- | -------------------------------------------------------------------------------- |
| git push --delete origin review         | 删除远程的名字为review的分支                                                                |
| git push -f origin(远程) head(源):test(目标) | 1. 将当前分支强推到远程的test分支上。<br />2. 需要到gitlab的设置 --> 版本库 --> 保护分支，把相关分支摘出来            |
| git push -f personal head:dev           | 1. 强推本地head到personal/dev分支，简记为PHD<br />2. 需要到gitlab的设置 --> 版本库 --> 保护分支，把相关分支摘出来 |



##### 09 git rebase



| 命令                      | 详解                                                         |
| ------------------------- | ------------------------------------------------------------ |
| git rebase -i srcBranch   | 1. git命令从来都是**修改当前分支**的<br />2. 将srcBranch上的改动在当前分支上重新apply一遍 |
| git rebase -i [commit-id] | 1. 将[commit-id]之后的所有commit[合并为一个commit](https://www.jianshu.com/p/571153f5daa1)<br />2. 进入vi提交模式之后，把pick改为squash/s<br />3. git add .<br />4. git rebase --continue |



目标：将master分支上的代码合并到lixianmin这个分支上

1. git checkout master，切换到master分支
2. git pull，更新master分支的数据
3. git checkout lixianmin，切换到lixianmin分支
4. git rebase -i master，基于master分支将所有lixianmin分支上的修改应用到lixianmin分支（**在哪个分支上改哪个分支**）



##### 10 git remote



| 命令                            | 详解               |
| ------------------------------- | ------------------ |
| git remote -v                   | 查看远程的url地址  |
| git remote set-url origin [url] | 重新设置remote地址 |
| git remote add personal [url]   | 增加远程分支       |
| git remote rm personal          | 删除远程分支       |



##### 11 git stash



| 命令                      | 详解               |
| ------------------------- | ------------------ |
| git stash                 | stash当前变更      |
| git stash list            | 打印stash列表      |
| git stash apply           | apply最后一个stash |
| git stash apply stash@{2} | 按名字apply        |



##### 12 git tag

| 命令            | 详解                      |
| --------------- | ------------------------- |
| git tag         | 同git tag -l，查看tag列表 |
| git tag v1.2    | 打一个v1.2的tag           |
| git tag -d v1.2 | 删除名为v1.2的tag         |
|                 |                           |





----

#### 02 FAQ列表



---


##### 02 增加一个remote

```shell

# 新加一个remote，用作dev分支
git remote add personal ssh://xxxx.com/personal-code/risk-lua

# 创建一个dev分支
git checkout -b dev
git push --set-upstream personal dev
git push -f personal head:dev
```



##### 03 回滚dev分支代码

1. git checkout dev，切换到dev分支
2. git checkout -b kotlin，将dev分支内容复制一份并新建取个新名字kotlin
3. git reset --hard 2f83e3，将dev强制重置到2f83e3提交，后续的提交会被删除
4. git push -f，强制提交，覆盖远端的数据
5. git checkout kotlin，切换到kotlin分支
6. git push origin kotlin，将kotlin分支推送到远端
7. 现在可以在kotlin分支上开发了



git reset --hard xxxx 是重置代码到某个版本，但如果想回滚代码的话，需要使用git revert，另外，可以使用git rebase -i 在交互界面里把一些revert的操作合并到一个里面提交



##### 04 永久删除文件历史记录



```shell
# 删除文件
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch software/Intelij-idea.md' --prune-empty --tag-name-filter cat -- --all

git push --all --force

# 如果历史文件占用太大空间的话，可以使用 git gc 释放空间。需要在各自的git目录中各自执行
```



##### 05 submodule使用

1. git submodule add git://github.com/lixianmin/rack.git rack
2. 如果无意之中已经加了一个同名的module，则删除的时候需要到git根目录下的.git/config中移除section，同时到.git/modules中移除目录



##### 06 git log中文乱码问题

1. `$ echo $LANG;`如果输出结果为空，则执行 `   export LANG="zh_CN.UTF-8"      `
2. 如果还不能解决，则尝试修改git config：

```shell
git config --global i18n.commitencoding utf-8
git config --global i18n.logoutputencoding utf-8
export LESSCHARSET=utf-8
```



##### 07 wsl配置winmerge

```shell

git config --global diff.tool winmerge
git config --global difftool.winmerge.cmd "'C:\Progra~1\WinMerge\WinMergeU.exe' -e -u \$LOCAL \$REMOTE"
git config --global difftool.prompt false
```





---

#### 03 tig [vim版]


命令    | 详解
---     |---
cc      | commit当前staged files
O       | Maximize当前界面
R       | refresh当前界面
ur      | revert chunk
vh      | 帮助文档
vr      | refs view，查找所有分支信息，查看origin/master上的进度
vs      | status view，通过uu可以track或untrack文件
vy      | stash view，查看有哪一些stash，A -> apply，P -> pop, ! -> drop
!       | revert那些unstaged files，这个只能在修改点使用，也就是光标在这里时“@@ -1,212 +1,212 @@”
tig filepath | 显示同一个文件的所有log

