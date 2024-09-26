# OpenIPC Wiki 
[目录](../README.zh.md)

请务必同时使用[常见问题解答](../zh/fpv-faq.md)。

# 缩略语和定义

* BEC - BEC 代表电池消除器电路。它用于为设备中的组件提供稳定的电压供应，通常代替电池。

* 信道 - Wi-Fi 信道是用于无线设备之间通信的特定频率范围。 Wi-Fi 网络通常将这些频率划分为 2.4 GHz 频段中的 14 个通道、5 GHz 频段中的 34 个通道以及 6 GHz 频段中最多 59 个通道。

* [配置器](https://github.com/OpenIPC/configurator) 用于设置 FPV 和 URLLC 设备的 OpenIPC 控制器

* Datalink - 数据链路关注管理数据通信链路并确保设备和外部系统之间的可靠数据传输。

* FEC_K 和 FEC_N

FEC_K和FEC_N是前向纠错(FEC)中使用的参数，用于定义纠错码的结构和效率。

#### FEC_K

    K 是指在应用纠错编码之前块中信息（或数据）比特的数量。
    这是需要传输的实际数据，没有添加任何冗余来进行纠错。

    #### FEC_N

    N 是指应用纠错编码后的总比特数。

    这包括原始数据位 (K) 和为错误检测和纠正而添加的冗余位。
    FEC_K与FEC_N的关系

    N 和 K 之差代表纠错码添加的冗余度。换句话说，冗余位用于检测和纠正传输数据中的错误。

    ```mathematica
    N = K + Number of Redundant Bits
    ```

    例子

    如果 FEC_K = 1000 且 FEC_N = 1200，这意味着在原始 1000 个数据位中添加了 200 个冗余位，总共创建了 1200 个传输位。

    码率

    信息比特（K）与总比特（N）的比值称为码率：

    ```mathematica
    Code Rate = K / N
    ```

    例如，如果 K = 1000 且 N = 1200，则码率将为：

    ```yaml

    Code Rate = 1000 / 1200 = 0.833
    ```
    这意味着83.3%的传输位是实际数据，16.7%用于纠错。


    #### 在通信系统中的使用

    FEC_K 和 FEC_N 通常用于描述无线通信标准（例如Wi-Fi、5G、卫星通信和广播系统）中如何处理数据。

    K 和 N 的值决定了对数据添加多少纠错，影响传输可靠性和带宽效率。

* FPV - FPV 代表第一人称视角，这项技术允许用户体验来自安装在无人机或其他遥控设备上的摄像头的实时视频输入，就好像他们坐在飞行员的座位上一样。 FPV 提供实时、身临其境的视角，增强飞行或远程操作过程中的控制和态势感知。

* 频率 - Wi-Fi 使用特定频率的无线电波在您的设备和路由器之间传输数据。根据传输的数据量，它可以使用两种频率之一：2.4 GHz 或 5 GHz。

* H265/H264 - 用于编码和解码视频流的视频压缩标准。

    * H.265 (HEVC)：与 H.264 相比，压缩效率更高，可以以更低的比特率获得更高的视频质量，非常适合 4K 和高清视频流。
    * H.264 (AVC)：一种广泛使用的视频压缩标准，可提供良好的压缩和视频质量，通常用于流媒体、录制和视频会议。

* LDPC - LDPC 代表低密度奇偶校验码，是数字通信系统中使用的一种高级纠错码，可提高噪声信道上数据传输的可靠性。 LDPC 码旨在检测和纠正数据传输过程中发生的错误，从而实现更高效、更稳健的通信。

* [Majestic](https://github.com/OpenIPC/majestic-webui) - 为 OpenIPC 固件提供 Web 界面，可在相机的端口 80 上使用。它用于管理和配置系统的各个方面。它也是一个命令行工具。

* MCS 指数 - 调制编码方案 (MCS) 指数是一个标准行业指标，反映客户端设备和无线接入点之间 Wi-Fi 连接的各种因素，例如数据速率、信道宽度和天线数量或设备中的空间流。

* [MSPOSD](https://github.com/OpenIPC/msposd) - 适用于 INAV/Betaflight/ArduPilot 的 MSP Displayport OSD 的 OpenIPC 实现。

* OpenIPC - 是一个固件项目，专注于增强和定制 IP 摄像机的功能。 IPC 是 IP 摄像机的缩写。

* [PixelPilot](https://github.com/OpenIPC/PixelPilot) - PixelPilot 是一款 Android 应用程序，将多个部分打包在一起，用于解码 wfb-ng 无线广播的 H264/H265 视频源。

* [PixelPilot_rk](https://github.com/OpenIPC/PixelPilot_rk) - 用于解码 RTP 视频流并将其显示在屏幕上的应用程序，适用于 Rockchip 设备（又名 Radxa）。

* STBC - STBC 代表空时分组编码，这是无线通信系统中使用的一种技术，用于提高信号可靠性和数据传输性能，特别是在有干扰或信号衰落的环境中。

* WFB - 无线帧缓冲区 (WFB) 是一个能够通过无线网络远程显示和控制设备图形界面的组件。

## 文件系统

### 无人机


|文件路径 |描述 | 
|--------------------------------|------------------------- -- --------------------------| 
|配置 | 
| `/etc/wfb.conf` |配置无线帧缓冲区 (WFB) 的设置。 WFB 是一个允许通过无线网络远程显示和控制设备图形界面的组件。                                    | 
| `/etc/drone.key` |用于存储与地面站交换的密钥。该密钥在确保无人机与地面站之间的通信安全方面发挥着至关重要的作用。     | 
| `/etc/datalink.conf` |用于配置与数据链接通信相关的设置。此文件在定义如何管理数据以及如何在系统内的各个组件之间传输数据方面发挥作用。 
| `/etc/majestic.yaml` |雄伟的设置 | 
| `/etc/mavlink` | Mavlink 设置 | 
| `/etc/openipc_banner`| | 
| `/etc/openipc_donors`| | 
| `/etc/telemetry.conf`|用于配置与遥测相关的设置。遥测涉及从设备收集数据并将其传输到外部系统以进行监控和分析。 
|启动文件| 
| `/etc/init.d/S95majestic` |启动脚本用于在系统启动期间管理 Majestic 服务的初始化和执行。      | 
| `/etc/init.d/S98datalink` |  启动脚本用于管理系统启动期间 Datalink 服务的初始化。 |
|应用程序 | 
| `/usr/bin/wfb-cli` |用于与地面站环境中的无线帧缓冲 (WFB) 服务进行交互或配置。即`wfb-cli gs` | 
| `/usr/bin/msposd` | MSPOSD 二进制| 
| `/usr/bin/font_hd.png` | msposd 的字体文件 | 
| `/usr/bin/font.png` | msposd 的字体文件 | 
| `/usr/bin/telemetry` |遥测脚本 | 
| `/usr/bin/majestic` |majestic 二进制|


### Ground Station

### Radxa
| File Path            | Description                                          |
|----------------------|------------------------------------------------------|
| Config    |
| `/etc/gs.key` |  Shared key|
| `/etc/wifibroadcast.cfg` |  定义连接参数|
| Startup |
| `/etc/systemd/system/openipc.service` | 主进程 开机启动
| Apps |
| `/home/radxa/wfb_keygen` |  Generates shared key|
| `/home/radxa/resizefs.sh` | 调整根文件系统的大小 |
| `/home/radxa/scripts/screen-mode` | Setup resolution |
| `/home/radxa/scripts/stream.sh` | Starts main process |
| `/home/radxa/scripts/wifi-connect.sh` | 设置本地 wifi 连接到家庭路由器的脚本|
| Media |
| `/home/radxa/Videos/` | 飞行视频的存储位置|