---
title: Non-Maximum Suppression
urlname: non_maximum_suppression
date: 2018-04-25 22:03:48
tags:
 - 机器学习
---

## 介绍

Non-Maximum Suppression，简称NMS，在计算视觉领域有着非常重要的应用，主要应用在冗余的检测框的去除，例如在人脸检测应用中，检测出多个人脸框，此时需要去除冗余的检测框，保留最好的一个，还有在目标检测算法中会遇到一个物体有多个检测框，此时也需要去除冗余的检测框。

## 原理

利用参考[1]中提供的例子来简单阐述算法的流程。已知图片中有一辆汽车

{% asset_img car.png 500 %}

目标检测算法定位该汽车的位置时找出了一堆检测框，此时我们需要去除冗余的检测框。假设目标算法找到了6个检测框，而且算法还提供了每个框中内容属于汽车的概率或者得分 (在RCNN中，使用SVM计算检测框属于该类别的得分)，NMS方法首先根据得分大小对检测框进行排序，假设从小到大的排序为 `A, B, C, D, E, F`。

1. 从最大得分的检测框 `F` 开始，分别判断 `A~E` 与 `F` 的重叠度IoU是否大于某个设定的阈值。

2. 假设检测框 `B、D` 与 `F`  的重叠度超过阈值，那么就抛弃 `B、D`，并将检测框 `F` 标记为要保留的检测框。

3. 第2步去掉 `B` 和 `D` 后，剩余检测框 `A、C、E`，接着在剩下的检测框中选择得分最大检测框 `E`，然后判断 `E` 和 `A、C` 的重叠度，如果重叠度大于设定阈值，那就抛弃该检测框，否则留到下一轮的筛选过程，并将检测框 `E` 标记为要保留的检测框。

重复步骤 3，直到剩余待筛选的框个数为 0。

## 实现

关于单类别的NMS的实现，网上已经有实现好的，这里贴出 Ross Girshick (RCNN提出者) 写的Python实现 [py_cpu_nms.py](https://github.com/rbgirshick/py-faster-rcnn/blob/master/lib/nms/py_cpu_nms.py)，并且加了个人的标注。

```
import numpy as np


def py_cpu_nms(dets, thresh):
    """Pure Python NMS baseline."""
    x1 = dets[:, 0]
    y1 = dets[:, 1]
    x2 = dets[:, 2]
    y2 = dets[:, 3]
    scores = dets[:, 4]

    # 每个框 (bounding box) 的面积
    areas = (x2 - x1 + 1) * (y2 - y1 + 1)
    # 根据得分 (score) 的大小进行降序排序
    order = scores.argsort()[::-1]

    keep = []
    while order.size > 0:
        # 保留剩余框中得分最高的那个
        i = order[0]
        keep.append(i)

        # 计算相交区域位置，左上以及右下
        xx1 = np.maximum(x1[i], x1[order[1:]])
        yy1 = np.maximum(y1[i], y1[order[1:]])
        xx2 = np.minimum(x2[i], x2[order[1:]])
        yy2 = np.minimum(y2[i], y2[order[1:]])

        # 计算相交区域面积
        w = np.maximum(0.0, xx2 - xx1 + 1)
        h = np.maximum(0.0, yy2 - yy1 + 1)

        # 计算IoU，即 重叠面积 / (框1面积 + 框2面积 - 重叠面积)
        inter = w * h
        ovr = inter / (areas[i] + areas[order[1:]] - inter)

        # 保留IoU小于阈值的框
        inds = np.where(ovr <= thresh)[0]

        # 因为ovr数组的长度比order长度小1，所以这里要将所有下标后移一位
        order = order[inds + 1]

    return keep
```

关于该代码的使用，我写了一个简单的测试脚本，在 [GitHub](https://github.com/luowanqian/MachineLearning/tree/master/Non-MaximumSuppression)，测试了一张图片，实现效果如下：

{% asset_img nms.png 500 %}

## 参考

1. [非极大值抑制（Non-Maximum Suppression，NMS）](http://www.cnblogs.com/makefile/p/nms.html)
