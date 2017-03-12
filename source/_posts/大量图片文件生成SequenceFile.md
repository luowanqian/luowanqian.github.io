---
title: 大量图片文件生成SequenceFile
date: 2017-03-12 09:38:34
tags:
 - Hadoop
---

本文主要介绍如何将大量图片数据集合起来，然后生成一个SequenceFile，这么做的原因是避免Hadoop处理大量小文件导致性能下降。SequenceFile的key-value内容以及数据类型为：

```
key : 储存图片文件名，数据类型为Text
value : 储存图片数据，数据类型为ArrayPrimitiveWritable
```

## 生成SequenceFile

生成操作涉及遍历目录 (参考:{% post_link Java获取指定目录下的所有文件%}) 以及读取图片像素 (参考:{% post_link Java读取PGM图片%})。图片文件为：

{% asset_img 1.png %}

```
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.ArrayPrimitiveWritable;
import org.apache.hadoop.io.IOUtils;
import org.apache.hadoop.io.SequenceFile;
import org.apache.hadoop.io.Text;

import javax.imageio.ImageIO;
import java.awt.image.BufferedImage;
import java.awt.image.Raster;
import java.io.File;
import java.io.IOException;
import java.util.ArrayList;

public class WriteImagesToSequenceFile {
    public static void main(String[] args) throws IOException {
        String imagesPath = "/tmp/images";
        String seqPath = "/tmp/images.seq";

        Configuration conf = new Configuration();
        FileSystem fs = FileSystem.get(conf);
        Path path = new Path(seqPath);

        Text key = new Text();
        ArrayPrimitiveWritable value = new ArrayPrimitiveWritable();
        SequenceFile.Writer writer = null;

        try {
            BufferedImage image = null;
            int[] pixels = null;
            Raster source = null;
            int width = 0;
            int height = 0;

            writer = SequenceFile.createWriter(fs, conf, path,
                    key.getClass(), value.getClass());

            ArrayList<File> files = ListFiles.getAllFiles(imagesPath, 1);
            for (File file : files) {
                System.out.println("Processing " + file.getName());

                image = ImageIO.read(file);
                source = image.getRaster();
                width = image.getWidth();
                height = image.getHeight();
                pixels = new int[width*height];
                source.getPixels(0, 0, width, height, pixels);

                key.set(file.getName());
                value.set(pixels);

                writer.append(key, value);
            }
        } finally {
            IOUtils.closeStream(writer);
        }

    }
}
```

## 读取SequenceFile

使用Hadoop命令来读取SequenceFile内容，显示如下：

{% asset_img 2.png %}

图片内容没有显示出来，只是显示了`org.apache.hadoop.io.ArrayPrimitiveWritable@17497425`，要显示具体内容使用下面代码：

```
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.ArrayPrimitiveWritable;
import org.apache.hadoop.io.IOUtils;
import org.apache.hadoop.io.SequenceFile;
import org.apache.hadoop.io.Writable;
import org.apache.hadoop.util.ReflectionUtils;

import java.io.IOException;
import java.net.URI;

public class SequenceFileReadDemo {
    public static void main(String[] args) throws IOException {
        String uri = "/tmp/images.seq";
        Configuration conf = new Configuration();
        FileSystem fs = FileSystem.get(URI.create(uri), conf);
        Path path = new Path(uri);

        SequenceFile.Reader reader = null;
        try {
            reader = new SequenceFile.Reader(fs, path, conf);
            Writable key = (Writable)
                    ReflectionUtils.newInstance(reader.getKeyClass(), conf);
            Writable value = (Writable)
                    ReflectionUtils.newInstance(reader.getValueClass(), conf);
            while (reader.next(key, value)) {
                int[] pixels = (int[])((ArrayPrimitiveWritable)value).get();
                System.out.printf("%s\t:\t", key);
                for (int i=0; i<10; i++)
                    System.out.printf("%d ", pixels[i]);
                System.out.println();
            }
        } finally {
            IOUtils.closeStream(reader);
        }
    }
}
```

读取图片的第一行的前十个像素，显示如下：

{% asset_img 3.png %}

## Reference

1. [https://github.com/kmicinski/hadoop-class-project/blob/master/src/main/phase2/WriteImagesToSequenceFile.java][1]

[1]: https://github.com/kmicinski/hadoop-class-project/blob/master/src/main/phase2/WriteImagesToSequenceFile.java
