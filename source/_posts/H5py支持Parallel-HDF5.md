---
title: H5py支持Parallel HDF5
date: 2018-04-28 23:14:06
urlname: h5py_parallel_hdf5
tags:
 - 软件配置
 - HDF5
---

## 环境

操作系统：OS X 10.11.6

Python版本：3.6.5

## 安装HDF5

使用 Homebrew 安装 HDF5，注意要开启 mpi 编译选项

```
$ brew install hdf5 --with-mpi
```

## 安装mpi4py

官网教程在使用 Parallel HDF5 时会用到 mpi4py 这个包，直接使用 pip 安装即可

```
$ pip3 install mpi4py
```

__Note:__ 安装遇到一个问题是，如果之前安装过这个包，现在卸载再安装这个包时，pip 不会重新编译这个包，此时需要 `--no-cache-dir` 这个选项。

```
$ pip3 install --no-cache-dir mpi4py
```

## 安装h5py

直接使用 pip 安装 h5py 是不会开启 Parallel HDF5 的，需要添加一些编译选项，参考官网安装教程，运行命令

```
$ export CC=mpicc
$ export HDF5_MPI="ON"
$ pip3 install --no-binary=h5py h5py
```

同前面所诉，如果需要重新编译这个包，需要用到 `--no-cache-dir` 这个选项

```
$ pip3 install --no-cache-dir --no-binary=h5py h5py
```
