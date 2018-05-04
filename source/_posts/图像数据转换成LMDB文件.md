---
title: 图像数据转换成LMDB文件
urlname: images_to_lmdb
date: 2018-04-28 10:54:12
tags:
 - Caffe
---

## 介绍

在使用 Caffe 时，一个经常使用的数据输入来源就是LMDB数据库，通常我们手头的数据是一堆图片，此时需要将图片数据放入到LMDB数据库中。Caffe 有自个的转换程序，是一个用 C++ 编写的程序，需要编译，由于本文主要使用 Python语言，因此使用[3]提供的包来做数据转换。

__Note:__ 相关的代码和图片数据在 [GitHub](https://github.com/luowanqian/CaffeTutorials/tree/master/LMDB)

## LMDB读写

### 数据描述

已有 10 张图片，放在目录 `data` 中，图片文件名列表为：

```
$ ls data | sort -n
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

每张图片都有一个类别，存在文件 `labels.csv`

```
$ cat labels.csv
id,label
1,frog
2,truck
3,truck
4,deer
5,automobile
6,automobile
7,bird
8,horse
9,ship
10,cat
```

文件中第一列是图片文件名 (不包含后缀名)，第二列是图片的类别。

### LMDB写入

```
import numpy as np
import pandas as pd
from skimage import io
import matplotlib.pyplot as plt
import os
import lmdb
import caffe


def make_datum(image, label, channels, height, width):
    datum = caffe.proto.caffe_pb2.Datum()
    datum.channels = channels
    datum.label = int(label)
    datum.height = height
    datum.width = width
    datum.data = image.tobytes()

    return datum


# data path and lmdb path
dataset_path = './data'
label_file = 'labels.csv'
lmdb_path = 'cifar10_lmdb'

# labels mapping
labels_mapping = {'airplane': 0, 'automobile': 1,
                  'bird': 2, 'cat': 3, 'deer': 4, 'dog': 5,
                  'frog': 6, 'horse': 7, 'ship': 8, 'truck': 9}
classes = {}
for key in labels_mapping:
    classes[labels_mapping[key]] = key

# load data
df = pd.read_csv(label_file)
df['label'] = df['label'].map(labels_mapping)
images = list(df.id)
labels = list(df.label)

# write data to LMDB
map_size = 1e6
batch_size = 4

count = 0
lmdb_env = lmdb.open(lmdb_path, map_size=map_size)
lmdb_txn = lmdb_env.begin(write=True)

for image_id, label in zip(images, labels):
    count = count + 1
    image_file = os.path.join(dataset_path, str(image_id) + '.png')
    image = io.imread(image_file)
    height, width, channels = image.shape
    datum = make_datum(image, label, channels, height, width)
    str_id = '{:08}'.format(count)
    lmdb_txn.put(str_id, datum.SerializeToString())

    if count % batch_size == 0:
        lmdb_txn.commit()
        lmdb_txn = lmdb_env.begin(write=True)

lmdb_txn.commit()
lmdb_env.close()
```

### LMDB读取

```
lmdb_env = lmdb.open(lmdb_path)
lmdb_txn = lmdb_env.begin()
lmdb_cursor = lmdb_txn.cursor()
datum = caffe.proto.caffe_pb2.Datum()

count = 0
for key, value in lmdb_cursor:
    datum.ParseFromString(value)
    label = datum.label
    height = datum.height
    width = datum.width
    channels = datum.channels
    data = datum.data
    count = count + 1

    if count == 2:
        image = np.frombuffer(data, dtype=np.uint8)
        image = np.reshape(image, (height, width, channels))
        print('Label: {}, Class: {}'.format(label, classes[label]))
        plt.imshow(image)
        break

print('Number of items: {}'.format(count))

lmdb_env.close()
```

## 参考

1. [Creating an LMDB database in Python](http://deepdish.io/2015/04/28/creating-lmdb-in-python/)
2. [Write mnist image to lmdb with python](https://gist.github.com/twmht/ad0769dde3b1e1d002752207a207e6dc)
3. [lmdb](https://lmdb.readthedocs.io/en/release/)
