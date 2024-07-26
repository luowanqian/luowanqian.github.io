---
title: Ubuntu18.04安装VNC
urlname: ubuntu_vnc
date: 2019-05-01 19:37:36
tags:
 - 软件配置
 - Software Installation
---

## 安装桌面环境和VNC服务端

首先更新包列表

```
$ sudo apt-get update
```

安装桌面环境Xfce

```
$ sudo apt-get install xfce4 xfce4-goodies
```

安装VNC服务端

```
$ sudo apt-get install tightvncserver
```

## 设置VNC连接密码设置以及生成配置文件

首先执行`vncserver`命令来设置VNC连接密码以及生成VNC配置文件

```
$ vncserver
```

执行命令后会要求设置连接密码，显示以下内容

```
You will require a password to access your desktops.

Password:
Verify:
```

设置完密码后，命令会生成VNC配置文件并启动一个VNC实例

```
New 'X' desktop is your_hostname:1

Creating default startup script /home/your_username/.vnc/xstartup
Starting applications specified in /home/your_username/.vnc/xstartup
Log file is /home/your_username/.vnc/your_hostname:1.log
```

配置文件在下面目录里面

```
/home/your_username/.vnc/
```

第一次运行`vncserver`命令会自动启动VNC实例，分配到`:1`上，对应端口为5901 (端口5901=5900+1，如果是`:2`，则端口为5902，以此类推)。由于要配置VNC，所以先要关闭VNC实例

```
$ vncserver -kill :1
```

关闭成功后会显示以下信息

```
Killing Xtightvnc process ID 30095
```

## 配置VNC

要配置的文件为`xstartup`，该文件在`$HOME/.vnc`里面，即

```
/home/your_username/.vnc/
```

首先备份原始配置文件

```
$ mv ~/.vnc/xstartup ~/.vnc/xstartup.bak
```

然后创建新的配置文件

```
$ touch ~/.vnc/xstartup
```

编辑该文件，添加以下内容

```
#!/bin/sh

xrdb $HOME/.Xresources
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
startxfce4 &
```

网上很多教程会省略销毁那两个环境变量，即没有

```
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
```

我实际操作时发现如果没有销毁变量，连接上VNC时，画面是全灰的，销毁变量后，显示就正常了。为了保证VNC配置文件能够生效，赋予该文件执行权限

```
$ chmod +x ~/.vnc/xstartup
```

## VNC连接

启动VNC实例，即执行命令

```
$ vncserver
```

执行成功后，显示

```
New 'X' desktop is your_hostname:1

Starting applications specified in /home/your_username/.vnc/xstartup
Log file is /home/your_username/.vnc/your_hostname:1.log
```

查看端口开启情况，可以看到`5901`端口已经开启了

```
$ ss -ltn
```

此时可以用VNC连接该电脑了，连接地址格式为`ip:port`

## 参考

[1] [How to Install and Configure VNC on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-vnc-on-ubuntu-18-04)

[2] [VNC server on Ubuntu 18.04 Bionic Beaver Linux](https://linuxconfig.org/vnc-server-on-ubuntu-18-04-bionic-beaver-linux)

[3] [2018-06-07【Ubuntu 18.04 搭建VNC服务器】](https://www.jianshu.com/p/f58fe5cdeb5f)