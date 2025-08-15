# Caffe for chexagon

This is our fork to use [Caffe deep learning framework](https://caffe.berkeleyvision.org/) on the [chexagon](https://github.com/chexagon) organization

## Known uses 

* [Image API](https://github.com/chexagon/imageapi)
* [CH Java Caffe](https://github.com/chexagon/ch-java-caffe)

## Java support

There's java support to talk to Caffe on [CH Java Caffe](https://github.com/chexagon/ch-java-caffe)

### Compiling

This is for Ubuntu 24 on a G6 (x86_64) instance. You might need to adjust things in other systems

#### Install requirements

```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID | sed -e 's/\.//g')
arch=$(uname -m)
wget https://developer.download.nvidia.com/compute/cuda/repos/$distribution/$arch$/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb

sudo apt update
sudo DEBIAN_FRONTEND=noninteractive apt install -y \
 cuda-runtime-12-9\
 cuda-toolkit-12-9 \
 cuda-drivers \
 nvidia-cudnn \
 nvidia-cuda-toolkit \
 libgoogle-glog-dev \
 libprotobuf-dev \
 protobuf-compiler \
 libhdf5-dev \
 libhdf5-serial-dev \
 libleveldb-dev \
 libopencv-dev \
 libboost-thread-dev \
 libopenblas-dev \
 liblmdb-dev \
 libboost-filesystem-dev \
 libsnappy-dev \
 libboost-regex-dev \
 make \
 g++
```

#### Compile Caffe C++ library (no python)

```
# create caffe make config
cat > Makefile.config << EOF
USE_CUDNN := 1
OPENCV_VERSION := 3
CUDA_DIR := /usr/local/cuda

CUDA_ARCH :=    -gencode arch=compute_80,code=sm_80 \\
                -gencode arch=compute_86,code=sm_86 \\
     	        -gencode arch=compute_87,code=sm_87 \\
                -gencode arch=compute_89,code=sm_89 \\
                -gencode arch=compute_90,code=sm_90 \\
                -gencode arch=compute_100,code=sm_100
             
BLAS := open
WITH_PYTHON_LAYER := 0

INCLUDE_DIRS :=     /usr/local/include /usr/include/opencv4 \\
                    /usr/include/hdf5/serial

LIBRARY_DIRS :=     /usr/local/lib \\
                    /usr/lib \\
                    /usr/lib/x86_64-linux-gnu \\
                    /usr/lib/x86_64-linux-gnu/hdf5/serial
                    
BUILD_DIR := build
DISTRIBUTE_DIR := distribute
TEST_GPUID := 0
Q ?= @
EOF

make lib

# show compiled files
ls .build_release/lib/
```
