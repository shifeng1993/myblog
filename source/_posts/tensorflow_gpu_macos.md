---
title: 【tensorflow】macOS 10.13.4 编译 GPU 版本的 TensorFlow 1.8 版本
categories: tensorflow
date: 2018-05-26
tags:
  - tensorflow
---
macos版本 10.13.4，确保Homebrew内包全为最新，无需回退至Command-Line Tools 8.2.3.

- [文章链接](https://github.com/JDeanThomas/Compile-TensorFlow-with-GPU-MacOS/blob/master/TF_1.8_MacOS_10.13.md)

# 环境要求

* 在mac上禁用SIP
* NVIDIA Web-Drivers 驱动
* CUDA-Drivers 驱动
* CUDA 9.1 Toolkit 开发工具
* cuDNN 7.0.5 神经计算加速工具
* Python 3.6
* Apple Command-Line-Tools 8.3.2
* bazel


# 环境准备
## NVIDIA Web-Drivers
10.13.4。在安装CUDA驱动程序之前下载并安装最新的NVIDIA图形驱动程序。

https://www.insanelymac.com/forum/topic/324195-nvidia-web-driver-updates-for-macos-high-sierra-update-04252018/

## Homebrew
如果尚未安装，Homebrew还将安装最新的Apple Command-Line-Tools
```bash
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

## Python 依赖
```bash
$ pip install six numpy wheel
```

## coreutils
```bash
$ brew install coreutils
```
## bazel 
```bash
$ brew install bazel
```



## 降级 Command-Line-Tools 到 Version 8.3.2
如果您有更新版本的CLT，请下载8.3.2并进行降级。

```
$ sudo mv /Library/Developer/CommandLineTools /Library/Developer/CommandLineTools_backup
$ sudo xcode-select --switch /Library/Developer/CommandLineTools
```
## NVIDIA
### 安装 CUDA Toolkit 9.1
[Download CUDA-9.1](https://developer.nvidia.com/cuda-downloads?target_os=MacOSX&target_arch=x86_64&target_version=1013&target_type=dmglocal)


### 安装 cuDNN
Download [cuDNN 7.0.5](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/v7.0.5/prod/9.1_20171129/cudnn-9.1-osx-x64-v7-ga)[^1]

切换到解压缩的CUDNN目录
```bash
$ sudo cp cuda/include/cudnn.h /usr/local/cuda/include
$ sudo cp cuda/lib/libcudnn_static.a /usr/local/cuda/lib
$ sudo cp cuda/lib/libcudnn.7.dylib /usr/local/cuda/lib
$ sudo ln -s /usr/local/cuda/lib/libcudnn.7.dylib /usr/local/cuda/lib/libcudnn.dylib
$ sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib/libcudnn*
```

## 添加环境变量
```
export CUDA_HOME=/usr/local/cuda
export DYLD_LIBRARY_PATH=/usr/local/cuda/lib:/usr/local/cuda/extras/CUPTI/lib
export LD_LIBRARY_PATH=$DYLD_LIBRARY_PATH
export PATH=$PATH:$DYLD_LIBRARY_PATH
```

# Build准备
## 拉取TensorFlow源码
```bash
$ git clone https://github.com/tensorflow/tensorflow
$ cd tensorflow
```

## 补丁文件
将补丁文件夹中的文件复制到`/tensorflow/tensorflow/core/kernels`

# 开始Build
## Build 配置

```bash
$ bazel clean --expunge
$ ./configure
```

```
You have bazel 0.13 installed.
Please specify the location of python. [Default is /Users/user/.pyenv/versions/tensorflow-gpu/bin/python]: 


Found possible Python library paths:
  /Users/user/.pyenv/versions/tensorflow-gpu/lib/python3.6/site-packages
Please input the desired Python library path to use.  Default is [/Users/user/.pyenv/versions/tensorflow-gpu/lib/python3.6/site-packages]

Do you wish to build TensorFlow with Google Cloud Platform support? [Y/n]: n
No Google Cloud Platform support will be enabled for TensorFlow.

Do you wish to build TensorFlow with Hadoop File System support? [Y/n]: n
No Hadoop File System support will be enabled for TensorFlow.

Do you wish to build TensorFlow with Amazon S3 File System support? [Y/n]: n
No Amazon S3 File System support will be enabled for TensorFlow.

Do you wish to build TensorFlow with Apache Kafka Platform support? [y/N]: n
No Apache Kafka Platform support will be enabled for TensorFlow.

Do you wish to build TensorFlow with XLA JIT support? [y/N]: n
No XLA JIT support will be enabled for TensorFlow.

Do you wish to build TensorFlow with GDR support? [y/N]: n
No GDR support will be enabled for TensorFlow.

Do you wish to build TensorFlow with VERBS support? [y/N]: n
No VERBS support will be enabled for TensorFlow.

Do you wish to build TensorFlow with OpenCL SYCL support? [y/N]: n
No OpenCL SYCL support will be enabled for TensorFlow.

Do you wish to build TensorFlow with CUDA support? [y/N]: y
CUDA support will be enabled for TensorFlow.

Please specify the CUDA SDK version you want to use, e.g. 7.0. [Leave empty to default to CUDA 9.0]: 9.1


Please specify the location where CUDA 9.1 toolkit is installed. Refer to README.md for more details. [Default is /usr/local/cuda]: 


Please specify the cuDNN version you want to use. [Leave empty to default to cuDNN 7.0]: 


Please specify the location where cuDNN 7 library is installed. Refer to README.md for more details. [Default is /usr/local/cuda]:


Please specify a list of comma-separated Cuda compute capabilities you want to build with.
You can find the compute capability of your device at: https://developer.nvidia.com/cuda-gpus.
Please note that each additional compute capability significantly increases your build time and binary size. [Default is: 3.5,5.2]6.1


Do you want to use clang as CUDA compiler? [y/N]: n
nvcc will be used as CUDA compiler.

Please specify which gcc should be used by nvcc as the host compiler. [Default is /usr/bin/gcc]: 


Do you wish to build TensorFlow with MPI support? [y/N]: 
No MPI support will be enabled for TensorFlow.

Please specify optimization flags to use during compilation when bazel option "--config=opt" is specified [Default is -march=native]: 


Would you like to interactively configure ./WORKSPACE for Android builds? [y/N]: 
Not configuring the WORKSPACE for Android builds.

Preconfigured Bazel build configs. You can use any of the below by adding "--config=<>" to your build command. See tools/bazel.rc for more details.
	--config=mkl         	# Build with MKL support.
	--config=monolithic  	# Config for mostly static monolithic build.
Configuration finished
```

## Build
```bash
$ bazel build --config=cuda --config=opt --verbose_failures --action_env PATH --action_env LD_LIBRARY_PATH --action_env DYLD_LIBRARY_PATH //tensorflow/tools/pip_package:build_pip_package
```

#### 创建wheel文件并安装
```bash
$ bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
$ cd ~
$ sudo pip install /tmp/tensorflow_pkg/tensorflow-1.8-cp36-cp36m-macosx_10_13_x86_64.whl
```