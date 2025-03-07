---
title: Linux From Scratch构建记录
date: 2025-03-07 21:28:26
tags:
 - Linux
---

# Linux From Scratch

Version 12.3

状态：正在进行

## 2. Preparing the Host System

### 2.2 Host System Requirements

> Host系统：Ubuntu 24.04.2 LTS

使用教程提供的脚本检查了下依赖包，发现有些依赖未装上

{% asset_img 2_2_requirements_check.png %}

使用`apt`安装以下包

```shell
sudo apt install build-essential
sudo apt install bison
sudo apt install gawk
sudo apt install texinfo
```

检查脚本发现`sh`不是指向`bash`，当前指向的是dash

```
lrwxrwxrwx 1 root root 4 Mar 31  2024 /bin/sh -> dash*
```

使用ln命令修改指向

```shell
sudo ln -sf /bin/bash /bin/sh
```

安装完依赖后效果如下

{% asset_img 2_2_requirements.png %}

### 2.3. Building LFS in Stages

先Mark住，重启系统后要恢复的操作