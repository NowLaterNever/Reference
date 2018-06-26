# 1. 查看当前go的环境变量
```shell
go env
```
> go使用protobuf，首先需要先搭建好go的开发环境。

# 2. 安装protobuf3的go插件,需要切换到root用户
```shell
go get github.com/golang/protobuf/protoc-gen-go 
```
> 这条命令会下载protoc-gen-go到GOPATH/bin下，需要将protoc-gen-go拷贝到/usr/bin。protoc-gen-go只是protobuf3的golang插件，并不是真正的编译器。这条命令只有root用户才能执行成功。

# 3. 下载[protoc](https://github.com/google/protobuf/releases)
```shell
地址：https://github.com/google/protobuf/releases
```
>在这个页面中，可以找到win/linux/mac的二进制文件，找到并下载protoc-3.6.0-linux-x86_64.zip，解压后将里面的protoc拷贝到/usr/bin。

# 4. 生成golang代码
```shell
protoc --go_out=. helloworld.proto
```
>helloworld.proto存在于执行命令的目录下。

# 5. 参考资料
[proto3文档](https://developers.google.com/protocol-buffers/docs/proto3)
[go api文档](https://godoc.org/github.com/golang/protobuf/proto)
