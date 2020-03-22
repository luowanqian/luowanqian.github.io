---
title: Python Snippets Part 1
urlname: python_snippets_part1
date: 2018-05-02 22:57:40
list_number: false
tags:
 - Python
 - 编程语言
---

- [1. 判断文件或目录是否存在](#1-判断文件或目录是否存在)
- [2. ParameterGrid](#2-parametergrid)
- [3. 批量下载图片](#3-批量下载图片)
- [4. 遍历文件夹中所有文件](#4-遍历文件夹中所有文件)
- [5. 计算函数运行时间](#5-计算函数运行时间)
- [6. 判断对象是否可迭代](#6-判断对象是否可迭代)
- [7. 判断操作系统类型](#7-判断操作系统类型)

## 1. 判断文件或目录是否存在

创建一个目录和一个文件

```shell
$ mkdir dir1 && touch file1.txt
$ ls -l
total 0
drwxr-xr-x  2 luowanqian  wheel  68  5  2 23:21 dir1
-rw-r--r--  1 luowanqian  wheel   0  5  2 23:21 file1.txt
```

使用 `os.path.exists()` 可以判断文件或目录是否存在，但是不能判断是该路径是一个文件还是目录，要进一步判断，需要使用 `os.path.isfile()`，如果该路径是一个文件，则返回 True，否则返回 False，当然，也可以直接使用 `os.path.isfile()` 判断文件是否存在。测试代码如下：

```python
>>> import os
>>> os.path.exists('dir1')
True
>>> os.path.exists('file1.txt')
True
>>> os.path.isfile('dir1')
False
>>> os.path.isfile('file1.txt')
True

>>> os.path.exists('dir2')
False
>>> os.path.exists('file2.txt')
False
>>> os.path.isfile('dir2')
False
>>> os.path.isfile('file2.txt')
False
```

目录 `dir2` 和文件 `file2.txt` 均不存在，所以函数 `os.path.exists()` 和 `os.path.isfile()` 均返回 False。

## 2. ParameterGrid

机器学习算法最常见的调参方法是网格搜索，需要将多组参数进行组合，Scikit-learn提供了一个类 `ParametGrid` 可以生成所有的参数组合，这里提取其关键代码单独写成一个生成器：

```python
from itertools import product


def parameters_grid(parameter_map):
    items = sorted(parameter_map.items())
    if not items:
        yield {}
    else:
        keys, values = zip(*items)
        for v in product(*values):
            params = dict(zip(keys, v))
            yield params


if __name__ == '__main__':
    parameter_map = {'a': [1, 2], 'b': [True, False]}
    for params in parameters_grid(parameter_map):
        print(params)

```

代码运行结果

```
{'a': 1, 'b': True}
{'a': 1, 'b': False}
{'a': 2, 'b': True}
{'a': 2, 'b': False}
```

## 3. 批量下载图片

这里使用`requests`库批量下载图片，为了加快下载速度，还实现了多线程下载，同时为了避免一次下载失败，脚本支持自动重试下载。没有处理具体的异常，只是捕获异常后输出异常信息。

```python
import os
import requests
from retrying import retry
from io import BytesIO
from PIL import Image
import progressbar
import concurrent.futures as concurrent


@retry(stop_max_attempt_number=10)
def download(image_url, image_path):
    response = requests.get(image_url)
    img = Image.open(BytesIO(response.content))
    img.save(image_path)


if __name__ == '__main__':
    base_url = 'http://www.baidu.com'
    num_images = 3
    suffix = '.jpg'
    image_dir = 'images'

    num_workers = 4

    if not os.path.isdir(image_dir):
        os.mkdir(image_dir)

    with concurrent.ThreadPoolExecutor(max_workers=num_workers) as executor:
        image_urls = []
        image_paths = []
        for image_id in range(num_images):
            url = base_url + '/' + str(image_id + 1) + suffix
            file_path = os.path.join(image_dir, str(image_id + 1) + suffix)
            image_urls.append(url)
            image_paths.append(file_path)

        tasks = {
            executor.submit(download, url, file_path):
                           (url, file_path) for url, file_path in zip(image_urls, image_paths)
        }

        i = 0
        total = len(image_urls)
        pbar = progressbar.ProgressBar(max_value=total).start()
        for task in concurrent.as_completed(tasks):
            url, file_path = tasks[task]
            try:
                task.result()
                i = i + 1
                pbar.update(i)
            except Exception as exc:
                print('{} generated an exception: {}'.format(url, exc))
        pbar.finish()
```

## 4. 遍历文件夹中所有文件

首先目录结构如下：

```
$ tree test
test
├── 1.txt
├── 2.txt
└── test2
    ├── 3.txt
    └── 4.txt
```

使用`os.walk()`遍历`test`目录，代码如下：

```python
root_dir = '/tmp/test'
for root, dirs, files in os.walk(root_dir, topdown=True):
    for name in files:
        print(os.path.join(root, name))
    for name in dirs:
        print(os.path.join(root, name))
```

得到结果如下：

```
/tmp/test/1.txt
/tmp/test/2.txt
/tmp/test/test2
/tmp/test/test2/3.txt
/tmp/test/test2/4.txt
```

如果设置`topdown=False`，得到结果如下：

```
/tmp/test/test2/3.txt
/tmp/test/test2/4.txt
/tmp/test/1.txt
/tmp/test/2.txt
/tmp/test/test2
```

## 5. 计算函数运行时间

这里使用装饰器来计算函数运行时间

```python
import time
from functools import wraps


def timethis(func):
    """
    Decorator that reports the execution time
    """
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(func.__name__, end-start)

        return result

    return wrapper


@timethis
def countdown(n):
    while n > 0:
        n -= 1
```

## 6. 判断对象是否可迭代

```python
def isiterable(obj):
    try:
        obj = iter(obj)
    except:
        return False
    else:
        return True


if __name__ == "__main__":
    a = [1, 2]
    b = 3
    print(f"'a' is {'iterable' if isiterable(a) else 'not iterable'}")
    print(f"'b' is {'iterable' if isiterable(b) else 'not iterable'}")
```

执行结果

```
'a' is iterable
'b' is not iterable
```

## 7. 判断操作系统类型

可以使用 `sys.platform` 判断当前是什么操作系统。常见的操作系统，其返回值如下

| System | platform value |
| ---- | ---- |
| AIX | `'aix'` |
| Linux | `'linux'` |
| Windows | `'win32'` |
| Windows/Cygwin | `'cygwin'` |
| macOS | `'darwin'` |

官方推荐使用 `startswith()` 判断系统类型 (见 [sys.platform](https://docs.python.org/3/library/sys.html#sys.platform))，这里贴一段测试代码

```python
import sys


def identify_platform():
    platform = sys.platform
    if platform.startswith("freebsd"):
        return "freebsd"
    elif platform.startswith("linux"):
        return "linux"
    elif platform.startswith("aix"):
        return "aix"
    elif platform.startswith("win"):
        return "windows"
    elif platform.startswith("darwin"):
        return "macos"
    else:
        return "unknown"


if __name__ == "__main__":
    print(f"Platform: {identify_platform()}")
```