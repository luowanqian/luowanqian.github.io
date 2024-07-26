---
title: 基于Docker的Caffe环境搭建
urlname: docker_caffe
date: 2018-04-07 23:16:50
tags:
 - Docker
 - Caffe
---

## 介绍

本文主要介绍如何利用Docker搭建Caffe的开发环境，内容分两部分，第一部分是CPU模式环境搭建，第二部分是GPU模式环境搭建。

## 准备工作

由于本文需要用到Docker的，所以要先装好Docker，这个教程可以参考官方提供的最新教程 [Install Docker](https://docs.docker.com/install/)，这里就不详细介绍了。按照官方教程，装好Docker的社区版 (Community Edition)。

## CPU模式

如果要想直接使用Caffe提供的Docker镜像 ([bvlc/caffe](https://hub.docker.com/r/bvlc/caffe/))，可以直接使用 `docker pull` 命令把镜像下下来，然后使用 `docker run` 运行一个容器。

```
docker pull bvlc/caffe:cpu
docker run -it bvlc/caffe:cpu bash
```

此时，CPU模式的环境就可以使用了。如果需要在这个镜像的基础上添加点东西，例如安装 Jupyter Notebook 从而方便地使用Caffe的Python接口，这就需要自己编写一个Dockerfile了，然后生成一个镜像。

### 编写Dockerfile

我这里直接贴上一份Dockerfile，内容如下：

```
FROM bvlc/caffe:cpu

RUN pip install -U pip \
    && pip install -U scipy \
    && pip install -U scikit-learn \
    && pip install -U matplotlib \
    && pip install -U jupyter \
    && pip install -U lmdb \
    && pip install -U python-mnist

# Set the working directory to /caffe
WORKDIR /caffe

VOLUME ["/caffe/data", "/caffe/notebooks"]

# Expose port 8888 (jupyter uses)
EXPOSE 8888

CMD jupyter notebook --no-browser --ip=0.0.0.0 --allow-root --NotebookApp.token='science'
```

关于Dockerfile的编写规则，可以参考[Docker - 从入门到实践](https://yeasy.gitbooks.io/docker_practice/content/)这个电子书，内容写的很不错。Dockerfile选择Caffe作为基础镜像，然后安装了一些Python 包。在这个Dockerfile中我使用 `VOLUME` 命令定义了两个匿名卷

```
VOLUME ["/caffe/data", "/caffe/notebooks"]
```

后面我会把主机的真实目录挂载为这两个匿名卷，这样你在容器里往这两个目录写入东西就会保存到主机的真实目录里面。Dockerfile还指定了容器启动命令

```
CMD jupyter notebook --no-browser --ip=0.0.0.0 --allow-root --NotebookApp.token='science'
```

每次使用 `Docker run` 启动容器都会运行这个命令，这里我选择启动 Jupyter Notebook，这样我就可以通过浏览器访问 Jupyter Notebook了。

### 制作镜像

在Dockerfile所在目录使用命令即可

```
docker build -t mycaffe .
```

### 启动容器

创建两个目录，分别为 `data` 和 `notebooks`

```
mkdir data
mkdir notebooks
```

这两个目录就是所要挂载的目录，我要将这两个目录挂载到容器的匿名卷上。使用 `Docker run` 启动一个容器

```
docker run --name DeepLearning -d --mount type=bind,source=`pwd`/data,target=/caffe/data --mount type=bind,source=`pwd`/LeNet,target=/caffe/notebooks -p 8887:8888 mycaffe
```

这里解析下命令，首先是挂载两个匿名卷，将目录 `data` 挂载到 `/caffe/data`，将目录 `notebooks` 挂载到 `/caffe/notebooks`

```
--mount type=bind,source=`pwd`/data,target=/caffe/data --mount type=bind,source=`pwd`/LeNet,target=/caffe/notebooks
```

然后是 `-p 8887:8888` 将容器8888端口映射到本机的8887端口上，`-d` 是让容器后台运行，`--name DeepLearning` 将这个容器命名为 DeepLearning。运行成功后，可以使用浏览器访问 Jupyter Notebook了，访问地址为：

```
http://localhost:8887/
```

## GPU模式

GPU模式稍微复杂点，主要是GPU模式下需要CUDA以及显卡驱动，不过Caffe镜像已经集成了相应的CUDA，剩下就是安装显卡对应的驱动了。不同于CPU模式，这里启动容器需要使用 `nvidia-docker`，具体安装可以参考 [GitHub Nvida Docker](https://github.com/NVIDIA/nvidia-docker)，安装过程很简单，很快就可以安装好了。接下来就需要编写Dockerfile了，大体内容类似CPU模式的Dockerfile，不过基础镜像选择 `bvlc/caffe:gpu`。

### 编写Dockerfile

选用 `bvlc/caffe:gpu` 作为基础镜像，编写Dockerfile如下

```
FROM bvlc/caffe:gpu

RUN pip install -U pip \
    && pip install -U scipy \
    && pip install -U scikit-learn \
    && pip install -U matplotlib \
    && pip install -U jupyter \
    && pip install -U lmdb \
    && pip install -U python-mnist

# Set the working directory to /caffe
WORKDIR /caffe

VOLUME ["/caffe/data", "/caffe/notebooks"]

# Expose port 8888 (jupyter uses)
EXPOSE 8888

CMD jupyter notebook --no-browser --ip=0.0.0.0 --allow-root --NotebookApp.token='science'
```

### 制作镜像

在Dockerfile所在目录执行命令

```
docker build -t mycaffe .
```

### 启动容器

同前面所述，先创建好两个目录

```
mkdir data
mkdir notebooks
```

然后启动容器，启动容器使用的是 `nvidia-docker` 而不是 `docker`，启动命令如下

```
nvidia-docker run --name DeepLearning -d --mount type=bind,source=`pwd`/data,target=/caffe/data --mount type=bind,source=`pwd`/LeNet,target=/caffe/notebooks -p 8887:8888 mycaffe
```

启动完后可以直接通过浏览器访问容器内部的 Jupyter Notebook 了。

## 参考

1. [Docker Setup Cafee](https://github.com/BVLC/caffe/tree/master/docker)
