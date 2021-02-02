### sunxi-tools

apt install git build-essential wget make vim libusb-1.0-0-dev zlib1g-dev pkg-config cpio -y

``` shell
git clone --recursive https://github.com/sanxml/sunxi-tools.git
cd sunxi-tools/
git checkout f1c100s-spiflash
make
make install
```

### arm-eabi
``` shell
wget https://releases.linaro.org/components/toolchain/binaries/5.3-2016.05/arm-eabi/gcc-linaro-5.3.1-2016.05-x86_64_arm-eabi.tar.xz
tar -xvf gcc-linaro-5.3.1-2016.05-x86_64_arm-eabi.tar.xz -C /opt/
```

``` shell
vim ~/.bashrc
```
在.bashrc文件中行末添加：
```
export PATH="$PATH:/opt/gcc-linaro-5.3.1-2016.05-x86_64_arm-eabi/bin/"
```

```shell
source ~/.bashrc
```

### xboot

git clone https://github.com/xboot/xboot.git
cd ~/xboot
make CROSS_COMPILE=arm-eabi- PLATFORM=arm32-f1c100s -j10


### 获取docker及烧录

docker pull sanxml/lichee-nano:xboot # 获取镜像
docker run -t -i --privileged -v /dev/bus/usb:/dev/bus/usb sanxml/lichee-nano:xboot  # 生成一个可执行的新容器

sunxi-fel ver
sunxi-fel -p spiflash-write 0 ~/xboot/output/xboot.bin

