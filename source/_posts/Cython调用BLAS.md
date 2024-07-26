---
title: Cython调用BLAS
date: 2018-09-03 23:21:59
urlname: cython_blas
tags:
 - Python
 - Cython
---

## 介绍

看本文之前建议看文章"[Typed Memoryviews](http://docs.cython.org/en/latest/src/userguide/memoryviews.html)"，该文章主要介绍的是`Memoryviews`，使用`Memoryviews`可以直接访问Numpy数组的memory buffer，直接操作Numpy数组的数据，同时是连接C数组与Numpy数组之间的桥梁。相关代码在：[CythonDemos/Demo1](https://github.com/luowanqian/CythonDemos/tree/master/Demo1)

## BLAS

[BLAS](http://www.netlib.org/blas/)，全称Basic Linear Algebra Subprograms，即基础线性代数子程序库，里面有大量已经编写好的关于线性代数运算的程序。BLAS库的API分三大类，分别为`Level 1`、`Level 2`以及`Level 3`。

* Level 1: 函数处理单一向量的线性运算以及两个向量的二元运算
* Level 2: 函数处理矩阵与向量的运算，同时也包含线性方程组求解
* Level 3: 函数处理矩阵与矩阵之间的运算

API的全部介绍可以参考官方文档：[blasqr.pdf](http://www.netlib.org/blas/blasqr.pdf)

## Cython调用BLAS函数

Cython要调用BLAS函数，主要是通过BLAS库的C接口，接口的具体定义可以参考现有BLAS库的 [cblas.h](https://github.com/xianyi/OpenBLAS/blob/develop/cblas.h)，里面函数的命名和 [blasqr.pdf](http://www.netlib.org/blas/blasqr.pdf) 中函数命名是一一对应的，只不过函数前多了`cblas_`前缀。这里演示如何调用函数`cblas_dgemv`，在头文件  [cblas.h](https://github.com/xianyi/OpenBLAS/blob/develop/cblas.h) 中函数声明为

```c
void cblas_dgemv(OPENBLAS_CONST enum CBLAS_ORDER order, OPENBLAS_CONST enum CBLAS_TRANSPOSE trans, OPENBLAS_CONST blasint m, OPENBLAS_CONST blasint n, OPENBLAS_CONST double alpha, OPENBLAS_CONST double *a, OPENBLAS_CONST blasint lda, OPENBLAS_CONST double *x, OPENBLAS_CONST blasint incx, OPENBLAS_CONST double beta, double *y, OPENBLAS_CONST blasint incy);
```

去除一些宏以及替换定义的数据类型，简单表示就是

```c
void cblas_dgemv(enum CBLAS_ORDER order, enum CBLAS_TRANSPOSE trans, int m, int n, double alpha, double *a, int lda, double *x, int incx, double beta, double *y, int incy)
```

这个函数主要是实现的功能计算矩阵与向量相乘，在 [blasqr.pdf](http://www.netlib.org/blas/blasqr.pdf) 中对应的是`Level2`中`xGEMV`函数

$$
y = \alpha Ax + \beta y
$$

在Cython调用BLAS函数首先要使用`cdef extern`引入头文件，然后声明函数

```c
cdef extern from 'cblas.h':
    enum CBLAS_ORDER:
        CblasRowMajor=101
        CblasColMajor=102
    enum CBLAS_TRANSPOSE:
        CblasNoTrans=111
        CblasTrans=112
        CblasConjTrans=113
        CblasConjNoTrans=114
    void dgemv "cblas_dgemv"(CBLAS_ORDER Order, CBLAS_TRANSPOSE TransA,
                             int M, int N, double alpha, double *A, int lda,
                             double *X, int incX, double beta,
                             double *Y, int incY) nogil
```

关于`enum`中`CblasRowMajor`这些标识符的取值，是按照 [cblas.h](https://github.com/xianyi/OpenBLAS/blob/develop/cblas.h) 中定义来取值。函数声明后就可以编写一个wrapper函数来调用BLAS函数，之所以要写一个wrapper函数，是因为声明的BLAS函数只能在C中进行调用，我们还要使得其能够在Python中进行调用，wrapper函数定义如下：

```c
cpdef blas_dgemv(double[:, ::1] A, double[::1] x, double[::1] y,
                 int M, int N, double alpha, double beta):
    cdef double *A_ptr = &A[0, 0]
    cdef double *x_ptr = &x[0]
    cdef double *y_ptr = &y[0]

    dgemv(CblasRowMajor, CblasNoTrans, M, N,
          alpha, A_ptr, N, x_ptr, 1, beta, y_ptr, 1)
```

通过`cpdef`可以使得定义的函数可以被Python调用。这里说一下如何将`MemoryViews`转化成C的指针，对于向量`x`可以通过对第一个元素进行取址来进行转换`&x[0]`，对于矩阵`A`则是对其第一行第一列的元素进行取址操作`&A[0, 0]`。

由于BLAS库中将矩阵数据看成是一个一维数组，所以在调用BLAS函数时需要指定矩阵是Row Major还是Column Major，即参数`CBLAS_ORDER Order`，这里设定是Row Major，因此函数`blas_dgemv`中参数`A`限定是Row Major，即声明为`double[:, ::1]`。

整个pyx文件的内容为：

```
cdef extern from 'cblas.h':
    enum CBLAS_ORDER:
        CblasRowMajor=101
        CblasColMajor=102
    enum CBLAS_TRANSPOSE:
        CblasNoTrans=111
        CblasTrans=112
        CblasConjTrans=113
        CblasConjNoTrans=114
    void dgemv "cblas_dgemv"(CBLAS_ORDER Order, CBLAS_TRANSPOSE TransA,
                             int M, int N, double alpha, double *A, int lda,
                             double *X, int incX, double beta,
                             double *Y, int incY) nogil

cpdef blas_dgemv(double[:, ::1] A, double[::1] x, double[::1] y,
                 int M, int N, double alpha, double beta):
    cdef double *A_ptr = &A[0, 0]
    cdef double *x_ptr = &x[0]
    cdef double *y_ptr = &y[0]

    dgemv(CblasRowMajor, CblasNoTrans, M, N,
          alpha, A_ptr, N, x_ptr, 1, beta, y_ptr, 1)
```

剩下就是将pyx进行编译，然后就可以在Python中调用`blas_dgemv`函数了。

## 参考

1. [Calling BLAS from Cython](https://maciejkula.github.io/2015/01/01/calling-blas-from-cython/)

