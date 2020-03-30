# Opencv - Caffe - DLIB Ubuntu install how-to.

This tutorial provides some hints on how to install Opencv/Caffe/Dlib on Ubuntu (tested on 16.04) with emphasis on compilation, Debian package creation and python usage.

Usually after compiling these libraries, installation is performed using *make --install* command. This choice of installation creates 2 drawbacks:

- uninstalling may be troublesome.
- creation of your own distributable libraries is difficult.

## Contents
1. [Assumptions](README.md#1-Assumptions)
2. [OpenCV and Contrib Modules](https://github.com/heethesh/Computer-Vision-and-Deep-Learning-Setup/blob/master/README.md#7-install-opencv-and-contrib-modules)

    

## 1. Assumptions
Before installing anything, update package information is important.

    sudo apt-get update
    sudo apt-get upgrade



## CUDA

In case it is intended to use CUDA, then it will be necessary do install NVIDIA drivers. This tutorial used *version 440*. After installing graphics card driver, installation of the following libraries shall be done:

- CUDA driver available from the [official nvidia website here](https://developer.nvidia.com/cuda-downloads?target_os=Linux).
- cuDNN available from website [official cuDNN website](https://developer.nvidia.com/cudnn).



## BOOST

Both Caffe and Dlib require boost to be installed on the system. This tutorial used *version 1.72.0*. After [downloading Boost](https://www.boost.org/users/history/version_1_72_0.html) . Place source code in a directory of your choice and then compiled and install according with [Boost easy installation](https://www.boost.org/doc/libs/1_72_0/more/getting_started/unix-variants.html#easy-build-and-install), summarized below:




    ./bootstrap.sh --with-python-version=3.7 --with-libraries=all --prefix=/usr/local
    
    sudo ./b2 install 


Note: make sure to use python version installed in your machine. This tutorial is based on *version 3.7*.

## 2. Install OpenCV and Contrib Modules
First we will install dependencies required for our build to work. Other libraries may be necessary, depending of the options you set on cmake.

     sudo apt-get install build-essential cmake git unzip pkg-config
     sudo apt-get install libjpeg-dev libpng-dev libtiff-dev
     sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev
     sudo apt-get install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev
     sudo apt-get install libfaac-dev libmp3lame-dev libtheora-dev
     sudo apt-get install libavresample-dev libvorbis-dev
     sudo apt-get install libopencore-amrnb-dev libopencore-amrwb-dev
     sudo apt-get install libgtk2.0-dev libcanberra-gtk*
     sudo apt-get install x264 libxvidcore-dev libx264-dev libgtk-3-dev
     sudo apt-get install libtbb2 libtbb-dev libdc1394-22-dev
     sudo apt-get install libv4l-dev v4l-utils
     cd /usr/include/linux
     sudo ln -s -f ../libv4l1-videodev.h videodev.h
     cd ~
     sudo apt-get install libxine2-dev
     sudo apt-get install software-properties-common
     sudo add-apt-repository "deb http://security.ubuntu.com/ubuntu xenial-security main"
     sudo apt-get update
     sudo apt-get install libjasper-dev
     sudo apt-get install libopenblas-dev libatlas-base-dev libblas-dev
     sudo apt-get install liblapack-dev gfortran
     sudo apt-get install libhdf5-dev protobuf-compiler
     sudo apt-get install libprotobuf-dev libgoogle-glog-dev libgflags-dev

#### NOTE:  version of OpenCV. 4.2.0 is used here

Clone OpenCV (or download it to a folder of your preference)

    git clone https://github.com/opencv/opencv.git
    cd opencv
    cd ..

Clone (or download) OpenCV-contrib 4.2.0:

    git clone https://github.com/opencv/opencv_contrib.git
    cd opencv_contrib
    cd ..

Some aspects to note:

- inform cmake to build debian binary  package (CPACK_BINARY_DEB) .
- correctly inform python version and directory to be used (3.7).
- better to manually indicate where cuda toolkit is installed.
- make sure to inform where openc_contrib is located.
- indicate where installation files will be placed. In our case it will be a local directory.

Configure and generate the MakeFile in */opencv/build* folder:

    cd opencv
    mkdir build
    mkdir install
    cd install
    
    cmake -D CMAKE_VERBOSE_MAKEFILE=0N
    	-D CMAKE_BUILD_TYPE=RELEASE
    	-D CMAKE_INSTALL_PREFIX='$HOME'/opencv/install
    	-D CPACK_BINARY_DEB=ON
    	-D OPENCV_EXTRA_MODULES_PATH='$HOME'/opencv_contrib/modules
    	-D BUILD_TIFF=ON
    	-D WITH_FFMPEG=ON \ -D WITH_GSTREAMER=ON
    	-D WITH_TBB=ON
    	-D BUILD_TBB=ON
    	-D WITH_EIGEN=ON \ -D WITH_V4L=ON
    	-D WITH_LIBV4L=ON
    	-D WITH_VTK=OFF
        -D WITH_OPENGL=OFF
        -D OPENCV_ENABLE_NONFREE=ON
        -D INSTALL_C_EXAMPLES=OFF \ -D INSTALL_PYTHON_EXAMPLES=OFF
        -D BUILD_NEW_PYTHON_SUPPORT=ON \ -D OPENCV_GENERATE_PKGCONFIG=ON
        -D BUILD_TESTS=OFF
        -D BUILD_EXAMPLES=OFF
        -D WITH_CUDA=ON
        -D CUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda-10.2
        -D ENABLE_FAST_MATH=ON
        -D CUDA_FAST_MATH=ON
        -D WITH_CUBLAS=ON
        -D PYTHON3_EXECUTABLE=/usr/bin/python3.7 ..


Compile and install:

    make -j$(nproc-1)
    make install
    make package
    ls -1 *.deb

Check that *OpenCV-4.2.0-\*.deb* files  are listed.

Install each of one *dev,licenses,main,python and scripts*  packages:
`sudo dpkg -i OpenCV-4.2.0*.deb`

Check Installation:

```
dpkg --get-selections | grep opencv
opencv-dev					install
opencv-libs					install
opencv-licenses				install
opencv-main					install
opencv-python				install
opencv-scripts				install
```

Check python installation

    python3.7
    >>> import cv2
    >>> cv2.__version__
    '4.2.0'

To uninstall OpenCV:

    sudo dpkg -r opencv-*.deb

