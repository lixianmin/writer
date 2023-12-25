



##### 01 Basics



```shell

# 使用 nvm安装
nvm use default
corepack enable

#####################################################
yarn install 		# 安装依赖包
yarn run build		# 打包

```





##### 02 chathub

1. 参考: https://github.com/chathub-dev/chathub



```shell
nvm install lts/iron
nvm alias default lts/iron	# 设置默认版本

nvm use default
corepack enable	# 激活yarn, 否则yarn不可用
yarn install		# 安装缺失的lib
yarn rebuild		# 有时yarn build编译不过, 使用rebuild会清理那些旧的遗留数据
yarn build			# 编译生成可用代码

```

