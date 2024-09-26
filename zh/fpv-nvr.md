# OpenIPC Wiki
[Table of Content](../README.md)

OpenIPC FPV 地面站 
--------------------------


#### News roundup

- 现在可以将视频录制到连接的 SSD 或 USB 记忆棒上
- HDMI 和 VGA 输出现在均可使用，连接的显示器不受限制
- 很多不同的东西 ;)


#### 从原始 HI3536DV100 NVR 板固件升级到 OpenIPC FPV 固件

- 安装 [PUTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) 和 [TFTP](https://pjo2.github.io/tftpd64/) 服务器
- 从 OpenIPC 站点下载 NVR 的实际 [图像](https://openipc.org/cameras/vendors/hisilicon/socs/hi3536dv100/download_full_image?flash_size=16&flash_type=nor&fw_release=fpv)
- 将此图像上传到您的 TFTP 服务器
- 关闭 NVR 电源，将 USB 适配器连接到您的 NVR UART，指定您电脑上的 COM 端口
- 开机时快速按 Ctrl+C 进入 U-Boot
- 每行单独执行一组命令

```
# Сhanging the ip address of the NVR board and the ip address of your TFTP server
setenv ipaddr 192.168.1.10; setenv serverip 192.168.1.254
mw.b 0x82000000 0xff 0x1000000
tftp 0x82000000 openipc-hi3536dv100-fpv-16mb.bin
sf probe 0; sf lock 0;
sf erase 0x0 0x1000000; sf write 0x82000000 0x0 0x1000000
reset
```

#### The result

![](../images/fpv-nvr-hi3536dv100-openipc-ready.webp)

#### Buy a device

- [https://www.aliexpress.com/item/1005004023376532.html](https://www.aliexpress.com/item/1005004023376532.html)
- [https://www.aliexpress.com/item/1005002358182146.html](https://www.aliexpress.com/item/1005002358182146.html)
