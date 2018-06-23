# 1. 在/opt目录下解压go包
```shell
sudo mv go1.10.3.linux-amd64.tar.gz /opt
tar xf go1.10.3.linux-amd64.tar.gz
```

# 2. 在~/.bashrc中设置go的路径
```shell
export GOROOT=/opt/go
export GOPATH=$GOROOT/bin
export PATH=$PATH:$GOPATH
```