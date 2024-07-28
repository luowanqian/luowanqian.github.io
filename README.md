# 个人Blog

## 安装配置

### 安装Node.js

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

### Hexo配置

1、Git Clone代码

> $ git clone git@github.com:luowanqian/luowanqian.github.io.git blog

2、安装Hexo

局部安装Hexo，安装在`node_modules`目录中

```
$ cd blog
$ npm install 
```

3、生成Pages

```
$ npx hexo generate
```

如果要本地查看

```
$ npx hexo server
```

4、部署

部署前确认Github的Pages配置是Deploy from a branch。设置方法：

1. 前往 `Settings > Pages > Source`，将 `source` 更改为 `Deploy from a branch`
2. `branch` 选择_config.yml中配置的分支，这里是gh-pages

部署命令

```
$ npx hexo deploy
```
