---
title: Java获取指定目录下的所有文件
date: 2017-03-11 19:50:27
urlname: java_list_files
tags:
 - Java
---

本文介绍了如何使用Java来获取指定目录下的所有文件。

## 获取指定目录下的所有文件

使用递归方法遍历目录，如果访问的是文件夹，则进入递归，否则将文件加入到文件列表中，同时可以设定遍历深度（从深度1开始）。

```
import java.io.File;
import java.util.ArrayList;

public class ListFiles {

    /**
     * 获取指定目录下的所有文件（不包括文件夹)
     * @param path 文件夹的File对象或者文件夹路径字符串
     * @param depth 遍历深度，从深度1开始
     * @return 指定目录下所有的文件的File对象的集合
     */
    public static ArrayList<File> getAllFiles(Object path, int depth) {
        File directory = null;
        if (path instanceof File) {
            directory = (File)path;
        } else {
            directory = new File(path.toString());
        }

        ArrayList<File> files = new ArrayList<File>();
        if (directory.isFile()) {
            files.add(directory);
        } else if (directory.isDirectory()) {
            depth--;
            if (depth >= 0) {
                File[] fileList = directory.listFiles();
                for (File file : fileList) {
                    files.addAll(getAllFiles(file, depth));
                }
            }
        }

        return files;
    }
}
```

测试代码如下：

```
import org.junit.Test;

import java.io.File;
import java.util.ArrayList;

public class ListFilesTest {
    @Test
    public void getAllFiles() throws Exception {
        String path = "/Users/luowanqian/Documents/Datasets/LFWCrop/lfwcrop_grey/faces";

        ArrayList<File> files = ListFiles.getAllFiles(path, 1);
        int n = 0;
        for (File file : files) {
            n++;
            System.out.println(file.toString());
        }
        System.out.println("Total : " + n);
    }

}
```

运行结果如下：

```
...
/Users/luowanqian/Documents/Datasets/LFWCrop/lfwcrop_grey/faces/Zoran_Djindjic_0004.pgm
/Users/luowanqian/Documents/Datasets/LFWCrop/lfwcrop_grey/faces/Zorica_Radovic_0001.pgm
/Users/luowanqian/Documents/Datasets/LFWCrop/lfwcrop_grey/faces/Zulfiqar_Ahmed_0001.pgm
/Users/luowanqian/Documents/Datasets/LFWCrop/lfwcrop_grey/faces/Zumrati_Juma_0001.pgm
/Users/luowanqian/Documents/Datasets/LFWCrop/lfwcrop_grey/faces/Zurab_Tsereteli_0001.pgm
/Users/luowanqian/Documents/Datasets/LFWCrop/lfwcrop_grey/faces/Zydrunas_Ilgauskas_0001.pgm
Total : 13233
```

## Reference

1. [http://hw1287789687.iteye.com/blog/1946488][1]
2. [http://blog.csdn.net/u013457382/article/details/51015728][2]

[1]: http://hw1287789687.iteye.com/blog/1946488
[2]: http://blog.csdn.net/u013457382/article/details/51015728
