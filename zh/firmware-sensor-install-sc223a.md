# OpenIPC Wiki
[目录](../README.zh.md)

安装图像传感器 
-----------------------

如果固件镜像中不包含图像传感器驱动程序，您可以手动安装它。

例如，图像传感器 "sc223a" 将安装到 gk7205v210 板上（并刷入 openipc-gk7205v210-fpv-8mb.bin）。

您需要一个 [传感器库](https://github.com/OpenIPC/firmware/raw/master/general/package/goke-osdrv-gk7205v200/files/sensor/libsns_sc223a.so) 和一个 [传感器配置文件](https://github.com/OpenIPC/firmware/raw/master/general/package/goke-osdrv-gk7205v200/files/sensor/config/sc223a_i2c_1080p.ini)。

其他传感器所需的文件可以在[此列表](firmware-sensors.md) 之后找到。

通过直接从 github 下载文件到您的设备来安装它们：
```sh
curl -s -L -o /usr/lib/sensors/libsns_sc223a.so https://github.com/OpenIPC/firmware/raw/master/general/package/goke-osdrv-gk7205v200/files/sensor/libsns_sc223a.so
curl -s -L -o /etc/sensors/sc223a_i2c_1080p.ini https://github.com/OpenIPC/firmware/raw/master/general/package/goke-osdrv-gk7205v200/files/sensor/config/sc223a_i2c_1080p.ini
```

根据您的传感器规格调整 /etc/majestic.yaml 中的 fps 值。在您的传感器配置文件 [/etc/sensors/sc223a_i2c_1080p.ini](https://github.com/OpenIPC/firmware/raw/master/general/package/goke-osdrv-gk7205v200/files/sensor/config/sc223a_i2c_1080p.ini) 中搜索"Isp_FrameRate"。

```sh
cli -s .video0.fps 30
```

（重新）启动流媒体：

```sh
killall majestic
majestic
```

[有关传感器"sc223a"的更多信息]（https://github.com/RoboSchmied/Documentation/blob/main/sc223a.md）

