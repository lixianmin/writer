



```shell
# 安装conda
brew install miniconda
source ~/.bash_profile

# 创建虚拟环境
conda create -n vicuna python=3.9
conda activate vicuna

# 删除无用的虚拟环境
conda info --envs
conda remove --name vicuna --all
```
