---
title: Shell Snippets Part 1
urlname: shell_snippets_part1
date: 2018-04-23 12:40:43
tags:
 - Shell
 - 编程语言
---

## Introduction

Shell Snippets

## 列出目录里面文件名并进行排序

在目录 `train` 里面有大量的图片 (扩展名为 png)

```
$ ls -l train/ | head
total 400000
-rw-r--r--@ 1 luowanqian  staff  2455 10 19  2013 1.png
-rw-r--r--@ 1 luowanqian  staff  2101 10 19  2013 10.png
-rw-r--r--@ 1 luowanqian  staff  2466 10 19  2013 100.png
-rw-r--r--@ 1 luowanqian  staff  2171 10 19  2013 1000.png
-rw-r--r--@ 1 luowanqian  staff  2031 10 19  2013 10000.png
-rw-r--r--@ 1 luowanqian  staff  2051 10 19  2013 10001.png
-rw-r--r--@ 1 luowanqian  staff  2317 10 19  2013 10002.png
-rw-r--r--@ 1 luowanqian  staff  2511 10 19  2013 10003.png
-rw-r--r--@ 1 luowanqian  staff  2460 10 19  2013 10004.png
```

提取该目录下的所有图片的文件名并进行排序

### 方案1

```
$ ls -l train/ | grep ".png" | tr -s ' ' | cut -d ' ' -f 9 | sort -n > list.txt
$ head list.txt
1.png
2.png
3.png
4.png
5.png
6.png
7.png
8.png
9.png
10.png
```

其中

* `tr -s ' '` 是为了将多个space压缩成一个space
* `cut -d ' ' -f 9` 是为了取出最后一列的文件名
* `sort -n` 是将文件进行排序，由于文件名里面有数字，所以用`-n`选项


### 方案2

```
$ ls train/ | sort -n > list.txt
$ head list.txt
1.png
2.png
3.png
4.png
5.png
6.png
7.png
8.png
9.png
10.png
```

## pip升级所有包

pip 升级所有Python包，命令如下

```shell
pip3 list --outdated --format=freeze | cut -d = -f 1 | xargs pip3 install -U'
```

其中 `pip3` 可以根据 pip 版本替换。

## rsync断点续传

主要使用的是 rsync 的 `-P` 的选项，传输命令写为

```
$ rsync -P ubuntu/ubuntu-16.04.4-desktop-amd64.iso lab217server:/home/luowanqian/Downloads/
```

如果传输过程中网络中断或者使用了 `Ctrl + C`，此时可以再次使用该命令进行断点续传。

__参考：__

1. [How To Resume Partially Transferred Files Over SSH Using Rsync](https://www.ostechnix.com/how-to-resume-partially-downloaded-or-transferred-files-using-rsync/)
