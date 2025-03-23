---
title: Diary
date: 2025-03-07 20:46:48
---

# 2025

## 2025.3

| 索引 |  事项 | 加入日期 | 进展 |
| :------: | :------: | :------: | :------ |
| 1 | [YouTuBe: How I use LLMs](https://www.youtube.com/watch?v=EWvNQjAaOHw&t=1088s&ab_channel=AndrejKarpathy) | 2025.3.7 | 2025.3.7 : [00:18:03](https://www.youtube.com/watch?v=EWvNQjAaOHw&t=1083s) Be aware of the model you're using, pricing tiers<br>2025.3.9 : [00:31:00](https://www.youtube.com/watch?v=EWvNQjAaOHw&t=1860s) Tool use: internet search<br>2025.3.16 : [00:50:57](https://www.youtube.com/watch?v=EWvNQjAaOHw&t=3057s) File uploads, adding documents to context<br>2025.3.22 : [01:09:00](https://www.youtube.com/watch?v=EWvNQjAaOHw&t=4140s) Claude Artifacts, apps, diagrams |
| 2 | [Linux From Scratch](https://www.linuxfromscratch.org/lfs/view/stable/index.html) | 2025.3.7 | 2025.3.7 : 实操到2.4节，想办法在Virtual Box中创建一个新的Partition<br>2025.3.9 : 实操到2.6节，已创建一个新的分区`/dev/sdb`<br>2025.3.12 : 已实操完第3节的软件包下载<br>2025.3.15 : 实操到4.3节<br>2025.3.16 : 已实操完第4章内容<br>2025.3.22 : 已实操到5.4节<br>2025.3.23 : 已实操完第5章内容 |
| 3 | [《菜根谭》向古人学谋略与智慧](https://www.youtube.com/playlist?list=PLkWWcTwDGs4bHO8NsL000pBUixU5TT02m) | 2025.3.9 | 2025.3.9 : 第10、12节|
| 4 | 《Linux高性能服务器编程》 | 2025.3.23 | 2025.3.23 : 第5章，进度5/12，5.5 接受连接 |

### 2025.3.7

1. 个人Blog创建了日记页面，记录下日常生活学习工作内容
2. 开始从零构建一个Linux，参考教程Linux From Scratch

### 2025.3.8

1. Hexo插入图片方式变更

    在撰写博文时，发现使用`asset_img`方式插入的图片，在生成的页面会显示不出来，从生成日志中发现图片放置路径是错误的，Bug原因未深入研究，换成官方教程中的embedding方法后图片显示正常（[Embedding an image using markdown](https://hexo.io/docs/asset-folders#Embedding-an-image-using-markdown)）。
    吐槽下Hexo插件，随着版本变更，会出现语法不兼容情况，本次图片显示问题估计是这个原因

2. 发布新博文 “复盘一次SYN_SENT网络问题定位”

### 2025.3.9

1. 已完成LFS教程的分区创建和格式化，通过在VirtualBox新增一个硬盘用于新分区创建
2. 摘抄：
    1) “[世人以心肯处为乐，却被乐心引在苦处；达士以心拂处为乐，终必苦心换得乐来](https://www.8bei8.com/book/caigentan_470.html)” 《菜根谭》

### 2025.3.12

1. LFS教程已实操完第3节，已挂载创建的文件系统以及下载必备的软件包

### 2025.3.15

1. LFS教程已实操到4.3节

### 2025.3.16

1. LFS教程已实操完第4章内容

### 2025.3.22

1. LFS教程已实操到5.4节，完成Binutils、GCC和Linux API Headers的编译安装
2. 学习了下epoll原理，阅读了博文：[深入学习IO多路复用 select/poll/epoll 实现原理](https://www.cnblogs.com/88223100/p/Deeply-learn-the-implementation-principle-of-IO-multiplexing-select_poll_epoll.html)

### 2025.3.23

1. 已实操完LFS教程第5章内容
2. 加入新事项：《Linux高性能服务器编程》书籍阅读，书籍配套代码 [LinuxServerCodes](https://github.com/raichen/LinuxServerCodes)