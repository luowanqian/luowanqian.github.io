---
title: Hexo搭建个人Blog
date: 2024-07-28 12:47:05
tags:
 - Web
---

想在Github上搭建一个简易的个人Blog，Hexo是一个不错的选择。Hexo是一个快速、简洁的博客框架，使用Markdown解析文章，在几秒内就可以生成静态网页，然后就可以部署在Github上生成个人网站。

## 1. 环境

本次部署Hexo的环境为：

* Linux环境：基于WSL2部署的Linux
* Linux版本：Ubuntu 20.04

## 2. 安装

Hexo的安装需要git和node.js

### 2.1 Node.js

去Node.js官网下载Node.js安装包（见 [下载Node.js](https://nodejs.org/zh-cn/download/package-manager)），这里选择20.16.0版本进行安装

```
# layouts.download.codeBox.installsFnm
curl -fsSL https://fnm.vercel.app/install | bash

# layouts.download.codeBox.activateFNM
source ~/.bashrc

# layouts.download.codeBox.downloadAndInstallNodejs
fnm use --install-if-missing 20

# layouts.download.codeBox.verifiesRightNodejsVersion
node -v # layouts.download.codeBox.shouldPrint

# layouts.download.codeBox.verifiesRightNpmVersion
npm -v # layouts.download.codeBox.shouldPrint
```

### 2.2 Git

使用以下apt命令安装git

```
$ sudo apt-get install git-core
```

### 2.3 Hexo

必备的git和node.js安装完毕后，即可使用npm安装Hexo

```
$ npm install -g hexo-cli
```

## 3. Github仓

Hexo生成的静态网页需要托管到Github上，因此需要创建一个Github仓库。在Github创建一个名为 `username.github.io` 的仓库，其中 `username` 是Github的个人账号名

博主自己的仓为 `luowanqian.github.io`，博文后面Github地址都为该仓地址，实操时替换成自己代码仓名即可

## 4. 建站

### 4.1 Hexo初始化

安装完Hexo后，需要用Hexo命令在指定文件夹中新建所需要的文件

```
$ mkdir -p luowanqian.github.io
$ hexo init luowanqian.github.io
$ cd luowanqian.github.io
$ npm install
```

初始化后，项目文件夹中大概有以下文件：

```
├── _config.landscape.yml
├── _config.yml
├── node_modules
├── package-lock.json
├── package.json
├── scaffolds
│   ├── draft.md
│   ├── page.md
│   └── post.md
├── source
│   └── _posts
└── themes
```

### 4.2 关联Github仓

在项目文件夹中关联Github仓

```
$ git init
$ git remote add origin git@github.com:luowanqian/luowanqian.github.io.git
```

配置`.gitignore`（如果没有该文件，创建一个），设置以下内容：

```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
_multiconfig.yml
package-lock.json
```

这里排除了`package-lock.json`，因为这是npm安装包时生成的文件

### 4.3 部署

Hexo初始化生成的文件，已经可以部署到Github上生成个人网站了。Hexo支持多种方式部署（见 [一键部署](https://hexo.io/zh-cn/docs/one-command-deployment)），这里选择使用Git方式部署。

1、Git方式部署需要安装 `hexo-deployer-git` 插件，在Hexo项目文件夹中使用npm命令安装

```
$ npm install hexo-deployer-git --save
```

命令参数`--save`作用是将插件信息写入到项目文件夹中`package.json`，下次可以使用`npm install`安装这些插件（类似python的`requirements.txt`）

2、部署前需要修改`_config.yml`配置文件，配置git仓信息，找到`deploy`项，修改成以下内容：

```
deploy:
  type: git
  repo: git@github.com:luowanqian/luowanqian.github.io.git
  branch: gh-pages
```

其中`repo`为前面创建的Github仓地址，`branch`为Github Pages分支，存放的是Hexo生成的静态网页


3、在Github仓页面将Pages配置成Deploy from a branch。设置方法：

* 前往 `Settings > Pages > Source`，将 `source` 更改为 `Deploy from a branch`
* `branch` 选择_config.yml中配置的分支，这里是gh-pages

4、最后使用以下命令进行部署

```
$ hexo deploy -g
```