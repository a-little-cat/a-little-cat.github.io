---
tags: [深度学习,caffe,CUDA]
---
在centos7.2环境配置nvidia384.66驱动和cuda8.0
<!--more-->
# 项目整体
在新服务器上安装centos7.2,安装nvidia驱动384.66,安装cuda8.0和其他软件.

其中最复杂 耗时最久的是nvidia驱动,其它均为无脑下一步.

# 安装细节
安装驱动的细节
## 安装centos7.2
选择minimal安装,安装完后**不要手动更新**

## 安装nvidia驱动
### 禁用自带驱动
在查阅了一些第三方教程后,发现[nvidia官网](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)的文档值得阅读(虽然并不总是覆盖所有情况)

centos安装完成后系统自带一份vga驱动*nouveau*,安装nvidia驱动需要屏蔽掉它.安装过程将在无显卡驱动的命令行下完成,这也是安装minimal版系统的原因.

屏蔽*nouveau*的代码如下:
```sh
mkdir -p /etc/modprobe.d
tee /etc/modprobe.d/blacklist-nouveau.conf<<EOF
blacklist nouveau
options nouveau modeset=0
EOF
sudo dracut --force
```
屏蔽完成后进行重启,然后执行以下命令,如果没有输出,则说明屏蔽成功.
```sh
lsmod | grep nouveau
```

### 下载nvidia驱动
在[官网](https://www.nvidia.com/Download/index.aspx?lang=cn)选择自己的卡型号,cuda版本,语言后下载对应的驱动.

建议英文.

### 安装依赖库
安装nvidia驱动需要kernel-header和kernel-devel库,这两个库的版本必须和系统kernel一致.

部分教程建议使用命令
```sh
sudo yum install kernel-devel kernel-headers
```
但这会安装仓库里最新的库,和系统kernel版本不一致.

如果选择更新kernel版本至最新,有可能遇到新kernel和nvidia驱动不兼容的情况(部分内核接口修改)

因此另一部分教程推荐使用命令
```sh
sudo yum install kernel-devel-$(uname -r) kernel-headers-$(uname -r)
```
由于官方仓库被墙(!!!),系统从国内的华为和清华源下载,这两个库里没有对应版本的kernel-devel和kernel-headers.

最终解决方案:到centos[官网](https://buildlogs.centos.org/c7.1511.00/kernel/20151119220809/3.10.0-327.el7.x86_64/)下载这两个库该版本的本地安装包.

使用以下命令安装
```sh
yum install ./file.rpm
```

### 安装驱动
使用以下命令安装驱动
```sh
chmod +x driver.run
./driver.run
```
安装过程中需要手动接受协议.

### 安装cuda8.0
在[nvidia官网](https://developer.nvidia.com/cuda-80-ga2-download-archive)下载cuda8.0安装包.

我选择了runfile本地包.网速快的话使用在线包会更省时间.

下载完成后给执行权限,执行,一路下一步即可.部分simple示例,如decode需要以下库.
```sh
yum install mesa-libGLU-devel mesa-libGL-devel
```

### 安装并启动docker
docker官方提供了脚本来安装
```sh
curl -fsSL get.docker.com -o get-docker.sh
sudo sh get-docker.sh --mirror Aliyun
sudo systemctl enable docker
sudo systemctl start docker
```
在修改docker的base size
```sh
/usr/lib/systemd/system/docker.service
# ExecStart=/usr/bin/dockerd --storage-opt dm.basesize=200G
```

### 安装nvidia-docker2
添加nvidia官方仓库,然后从仓库安装.

官方没有提供任意版本的release包,只有源代码,如果不想自己编译,只能安装最新版.

我的部署包提供商文档里建议关闭selinx,下方最后一部分代码做了这件事.
```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.repo | \
  sudo tee /etc/yum.repos.d/nvidia-docker.repo
yum install nvidia-docker2
sudo pkill -SIGHUP dockerd

setenforce 0 #临时
vi /etc/selinux/config #修改SELINUX=permissive
```
手残更新了一次所有包,新版docker18.09.0和内核3.10.327不兼容,手动安装早版本的docker和nvidia-docker
```sh
yum install docker-ce-18.06.0.ce-3.el7 # 旧版本docker
yum --disablerepo="*" --enablerepo="nvidia-docker" --showduplicates list available # yum lisg | grep nvidia-docer不会显示历史版本,需要用这行来显示
# nvidia-docker2.noarch  2.0.3-1.docker18.06.0.ce  nvidia-docker
yum install nvidia-docker2-2.0.3-1.docker18.06.0.ce  # 官方文档只给了ubuntu下安装早期版本的命令,拼centos下的命令拼的想死.
```

### 开放防火墙特定端口(如果需要)
安装一个网络服务的时候,关闭防火墙后依然不能工作,测试发现使用特定命令打开特定端口可以工作
```sh
iptables -I INPUT 1 -p tcp --dport 8300 -j ACCEPT
```

### 清理docker缓存
```sh
docker ps -a | grep "Exited" | awk '{print $1 }'|xargs docker stop
docker ps -a | grep "Exited" | awk '{print $1 }'|xargs docker rm
docker images|grep none|awk '{print $3 }'|xargs docker rmi
```

## 测试
测试驱动:
```
nvidia-smi
```
测试cuda:
在**NVIDIA_CUDA-8.0_Samples**目录编译执行