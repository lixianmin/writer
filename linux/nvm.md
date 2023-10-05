



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
nvm use 14.21.3
nvm use default

nvm ls # 列出当前环境列表
```

