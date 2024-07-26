---
title: Java读取PGM图片
date: 2017-03-11 16:20:24
urlname: java_read_pgm
tags:
 - Java
---

最近下载了一个人脸数据库，里面的图片都是pgm格式，想提取每张图片的所有像素的像素值，初步设想是使用Java SE中读取图片。在使用Java SE中类来读取图片时，通常使用ImageIO包中的类来读取图片，但是有个缺点就是默认支持的图片格式很少，Java 8中javax.imageio支持的图片格式有：

{% asset_img 1.png %}

从图中看出库仅仅支持JPEG、PNG、BMP、WBMP以及GIF这些图片格式。如果想读取其他格式（例如PGM）时应该怎么办？网上搜索到了一个Java ImageIO的扩展插件——[TwelveMonkeys][1]，安装后ImageIO就可以支持多种图片格式，这个有一个好处就是原先读取图片的代码不需要改变，读取图片还是使用 __javax.imageio__ 包。

## 安装TwelveMonkeys

安装很简单，使用Maven可以很容易地添加到项目中，如果想让ImageIO支持JPEG和TIFF格式，可以在POM文件中添加下面的代码：

{% codeblock %}
...
<dependencies>
    ...
    <dependency>
        <groupId>com.twelvemonkeys.imageio</groupId>
        <artifactId>imageio-jpeg</artifactId>
        <version>3.3.1</version> <!-- Alternatively, build your own version -->
    </dependency>
    <dependency>
        <groupId>com.twelvemonkeys.imageio</groupId>
        <artifactId>imageio-tiff</artifactId>
        <version>3.3.1</version> <!-- Alternatively, build your own version -->
    </dependency>
</dependencies>
{% endcodeblock %}

如果要支持PGM格式，可以添加下面代码：

{% codeblock %}
<dependency>
    <groupId>com.twelvemonkeys.imageio</groupId>
    <artifactId>imageio-pnm</artifactId>
    <version>3.3.1</version>
</dependency>
{% endcodeblock %}

想要支持什么格式唯一要变的地方就是`artifactId`中内容，基本模版就是`imageio-xxx`。

## 读取PGM图片

读取一张PGM图片，然后将图片的所有像素的像素值输出到一个文本文件，实现代码如下：

### 灰度图片

{% codeblock %}
package Image;

import javax.imageio.ImageIO;
import java.awt.image.BufferedImage;
import java.awt.image.Raster;
import java.io.*;

public class PGM {
    public static void main(String[] args) throws IOException {
        // Grayscale image
        String path = "/Users/luowanqian/Downloads/Zach_Pillar_0001.pgm";
        String output = "/Users/luowanqian/Downloads/output.txt";

        BufferedImage image = ImageIO.read(new File(path));
        Raster source = image.getRaster();
        int width = image.getWidth();
        int height = image.getHeight();

        int[] pixels = new int[width*height];
        source.getPixels(0, 0, width, height, pixels);

        BufferedWriter out = new BufferedWriter(new FileWriter(output));
        for (int i=0; i<width*height; i++)
            out.write(pixels[i] + " ");
        out.close();
    }
}
{% endcodeblock %}

[1]: https://github.com/haraldk/TwelveMonkeys
