---
title: Hadoop的FileNotFound问题
date: 2017-02-28 16:35:14
urlname: hadoop_file_not_found
tags:
 - Hadoop
---

在实验《Hadoop权威指南 第3版》书本上的例子4-2时发现了一个关于 __FileSystem__ 的问题。例子代码如下：

{% codeblock lang:java %}
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IOUtils;
import org.apache.hadoop.io.compress.CompressionCodec;
import org.apache.hadoop.io.compress.CompressionCodecFactory;

import java.io.InputStream;
import java.io.OutputStream;
import java.net.URI;

public class FileDecompressor {
    public static void main(String[] args) throws Exception {
        String uri = args[0];
        Configuration conf = new Configuration();
        FileSystem fs = FileSystem.get(URI.create(uri), conf);

        Path inputPath = new Path(uri);
        CompressionCodecFactory factory = new CompressionCodecFactory(conf);
        CompressionCodec codec = factory.getCodec(inputPath);
        if (codec == null) {
            System.err.println("No codec found for " + uri);
            System.exit(1);
        }

        String outputUri =
                CompressionCodecFactory.removeSuffix(uri, codec.getDefaultExtension());

        InputStream in = null;
        OutputStream out = null;
        try {
            in = codec.createInputStream(fs.open(inputPath));
            out = fs.create(new Path(outputUri));
            IOUtils.copyBytes(in, out, conf);
        } finally {
            IOUtils.closeStream(in);
            IOUtils.closeStream(out);
        }
    }
}
{% endcodeblock %}

编译好按照使用书本上的命令行运行程序

{% codeblock %}
% hadoop FileDecompressor file.gz
{% endcodeblock %}

运行时抛出了 __java.io.FileNotFoundException__ 异常

{% asset_img 1.png %}

错误说找不到文件 __/user/luowanqian/file.gz__，但是我的 __file.gz__ 并不在这个目录里面，而是在 __/home/luowanqian/Downloads/Temp/MaxTemperature__，如果使用绝对路径作为参数传递给程序也会报错，最终在绝对路径前面加上 __file://__ 后程序就没有报错了。

{% asset_img 2.png %}

初步分析了这个问题，个人认为是程序默认在HDFS中寻找 __file.gz__，而 __file.gz__ 是在本地文件系统中，因此程序要识别这个文件是在本地文件系统中，需要在路径前面添加 __file://__，为了进一步验证我的猜想，我修改了原来的代码。

{% codeblock lang:java %}
FileSystem fs = FileSystem.get(URI.create(uri), conf);
{% endcodeblock %}

修改成

{% codeblock lang:java %}
FileSystem fs = FileSystem.getLocal(conf);
{% endcodeblock %}

再次使用下面命令运行程序

{% codeblock %}
% hadoop FileDecompressor file.gz
{% endcodeblock %}

这个时候程序就正常运行了

{% asset_img 3.png %}

至于程序如何判断文件是在怎么样的文件系统目前还不知道怎么解决，初学Hadoop还是很多地方不清楚。
