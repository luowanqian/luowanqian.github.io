---
title: H5py备忘录
date: 2018-04-27 17:32:59
urlname: h5py_memo
tags:
 - HDF5
 - Python
---

本文主要是记录一些h5py的使用方法。

__Note:__ 如果没有特别说明，`np` 指的是 `numpy`，代表导入的 numpy

```python
import numpy as np
```

## 存储浮点数采用单精度浮点数

在HDF5文件中存储浮点数时，可以选择单精度浮点数和双精度浮点数，常见是用单精度浮点数来存储浮点数，相比于双精度浮点数，单精度浮点数存储空间为双精度浮点数的一半，这样可以缩小一半的存储空间。不同于文件存储，在内存中需要使用双精度浮点数来保证计算的准确性，因此一个通常的操作是，在内存中使用双精度浮点数，然后存储到HDF5文件时，使用单精度浮点数，两者的使用只需要进行数据类型转换。

在内存中，我们使用Numpy创建一个数组，数据类型为双精度浮点数

```python
>>> import numpy as np
>>> bigdata = np.ones((100, 1000))
>>> bigdata.dtype
dtype('float64')
>>> bigdata.shape
(100, 1000)
```

直接使用赋值方式将数组存储到HDF5文件中，存储的浮点数为双精度浮点数

```python
>>> with h5py.File('big1.hdf5', 'w') as f1:
...     f1['big'] = bigdata
>>> f1 = h5py.File('big1.hdf5')
>>> f1['big'].dtype
dtype('float64')
```

文件大小为 783K

```shell
$ ls -lh big1.hdf5
-rw-r--r--  1 luowanqian  staff   783K  4 29 10:44 big1.hdf5
```

我们可以使用 `create_dataset` 函数来指定存储单精度浮点数

```python
>>> with h5py.File('big2.hdf5', 'w') as f2:
...     f2.create_dataset('big', data=bigdata, dtype=np.float32)
>>> f2 = h5py.File('big2.hdf5')
>>> f2['big'].dtype
dtype('float32')
```

文件大小为 393K

```shell
$ ls -lh big2.hdf5
-rw-r--r--  1 luowanqian  staff   393K  4 29 11:02 big2.hdf5
```

## 读取数据时进行数据转换

假设有个存储单精度浮点数的HDF5文件，我们想将数据读入到内存时是双精度浮点数。HDF5文件的数据如下

```python
>>> import numpy as np
>>> import h5py
>>> bigdata = np.ones((100, 1000))
>>> with h5py.File('big.hdf5', 'w') as f:
...     f.create_dataset('big', data=bigdata, dtype=np.float32)
>>> f = h5py.File('big.hdf5')
>>> dset = f['big']
>>> dset.dtype
dtype('float32')
>>> dset.shape
(100, 1000)
```

### 方案1

使用 `np.empty` 创建一个空数组，然后使用 `read_direct` 函数

```python
>>> out = np.empty((100, 1000), dtype=np.float64)
>>> dset.read_direct(out)
>>> out.dtype
dtype('float64')
```

如果不想读取全部数据，可以设置函数 `read_direct` 的 `source_sel` 和 `dest_sel` 参数。假设要将 `dset[0, :]` 的数据读入到 `out[50, :]`

```python
>>> out = np.empty((100, 1000), dtype=np.float64)
>>> dset.read_direct(out, source_sel=np.s_[0, :], dest_sel=np.s_[50, :])
```

其中使用 `np.s_` 返回是 Numpy 的 `slice` 对象，该对象包含索引信息。如果省略 `dset_sel` 参数，则采用类似 Numpy 的广播规则进行赋值

```python
>>> out = np.empty((100, 50), dtype=np.float32)
>>> dset.read_direct(out, source_sel=np.s_[:, 0:50])
```

#### 优势

如果要读取多次同样大小的数据时，使用 `read_direct` 可以节省很多时间，因为只需要申请一次空间，后面数据读入直接覆盖到这个空间。做一个Benchmark：

```python
import h5py
import numpy as np
from timeit import timeit


filename = 'test.hdf5'
n = 10000

f = h5py.File(filename, 'w')
dset = f.create_dataset('perftest', (n, n), dtype=np.float32)
dset[:] = np.random.random(n)
out = np.empty((n, 500), dtype=np.float32)


def time_simple():
    dset[:, 0:500].mean(axis=1)


def time_direct():
    dset.read_direct(out, np.s_[:, 0:500])
    out.mean(axis=1)


print('Time simple: {}'.format(timeit(time_simple, number=500)))
print('Time direct: {}'.format(timeit(time_direct, number=500)))

f.close()
```

运行结果

```
Time simple: 41.33699381200131
Time direct: 39.005692337988876
```

### 方案2

使用 `Dataset.astype` 这个上下文管理器

```python
>>> with dset.astype('float64'):
...     out = dset[...]
>>> out.shape
(100, 1000)
>>> out.dtype
dtype('float64')
```

当然，也适合读取部分数据

```python
>>> with dset.astype('float64'):
...     out = dset[0, :]
>>> out.dtype
dtype('float64')
```

