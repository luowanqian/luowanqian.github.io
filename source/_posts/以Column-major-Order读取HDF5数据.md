---
title: 以Column-major Order读取HDF5数据
date: 2018-07-18 09:39:07
urlname: hdf5_column_major
tags:
 - HDF5
---

在使用h5py这类库读取HDF5里面的数据时，数据的存储方式为row-major order，而一些机器学习算法的实现在使用数据时需要的存储方式为column-major order，此时需要对数据进行转换。如果数据量很小，转换是没有问题的，如果数据量巨大，进行转换时，由于需要多一倍内存来存放转换的数据，因此可能导致内存溢出。本篇博文主要是重新实现h5py库中的 `read_direct` 函数，使得该函数在读取HDF5文件时，可以使得读取到内存的数据是以column-major order方式存储。

相关实现代码在: [HDF5 gist](https://gist.github.com/luowanqian/b924987be33d51f50a30e741fe249526)

## 思路

基本思路就是一行行地读取HDF5文件里面的数据，然后存储到内存的数组中，内存的数组是以column-major order方式存储数据。这里假设HDF5文件里面的数据存储是连续的，没有进行分块，复杂的情况等以后再实现，这里只是应对最简单的情况。

## 实现

实现主要是 Python + C 形式，Python部分主要提供一个函数接口用于调用，C部分主要是使用HDF5 C API对HDF5数据进行读取，两者之间的相互调用使用的是Cython。

### C

这部分需要使用HDF5库提供的C API对HDF5文件数据进行读取。主要使用的API是 `H5Sselect_hyperslab`，这个函数可以设定读取存储空间的哪一部分数据或者将数据存在存储空间的哪一部分，这里的存储空间是一个抽象概念，既可以表示HDF5文件里面内容，也可以表示内存申请的存储空间，这里我们分别对HDF5文件内容和内存申请的空间分别进行设定。实现代码如下：

```
/*
 * 从H5文件中读取矩阵数据，以column order存储数据
 *
 * filename: H5文件名
 * dataset_name: 数据集名
 * buffer: 数据内存存储处，大小为 num_rows * num_cols
 * num_rows: 待读取的矩阵的行数
 * num_cols: 待读取的矩阵的列数
 * offset_x: 读取的数据在H5文件中的矩阵的x方向上的偏移 (第几行)
 * offset_y: 读取的数据在H5文件中的矩阵的y方向上的偏移 (第几列)
 *
 */
int read_direct_f(char *file_name, char *dataset_name, double *buffer,
                  int num_rows, int num_cols, int offset_x, int offset_y)
{
    int i;

    hid_t file_id, dataset_id;
    hid_t dataspace_id, memspace_id;
    herr_t status;

    hsize_t dims_mem[1];

    hsize_t offset[2];
    hsize_t count[2];
    hsize_t stride[2];
    hsize_t block[2];
    hsize_t offset_mem[1];
    hsize_t count_mem[1];
    hsize_t stride_mem[1];
    hsize_t block_mem[1];

    // open the hdf5 file and the dataset
    file_id = H5Fopen(file_name, H5F_ACC_RDONLY, H5P_DEFAULT);
    dataset_id = H5Dopen2(file_id, dataset_name, H5P_DEFAULT);

    // get file dataspace
    dataspace_id = H5Dget_space(dataset_id);

    // create memory space
    dims_mem[0] = num_rows;         // avoid numeric overflow
    dims_mem[0] = dims_mem[0] * num_cols;
    memspace_id = H5Screate_simple(1, dims_mem, NULL);

    // specify size and shape of subset to read
    offset[0] = offset_x;
    offset[1] = offset_y;
    count[0] = 1;
    count[1] = num_cols;
    stride[0] = 1;
    stride[1] = 1;
    block[0] = 1;
    block[1] = 1;
    offset_mem[0] = 0;
    count_mem[0] = num_cols;
    stride_mem[0] = num_rows;
    block_mem[0] = 1;

    for (i=0; i<num_rows; i++) {
        offset[0] = offset_x + i;
        offset_mem[0] = i;

        // select subset from dataspace
        status = H5Sselect_hyperslab(dataspace_id, H5S_SELECT_SET,
                                     offset, stride, count, block);
        if (status < 0) {
            H5Eprint2(H5E_DEFAULT, NULL);
            return -1;
        }

        status = H5Sselect_hyperslab(memspace_id, H5S_SELECT_SET,
                                     offset_mem, stride_mem,
                                     count_mem, block_mem);
        if (status < 0) {
            H5Eprint2(H5E_DEFAULT, NULL);
            return -1;
        }

        // read data
        status = H5Dread(dataset_id, H5T_NATIVE_DOUBLE,
                         memspace_id, dataspace_id,
                         H5P_DEFAULT, buffer);
        if (status < 0) {
            H5Eprint2(H5E_DEFAULT, NULL);
            return -1;
        }
    }

    // close the resources
    H5Sclose(memspace_id);
    H5Sclose(dataspace_id);
    H5Dclose(dataset_id);
    H5Fclose(file_id);

    return 0;
}
```

基本实现思路就是每次就只读取HDF5文件中矩阵的一行，然后写入到内存空间中，每次读取都要用函数 `H5Sselect_hyperslab` 函数设定读取区域，同样，每次写入都要使用函数 `H5Sselect_hyperslab` 设定写入区域。这个实现思想参考了HDF5官方教程 [Reading From or Writing To a Subset of a Dataset](https://portal.hdfgroup.org/display/HDF5/Reading+From+or+Writing+To+a+Subset+of+a+Dataset)，教程中仅仅设定了文件的读取区域，没有设定写入区域。

### Python

这部分使用Cython来实现Python中调用前面所写的C语言代码，这部分代码如下：

```
def read_direct_fortran(str file_name, str dataset_name,
                        double[::1, :] data, int offset_x, int offset_y):
    cdef int num_rows
    cdef int num_cols
    cdef bytes file_name_bytes = bytes(file_name, encoding='utf-8')
    cdef bytes dataset_name_bytes = bytes(dataset_name, encoding='utf-8')
    cdef int ret;

    num_rows = data.shape[0]
    num_cols = data.shape[1]

    ret = read_direct_f(file_name_bytes, dataset_name_bytes, &data[0, 0],
                        num_rows, num_cols, offset_x, offset_y)
```
