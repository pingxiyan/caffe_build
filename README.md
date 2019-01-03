# caffe_build

This project will introduce how to build caffe:

# Ubuntu 16.04
#### Dependency

    $ sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
    $ sudo apt-get install --no-install-recommends libboost-all-dev
    $ sudo apt-get install libatlas-base-dev
    $ sudo apt-get install libopenblas-dev
    $ sudo apt-get install python-dev
    $ sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
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

    1. modify CMakeLists as follow.
    +add_definitions(-std=c++11)    // latest version opencv need cpp11 support.
    
    $ cd caffe
    $ mkdir build
    $ make all -j
    
#### Test classification

    $ cd models/bvlc_reference_caffenet
    $ ../../build/examples/cpp_classification/classification deploy.prototxt bvlc_reference_caffenet.caffemodel mean.binaryproto synset_words.txt cat.jpg 
    
    Result:
    ---------- Prediction for cat.jpg ----------
    0.3134 - "n02123045 tabby, tabby cat"
    0.2380 - "n02123159 tiger cat"
    0.1235 - "n02124075 Egyptian cat"
    0.1003 - "n02119022 red fox, Vulpes vulpes"
    0.0715 - "n02127052 lynx, catamount"

# QA
1. ```ERROR```: F1212 09:13:27.964561 31483 syncedmem.hpp:22] Check failed: error == cudaSuccess (35 vs. 0)  CUDA driver version is insufficient for CUDA runtime version   <br>
You need to check your cuda path. for example: /usr/local/cuad-9.0  <br>

