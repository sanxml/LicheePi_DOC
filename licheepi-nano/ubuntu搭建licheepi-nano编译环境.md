## 使用 ubuntu 搭建自己的编译环境

### 安装 ubuntu 和相关依赖

- windows用户需要安装 ubuntu ,可以查看 [简要使用 docker 安装 ubuntu 及换源](../ubuntu安装与换源.md)

<!-- ```shell
docker cp ./tools/licheepi-nano.tar.gz xxx:/root/
``` -->

- 安装编译需要的依赖

```shell
  apt install build-essential libncurses5-dev git vim wget pkg-config libusb-1.0-0-dev bc cpio rsync zlib1g-dev unzip python3 swig cmake -y
```

<!--
安装交叉编译环境
``` shell
wget https://releases.linaro.org/components/toolchain/binaries/latest-7/arm-linux-gnueabihf/gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf.tar.xz
sudo tar -xvf gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf.tar.xz -C /opt
```


把工具链的路径添加到环境变量
``` shell
vim ~/.bashrc
```
在文件末尾添加以下内容
``` shell
alias LICHEEPI_NANO='export ARCH=arm export CROSS_COMPILE=arm-linux-gnueabihf- export PATH="$PATH:/opt/gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf/bin" export FORCE_UNSAFE_CONFIGURE=1'
```
使能环境变量
``` shell
source ~/.bashrc
``` -->


### buildroot

编译过程中 buildroot 会自动下载依赖的软件包到 dl 目录下，而下载的速度可能很慢，
这里将文件打包，按照下面命令解压到 buildroot/dl 目录下面即可

``` shell
tar -xvf licheepi_nano_dl.tar.gz -C ./buildroot-licheepi/
```

``` shell
git clone --recursive https://github.com/sanxml/buildroot-licheepi
cd buildroot-licheepi
make sipeed_lichee_nano_defconfig
#make menuconfig
make -j10
```

### sunxi-tools

``` shell
git clone --recursive https://github.com/sanxml/sunxi-tools.git
cd sunxi-tools/
git checkout f1c100s-spiflash
make
make install
```


### 编译可能会遇到的问题
1. 编译时

  解决办法：

  ``` shell
  export FORCE_UNSAFE_CONFIGURE=1
  ```

2. 编译python时，报错：error: unknown type name ‘uuid_t’？

  解决办法，修改报错的文件 xxx/Modules/_uuidmodule.c

  ``` c
  #include "Python.h"
  /* #ifdef HAVE_UUID_UUID_H */
  #include <uuid/uuid.h>
  /* #elif defined(HAVE_UUID_H)
  #include <uuid.h>
  #endif */
  ```