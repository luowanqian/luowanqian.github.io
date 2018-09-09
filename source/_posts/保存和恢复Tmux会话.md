---
title: 保存和恢复Tmux会话
urlname: tmux_session_save_restore
date: 2018-09-09 22:14:38
tags:
  - Tmux
---

## 问题

使用Tmux时经常会遇到的一个问题，那就是系统重启时，Session会被清除，每次打开电脑都要重启建一个Session，然后创建一堆Window以及Pane，这极大地降低了Tmux使用效率，因此需要想一个办法能够保存Tmux的Session。

## 解决方案

该问题的解决方案是安装一个叫`Tmux Resurrect`的插件。Tmux要安装插件，可以通过`Tmux Plugin Manager`这个插件进行安装，该插件相当于一个插件管理系统，可以快速地安装、更新以及删除插件。

### Tmux Plugin Manager

安装`Tmux Plugin Manager`插件可以参考该插件的GitHub：[tmux-plugins/tpm](https://github.com/tmux-plugins/tpm)。安装很简单，首先clone插件到本地

```shell
$ git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

然后修改`.tmux.conf`，在文件最底部添加以下内容

```
# List of plugins
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'

# Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)
run '~/.tmux/plugins/tpm/tpm'
```

重新加载配置文件

```shell
# type this in terminal if tmux is already running
$ tmux source ~/.tmux.conf
```

然后就可以在Tmux中使用快捷`prefix + I`(注意这里的`I`是大写)安装配置文件`.tmux.conf`中定义的插件了。

__补充：__

如果Tmux安装了[gpakosz/.tmux](https://github.com/gpakosz/.tmux)，在文件`.tmux.conf.local`中配置好`Tmux Plugin Manager`后，在Tmux中使用`prefix + I`安装插件会没有效果，相关的讨论见[“run '~/.tmux/plugins/tpm/tpm'” has no effect in .tmux.conf.local #61](https://github.com/gpakosz/.tmux/issues/61)，具体的解决方案是用另一种写法写`set -g @plugin`，即将

```
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
```

改写为

```
set -g @tpm_plugins '          \
  tmux-plugins/tpm             \
  tmux-plugins/tmux-sensible
'
```

解决方案来源于：[Help, tpm not working!](https://github.com/tmux-plugins/tpm/blob/master/docs/tpm_not_working.md)

## Tmux Resurrect

安装好`Tmux Plugin Manager`后，就可以安装`Tmux Resurrect`插件([tmux-plugins/tmux-resurrect](https://github.com/tmux-plugins/tmux-resurrect))了。在Tmux配置文件`.tmux.conf`中添加

```
set -g @plugin 'tmux-plugins/tmux-resurrect'
```

然后使用快捷键`prefix + I`就可以安装插件了。

## 保存和恢复

安装完`Tmux Plugin Manager`，就可以使用下面快捷键保存和恢复Session了。

* `prefix + Ctrl-s` - save
* `prefix + Ctrl-r` - restore

平常重启电脑后，首先在终端打开Tmux，然后使用`prefix + Ctrl-r`就可以恢复保存的Session了，如果有多个Session可以使用`prefix + s`来选择Session。

## 参考

1. [保存和恢复 Tmux 会话](https://liam0205.me/2016/09/10/tmux-plugin-resurrect/)