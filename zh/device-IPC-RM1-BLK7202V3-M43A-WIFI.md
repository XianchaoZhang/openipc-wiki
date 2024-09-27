# IPC-RM1-BLK7202V3-M43A-WIFI
- [Overview](#overview)
  - [Device info](#Device-info)
- [Connectors](#Connectors)
  - [Front side](#Front-side)
  - [Back side](#Back-side)
- [GPIOs](#GPIOs)
  - [Muxing](#Muxing)
  - [SD Card](#SD-Card)
  - [Speaker](#Speaker)
- [Flashing](#Flashing)
  - [Flash memory layout](#Flash-memory-layout)
- [Summary](#Summary)
- [TODO](#TODO)


# 概览 
在廉价的室内 Cootli WiFi PTZ 摄像头中发现的电路板。电路板看起来与 [XM IPG-G4-WR-BL](http://baike.xm030.cn:81/%E4%BA%A7%E5%93%81%E5%8F%82%E6%95%B0/English/IPG%E6%A8%A1%E7%BB%84/Parameters%20for%20IPG-G4-WR.pdf) 非常相似，但 PCB 布局略有不同。

所有测试均在 [gk7202v300_lite_cootli_camv0103-nor](https://github.com/OpenIPC/builder/releases/download/latest/gk7202v300_lite_cootli_camv0103-nor.tgz) 固件 (2024 年 2 月 8 日构建) 上完成。

## 设备信息 
| System | Description | Comments | 
|-|-|-|
| SoC | GK7202V300 | |
| Flash | XMC XM25QH64CHIQ | 8MB |
| Sensor | SmartSens SC223A* | 1920x1080 |
| Audio | MIC + SPK | |
| Storage | Micro SD | |
| LAN | - | - |
| WiFi | iComm SV6355 | UF.L (IPX) |
| BT | +? | +? |
| Motors | 2x Stepper | GPIO + ULN2803A |
| Dimensions | 38 x 54 mm | |

\* - reported by ipctool

正面 
![正面](../images/device-IPC-RM1-BLK7202V3-M43A-WIFI_front.jpg)

背面 
![背面](../images/device-IPC-RM1-BLK7202V3-M43A-WIFI_back.jpg)

PCB 标记 
![PCB 标记](../images/device-IPC-RM1-BLK7202V3-M43A-WIFI_markings.jpg)

# 连接器 
连接器类型 JST 1.25mm 
![JST 连接器](../images/device-IPC-RM1-BLK7202V3-M43A-WIFI_connectors.jpg)

## 正面 
| Connector | Type |
|:-:|:-|
| IRCUT | 2pin JST |
| LED | 5pin JST |
| MIC | 2pin JST |

## 背面
- Micro SD 卡插槽
- UART（未焊接，位于 SPK 左侧，引脚 1 RX，引脚 2 TX）

| Connector | Type |
|:-:|:-|
| SPK | 2pin JST |
| H | 5pin JST |
| V | 5pin JST |
| +5V | 2pin JST |
| RF | UF.L (IPX) |

# GPIO 
| GPIO | Connector | Description |
|:-:|:-:|:-:|
| 0* | - | Reset button |
| 4 | LED pin 5 | WLED |
| 8 | WiFi module pin 3 | LO - Power ON |
| 12 | H pin 5 | Mot H |
| 13 | H pin 2 | Mot H |
| 14 | H pin 4 | Mot H |
| 15 | H pin 3 | Mot H |
| 16 | LED pin 4 | IRLED |
| 52 | V pin 2 | Mot V |
| 53 | V pin 3 | Mot V |
| 54 | V pin 4 | Mot V |
| 55 | V pin 5 | Mot V |
| 56 | IRCUT pin 1 | LO - IRCUT ON |
| 57* | LED pin 3 | IRSens |
| 58 | IRCUT pin 2 | LO - IRCUT OFF |
| 70 | - | SD PWR (LO - Power ON) |
| 51 | - | AUDIO AMP |

\* - unconfirmed.


## 多路复用 
如果 Majestic 控制了引脚，则无需多路复用。否则，可以使用以下命令进行多路复用。

多路复用 GPIO16 以控制 IRLED 引脚：
```sh
devmem 0x120c0020 32 0x432      # GPIO2_0 (GPIO16)
```

也适用于电机。 
多路复用 GPIO12、GPIO14、GPIO15（电机 H 连接器）：
```sh
devmem 0x120c0010 32 0x1e02     # GPIO1_4 (GPIO12)
devmem 0x120c0018 32 0x1d02     # GPIO1_6 (GPIO14)
devmem 0x120c001c 32 0x1402     # GPIO1_7 (GPIO15)
```

Shortly after **Loading of kernel modules...** GPIO13 变为 HI（电机绕组之一持续通电），因此可能需要将其变为 LO：
```sh
gpio clear 13
gpio unexport 13
```

## SD 卡
默认情况下 SD 卡未通电，因此我们需要以某种方式将 GPIO70 变为 LO。

从内核启动 SD 卡：
```sh
gpio clear 70
```
or
```sh
devmem 0x120B8400 32 0x40       # turn GPIO8_6 to output mode
devmem 0x120B8100 32 0x00       # set GPIO8_6 to LO
```
或者重新连接 SD 卡。

从 U-Boot 启动 SD 卡：
```sh
mw 0x120B8400 0x40      # turn GPIO8_6 to output mode
mw 0x120B8100 0x00      # set GPIO8_6 to LO
mmc rescan
```

## 扬声器
设备支持通过向 http://192.168.0.10/play_audio 端点发送数据来播放 PCM 签名的 16 位小端、8000 Hz、1CH。

音频文件可以像这样编码：
```sh
ffmpeg -i input.wav -f s16le -ar 8000 -ac 1 output.pcm
```

并发送到相机的扬声器：
```sh
curl -v -u user:pass -H "Content-Type: application/json" -X POST --data-binary @audio.pcm http://192.168.0.10/play_audio
```


# 烧录 
库存固件被密码锁定，并且 LAN 接口不存在，所以我猜可以使用以下方法来烧录此板：
- [烧录](https://github.com/OpenIPC/burn) + [u-boot-gk7202v300-universal.bin](https://github.com/OpenIPC/firmware/releases/download/latest/u-boot-gk7202v300-universal.bin) 然后通过 X/Y/ZMODEM 上传固件（例如 **loady**。提示：使用 **baud** 选项加速）或从 SD 卡上传固件（需要电源，[见上文](#SD-Card))
- 通过库存 Web 界面加载完整图像（未经测试）
- 闪存编程器
- 以某种方式进入库存引导加载程序

## 闪存布局 
| Offset | Size | Description | 
|:-|:-|:-|
| 0x00000000 | 0x00040000 (262144 bytes) | bootloader |
| 0x00040000 | 0x00010000 (65536 bytes) | env |
| 0x00050000 | 0x00200000 (2097152 bytes) | kernel |
| 0x00250000 | 0x00500000 (5242880 bytes) | rootfs |
| 0x00750000 | 0x000B0000 (720896 bytes) | rootfs_data |

# 摘要
- [X] WiFi 工作正常
- [X] 视频测试/流式传输
- [X] 白天/夜晚工作正常（IRCUT 和 IRLED）
- [X] MIC 工作正常
- [X] 扬声器工作正常
- [ ] PTZ/电机（GPIO 引脚已找到/可访问，驱动程序未经测试）

# TODO
- 以某种方式修补/调整 camhi-motor.ko，使其在此板上工作。

