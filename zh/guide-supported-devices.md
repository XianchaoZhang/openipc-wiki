# OpenIPC Wiki
[目录](../README.zh.md)

支持的设备
-----------------

遗憾的是，我们无法为您提供与我们的固件完全兼容的设备列表。相机制造商往往会更改硬件设计并更换组件，即使在同一型号产品线中也是如此，且不会提前通知。

下面，我们列出了我们拥有的一些相机，但同样，不能保证如果您今天购买其中一台相机，您最终不会得到不受支持的硬件。

确定您的相机是否受我们最新版本固件支持的某种有效方法是 open[^1] 相机外壳并查看芯片标记。然后在[支持的硬件列表][1]中查找您的芯片并检查其开发状态。

如果您仍有疑问，请拍摄硬件特写的高分辨率照片，然后在我们的一个 Telegram 群组中寻求帮助。

[^1]: 注意！这样做，您将对自己的行为负全部责任。打开外壳很可能会使设备保修失效。我们不对相机、您自己、您的房子、您的宠物或其他任何东西造成的任何损坏负责。如果您对硬件不熟悉，这个项目可能不适合您。

您还可以在特殊的 Builder 项目中查看适配设备的列表 - https://github.com/OpenIPC/builder

_如果您要添加其他受支持的设备，请在[此处][2]进行添加。_

| Brand      | Model              | Processor   | Sensor | Flash Memory | LAN | WLAN           | USB  | Card |
|------------|--------------------|-------------|--------|--------------|-----|----------------|------|------|
|            |                    |             |        |              |     |                |      |      |
| Anjoy      | MS-J10             | SSC335      | IMX307 |              | Yes | No             | Yes  | No   |
| Anjoy      | YM-J10D            | SSC337      | IMX307 |              | Yes | No             | Yes  | No   |
| Amorvue    | NC1080AW           | HI3518EV200 | SC2135 | MX25L6405D   | Yes | RTL8188EUS[^2] | No   | No   |
| Chacon     | [IPCAM-RI01][3]    | HI3518EV300 | JXF23  | XM25QH128A   | No  | RTL8188FTV     | WiFi | Yes  |
|            |                    |             |        |              |     |                |      |      |
| LTV        | CNE-724 48         | HI3516EV200 |        |              | Yes | No             | No   | Yes  |
|            |                    |             |        |              |     |                |      |      |
| Rotek      | Switcam HS303 (v1) | HI3518EV200 |        |              | No  | RTL8188FU      | WiFi | Yes  |
| Rotek      | Switcam HS303 (v2) | HI3518EV200 | OV9732 | GD25Q128CSIG | No  | RTL8188EU      | WiFi | Yes  |
| Rotek      | Switcam HS303 (v3) | HI3518EV200 |        |              | No  | RTL8188EU      | WiFi | Yes  |
|            |                    |             |        |              |     |                |      |      |
| Smartwares | [CIP-37210][4]     | HI3518EV200 |        |              | No  | RTL8188FU      | WiFi | Yes  |
|            |                    |             |        |              |     |                |      |      |
| TOP        | TOP-201/MCO-720P Mini Camera  | HI3518EV100 | OV9712 |   | Yes | No             | No   | No   |
|            |                    |             |        |              |     |                |      |      |
| Xiaomi     | [MJSXJ02HL][7]     | HI3518EV300 |        |              | No  |                |      | Yes  |
| Xiaomi     | [MJSXJ03HL][6]     | T31N        | JXQ03 | QH128A-104HIP | No  | RTL8189FTV     | No   | Yes  |
| Xiaomi     | [CMSXJ25A]         | SSC325      | GC2053 | QH128A-104HIP| Yes | MT7603UN      | No   | Yes  |
|            |                    |             |        |              |     |                |      |      |
| XM         | BLK18EV-0062-0035  | HI3518EV200 | JXH62  |              | Yes |                |      | Yes  |
|            |                    |             |        |              |     |                |      |      |
| Zenotech   | HI3516D_MB_V13_RA  | HI3516DV100 | OV9689 | GD25Q128CSIG | Yes | No             |      | No   |
| Tungson    | DS-YTJ5301         | SSC30KD     | GC2053 |              | Yes | RTL8188FTV     | No   | No   |
| Haier      | HCC-18B30-U1       | T31ZX       | SC3335 | NM25Q128EVB  | No  | SSV6X5X        | No   | Yes  |
| iFlytek    | XFP301-M           | T31ZX       | JXQ03  | XM25QH128C   | No  | RTL8188FU      | No   | Yes  |
| Qihoo 360  | AP6PCM03           | T31ZX       | GC4653 | EN25QH256A   | Yes | ATBM6031       | No   | Yes  |
| Goke       | [MJ-C232V5-0SW1][5]| GK7205V200  | GC2305 | XM25QH128CHIQ| Yes | RTL8188FU      | No   | No   |
| Uniview    | CMCC-IPC-A35       | Hi3516EV300 | SP2305 | W25N01GV     | Yes |                | No   | No   |
| E-Life     | EF3113             | T31L        | GC2053 | ZB25VQ128    | Yes | SV6155P        | No   | Yes  |
| E-Life     | ET-N3431H-DW       | T31X        | OS03B10| ZB25VQ128    | Yes | SV6155P        | No   | Yes  |
| H3C        | C2041              | T31X        | JXK04  | XM25QH128C   | Yes | RTL8188FTV     | No   | Yes  |
| MEGVII     | SmartEye-D1A-24A-P | T31X        | JXK04  | FM25Q128A    | Yes | No             | No   | Yes  |
| Uniview    | IPC-D122-PF28      | SSC335      | SC2335 | XM25QH64C    | Yes | No             | No   | No   |


