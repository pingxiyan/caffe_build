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
#### Makefile (Makefile.config close OpenCV option), recommend build pycaffe.

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

#### CMake (Makefile.config open OpenCV), recommend build test caffemodel

    1. Modify CMakeLists.txt
    +add_definitions(-std=c++11)
    2. src/caffe/layers/window_data_layer.cpp
    -        cv_img = cv::imread(image.first, CV_LOAD_IMAGE_COLOR);
    +        cv_img = cv::imread(image.first, 1);
    3. /src/caffe/util/io.cpp
    -  int cv_read_flag = (is_color ? CV_LOAD_IMAGE_COLOR :
    -    CV_LOAD_IMAGE_GRAYSCALE);
    +  int cv_read_flag = (is_color ? 1 : 0 );

    -  int cv_read_flag = (is_color ? CV_LOAD_IMAGE_COLOR :
    -    CV_LOAD_IMAGE_GRAYSCALE);
    +  int cv_read_flag = (is_color ? 1 : 0);
    
    $ cd caffe
    $ mkdir build
    $ make all -j30
    $ make install     # this step will create install path.
    
    If you like to pycaffe, please build pycaffe again(default python2[need to open support_python_layer=1 in file Makefile.config] had been verifed pass)
    $ make pycaffe
    
    Attention: I don't why latest official CAFFE, CPU is ok, GPU will core dump, and tip ERRORS 3.  <br>
    But my old verison caffe is OK(GPU and CPU: http://47.74.231.192/xiping/model_convert/tree/master/caffe)   <br>

    
#### Test classification

    $ cd data/ilsvrc12
    $ ./get_ilsvrc_aux.sh
    $ cd -
    $ cd models/bvlc_reference_caffenet
    $ wget http://dl.caffe.berkeleyvision.org/bvlc_reference_caffenet.caffemodel
    $ cp ../../examples/images/cat.jpg ./
    $ cp ../../data/ilsvrc12/imagenet_mean.binaryproto ./
    $ cp ../../data/ilsvrc12/synset_words.txt ./

    $ ../../build/examples/cpp_classification/classification deploy.prototxt bvlc_reference_caffenet.caffemodel imagenet_mean.binaryproto synset_words.txt cat.jpg 
    
    Result:
    ---------- Prediction for cat.jpg ----------
    0.3134 - "n02123045 tabby, tabby cat"
    0.2380 - "n02123159 tiger cat"
    0.1235 - "n02124075 Egyptian cat"
    0.1003 - "n02119022 red fox, Vulpes vulpes"
    0.0715 - "n02127052 lynx, catamount"

# QA
1. ```ERROR```: F1212 09:13:27.964561 31483 syncedmem.hpp:22] Check failed: error == cudaSuccess (35 vs. 0)  CUDA driver version is insufficient for CUDA runtime version   <br>
You need to check your cuda path. for example: /usr/local/cuda-9.0  <br>

2. Python3
  By modify some option in Makefile.config, we can build python3 version caffe pass, but may be tip error as follow when using pycaffe.
  
    ... <br>
    ... <br>
    File "/home/xiping/.local/lib/python3.5/site-packages/matplotlib/axes/_axes.py", line 20, in <module> <br>
        import matplotlib.dates as _  # <-registers a date unit converter <br>
    File "/home/xiping/.local/lib/python3.5/site-packages/matplotlib/dates.py", line 145, in <module> <br>
        from dateutil.rrule import (rrule, MO, TU, WE, TH, FR, SA, SU, YEARLY, <br>
    File "/home/xiping/.local/lib/python3.5/site-packages/dateutil/rrule.py", line 55 <br>
        raise ValueError, "Can't create weekday with n == 0" <br>
    
3. Call caffe by caffe.cmake, CPU is OK, but GPU tips: <br>
    F0801 11:11:08.298451 11029 math_functions.cu:79] Check failed: error == cudaSuccess (74 vs. 0)  misaligned address <br>
    *** Check failure stack trace: ***  <br>
    Aborted (core dumped)               <br>

