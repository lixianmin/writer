

#### 1 basics

```shell
# 安装
brew install protobuf

# 如果link失败, 则需要强写
brew link --overwrite protobuf

# 安装go脚本
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
```



#### 2 unity3d

1. 引入GameWorkstore.Google.Protobuf.dll, 这个文件来源于https://github.com/GameWorkstore/google-protobuf这个项目, 是直接使用unity3d打开项目后, 在Library目录中找到的编译完成的dll
2. 引入System.Runtime.CompilerServices.Unsafe.dll, 原因是unity3d自带的Unsafe类被故意设置成了internal类, 因此需要自己找一个Unsafe.dll, 而这个dll其实也是unity3d安装包里自带的, 参考: https://github.com/protocolbuffers/protobuf/issues/10085这个问题下面的回答



#### 3 python & grpc

```shell

# 因为我使用了conda, 直接使用pip install grpcio-tools下载的版本并不行, 需要以下流程
# 该流程会自动下载protobuf
conda activate ai
conda install -c conda-forge grpcio-tools
```

