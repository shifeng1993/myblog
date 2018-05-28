---
title: 【tensorflow】macOS 10.13.4 编译 GPU 版本的 TensorFlow 1.8
categories: tensorflow
date: 2018-05-26
tags:
  - tensorflow
---
参考文章：
- [如何为 MacOS 构建 TensorFlow-GPU 1.8？](https://github.com/zylo117/tensorflow-gpu-macosx/blob/master/HOW-TO-BUILD/HOW%20TO%20BUILD%20TENSORFLOW-GPU%20FOR%20MAC%20OSX-1.8.0rc1.md)
- [TensorFlow 1.5 编译教程](https://github.com/geekcui/tensorflow-macos-egpu/blob/master/macOS10.13.3-TensorFlow1.5.0.md)

# 环境要求
* MAC OSX 10.6+
* 在mac上禁用SIP
* NVIDIA Web-Drivers 驱动
* CUDA-Drivers 驱动
* CUDA 9.1 Toolkit 开发工具
* cuDNN 7.0.5 神经计算加速工具
* Python 3.6
* xcode 8.2
* bazel 0.10


# 环境准备
## Homebrew
如果尚未安装，Homebrew还将安装最新的Apple Command-Line-Tools
```bash
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

## 安装coreutils，llvm，OpenMP
```bash
$ brew install coreutils llvm cliutils/apple/libomp
```

## Python 依赖
```bash
$ pip install six numpy wheel
```

## bazel 
下载0.10版本，
[bazel发布页](https://github.com/bazelbuild/bazel/releases)
需要注意，这里必须是 0.10 版本，新或旧都能导致编译失败。

二进制文件安装方法
```bash
$ chmod +x bazel-<version>-installer-darwin-x86_64.sh
$ ./bazel-<version>-installer-darwin-x86_64.sh 
```

## 降级 Xcode 到 8.2
不必在最开始就降级，这一步可以放到准备环节最后

去apple开发者官网下载包
```
$ sudo xcode-select -s /Applications/Xcode8.2.app
```

换回最新版开发ios可以用
```
$ sudo xcode-select -s /Applications/Xcode.app
```

## NVIDIA
### NVIDIA Web-Drivers
在安装CUDA驱动程序之前下载并安装与mac版本对应的GPU驱动。
[tonymacx86 下载](https://www.tonymacx86.com/nvidia-drivers/)

### 安装 CUDA Toolkit 9.1
cuda开发套件自带duda驱动
[下载 CUDA-9.1](https://developer.nvidia.com/cuda-downloads?target_os=MacOSX&target_arch=x86_64&target_version=1013&target_type=dmglocal)


### 安装 cuDNN
[下载cuDNN 7.0.5](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/v7.0.5/prod/9.1_20171129/cudnn-9.1-osx-x64-v7-ga)[^1]

切换到解压缩的CUDNN目录
```bash
$ sudo cp cuda/include/cudnn.h /usr/local/cuda/include
$ sudo cp cuda/lib/libcudnn_static.a /usr/local/cuda/lib
$ sudo cp cuda/lib/libcudnn.7.dylib /usr/local/cuda/lib
$ sudo ln -s /usr/local/cuda/lib/libcudnn.7.dylib /usr/local/cuda/lib/libcudnn.dylib
$ sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib/libcudnn*
```

### 添加环境变量
```
export CUDA_HOME=/usr/local/cuda
export DYLD_LIBRARY_PATH=/usr/local/cuda/lib:/usr/local/cuda/extras/CUPTI/lib
export LD_LIBRARY_PATH=$DYLD_LIBRARY_PATH
export PATH=$PATH:$DYLD_LIBRARY_PATH
```
### 检查NVIDIA开发环境
```bash
nvcc -V
```

# Build准备
## 拉取 TensorFlow 源码 release 1.8 分支
```bash
$ git clone https://github.com/tensorflow/tensorflow -b r1.8
$ cd tensorflow
```

## 修改代码，使其与macOS兼容
### 替换掉以下三个文件的 __align__(sizeof(T))
```bash
$ cd tensorflow
$ sed -i -e "s/ __align__(sizeof(T))//g" tensorflow/core/kernels/concat_lib_gpu_impl.cu.cc
$ sed -i -e "s/ __align__(sizeof(T))//g" tensorflow/core/kernels/depthwise_conv_op_gpu.cu.cc
$ sed -i -e "s/ __align__(sizeof(T))//g" tensorflow/core/kernels/split_lib_gpu.cu.cc
```
### 添加依赖头文件nccl.h
[nccl.h下载](https://github.com/NVIDIA/nccl/blob/master/src/nccl.h) 放在 `third_party/nccl` 文件夹内

### 修改`tensorflow/workspace.bzl`文件
```
  tf_http_archive(
      name = "protobuf_archive",
      urls = [
          "https://mirror.bazel.build/github.com/google/protobuf/archive/396336eb961b75f03b25824fe86cf6490fb75e3a.tar.gz",
          "https://github.com/google/protobuf/archive/396336eb961b75f03b25824fe86cf6490fb75e3a.tar.gz",
      ],
      sha256 = "846d907acf472ae233ec0882ef3a2d24edbbe834b80c305e867ac65a1f2c59e3",
      strip_prefix = "protobuf-396336eb961b75f03b25824fe86cf6490fb75e3a",
  )
```
搜索如上替换为如下
```
  tf_http_archive(
      name = "protobuf_archive",
      urls = [
          "https://github.com/dtrebbien/protobuf/archive/50f552646ba1de79e07562b41f3999fe036b4fd0.tar.gz",
      ],
      sha256 = "eb16b33431b91fe8cee479575cee8de202f3626aaf00d9bf1783c6e62b4ffbc7",
      strip_prefix = "protobuf-50f552646ba1de79e07562b41f3999fe036b4fd0",
  )
```
### 修复`third_party/gpus/cuda/BUILD.tpl`文件-lgomp报错
```
linkopts = ["-lgomp"],
```
搜索如上，注释掉
```
# linkopts = ["-lgomp"],
```

# 开始Build
## Build 配置

```bash
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
$ bazel clean --expunge
$ bazel build --config=cuda --config=opt --cxxopt="-D_GLIBCXX_USE_CXX11_ABI=0" --action_env PATH --action_env LD_LIBRARY_PATH --action_env DYLD_LIBRARY_PATH //tensorflow/tools/pip_package:build_pip_package
```

注意cpu老旧可以使用以下命令编译
```bash
$ bazel build --config=cuda --config=opt --cxxopt="-D_GLIBCXX_USE_CXX11_ABI=0" --action_env PATH --action_env LD_LIBRARY_PATH --action_env DYLD_LIBRARY_PATH --copt=-march=native --copt=-msse4.1 --copt=-msse4.2 --copt=-mavx --copt=-mavx2 --copt=-mfma //tensorflow/tools/pip_package:build_pip_package
```

## 创建wheel文件并安装
```bash
$ bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
$ cd ~
$ sudo pip install /tmp/tensorflow_pkg/tensorflow-1.8-cp36-cp36m-macosx_10_13_x86_64.whl
```