| Processor   | Sensor           | Vendor     | SKU          | Board identification            |
|-------------|------------------|------------|--------------|---------------------------------|
| Hi3516Cv100 | IMX222_spi_dc    | XM         |              | [BLK18C-0222-38X38_S-V1.03][1]  |
| Hi3516Cv100 | OV2710_i2c_dc    | Jovision   |              | IPG5020A-H-V1.0                 |
|             |                  |            |              |                                 |
| Hi3516Cv200 | IMX323_i2c_dc    | XM         |              | BLK16CV-0323-38X38-V1.01        |
| Hi3516Cv200 | IMX323_i2c_dc    | JVT        | S323H16VF    | IPS323-H16V-38X38-V2            |
| Hi3516Cv200 | IMX323_i2c_mipi  | XM         |              |                                 |
|             |                  |            |              |                                 |
| Hi3516Cv300 | AR0237_i2c_dc    | XM         |              | BLK16CV3-0237P-38X38-S-V1.01    |
| Hi3516Cv300 | IMX307_i2c_lvds  | Raysharp   |              | RS-CM-188D 2018-03-16 E150111   |
| Hi3516Cv300 | IMX323_i2c_dc    | Longse/HS  |              | HI3516CV300-IMX323-POE-TF V1.1  |
| Hi3516Cv300 | IMX323_i2c_dc    | Sunywo     | ZB6323       | IPG5020A-T-N6-V0.1              |
| Hi3516Cv300 | IMX323_i2c_dc    | XM         |              | IVG-HP201Y-AE                   |
| Hi3516Cv300 | IMX291_i2c_lvds  | XM         |              | IVG-HP203Y-AE                   |
| Hi3516Cv300 | IMX323_spi_dc    | JVT        | S323H16XF    | IPS323-H16X-38X38-V2/V3         |
| Hi3516Cv300 | JXF22_i2c_dc     | XM         |              | BLK16CV3-0022-38X38-S-V1.01     |
|             |                  |            |              |                                 |
| Hi3516Ev100 | IMX323_i2c_dc    | XM         |              | BLK16E-0323-38X38-B-V1.01       |
| Hi3516Ev100 | SC2235P_i2c_dc   | XM         | 80HE20PS-S   | BLK16E-0235-38X38-S-V2.03       |
|             |                  |            |              |                                 |
| Hi3516Ev200 | IMX307_i2c_mipi  | XM         |              | IVG-85HF20PY-S                  |
| Hi3516Ev200 | SC4239P_i2c_mipi | XM         |              | IVG-85HF30PS-S                  |
|             |                  |            |              |                                 |
| Hi3516Ev300 | IMX335_i2c_mipi  | XM         |              | IVG-85HG50PYA-S                 |
|             |                  |            |              |                                 |
| Hi3518Ev100 | OV9712_i2c_dc    | CamHi/Xin  |              | IPC18E_9712_V2.0/V3.1           |
|             |                  |            |              |                                 |
| Hi3518Ev200 | AR0130_i2c_dc    | XM         |              | BLK18EV-0732-0035-38X38-V1.01   |
| Hi3518Ev200 | JXF22_i2c_dc     | XM         |              | BLK18EV-0002-2035-38X38-V1.01   |
| Hi3518Ev200 | JXF22_i2c_dc     | XM         |              | BLK18EV-0022-0130-38X38-V1.01   |
| Hi3518Ev200 | OV2735_i2c_dc    | Dahua      | DH-IPC-C22P  | E305654 JX02 94V-0              |
| Hi3518Ev200 | OV9732_          | XM         |              | BLK18EV-0732-0035-38X38-V1.01   |
| Hi3518Ev200 | OV9732_i2c_dc    | Longse/HS  | LS-IP100/40  | 3518EV200-OV9732-V1.0           |
| Hi3518Ev200 | OV9732_i2c_dc    | Rostelecom | QVC-IPC-136W | E305654 JX02 94V-0              |
| Hi3518Ev200 | SC2135_i2c_dc    | Amorvue    |              | 18EV200_V202P                   |
| Hi3518Ev200 | SC2135_i2c_dc    | XM         |              | BLK18EV-0035-0042-38X38_S-V1.01 |
| Hi3518Ev200 | SC2235_i2c_dc    | XM         |              | BLK18EV-0235-38X38-B-V1.01      |
|             |                  |            |              |                                 |
|             |                  |            |              |                                 |
| SSC335      | SC3335           | Uniview    |              | Uniview                         |
| SSC338Q     | IMX415           | CamHi/Xin  |              | SSC338Q_38M_1.1                 |


[1]: https://openipc.org/supported-hardware
[2]: https://github.com/OpenIPC/wiki/blob/master/zh/guide-supported-devices.md
[3]: https://github.com/OpenIPC/wiki/blob/master/zh/device-chacon-ipcam-ri01.md
[4]: https://ipcamtalk.com/threads/smartwares-cip-37210-wifi.64605/
[5]: http://www.hnamg.cn/h-col-139.html
[6]: https://github.com/OpenIPC/device-mjsxj03hl
[7]: https://github.com/OpenIPC/device-mjsxj02hl


[^2]: 可以使用自定义版本通过 8MB 闪存启用 WiFi，或者升级到 16MB 闪存并使用 Ultimate 固件。

