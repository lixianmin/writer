​	

---
#### 基础命令

1. HEAD表示当前版本， HEAD^是前一个版本，HEAD^^是前前一个版本, 或者用 HEAD@{5}这样的格式
2. git config --global pull.rebase true 设置[pull下的rebase=true

命令 | 详解
---|---
git branch -va      | 显示所有的分支信息
git branch -d hotfix| 删除hotfix分支
git checkout master | 切换到master分支
git checkout -b hotfix  | 新建一个hotfix分支并checkout
git checkout -b hotfix remotes/origin/hotfix | 拉取远程hotfix分支 
git fetch origin<br>git checkout hotfix | 拉取远程的hotfix分支 
git checkout -- .   | discard本地所有unstagged files
git cherry-pick commit-id | 将commit-id提交到当前分支 
git clone http://.. | clone一个git目录到本地
git diff dev ^master | 查询dev有，而mater没有的修改 
git  diff HEAD  ^efe03951 | 查询当前分支从efe03951开始的改动 
git gc              | 清空无用文件，解决格式错误
git merge hotfix    | 假定当前在master分支，则该命令将hotfix分支上的内容合并到master中
git pull            | pull
git push            | push
**git push origin --delete review** | 删除远程的名字为review的分支 
**git push origin HEAD:test -f** | 将当前分支的内容强推到远程的test分支上 
**git rebase -i targetBase** | 将targetBase拿过来，将当前分支上的修改在targetBase的基础上重新应用一遍 
git remote -v       | 查看远程的url地址
git remote set-url origin [url] | 重新设置remote地址 
git reset --hard HEAD   | 把你工作目录中所有未提交的内容清空
git rm --cached filename | 删除远程的文件，但是保存本地的 




log | 详解
---|---
git log --oneline   | 在同一行内打印日志
git log -p          | 打印详细日志
git log --stat      | 打印统计信息
git reflog          | 对git误操作进行数据恢复



stash | 详解
---|---
git stash           | stash当前变更
git stash list      | 打印stash列表
git stash apply     | apply最后一个stash
git stash apply stash@{2}   | 按名字apply



----

#### 强推本地master到远程release

1. git push origin master:release -f



---
#### rebase代码流程
目标：将master分支上的代码合并到lixianmin这个分支上

1. git checkout master，切换到master分支
2. git pull，更新master分支的数据
3. git checkout lixianmin，切换到lixianmin分支
4. git rebase -i master，基于master分支将所有lixianmin分支上的修改应用到lixianmin分支（**在哪个分支上改哪个分支**）



---
#### 如何回滚dev分支上的一批代码

1. git checkout dev，切换到dev分支
2. git checkout -b kotlin，将dev分支内容复制一份并新建取个新名字kotlin
3. git reset --hard 2f83e3，将dev强制重置到2f83e3提交，后续的提交会被删除
4. git push -f，强制提交，覆盖远端的数据
5. git checkout kotlin，切换到kotlin分支
6. git push origin kotlin，将kotlin分支推送到远端
7. 现在可以在kotlin分支上开发了



git reset --hard xxxx 是重置代码到某个版本，但如果想回滚代码的话，需要使用git revert，另外，可以使用git rebase -i 在交互界面里把一些revert的操作合并到一个里面提交



---
#### tig [vim版]


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

---
#### submodule

1. git submodule add git://github.com/lixianmin/rack.git rack
2. 如果无意之中已经加了一个同名的module，则删除的时候需要到git根目录下的.git/config中移除section，同时到.git/modules中移除目录