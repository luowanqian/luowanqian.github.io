---
title: Hadoop Streaming使用
date: 2017-03-07 16:44:28
urlname: hadoop_streaming_usage
tags:
 - Hadoop
---

关于 __Hadoop Streaming__ 的使用，可以参考官方网站 [Hadoop Streaming][1]。个人觉得使用Steaming这个功能，可以使用其他语言来编写MapReduce程序，相比使用Java来编写程序，工作量小了很多。

根据《Hadoop权威指南》中例子使用python来实现 __Max Temperature__ 这个程序，要分别实现Map函数以及Reduce函数。

### Map函数

{% codeblock lang:python %}
#!/usr/bin/env python

import re
import sys

for line in sys.stdin:
    val = line.strip()
    (year, temp, q) = (val[15:19], val[87:92], val[92:93])
    if (temp != "+9999" and re.match("[01459]", q)):
        print "%s\t%s" % (year, temp)
{% endcodeblock %}

### Reduce函数

{% codeblock lang:python %}
#!/usr/bin/env python

import sys

(last_key, max_val) = (None, -sys.maxint)
for line in sys.stdin:
    (key, val) = line.strip().split("\t")
    if last_key and last_key != key:
        print "%s\t%s" % (last_key, max_val)
        (last_key, max_val) = (key, int(val))
    else:
        (last_key, max_val) = (key, max(max_val, int(val)))

if last_key:
    print "%s\t%s" % (last_key, max_val)
{% endcodeblock %}

本文用到的文件如下：

{% asset_img 1.png %}

其中 `1901.txt`为数据文件，`max_temperature_map.py`定义了Map函数，`max_temperature_reduce.py`定义了Reduce函数

定义好Map函数以及Reduce函数后，利用unix的管道将Map过程以及Reduce过程连接起来，使用下面命令

```
$ cat 1901.txt | ./max_temperature_map.py | sort | ./max_temperature_reduce.py
```

运行结果如下：

{% asset_img 2.png %}

运行结果正确，此时就可以使用Hadoop的Streaming了，使用下面命令

```
$ hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-2.7.3.jar \
    -fs file:///  \
    -input 1901.txt \
    -output output \
    -mapper max_temperature_map.py \
    -reducer max_temperature_reduce.py
```

注意：如果文件是在本地文件系统中而不是HDFS中时，要使用 `-fs file:///` 这个命令选项

运行结果保存到 `output` 文件夹中

{% asset_img 3.png %}

[1]: http://hadoop.apache.org/docs/r2.7.3/hadoop-streaming/HadoopStreaming.html
