---
title: VGG网络实现
urlname: vgg_network_implementation
date: 2018-04-30 15:39:03
tags:
 - 深度学习
 - PyTorch
---

## 网络结构

关于 VGG 的详细内容，可以去看论文 [Very Deep Convolutional Networks for Large-Scale Image Recognition](https://arxiv.org/abs/1409.1556)，这里贴出网络结构图。

{% asset_img vgg.png 600 %}

卷积层表示为 `conv<receptive field size>-<number of channels>`，卷积步长 (stride) 为 1，填充 (padding) 大小为 1，Pooling层的窗口大小为 2x2，步长 (stride) 为 2。为了显示简洁，图中未显示ReLU层。从图中可以看出网络输入的图片的大小为 224x224x3，经过每一层后，大小变化如下所示 (以下为VGG16网络，也就是图中的网络 D)

| 网络层    | 大小       |
| :--------- | :---------- |
| 输入层    | 224x224x3  |
| conv3-64 | 224x224x64 |
| conv3-64  | 224x224x64 |
| maxpool | 112x112x64 |
| conv3-128 | 112x112x128 |
| conv3-128 | 112x112x128 |
| maxpool | 56x56x128 |
| conv3-256 | 56x56x256 |
| conv3-256 | 56x56x256 |
| conv3-256 | 56x56x256 |
| maxpool | 28x28x256 |
| conv3-512 | 28x28x512 |
| conv3-512 | 28x28x512 |
| conv3-512 | 28x28x512 |
| maxpool | 14x14x512 |
| conv3-512 | 14x14x512 |
| conv3-512 | 14x14x512 |
| conv3-512 | 14x14x512 |
| maxpool | 7x7x512 |
| FC | 1x1x4096 |
| FC | 1x1x4096 |
| FC | 1x1x1000|

## PyTorch实现

VGG 网络的 PyTorch 实现可以在 [vgg.py](https://github.com/pytorch/vision/blob/master/torchvision/models/vgg.py) 中找到，里面实现了网络 `A, B, D, E` 即 VGG11, VGG13, VGG16 以及 VGG19，同时还有相对应的 Batch Normalization 版本。如果要将 VGG 网络应用到其他大小输入的图片，主要修改的参数就是最后几个全连接层的大小即可，也就是只用修改类 VGG 中 `classifier` 属性即可

```
class VGG(nn.Module):
	def __init__(self, features, num_classes=1000, init_weights=True):
        super(VGG, self).__init__()
        self.features = features
        self.classifier = nn.Sequential(
            nn.Linear(512 * 7 * 7, 4096),
            nn.ReLU(True),
            nn.Dropout(),
            nn.Linear(4096, 4096),
            nn.ReLU(True),
            nn.Dropout(),
            nn.Linear(4096, num_classes),
        )
        if init_weights:
            self._initialize_weights()
```

如果输入图片来自于CIFAR-10数据集，即大小为 32x32，类别数为10，经过网络的最后一个Pooling层后，输出大小为 1x1x512，因此修改类的 `classifier` 属性为

```
self.classifier = nn.Sequential(
    nn.Linear(512 * 1 * 1, 512),
    nn.ReLU(True),
    nn.Dropout(),
    nn.Linear(512, 512),
    nn.ReLU(True),
    nn.Dropout(),
    nn.Linear(512, num_classes),
)
```

同时在使用函数生成对应版本的网络时，设置函数参数 `num_classes=10`。

## 参考

1. [VGG论文翻译——中文版](http://noahsnail.com/2017/08/17/2017-8-17-VGG%E8%AE%BA%E6%96%87%E7%BF%BB%E8%AF%91%E2%80%94%E2%80%94%E4%B8%AD%E6%96%87%E7%89%88/)
2. [Pytorch实现的Vgg网络](https://guanfuchen.github.io/post/markdown_blog_ws/markdown_blog_2017_11/pytorch%E5%AE%9E%E7%8E%B0%E7%9A%84vgg%E7%BD%91%E7%BB%9C/)
