---
title: Hadoop的Connection Refused问题
date: 2017-02-24 17:09:04
urlname: hadoop_connection_refused
tags:
 - Hadoop
---
最近在学习Hadoop时候遇到了一个问题，就是操作Hadoop文件系统时遇到 __Connection Refused__ 问题，当时问题显示如下：

{% asset_img 1.png %}

遇到这问题时，首先查看了错误信息提示中提到的网址 [https://wiki.apache.org/hadoop/ConnectionRefused][1]，文章中提示说是端口问题，但是经过排查不是端口问题，所以只能排查其他原因了。首先使用Java的 __jps__ 命令查看JVM，查看结果如下：

{% asset_img 2.png %}

发现没有NameNode，然后查看了Hadoop关于NameNode日志（日志文件默认在Hadoop安装目录中的 logs 目录下），发现了一些东西：

{% asset_img 3.png %}

日志说 __/tmp/hadoop-luowanqian/dfs/name__ 目录不存在，这个目录是HDFS文件系统的镜像文件保存的地方，HDFS文件系统格式化后其镜像文件默认保存在 __tmp__ 目录中，而这个目录中的文件在重启系统后会自动被删除，因此访问HDFS文件系统会失败，重新格式化HDFS文件系统后再访问文件系统正常了。

{% asset_img 4.png %}

[1]: https://wiki.apache.org/hadoop/ConnectionRefused
