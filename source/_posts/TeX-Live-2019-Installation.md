---
title: TeX Live 2019 Installation
urlname: tex_live_2019_installation
date: 2020-01-28 21:32:26
list_number: false
tags:
  - Tex Live
  - Software Installation
---

# 1. 安装环境

OS Version: OS X 10.11.6

# 2. 获取安装脚本

这里没有选择 [MacTex](http://www.tug.org/mactex/index.html) 安装方式，而是采用 [Unix Install Script](https://www.tug.org/texlive/acquire-netinstall.html) 进行在线安装。之所以不用 `MaxTex` 是由于电脑系统版本有点老，MacTeX-2019 需要 Mac OS 10.12 以上版本的系统。

下载 [install-tl-unx.tar.gz](http://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz) 安装脚本，解压后，得到以下文件。

{% asset_img 1.png 500 %}

安装脚本为 `install-tl`

# 3. 开始安装

启动安装只需要执行 `install-tl` 即可。这里直接在控制台执行安装脚本。

```bash
./install-tl-20200127/install-tl
```

{% asset_img 2.png 500 %}

# 4. 安装配置

## 4.1. 配置镜像地址

安装第一步是配置镜像地址，具体镜像的选择根据所处网络环境进行决定，这里选择了清华大学的镜像地址。

{% asset_img 3.png 700 %}

选择完镜像后，安装界面会显示切换后的镜像地址。

{% asset_img 4.png 500 %}

## 4.2. 详细配置

接下来是安装配置，简易设置仅仅设置安装路径以及默认纸张。如果要非root安装Tex Live，安装路径设为用户目录即可，这里设置安装路径为：

```
/Users/luowanqian/local/texlive/2019
```

{% asset_img 5.png 500 %}

如果要更加详细的配置，点 `Advanced` 按钮即可。

{% asset_img 6.png 800 %}

配置完后点 `安装` 启动安装。

## 4.3. 等待安装完毕

启动安装后，安装程序会自动下载包进行安装。这部分时间消耗较长，耐心等待即可。

{% asset_img 7.png 500 %}

安装完毕会显示如下图。

{% asset_img 8.png 500 %}

__注意__：安装完毕后，界面（见上图）会提示相关环境变量的设置，内容大概如下。

```
欢迎进入 TeX Live 的世界！

See /Users/luowanqian/local/texlive/2019/index.html for links to documentation.
The TeX Live web site (https://tug.org/texlive/) contains any updates and corrections. TeX Live is a joint project of the TeX user groups around the world; please consider supporting it by joining the group best for you. The list of groups is available on the web at https://tug.org/usergroups.html.

Add /Users/luowanqian/local/texlive/2019/texmf-dist/doc/man to MANPATH.
Add /Users/luowanqian/local/texlive/2019/texmf-dist/doc/info to INFOPATH.
Most importantly, add /Users/luowanqian/local/texlive/2019/bin/x86_64-darwinlegacy
to your PATH for current and future sessions.
```

# 5. 环境变量设置

安装最后一步是设置环境变量，由前面提示可知，要设置的环境变量有三个：`MANPATH`、`INFOPATH` 以及 `PATH`。

根据提示在 `.bashrc` 文件中设置环境变量即可完成安装。

```bash
export MANPATH="$MANPATH:/Users/luowanqian/local/texlive/2019/texmf-dist/doc/man"
export INFOPATH="$INFOPATH:/Users/luowanqian/local/texlive/2019/texmf-dist/doc/info"
export PATH="$PATH:/Users/luowanqian/local/texlive/2019/bin/x86_64-darwinlegacy"
```

# Reference

1: [TeX Live - Quick install](https://www.tug.org/texlive/quickinstall.html)

2: [Unix Install of TeXLive 2019](http://www.tug.org/mactex/mactex-unix-download.html)