



##### macos



```shell
# 安装conda
brew install miniconda
mkdir ~/.nvm

# 修改.bash_profile
export NVM_DIR="$HOME/.nvm"
[ -s "/opt/homebrew/opt/nvm/nvm.sh" ] && \. "/opt/homebrew/opt/nvm/nvm.sh"  # This loads nvm
[ -s "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm" ] && \. "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm"  # This loads nvm bash_completion


# 创建虚拟环境
nvm install 14.21.3
nvm uninstall 14.21.3
nvm alias default 20.10.0	# 设置默认版本
nvm use 14.21.3
nvm use default

corepack enable	# 似乎可以直接启用yarn
nvm ls 			# 列出当前node版本
nvm ls-remote   # 列出可用的node版本
```



##### ubuntu



```shell
# https://github.com/nvm-sh/nvm

wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

```

