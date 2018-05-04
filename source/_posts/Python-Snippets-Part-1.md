---
title: Python Snippets Part 1
urlname: python_snippets_part1
date: 2018-05-02 22:57:40
tags:
 - Python
 - 编程语言
---

## Introduction

Python 代码片段

## 判断文件或目录是否存在

创建一个目录和一个文件

```shell
$ mkdir dir1 && touch file1.txt
$ ls -l
total 0
drwxr-xr-x  2 luowanqian  wheel  68  5  2 23:21 dir1
-rw-r--r--  1 luowanqian  wheel   0  5  2 23:21 file1.txt
```

使用 `os.path.exists()` 可以判断文件或目录是否存在，但是不能判断是该路径是一个文件还是目录，要进一步判断，需要使用 `os.path.isfile()`，如果该路径是一个文件，则返回 True，否则返回 False，当然，也可以直接使用 `os.path.isfile()` 判断文件是否存在。测试代码如下：

```python
>>> import os
>>> os.path.exists('dir1')
True
>>> os.path.exists('file1.txt')
True
>>> os.path.isfile('dir1')
False
>>> os.path.isfile('file1.txt')
True

>>> os.path.exists('dir2')
False
>>> os.path.exists('file2.txt')
False
>>> os.path.isfile('dir2')
False
>>> os.path.isfile('file2.txt')
False
```

目录 `dir2` 和文件 `file2.txt` 均不存在，所以函数 `os.path.exists()` 和 `os.path.isfile()` 均返回 False。
