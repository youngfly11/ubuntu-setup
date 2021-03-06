# ubuntu 新机器配置指南
## 1. 更新源设置
### 1.1 备份原 更新源 列表
`sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak`

### 1.2 更新源设置
`sudo gedit /etc/apt/sources.list`，全部替换为如下内容

```
# deb cdrom:[Ubuntu 16.04 LTS _Xenial Xerus_ - Release amd64 (20160420.1)]/ xenial main restricted  
deb-src http://archive.ubuntu.com/ubuntu xenial main restricted #Added by software-properties  
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted  
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted multiverse universe #Added by software-properties  
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted  
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted multiverse universe #Added by software-properties  
deb http://mirrors.aliyun.com/ubuntu/ xenial universe  
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe  
deb http://mirrors.aliyun.com/ubuntu/ xenial multiverse  
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates multiverse  
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse  
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse #Added by software-properties  
deb http://archive.canonical.com/ubuntu xenial partner  
deb-src http://archive.canonical.com/ubuntu xenial partner  
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted  
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted multiverse universe #Added by software-properties  
deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe  
deb http://mirrors.aliyun.com/ubuntu/ xenial-security multiverse  
```
## 2. 准备工作
### 2.1 更新系统
`sudo apt-get upgrade`

### 2.2 安装常用软件
`sudo apt-get install sshfs nfs-common lvm2 dkms`

可能系统分辨率会有变化，先不用管，之后安装显卡驱动之后即可

`sudo apt-get install build-essential libopencv-dev python-opencv git htop vim python-dev tmux python-pip python-all-dev libatlas-base-dev gfortran openssh-server libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler libboost-all-dev libgflags-dev libgoogle-glog-dev liblmdb-dev graphviz unzip`

### 2.3 安装常用python包
`sudo pip install numpy jupyter pandas scipy scikit-learn matplotlib graphviz easydict`

## 3.显卡驱动和cuda, cuDNN
### 3.1 显卡驱动安装
- `sudo apt-get update`
- `sudo apt-get remove --purge nvidia*`
- `sudo add-apt-repository ppa:graphics-drivers/ppa`
- `sudo apt-get install nvidia-378`
- `sudo apt-get install mesa-common-dev`
- `sudo apt-get install freeglut3-dev`
- -----重启系统 reboot----------
- `sudo apt-get reboot`

### 3.2 显卡驱动测试
- `nvidia-smi`
- `nvidia-settings`

### 3.3 安装cuda 8.0
- 下载文件 `wget https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_375.26_linux-run`

- 更改文件权限 `chmod +x cuda_8.0.61_375.26_linux-run`
- 安装cuda `sudo sh cuda_8.0.61_375.26_linux-run`
  - ！！！务必注意，询问是否安装 nvidia driver时，选择no
- 添加cuda路径
  - `sudo gedit ~/.bashrc`
  - 在最后添加
    - `export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}`
    - `export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}`
  - `sudo gedit /etc/profile`
    - 最后添加 `export PATH = /usr/local/cuda/bin:$PATH`
  - `sudo vi /etc/ld.so.conf.d/cuda.conf`
    - 添加`/usr/local/cuda/lib64`
  - 更新配置 `sudo ldconfig`
  - -------------test----------
  - `cd /usr/local/cuda-8.0/samples/1_Utilities/deviceQuery`
  - `sudo make`
  - `sudo ./deviceQuery` then GPU info appears. Result = pass

### 3.4 安装cuDNN 5
Download cudnn 5.1 for linux from https://developer.nvidia.com/cudnn
1. `tar xvzf cudnn-8.0-linux-x64-v5.1-ga.tgz`
2. `sudo cp -P cuda/include/cudnn.h /usr/local/cuda/include`
3. `sudo cp -P cuda/lib64/libcudnn* /usr/local/cuda/lib64`
4. `sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*`
5. `sudo ldconfig`

## 4 安装opencv 3.2
Required Packages
```
[compiler] sudo apt-get install build-essential
[required] sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
[optional] sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
```
Download opencv for linux from http://opencv.org/downloads.html.
1. `unzip opencv-3.2.0.zip`
2. `cp opencv-3.2.0`
3. `make build && cp build`
4. `cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D WITH_CUDA=OFF ..`
5. `make -j $(nproc)`
6. `sudo make install`
7. `pip install opencv-python`
8. `sudo vi /etc/ld.so.conf.d/opencv.conf` 添加 `/usr/local/lib`
9. `sudo ldconfig`
10. test: 进入`python` ,then `import cv2`
