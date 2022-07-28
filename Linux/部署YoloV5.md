---
title: 部署YoloV5
author: chiazz
date: 2021-07-10 15:33:25
tags: YoloV5
photos: ["https://tc.chaizz.com/5e4e19f6e15111eb9d7c5254006b8f1d.jpeg"]

---

​                                    

<!--more-->

一、安装Ubuntu 20.04 系统。

二、更换Ubuntu 源为国内源。（尽量不要用阿里源）

三、升级包

```shell
sudo apt-get update    
```

```shell
sudo apt-get upgrade
```

四、下载Nvidia 驱动。[下载地址](https://www.nvidia.cn/content/DriverDownload-March2009/confirmation.php?url=/XFree86/Linux-x86_64/460.84/NVIDIA-Linux-x86_64-460.84.run&lang=cn&type=TITAN)。根据自己的显卡查找驱动。

- 禁用nouveau驱动
  
  - 编辑 /etc/modprobe.d/blacklist-nouveau.conf 
    
    ```
    blacklist nouveau
    blacklist lbm-nouveau
    options nouveau modeset=0
    alias nouveau off
    alias lbm-nouveau off
    ```
  
  - 接下来然后执行：
    
    ```shell
    echo options nouveau modeset=0 | sudo tee -a /etc/modprobe.d/nouveau-kms.conf
    ```
  
  - 然后重启
    
    ```shell
    sudo reboot
    ```

提示gcc 问题：

- 直接安装  `sudo apt-get install gcc g++`

提示make问题：

- 直接安装 `sudo apt-get install  make`

五、安装CUDA。（需要根据pytorch 的版本选择CUDA版本。这里选择的是CUDA 11.1）  下载地址：[CUDA地址](https://developer.nvidia.com/cuda-11.1.0-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=2004&target_type=runfilelocal)。

官网安装步骤：

```shell
wget https://developer.download.nvidia.com/compute/cuda/11.1.0/local_installers/cuda_11.1.0_455.23.05_linux.run
sudo sh cuda_11.1.0_455.23.05_linux.run
```

接下来按照提示输入。

选择安装界面：因为上面已经安装过驱动，所以取消选择第一个 driver ，然后选中 按下回车。

配置CUDA环境变量

```shell
sudo vim ~/.bashrc
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-11.1/lib64
export PATH=$PATH:/usr/local/cuda-11.1/bin
export CUDA_HOME=$CUDA_HOME:/usr/local/cuda-11.1
source  ~/.bashrc
```

验证是CUDA否安装成功

```
nvcc -V
输出CUDA版本即安装成功
```

六、安装CUDNN。 下载地址：[CUDNN]() 。 需要登录账户 。

七、配置Python 虚拟环境 （Pyenv）

- 安装步骤
  
  - 安装git
    
    ```shell
    sudo apt-get install git    
    git clone https://github.com/yyuu/pyenv.git ~/.pyenv
    ```
  
  - 打开环境变量
    
    ```shell
    sudo vim ~/.bashrc
    #输入以下内容：
    export PATH=~/.pyenv/bin:$PATH
    export PYENV_ROOT=~/.pyenv
    eval "$(pyenv init -)"
    ```
    
    ```shell
    刷新环境变量
    source ~/.bashrc
    ```
  
  - 查看可安装的Python版本
    
    ```
    pyenv install --list
    ```
  
  - 安装指定版本的Python
    
    ```
    pyenv install 3.8.5
    ```
  
  - 更新数据库
    
    ```
    pyenv rehash
    ```
  
  - 切换python版本
    
    ```
    # xxx表示要切换的版本
    **有三种切换方式 glocal local shell**
    　　1. glocal 全局环境,在未再次使用 glocal切换环境之前，一直使用此环境。
    　　2. local 本次登录环境。重启后，则环境失效，并返回当前glocal的环境。
    　　3. shell 局部（临时）环境。关闭命令行窗口，则环境失效，并返回当前glocal的环境。
    pyenv glocal xxx  
    ```
  
  - 查看已经安装的版本
    
    ```
    pyenv versions
    ```
  
  - 卸载已经安装的版本
    
    ```
    pyenv uninstall xxx
    ```
  
  - 安装virtualenv   创建虚拟环境
    
    ```
    git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
    ```
  
  - 添加环境变量
    
    ```
    cho 'eval "$(pyenv virtualenv-init -)"' >> ~/.bashrc
    
    source ~/.bashrc
    ```
  
  - 创建虚拟环境
    
    ```
    pyenv virtualenv 3.7.0 yolo385
    ```
  
  - 删除虚拟环境
    
    ```
    rm -rf ~/.pyenv/versions/env370
    ```

八、安装pytorch

torch 官网 ：[torch](https://pytorch.org/)

按照自己的配置选择：

```
pip3 install torch==1.9.0+cu111 torchvision==0.10.0+cu111 torchaudio==0.9.0 -f https://download.pytorch.org/whl/torch_stable.html
```

出现 not import _lzma :

解决办法 ：重新安装pandas
