---
tag: [terminal]
---
喵
<!--more-->

# 起
微软给win10增加了官方ssh server支持

比较坑的是**authorized_keys**的默认目录不是.ssh目录,以及文件权限要求比较高

[官方文档](https://docs.microsoft.com/zh-cn/windows-server/administration/openssh/openssh_install_firstuse)

[巨坑的权限问题](https://github.com/PowerShell/Win32-OpenSSH/issues/1306)

文档是在线的,就不复制过来了.

## wsl-ssh自启
找了一些教程,似乎都要求windows用户登录后才能自启

不想登录桌面的话,只能先ssh到windows,然后手动打开wsl的ssh

```sh
wsl -d ubuntu -u root ssh start
```

## ssh关机
可使用**shutdown**命令在pwsh里关机,重启,支持局域网设备,需要到**计算机配置/Windows 设置/安全设置/本地策略/用户权利指派**中打开允许远程关机

示例:
```sh
# 关机
shutdown /s /m 192.168.123.4 /t 10
# 重启
shutdown /r /m 192.168.123.4 /t 10
# 取消
shutdown /a

```