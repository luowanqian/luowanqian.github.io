---
title: CMake使用笔记
date: 2018-04-03 08:24:31
urlname: cmake_note
tags:
 - Makefile
---

本文主要记录一些CMake的常见用法，例子代码都可以在[CMakeDemos](https://github.com/luowanqian/CMakeDemos)这里找到。

## 单个源文件

> 代码：[Demo1](https://github.com/luowanqian/CMakeDemos/tree/master/Demo1)

目录里面只有一个源文件，这个最简单的情况。目录结构如下所示

```
Demo1
├── CMakeLists.txt
├── build
└── main.cpp
```

文件 CMakeLists.txt 内容如下

```
# CMake 最低版本号要求
cmake_minimum_required(VERSION 3.9)

# 项目信息
project(Demo1)

# 指定生成目标
add_executable(Demo main.cpp)
```

符号`#`后面的内容被认为是注释。由`add_executable`知道，最终编译出的可执行文件是 Demo。

### 编译项目

如果在源码目录中直接使用命令`cmake .`生成Makefile文件的话，会发现目录多出很多文件，这样会导致目录内容很乱，通常做法是在一个目录里面生成CMake的临时文件，这里选择在目录 build 中生成。

```
➜  Demo1 git:(master) cd build
➜  build git:(master) cmake ..
-- The C compiler identification is AppleClang 8.0.0.8000042
-- The CXX compiler identification is AppleClang 8.0.0.8000042
-- Check for working C compiler: /Library/Developer/CommandLineTools/usr/bin/cc
-- Check for working C compiler: /Library/Developer/CommandLineTools/usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /Library/Developer/CommandLineTools/usr/bin/c++
-- Check for working CXX compiler: /Library/Developer/CommandLineTools/usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/luowanqian/Documents/Project/CMakeDemos/Demo1/build
➜  build git:(master) make
Scanning dependencies of target Demo
[ 50%] Building CXX object CMakeFiles/Demo.dir/main.cpp.o
[100%] Linking CXX executable Demo
[100%] Built target Demo
➜  build git:(master) ./Demo
Sqrt(2) is 1.41421
➜  build git:(master)
```

进入`build`目录，然后使用命令`cmake ..`生成Makefile文件，然后使用`make`命令编译得到可执行文件`Demo`。

## 同一个目录，多个源文件

> 代码：[Demo2](https://github.com/luowanqian/CMakeDemos/tree/master/Demo2)

在同一个目录里面，有个多个源文件，此时目录结构如下所示

```
Demo2
├── CMakeLists.txt
├── MathFunctions.cpp
├── MathFunctions.h
├── build
└── main.cpp
```

此时 MathFunctions.cpp 里面实现了一个函数 mysqrt，main.cpp 会调用这个函数。此时文件 CMakeLists.txt 的内容如下

```
# CMake 最低版本号要求
cmake_minimum_required(VERSION 3.9)

# 项目信息
project(Demo2)

# 指定生成目标
add_executable(Demo main.cpp MathFunctions.cpp)
```

大致内容同前一节的所写的那样，只不过`add_executable`里面多了 MathFunctions.cpp 项。


## 多个目录，多个源文件

> 代码：[Demo3](https://github.com/luowanqian/CMakeDemos/tree/master/Demo3)

有时候我们不想把所有源文件放到同一个目录里面，想根据实现的功能放到不同的目录中，前面的 MathFunctions.cpp 实现了数学函数功能，因此这里把它放入 Math 目录中，此时目录结构如下

```
Demo3
├── CMakeLists.txt
├── build
├── main.cpp
└── math
    ├── CMakeLists.txt
    ├── MathFunctions.cpp
    └── MathFunctions.h
```

对于这种情况，需要分别在项目根目录 Demo3 和 math 目录中分别编写 CMakeLists.txt 文件。在项目编译时，现将 math 目录里的文件编译成静态库再链接到程序 Demo 中。

### 根目录CMakeLists.txt

```
# CMake 最低版本号要求
cmake_minimum_required(VERSION 3.9)

# 项目信息
project(Demo3)

# 添加 math 子目录
add_subdirectory(math)

# 添加 math 到头文件搜索路径
include_directories(math)

# 指定生成目标
add_executable(Demo main.cpp)

# 添加链接库
target_link_libraries(Demo MathFunctions)
```

文件的使用命令`add_subdirectory`添加子目录 math，这个命令很类似Makefile中的`include`命令，使用该命令后，CMake会处理 math 目录下的 CMakeLists.txt。这里还用了命令`include_directories`，主要是文件 main.cpp 会调用头文件 MathFunctions.h。链接静态库使用命令`target_link_libraries`，其中 MathFunctions 是静态库的名称，定义在 math 目录的 CMakeLists.txt 中。

### math目录CMakeLists.txt

```
add_library(MathFunctions MathFunctions.cpp)
```

子目录的 CMakeLists.txt 文件内容如上，使用命令`add_library`命令将本目录的源文件编译为静态链接库，名称为 MathFunctions。

## 自定义编译选项

> 代码：[Demo4](https://github.com/luowanqian/CMakeDemos/tree/master/Demo4)

CMake可以项目设置编译选项，例如可以将 MathFunctions 库设为一个可选的库，如果该选项为 `ON`，则使用该库中的函数来进行运算，否则使用标准库的数学函数进行运算。首先看看项目目录的结构

 ```
Demo4
├── CMakeLists.txt
├── build
├── config.h.in
├── main.cpp
└── math
    ├── CMakeLists.txt
    ├── MathFunctions.cpp
    └── MathFunctions.h
 ```

目录中比上一节的目录结构中多了一个文件 config.h.in，CMake会将文件 config.h.in 转换成 config.h，这个头文件主要是定义了一些宏，源文件可以包含这个头文件，根据里面调用的宏来确定是否调用某个功能，所谓编译选项说白就是一堆宏。上面描述可能有点抽象，下面详细介绍各个文件内容

### 根目录CMakeLists.txt

```
# CMake 最低版本号要求
cmake_minimum_required(VERSION 3.9)

# 项目信息
project(Demo3)

# 是否使用 MathFunctions 库选项
option(USE_MYMATH "Use provided math implementation" ON)

# 生成一个配置头文件
configure_file(
    "${PROJECT_SOURCE_DIR}/config.h.in"
    "${PROJECT_BINARY_DIR}/config.h"
)

# 是否加入 MathFunctions 库
if (USE_MYMATH)
    include_directories("${PROJECT_SOURCE_DIR}/math")
    add_subdirectory(math)
    set(EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
endif(USE_MYMATH)

# 使得 CMake 可以找到生成的 config.h
include_directories("${PROJECT_BINARY_DIR}")

# 指定生成目标
add_executable(Demo main.cpp)

# 添加链接库
target_link_libraries(Demo ${EXTRA_LIBS})
```

文件中`configure_file`命令用于生成配置头文件 config.h，这个头文件由CMake从 config.h.in 中生成，config.h.in 这个文件里面有用户自己定义的编译选项，其中 `USE_MYMATH` 就是定义在文件 `config.h.in` 中，这里使用命令 `option` 默认开启这个选项，然后后面的 if 语句根据 `USE_MYMATH` 变量的值来决定是否使用我们自己的编写的数学函数库，由于 `USE_MYATH` 为 ON，所以默认是使用我们自己的库。

### main.cpp

```
#include <iostream>
#include <cmath>

#include "config.h"

#ifdef USE_MYMATH
#include "MathFunctions.h"
#endif
using namespace std;

int main()
{
#ifdef USE_MYMATH
    cout << "Now we use our own Math library." << endl;
    cout << "Sqrt(2) is " << mysqrt(2) << endl;
#else
    cout << "Now we use the standard libary." << endl;
    cout << "Sqrt(2) is " << sqrt(2) << endl;
#endif

    return 0;
}
```

源文件中可以使用我们定义 `USE_MYMATH` 来决定是否调用自己编写的库，要使用这个宏，需要包含 config.h 这个头文件，这个头文件是CMake根据 config.h.in 生成。

### config.h.in

```
#cmakedefine USE_MYMATH
```

编译选项 `USE_MYMATH` 定义在这个文件中。

### 编译项目

现在编译这个项目，为了便于交互式的修改编译选项，可以使用 `ccmake` 命令：

```
➜  Demo4 git:(master) ✗ ll
total 24
-rw-r--r--  1 luowanqian  staff   722B  4  3 10:07 CMakeLists.txt
drwxr-xr-x  2 luowanqian  staff    68B  4  3 10:11 build
-rw-r--r--  1 luowanqian  staff    23B  4  2 08:46 config.h.in
-rw-r--r--  1 luowanqian  staff   398B  4  2 08:54 main.cpp
drwxr-xr-x  5 luowanqian  staff   170B  4  2 08:44 math
➜  Demo4 git:(master) ✗ cd build
➜  build git:(master) ✗ cmake ..
-- The C compiler identification is AppleClang 8.0.0.8000042
-- The CXX compiler identification is AppleClang 8.0.0.8000042
-- Check for working C compiler: /Library/Developer/CommandLineTools/usr/bin/cc
-- Check for working C compiler: /Library/Developer/CommandLineTools/usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /Library/Developer/CommandLineTools/usr/bin/c++
-- Check for working CXX compiler: /Library/Developer/CommandLineTools/usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/luowanqian/Documents/Project/CMakeDemos/Demo4/build
➜  build git:(master) ✗ ccmake ..
```

{% asset_img ccmake.png ccmake %}

从中可以找到刚刚定义的`USE_MYMATH`选项，按键盘的方向键可以在不同的选项窗口间跳转，按下`enter`键可以修改该选项。修改完成后可以按下`c`键完成配置，之后再按`g`键确认生成 Makefile。下面分别展示将`USE_MYMATH`设为`ON`和`OFF`得到的结果：

__USE_MYMATH 设为 ON__

```
➜  build git:(master) ✗ make
Scanning dependencies of target MathFunctions
[ 25%] Building CXX object math/CMakeFiles/MathFunctions.dir/MathFunctions.cpp.o
[ 50%] Linking CXX static library libMathFunctions.a
[ 50%] Built target MathFunctions
Scanning dependencies of target Demo
[ 75%] Building CXX object CMakeFiles/Demo.dir/main.cpp.o
[100%] Linking CXX executable Demo
[100%] Built target Demo
➜  build git:(master) ✗ ./Demo
Now we use our own Math library.
Sqrt(2) is 1.41421
➜  build git:(master) ✗
```

__USE_MYMATH 设为 OFF__

```
➜  build git:(master) ✗ make
Scanning dependencies of target Demo
[ 50%] Building CXX object CMakeFiles/Demo.dir/main.cpp.o
[100%] Linking CXX executable Demo
[100%] Built target Demo
➜  build git:(master) ✗ ./Demo
Now we use the standard libary.
Sqrt(2) is 1.41421
```

## 参考

1. [CMake入门实战](http://www.hahack.com/codes/cmake/)
2. [CMake Tutorial](https://cmake.org/cmake-tutorial/)
