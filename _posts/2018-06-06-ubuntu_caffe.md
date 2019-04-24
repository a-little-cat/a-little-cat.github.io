---
title: ubuntu下caffe环境搭建
tags: [深度学习,caffe]
---
# 在ubuntu16.04下搭建caffe GPU环境
caffe的编译和其依赖项的配置总是各自更新,网上找到的之前的教程总是不完全,记录一版,留待重装.
<!--more-->
# 搭建环境
|喵|呜|
|--|--|
服务器|think ts250
gpu|1050Ti
系统|ubuntu16.04
cuda|9.2
cudnn|无

# 搭建流程
## 装系统
从ubuntu官网下载16.04的iso镜像,给服务器装系统.据说17以后支持使用apt安装caffe,但是调试不方便,没有用.
## 安装依赖项
首先进行系统更新,安装常用的软件.(这次搭建使用的联通宽带,直接从ubuntu源更新,感谢联通)

然后安装以下库
```sh
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
sudo apt-get install --no-install-recommends libboost-all-dev
sudo apt-get install libopenblas-dev #修改以使用其它blas库
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
```

## 安装NVIDIA驱动
驱动可以在线或者离线安装,之前离线安装过一次,非常麻烦,找得到的教程拼拼凑凑最终成功.

在线安装需要一个直通ubuntu apt源的网络,感谢联通.

1. 卸载驱动
```
sudo apt-get remove --purge nvidia*
```
2. 添加PPA源到系统
```
sudo add-apt-repository ppa:graphics-drivers/ppa
```
3. 更新系统
```
sudo apt-get update
```
4. 进入命令行(**Ctrl + Alt + F1**)
```
sudo service lightdm stop
```
5. 安装驱动
```
sudo apt-get install nvidia-384  #384应修改为最新的驱动版本,在nvidia官网查看
```
6. 重启

## 安装CUDA
到nvidia官网下载对应版本的cuda库(我选择了离线deb包).
```sh
sudo dpgk -i *.deb
sudo apt update
sudo apt install cuda
```
设置环境变量,在/etc/bash.bashrc文件末尾添加cuda路径.
> export PATH=/usr/local/cuda-9.1/bin:$PATH
>
> export LD_LIBRARY_PATH=/usr/local/cuda-9.1/lib64:$LD_LIBRARY_PATH

执行source更新
```sh
source /etc/bash.bashrc
```

## 安装CUDNN
并没有做

## 下载caffe并编译
早期的caffe需要手动编译各种库,如今大部分库都可以用apt安装dev版本了,感谢开源.openCV是我自己编译的,因为dev版本找不到库文件和头文件.

clone caffe的源代码后,复制Makefile.config.example到Makefile.config,按需修改.

我做的修改包括:打开opencv leveldb lmdb,指定opencv路径 版本,指定cuda路径,指定blas库,关闭python,打开debug.

编译时提示找不到hdf5.h,可能原因是hdf5的最新库更改了文件名和路径...按一下修改即可:
> 在Makefile.config文件的第85行，添加/usr/include/hdf5/serial/ 到 INCLUDE_DIRS.
> 在Makefile文件的第173行，把 hdf5_hl 和hdf5修改为hdf5_serial_hl 和 hdf5_serial.
行号很可能随版本修改...

编译,测试,通过.
```sh
make -j?
make test
make runtest
```