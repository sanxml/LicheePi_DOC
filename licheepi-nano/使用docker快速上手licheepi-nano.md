## **使用docker快速上手licheepi-nano**

### 1. 安装docker
  docker的介绍，安装及基本指令,具体查看 [文档](../docker的介绍，安装及基本指令.md)

### 2. linux环境下快速上手

- 获取licheepi-nano镜像，并将主机驱动共享到容器
  ```shell
  docker pull sanxml/lichee-nano # 获取镜像
  # 这里使用--privileged共享主机上的驱动程序，即可以直接通过 docker 使用 sunxi-tools 烧录镜像
  docker run -t -i --privileged -v /dev/bus/usb:/dev/bus/usb sanxml/lichee-nano  # 生成一个可执行的新容器
  ```

- 获取的镜像已经帮我们搭建好了编译环境,下面直接进行编译
  ``` shell
  cd ~/buildroot-licheepi/
  export FORCE_UNSAFE_CONFIGURE=1
  # make menuconfig 配置，这里默认适配了 4.3 寸 480X272 LCD
  make -j10
  ```

- 使用 sunxi-tools 烧录镜像

  编译好镜像后，镜像文件在 ` ~/buildroot-licheepi/output/images/ ` 文件夹下，
  其中，sysimage-nand.img、sysimage-nor.img，sysimage-sdcard.img，分别对应flash和tf卡的镜像

  下面是镜像的烧录，连接licheepi-nano后，验证是否进入fel模式
  ``` shell
  sunxi-fel ver
  ```
  ![](../assets/2021-01-30_23-57.png)

  烧录镜像文件
  ``` shell
  cd ~/buildroot-licheepi
  sunxi-fel -p spiflash-write 0 ./output/images/sysimage-nor.img
  ```
  ![](../assets/2021-02-01_11-36.png)

### 3. windows环境下快速上手

- 获取licheepi-nano镜像

  ``` shell
  docker pull sanxml/licheepi-nano:v1 # 获取镜像
  docker run -t -i  sanxml/licheepi-nano:v1  # 生成一个可执行的新容器
  ```
  ![](../assets/微信截图_20210201114354.png)

- 获取的镜像已经帮我们搭建好了编译环境,下面直接进行编译
  ``` shell
  cd ~/buildroot-licheepi/
  export FORCE_UNSAFE_CONFIGURE=1
  # make menuconfig 配置，这里默认适配了 4.3 寸 480X272 LCD
  make -j10
  ```
  ![](../assets/微信截图_20210201114955.png)

- 安装sunxi-tools烧录工具并烧录镜像文件

  待补充

### 报错及解决办法

1. 运行 make menuconfig 时，报错 `Unable to find the ncurses libraries or the required header files.`

  缺少依赖，使用 apt 安装依赖
  ``` shell
  apt install libncurses5-dev
  ```

2. 编译时，如果报错 `error: you should not run configure as root (set FORCE_UNSAFE_CONFIGURE=1 in environment to bypass this check)`

  解决办法：

  ``` shell
  export FORCE_UNSAFE_CONFIGURE=1
  ```

3. 编译python时，报错：error: unknown type name ‘uuid_t’？

  解决办法，修改报错的文件 xxx/Modules/_uuidmodule.c

  ``` c
  #include "Python.h"
  /* #ifdef HAVE_UUID_UUID_H */
  #include <uuid/uuid.h>
  /* #elif defined(HAVE_UUID_H)
  #include <uuid.h>
  #endif */
  ```