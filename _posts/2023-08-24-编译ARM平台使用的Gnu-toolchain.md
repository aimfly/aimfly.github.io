---
layout: post
title: "编译arm平台Gnu toolchain"
categories: arm compile gcc
---

本文描述如何在x86平台编译arm平台使用Gnu toolchain，编译其他平台版本类似。

首先从ARM Gnu Toolchain[下载页面](https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads)下载源码并解压。

编译gcc需要gmp，mpc，mpfr模块，从[https://ftp.gnu.org/gnu/](https://ftp.gnu.org/gnu/)下载。

配置并编译gmp，mpc，mpfr：
<!--more-->

```
cd /home/testing/gcc_build/gmp-6.2.1
#prefix指定安装路径，host用来指定该模块运行平台，target用来指定模块处理的目标平台，host需要是本地交叉编译器命令的前缀，否则出现找不到编译命令
./configure --host=arm-linux-gnueabi --target=arm-linux-gnueabi --prefix=/home/testing/gcc_build/gmp-6.2.1/install
make && make install

#进入mpfr目录编译mpfr，这里--with-gmp需要指定之前编译并安装gmp模块的位置
./configure --host=arm-linux-gnueabi --target=arm-linux-gnueabi --prefix=/home/testing/gcc_build/mpfr-4.1.0/install --with-gmp=/home/testing/gcc_build/gmp-6.2.1/install
make && make install

#编译mpc
./configure --host=arm-linux-gnueabi --target=arm-linux-gnueabi --prefix=/home/testing/gcc_build/mpc-1.2.1/install --with-gmp=/home/testing/gcc_build/gmp-6.2.1/install --with-mpfr=/home/testing/gcc_build/mpfr-4.1.0/install
make && make install

```

配置并编译gcc：

```
cd /home/testing/arm-gnu-toolchain-src-snapshot-12.3.rel1/

#因为有时配置makefile时会出现参数错误需要重新配置的情况，所以不要直接在源码目录执行configure
mkdir build
cd build
#一定要用--enable-languages指定gcc处理语言，否则编译时会因为缺少处理特定语言的模块而失败
../configure --host=arm-linux-gnueabi --target=arm-linux-gnueabi --with-mpc=/home/testing/gcc_build/mpc-1.2.1/install --with-gmp=/home/testing/gcc_build/gmp-6.2.1/install --with-mpfr=/home/testing/gcc_build/mpfr-4.1.0/install --prefix=/home/testing/arm-gnu-toolchain-src-snapshot-12.3.rel1/build/install --enable-languages=c,c++
make && make install
```

编译时可能会出现有些宏未定义，这是因为gcc编译时有一些内置宏，而这些宏可能在高版本才有，所以用低版本的gcc编译高版本gcc时可能会出现宏找不到的情况

gcc内置宏可以用如下命令查看：
```
arm-linux-gnueabi-gcc -dM -E - < /dev/null
```





