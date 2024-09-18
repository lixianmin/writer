



#### 1 Basics

##### 01 Basics

```shell

svn ci -m "message"	# 提交到svn

svn diff					# 对比工作文件与缓存在.svn的“原始”拷贝：
svn diff -r 858453			# 显示工作区文件与server上版本r858453的不同
svn diff -r 2:6 test.txt	# 对比test.txt的两个版本


svn log | less			# 显示历史
svn revert test.txt		# 回滚test.txt
svn revert -R dirname	# 回滚某个目录
svn up -r 10			# 回滚到r10
svn status				# 打印状态
svn up					# 更新当前目录，在哪个目录执行就只更新哪个目录


export SVN_EDITOR=vim
svn propedit svn:ignore .
```



##### 02 lock导致无法更新

1. 右键→Tortoise SVN→Settings→General→Context Menu→Win 11 Context Menu→勾选Clean up..
2. 然后: 右键→Tortoise SVN→Clean up..



##### 03 切分支

1. 在Tortoise SVN中, 选中要切分支的目录, 比如 metadata
2. 右键点击metadata目录, 找到Branch/tag..
3. 在弹出的dialog中, 找到To path, 填写入新的名字, 如: metadata2
   1. 如果需要创建多级目录, 可能需要选中 create intermediate folders
4. 点击OK
