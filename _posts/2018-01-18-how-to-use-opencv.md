---
layout: post
title: "OpenCV 使用记录"
description: ""
category: 技术
tags: [ 计算机视觉]
---


## 1.安装
### 1.1 windows
### 1.2 linux (Debian)

- [Install OpenCV on Ubuntu or Debian](http://milq.github.io/install-opencv-ubuntu-debian/)
- [opensv-tutorial_linux_install](https://docs.opencv.org/master/d7/d9f/tutorial_linux_install.html)
- [Ubuntu 16.04: How to install OpenCV](https://www.pyimagesearch.com/2016/10/24/ubuntu-16-04-how-to-install-opencv/)
- [Installing OpenCV 3.2.0 for Python 3.5.2 on Ubuntu 16.04.2 LTS](https://www.begueradj.com/installing-opencv-3.2.0-for-python-3.5.2-on-ubuntu-16.04.2-lts.html)
- [Ubuntu 16.04编译安装OpenCV（Python）](http://blog.topspeedsnail.com/archives/4755/comment-page-1)
- [ubuntu下编译安装OpenCV 3.2.0 + OpenCV_contrib](https://ricky.moe/2017/08/27/ubuntu-opencv-3-2-0-install/)
- [OpenCV闯关记——Ubuntu16.04 OpenCV Python开发环境搭建](https://segmentfault.com/a/1190000007715243)
- [Install OpenCV in Ubuntu 16.04 LTS (Linux)-youtube](https://www.youtube.com/watch?v=0vjC2UHptU4)
- [Install opencv3 for python 3.5.0 with pyenv on ubuntu 14.04](https://gist.github.com/pohmelie/cf4eda5df24303325b16)
- [Python 3.6, OpenCV 3.2, and PyEnv on macOS Sierra (and Ubuntu)](https://medium.com/@nszceta/python-3-6-opencv-3-2-and-pyenv-on-macos-sierra-6ebcebd6193e)
- [opencv_installation_scripts](https://github.com/nszceta/opencv_installation_scripts)
- [Compiling OpenCV 3 with Python bindings and pyenv](https://gist.github.com/rjw57/d50dd8a023afa84a5bdb)
- [Ubuntu16.04下，通过pyenv安装anaconda，并安装tensorflow-gpu和opencv等库的简单方法](http://blog.csdn.net/mw5wind/article/details/70194938)
- [ubuntu 在pyenv環境中安裝 opencv](https://hk.saowen.com/a/984ec86613a107149d5f3ff604ef810e54e6a21bd0914021f604cdaa2ef21846)
- [Build opencv for ubuntu 16.04 with pyenv](https://gist.github.com/pohmelie/906a79f8ad665d0591186549e191ae9f)
- [在Ubuntu 16.04下装炼丹炉(python3+pyenv+opencv3+cuda8+tensorflow+mxnet+warpctc)](https://zhuanlan.zhihu.com/p/25304537)
- [Install opencv3 for python 3.5.0 with pyenv on ubuntu 14.04](https://gist.github.com/pohmelie/cf4eda5df24303325b16#file-readme-md)

#### 1.2.1 从源码编译
##### 安装依赖

```
# Remove any previous installations of x264</h3>
$ sudo apt-get remove x264 libx264-dev
# If you are using Ubuntu 16.04
$ sudo apt-get install libtiff5-dev
```

- Update packages

```
$ sudo apt-get update
$ sudo apt-get upgrade
```

- 基本编译工具

```
$ sudo apt-get install build-essential cmake pkg-config checkinstall
```

- 图像处理相关依赖


```
$ sudo apt-get install libjpeg8-dev libtiff5-dev libjasper-dev libpng12-dev 
# ref: https://github.com/milq/milq/blob/master/scripts/bash/install-opencv.sh
$ sudo apt-get install zlib1g-dev libjpeg-dev libwebp-dev libpng-dev libopenexr-dev libgdal-dev
```

- 视频处理相关

```
$ sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
$ sudo apt-get install libxvidcore-dev libx264-dev

$ sudo apt-get install libdc1394-22-dev libtheora-dev libvorbis-dev  yasm libopencore-amrnb-dev libopencore-amrwb-dev libxine2-dev

$ sudo apt-get install libgstreamer0.10-dev libgstreamer-plugins-base0.10-dev
```

- GUI 模块

```
# GTK
$ sudo apt-get install libgtk-3-dev
# QT
$ sudo apt-get install -y qt5-default libvtk6-dev libqt4-dev libgtk2.0-dev
```

- 高性能库


```
$ sudo apt-get install libatlas-base-dev gfortran libtbb-dev libeigen3-dev
```

- python 开发相关库


```
$ sudo apt-get install python2.7-dev python3.6-dev
```
- Documentation

```
$ sudo apt-get install -y doxygen
```
- 其它

```
sudo apt-get install libprotobuf-dev protobuf-compiler
sudo apt-get install libgoogle-glog-dev libgflags-dev
sudo apt-get install libgphoto2-dev libhdf5-dev
sudo apt-get install libfaac-dev libmp3lame-dev 
sudo apt-get install v4l-utils
```
##### 编译 OpenCV
- 下载 OpenCV 和 opencv_contrib 源码

`opencv_contrib` 是一些扩展功能和 non-free 代码

```
# 1. git clone 的方式
$ git clone https://github.com/opencv/opencv.git

# 2. curl 下载 release 版本
$ curl -L https://github.com/opencv/opencv/archive/3.4.0.zip -o opencv34.zip
$ curl -L https://github.com/opencv/opencv_contrib/archive/3.4.0.zip -o opencv34contrib.zip

# 3. wget 下载 release 版本
$ wget -O  opencv34.zip https://github.com/opencv/opencv/archive/3.4.0.zip
$ wget -O  opencv34contrib.zip https://github.com/opencv/opencv_contrib/archive/3.4.0.zip

```
> 注意：opencv 和 opencv_contrib 的版本要一致。

- 切换到 Pyenv 的虚拟环境

```
$ pyenv install 3.6.1
$ pyenv virtualenv 3.6.1 cv-py3
$ pyenv activate cv-py3
```

- 安装 numpy

```
$ pip install numpy
```

- 编译 OpenCV


```
$ unzip opencv34.zip
$ unzip opencv34contrib.zip
$ cd opencv-3.4.0
$ mkdir build
$ cd build

# 1. https://github.com/BVLC/caffe/wiki/OpenCV-3.3-Installation-Guide-on-Ubuntu-16.04
$ cmake -D CMAKE_BUILD_TYPE=RELEASE \
        -D CMAKE_INSTALL_PREFIX=/usr/local \
        -D FORCE_VTK=ON \
        -D WITH_TBB=ON \
        -D WITH_V4L=ON \
        -D WITH_QT=ON \
        -D WITH_OPENGL=ON \  
        -D WITH_CUBLAS=ON \
        -D CUDA_NVCC_FLAGS="-D_FORCE_INLINES" \
        -D WITH_GDAL=ON \
        -D WITH_XINE=ON \
        -D BUILD_EXAMPLES=ON ..
        
# 2. https://www.begueradj.com/installing-opencv-3.2.0-for-python-3.5.2-on-ubuntu-16.04.2-lts.html
$ cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D INSTALL_PYTHON_EXAMPLES=ON \
    -D INSTALL_C_EXAMPLES=OFF \
    -D OPENCV_EXTRA_MODULES_PATH=~/downloads/opencv_contrib-3.4.0/modules \
    -D PYTHON_EXECUTABLE=~/.virtualenvs/cv/bin/python \
    -D BUILD_EXAMPLES=ON .. 
    
# 3. https://zhuanlan.zhihu.com/p/25304537
cmake  -D CMAKE_BUILD_TYPE=RELEASE \
  -D CMAKE_INSTALL_PREFIX=/usr/local \
  -D BUILD_SHARED_LIBS=OFF \
  -D WITH_CUDA=ON \
  -D WITH_QT=ON \
  -D WITH_OPENGL=ON \
  -D ENABLE_FAST_MATH=ON \
  -D CUDA_FAST_MATH=ON \
  -D CUDA_NVCC_FLAGS="-D_FORCE_INLINES" \
  -D WITH_CUBLAS=ON \
  -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib/modules \
  -D BUILD_TESTS=OFF \
  -D BUILD_PERF_TESTS=OFF \
  -D BUILD_opencv_python3=ON \
  -D OPENCV_BUILD_3RDPARTY_LIBS=ON \
  -D PYTHON3_EXECUTABLE=$(which python3) \
  -D PYTHON3_INCLUDE_PATH=$(python3 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
  -D PYTHON3_LIBRARIES=$(python3 -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())") \
  -D OPENCV_ENABLE_NONFREE=ON \
  ..
  
# 4. https://github.com/nszceta/opencv_installation_scripts/blob/master/opencv_install_ubuntu_1604.sh
cmake \
    -D BUILD_opencv_legacy=OFF \
    -D CMAKE_INSTALL_PREFIX=/opt/opencv32_py36 \
    -D OPENCV_EXTRA_MODULES_PATH=/opt/src/opencv32_py36_contrib/modules \
    -D BUILD_opencv_python2=OFF \
    -D BUILD_opencv_python3=ON \
    -D BUILD_TIFF=ON \
    -D BUILD_opencv_java=OFF \
    -D WITH_CUDA=ON \
    -D ENABLE_FAST_MATH=1 \
    -D CUDA_FAST_MATH=1 \
    -D WITH_CUBLAS=1 \
    -D CUDA_GENERATION="" \
    -D CUDA_ARCH_BIN=3.0 \
    -D ENABLE_AVX=ON \
    -D WITH_OPENGL=ON \
    -D WITH_OPENCL=ON \
    -D WITH_IPP=OFF \
    -D WITH_TBB=ON \
    -D WITH_EIGEN=ON \
    -D WITH_V4L=ON \
    -D WITH_VTK=OFF \
    -D BUILD_TESTS=OFF \
    -D BUILD_PERF_TESTS=OFF \
    -D CMAKE_BUILD_TYPE=RELEASE \
    -D PYTHON3_LIBRARY=$(python -c "import re, os.path; print(os.path.normpath(os.path.join(os.path.dirname(re.__file__), '..', 'libpython3.6m.so')))") \
    -D PYTHON3_EXECUTABLE=$(which python) \
    -D PYTHON3_INCLUDE_DIRS=$(python -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
    -D PYTHON3_PACKAGES_PATH=$(python -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())") ..
     
# me
#  -D CUDA_GENERATION="" \
#  -D CUDA_ARCH_BIN=3.0 \
$ cmake -D BUILD_opencv_legacy=OFF \
        -D CMAKE_INSTALL_PREFIX=/usr/local \
        -D OPENCV_EXTRA_MODULES_PATH=~/downloads/opencv_contrib-3.4.0/modules \
        -D BUILD_opencv_python2=OFF \
        -D BUILD_opencv_python3=ON \
        -D OPENCV_BUILD_3RDPARTY_LIBS=ON \
        -D BUILD_TIFF=ON \
        -D BUILD_opencv_java=OFF \
        -D WITH_CUDA=ON \
        -D ENABLE_FAST_MATH=ON \
        -D CUDA_FAST_MATH=ON \
        -D CUDA_NVCC_FLAGS="-D_FORCE_INLINES" \
        -D WITH_CUBLAS=ON \
	     -D ENABLE_AVX=ON \
        -D WITH_OPENGL=ON \
        -D WITH_OPENCL=ON \
	     -D WITH_IPP=OFF \
	     -D WITH_TBB=ON \
        -D WITH_EIGEN=ON \
        -D WITH_V4L=ON \
        -D WITH_VTK=OFF \
        -D WITH_QT=ON \
        -D WITH_GDAL=ON \
        -D WITH_XINE=ON \
	     -D BUILD_EXAMPLES=ON \
        -D BUILD_TESTS=OFF \
        -D BUILD_PERF_TESTS=OFF \
        -D BUILD_SHARED_LIBS=OFF \
        -D CMAKE_BUILD_TYPE=RELEASE \
        -D PYTHON3_LIBRARY=$(python -c "import re, os.path; print(os.path.normpath(os.path.join(os.path.dirname(re.__file__), '..', 'libpython3.6m.so')))") \
        -D PYTHON3_LIBRARIES=$(python3 -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())") \
        -D PYTHON3_INCLUDE_PATH=$(python3 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
        -D PYTHON3_EXECUTABLE=$(which python) \
        -D PYTHON3_INCLUDE_DIRS=$(python -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
        -D PYTHON3_PACKAGES_PATH=$(python -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())") \
        -D OPENCV_ENABLE_NONFREE=ON \
        ..
```
> You will need to change the CUDA_NVCC_FLAGS to the following if you have CUDA Toolkit 9.0:
CUDA_NVCC_FLAGS="-D_FORCE_INLINES --expt-relaxed-constexpr"

- 安装 OpenCV

```
$ make -j $(($(nproc) + 1))
$ sudo make install
$ sudo /bin/bash -c 'echo "/usr/local/lib" > /etc/ld.so.conf.d/opencv.conf'
$ sudo ldconfig
$ sudo apt-get update
$ sudo checkinstall
$ pkg-config --modversion opencv
```




编译的时候出错了，`undefined symbol: gotoblas issue ` 是 [Installing lapack for numpy
](https://stackoverflow.com/questions/8917977/installing-lapack-for-numpy) 这个问题,解决方法是:

- https://github.com/xianyi/OpenBLAS/issues/1033
- [Symbol lookup error: /usr/lib/libblas.so.3: undefined symbol: gotoblas #1114](https://github.com/xianyi/OpenBLAS/issues/1114)

```
# remove libopenblas-base
$ sudo apt-get remove libopenblas-base
# 没试过
$ sudo update-alternatives --set libblas.so.3 /usr/lib/openblas-base/libblas.so.3
```
重新编译 `OpenBLAS`

- [Replacing system BLAS/updating APT OpenBLAS in Ubuntu/Debian](https://github.com/xianyi/OpenBLAS/wiki/faq#debianlts)

```
$ make clean
$ make DYNAMIC_ARCH=1
$ sudo make DYNAMIC_ARCH=1 install
$ sudo apt install libblas-dev liblapack-dev
$ $ sudo update-alternatives --install /usr/lib/libblas.so.3 libblas.so.3 /opt/OpenBLAS/lib/libopenblas.so.0 41 --slave /usr/lib/liblapack.so.3 liblapack.so.3 /opt/OpenBLAS/lib/libopenblas.so.0

# update
sudo update-alternatives --remove libblas.so.3 /opt/OpenBLAS/lib/libopenblas.so.0
```

- 链接 `cv2.so` 到对应的 Python 的 site-packages





#### 1.2.2 直接通过 apt 安装 (Debian)

```
sudo apt-get install libopencv-dev python-opencv
```
##### 缺点
1. 安装的是比较旧的版本，有一些新特性没有。
2. Python3 不支持

#### 1.2.3 通过官网安装
- 先把旧版本的安装移除

```
sudo apt-get autoremove libopencv-dev python-opencv
```
- 下载 [ install-opencv.sh](https://github.com/milq/milq/blob/master/scripts/bash/install-opencv.sh) 脚本
里面的内容:


```
######################################
# INSTALL OPENCV ON UBUNTU OR DEBIAN #
######################################

# |         THIS SCRIPT IS TESTED CORRECTLY ON         |
# |----------------------------------------------------|
# | OS             | OpenCV       | Test | Last test   |
# |----------------|--------------|------|-------------|
# | Ubuntu 16.04.2 | OpenCV 3.2.0 | OK   | 20 May 2017 |
# | Debian 8.8     | OpenCV 3.2.0 | OK   | 20 May 2017 |
# | Debian 9.0     | OpenCV 3.2.0 | OK   | 25 Jun 2017 |

# 1. KEEP UBUNTU OR DEBIAN UP TO DATE

sudo apt-get -y update
sudo apt-get -y upgrade
sudo apt-get -y dist-upgrade
sudo apt-get -y autoremove


# 2. INSTALL THE DEPENDENCIES

# Build tools:
sudo apt-get install -y build-essential cmake

# GUI (if you want to use GTK instead of Qt, replace 'qt5-default' with 'libgtkglext1-dev' and remove '-DWITH_QT=ON' option in CMake):
sudo apt-get install -y qt5-default libvtk6-dev

# Media I/O:
sudo apt-get install -y zlib1g-dev libjpeg-dev libwebp-dev libpng-dev libtiff5-dev libjasper-dev libopenexr-dev libgdal-dev

# Video I/O:
sudo apt-get install -y libdc1394-22-dev libavcodec-dev libavformat-dev libswscale-dev libtheora-dev libvorbis-dev libxvidcore-dev libx264-dev yasm libopencore-amrnb-dev libopencore-amrwb-dev libv4l-dev libxine2-dev

# Parallelism and linear algebra libraries:
sudo apt-get install -y libtbb-dev libeigen3-dev

# Python:
sudo apt-get install -y python-dev python-tk python-numpy python3-dev python3-tk python3-numpy

# Java:
sudo apt-get install -y ant default-jdk

# Documentation:
sudo apt-get install -y doxygen


# 3. INSTALL THE LIBRARY (YOU CAN CHANGE '3.2.0' FOR THE LAST STABLE VERSION)

sudo apt-get install -y unzip wget
wget https://github.com/opencv/opencv/archive/3.2.0.zip
unzip 3.2.0.zip
rm 3.2.0.zip
mv opencv-3.2.0 OpenCV
cd OpenCV
mkdir build
cd build
cmake -DWITH_QT=ON -DWITH_OPENGL=ON -DFORCE_VTK=ON -DWITH_TBB=ON -DWITH_GDAL=ON -DWITH_XINE=ON -DBUILD_EXAMPLES=ON -DENABLE_PRECOMPILED_HEADERS=OFF ..
make -j4
sudo make install
sudo ldconfig


# 4. EXECUTE SOME OPENCV EXAMPLES AND COMPILE A DEMONSTRATION

# To complete this step, please visit 'http://milq.github.io/install-opencv-ubuntu-debian'.
```

- 执行安装

```
$ bash install-opencv.sh
```

> Ubuntu 14.04 LTS, Ubuntu 16.04 LTS and Debian 8.0 "Jessie", with OpenCV 3.0.0, OpenCV 3.1.0, and OpenCV 3.2.0.

- 测试

```
# C++
$ cd build/bin
$ ./cpp-example-edge ../../samples/data/fruits.jpg
# python
$ cd samples/python
$ python video.py
```
#### 1.2.4 通过 pip 安装 (不推荐)
- [Installing OpenCV 3.3.0 on Ubuntu 16.04 LTS](https://medium.com/@debugvn/installing-opencv-3-3-0-on-ubuntu-16-04-lts-7db376f93961)

```
$ pip install opencv-contrib-python
```
暂时还没试过，根据 [Installing OpenCV 3.2.0 for Python 3.5.2 on Ubuntu 16.04.2 LTS](https://www.begueradj.com/installing-opencv-3.2.0-for-python-3.5.2-on-ubuntu-16.04.2-lts.html)
> The reason is that this easy way of doing things will lead you to install a package which is not enough mature as you can not even deal with videos and can not even display an image using cv2.imshow() function. So do not be lazy and follow the below steps.

### Mac Os
- [pyenv + pyenv-virtualenv + OpenCV 3 on Mac OS X El Capitan](http://ehealth-aussie.blogspot.com/2016/10/pyenv-pyenv-virtualenv-opencv-3-on-mac.html)

```
$ brew install opencv3 --HEAD --with-python3 --with-ffmpeg --with-tbb --with-contrib --with-opengl --with-qt5

```

