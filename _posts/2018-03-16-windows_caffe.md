---
title: windows下caffe安装以及mnist训练测试
tags: [深度学习,caffe]
---
# 在windows设备的caffe环境配置

安装环境:win10,vs2013,python3.5,cmake3.8.2
<!--more-->
## 下载环境文件(可略过,如果网好)
在scripts/download_prebuilt_dependencies.py文件中找到自己环境对应的下载链接,下载后copy到**~\\.caffe\dependencies\download**文件夹下
## 复制caffe源代码到自己的目录并解压
```sh
cd caffe #你自己的caffe安装目录
git clone https://github.com/BVLC/caffe.git
git checkout windows
```
## 进入caffe目录 修改并执行生成脚本
修改内容包括:vs版本,python版本,是否使用ninga,是否编译python,matlab接口,是否执行测试安装.

新手建议能关的都关了,然后执行
```sh
scripts\build_win.cmd
```

## 打开sln文件
进入D:\ycc\code\caffe-windows\build

用vs13打开caffe.sln,生成.

## mnist示例
主要分为:下载并预处理数据,训练,测试.

### 下载并预处理数据
```sh
cd caffe\data\mnist
.\get_mnist.ps1
.\create_mnist.ps1 #Debug模式需要微调文件路径
```
如果提示不能运行,可以尝试执行**set-ExecutionPolicy RemoteSigned**打开脚本执行权限
### 执行训练
```sh
.\train_lenet.ps1
# caffe-d.exe train --solver=caffe\examples\mnist\lenet_solver.prototxt
```
Debug模式下**lenet_solver.prototxt**中的路径需要微调
### 执行测试
```sh
caffe-d.exe test --model=lenet_train_test.prototxt --weights=lenet_iter_10000.caffemodel
```
文件路径需要修改 在caffe/examples/mnist下