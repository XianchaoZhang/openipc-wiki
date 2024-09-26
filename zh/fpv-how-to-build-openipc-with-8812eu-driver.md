# OpenIPC Wiki
[Table of contents](../README.zh.md)


如何使用 RTL8812EU 驱动程序构建 OpenIPC 
--------------------------------

有关 rtl8812eu 的更多详细信息，请参见此处 https://github.com/OpenIPC/wiki/blob/master/en/fpv-bl-m8812eu2-wifi-adaptors.md

- 启动 Ubuntu
- 打开终端

```
sudo apt-get install -y automake autotools-dev bc build-essential curl fzf git libtool rsync unzip
```

```
rm -r -f yourOpenipc #remove any old firmware build directory (optional)
git clone --depth=1 https://github.com/OpenIPC/firmware.git yourOpenipc
cd yourOpenipc
make clean
cd br-ext-chip-sigmastar
cd configs
ls
sudo nano yourSelectconfig
```

```
	Then under Wireless add the line 
BR2_PACKAGE_RTL88X2EU_OPENIPC=y
	Save the file

cd ..
cd ..
make
```

- 选择您的主板并输入 ssc338q fpv 等并构建固件
- 导航至 /home/YourUser/yourOpenipc/output/images
- 找到合适的输出 .tgz 存档，即 openipc.ssc338q-nor-fpv.tgz，并提取 rootfs 和 uboot 文件
- 将这 2 个文件复制到 OpenIPC 摄像头 /tmp
  - 通过 ssh 连接到摄像头
`cd /tmp`

`sysupgrade --kernel=uImage.ssc338q --rootfs=rootfs.squashfs.ssc338q` 
或 
`sysupgrade --kernel=uImage.ssc30kq --rootfs=rootfs.squashfs.ssc30kq`

