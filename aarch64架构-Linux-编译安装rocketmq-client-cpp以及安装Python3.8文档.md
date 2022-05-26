# 编译安装Rocketma-client-cpp



## 1、安装 Rocketmq-client-cpp 所需工具

- gcc-c++ 4.8.2: c++ compiler while need support C++11
- cmake 2.8.0: build jsoncpp require it
- automake 1.11.1: build libevent require it
- autoconf 2.65: build libevent require it
- libtool 2.2.6: build libevent require it

### 1.1 检查各项工具是否可用

```shell
g++ -v

# 如下提示代表已经安装， 需要指定gcc 版本为 4.8.2 (4.8.5 也可以安装成功)
gcc version 10.2.1 20210110 (Raspbian 10.2.1-6+rpi1) 

```

``` shell
cmake  -version

安装地址：https://zhuanlan.zhihu.com/p/110793004
```

```shell
automake --version
# 如下提示代表已经安装
automake (GNU automake) 1.16.1
```

```shell
automake --version

autoconf (GNU Autoconf) 2.69
```

```shell
libtool --version

libtool (GNU libtool) 2.4.6
```



### 1.2 命令行安装 cmake、automake 、autoconf 、libtool  (推荐apt-get 安装)

``` shell
sudo apt-get install cmake, aotumake, autoconf, libtool, libtool-bin
```



### 1.3 源码安装 gcc、cmake、automake 、autoconf 、libtool 

```shell
wget https://mi shellrrors.tuna.tsinghua.edu.cn/gnu/gcc/gcc-4.8.2/gcc-4.8.2.tar.gz
tar zxvf  gcc-4.8.2.tar.gz

configure --prefix=/home/share/tools/usr/gcc-4.8.2 --enable-threads=posix --disable-checking --disable-multilib --enable-languages=c,c+

make  && make install 


curl -OL https://github.com/Kitware/CMake/releases/download/v3.23.1/cmake-3.23.1.tar.gz
tar zxvf cmake-3.22.4.tar.gz
cd cmake-3.22.4
./configure --prefix=/home/share/tools/usr/cmake-3.22.4 && make  && make install 


curl -OL http://ftpmirror.gnu.org/automake/automake-1.11.1.tar.gz
tar -xzf automake-1.11.1.tar.gz
cd automake-1.11.1
./configure --prefix=/home/share/tools/usr/automake-1.11.1 && make && sudo make install


curl -OL http://ftpmirror.gnu.org/autoconf/autoconf-2.65.tar.gz
tar -xzf autoconf-2.65.tar.gz 
cd autoconf-2.65
./configure --prefix=/home/share/tools/us/autoconf && make && sudo make install
 
 
curl -OL http://ftpmirror.gnu.org/libtool/libtool-2.2.6.tar.gz
tar -xzf libtool-2.2.6.tar.gz
cd libtool-2.2.6
./configure --prefix=/home/share/tools/usr/libtool-2.2.6 && make && sudo make install
```


## 2、安装 Rocketma-client-cpp 所需依赖


```shell
# bzip2-devel 

sudo apt-get install zlib1g
sudo apt-get install zlib1g-dev
```


```shell
# zlib-devel
sudo apt-get install libbz2-dev
```


## 3、安装rocketmq-cpp

```shell
cd rocketmq-client-cpp-2.0.0

# 执行安装命令， 等待安装。
./build.sh
```



## 4、将编译安装好的so文件建立连接

```shell
ln -s /home/zjj/rocketmq/librocketmq.so /usr/lib/librocketmq.so
```





# 安装python3.8

## 1、下载安装Python

```shell
tar zxvf Python-3.8.13.tgz
cd Python-3.8.13
sudo ./configure 
sudo make && makeinstall
```

## 2、设置软连接 

```shell
# /usr/bin/python3.8/python 编译后的文件地址
sudo ln -s /usr/bin/python3.8/python /usr/bin/python38
```

 ## 3、安装 pip 

```shell
wget https://bootstrap.pypa.io/get-pip.py --no-check-certificate
python get-pip.py
```





# 问题：

## 问题1：

```shell
Unable to locally verify the issuer's authority.
To connect to github.com insecurely, use `--no-check-certificate'.
```

## 解决：

https://blog.csdn.net/iMatt/article/details/109570935

## 问题2：

```
unable to resolve host topeet: Name or service not knowncd
```

## 解决：

https://blog.csdn.net/ichuzhen/article/details/8241847

## 问题3：

```
安装libevent提示：
openssl is a must but can not be found.
```

## 解决：

```
sudo apt install libssl-dev
```



## 问题4：

安装gcc 缺少依赖：GMP

```
wget https://mirrors.tuna.tsinghua.edu.cn/gnu/gmp/gmp-4.2.tar.gz
tar -zxvf gmp-4.2.tar.gz
cd gmp-4.2
configure --prefix=/home/share/tools/usr/gmp-4.2
sudo make && make install
```

遇到问题：

```shell
configure: error: cannot guess build type; you must specify one
```

## 解决：

```SHELL
# 查看当前系统的型号
uname -a

# 根据钱型号测试
configure --prefix=/home/share/tools/usr/gmp-4.2 --build=aarch64-linnx

# 提示错误
Invalid configuration `aarch64-linux': machine `aarch64' not recognize
```

查阅资料：[解决](https://blog.csdn.net/lile777/article/details/81389098)

```shell
# 在 gmp-4.2 目录下，根据文章方法下载 config.guess 和 config.sub 文件， 然后替换掉原来的同名文件
wget -O config.guess 'http://git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.guess;hb=HEAD'


wget -O config.sub 'http://git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.sub;hb=HEAD'
```

再重新配置编译

```shell
configure --prefix=/home/share/tools/usr/gmp-4.2
```

GMP 安装成功



## 问题5：

```
找不到 jsoncpp.a 文件
```

## 解决：

```
将  /bin/aarch64-linux  目录下的jsoncpp.a 文件移动到 /bin/
```



## 问题6

```shell
Traceback (most recent call last):
  File "/var/local/mq/producer.py", line 12, in <module>
    from rocketmq.client import Producer, Message
  File "/usr/local/lib64/python3.8/site-packages/rocketmq/client.py", line 24, in <module>
    from .ffi import (
  File "/usr/local/lib64/python3.8/site-packages/rocketmq/ffi.py", line 42, in <module>
    dll = ctypes.cdll.LoadLibrary(_DYLIB_PATH)
  File "/usr/lib64/python3.8/ctypes/__init__.py", line 451, in LoadLibrary
    return self._dlltype(name)
  File "/usr/lib64/python3.8/ctypes/__init__.py", line 373, in __init__
    self._handle = _dlopen(self._name, mode)
OSError: librocketmq.so: cannot open shared object file: No such file or directory
```



## 解决

``` shell
# 将librocketmq.so 文件软连接到/usr/lib/librocketmq.so 

ln -s /usr/local/lib/librocketmq.so /usr/lib
```

```
sudo ldconfig 
```

## 



## 其他问题：

https://blog.csdn.net/qq_34174198/article/details/120663608

