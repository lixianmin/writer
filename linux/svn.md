





```shell

svn ci -m "message"	# 提交到svn

svn diff									# 对比工作文件与缓存在.svn的“原始”拷贝：
svn diff -r 858453				# 显示工作区文件与server上版本r858453的不同
svn diff -r 2:6 test.txt	# 对比test.txt的两个版本


svn log | less			# 显示历史
svn revert test.txt	# 回滚test.txt
svn status					# 打印状态
svn up							# 更新当前目录，在哪个目录执行就只更新哪个目录


export SVN_EDITOR=vim
svn propedit svn:ignore .
```

