# caffe_build

This project will introduce how to build caffe:

# Ubuntu 16.04
#### Makefile

    $ cd caffe
    $ cp Makefile.config.example Makefile.config
    Modify Makefile.config as follow:
    $ diff Makefile.config Makefile.config.example 
  
 ```
23c23
<  OPENCV_VERSION := 3
---
> # OPENCV_VERSION := 3
39c39,41
< CUDA_ARCH := -gencode arch=compute_30,code=sm_30 \
---
> CUDA_ARCH := -gencode arch=compute_20,code=sm_20 \
> 		-gencode arch=compute_20,code=sm_21 \
> 		-gencode arch=compute_30,code=sm_30 \
95,96c97,98
< INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial/
< LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu/hdf5/serial/
---
> INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include
> LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib
```

#### CMake

    $ cd caffe
    $ mkdir build
    $ make all -j
