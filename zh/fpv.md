# OpenIPC Wiki
[Table of Content](../README.md)

使用 OpenIPC 作为 FPV 系统的预算视频链接 
---------------------------------------------------

<p align="center">
  <img src="https://github.com/OpenIPC/wiki/blob/master/images/fpv-logo.jpg?raw=true" alt="Logo"/>
</p>

2015 年，德国爱好者 Befi [提出了开源无人机的想法](https://befinitiv.wordpress.com/wifibroadcast-analog-like-transmission-of-live-video-data/)，他建议使用普通的 WiFi 适配器，这种适配器在全球范围内的数量已经达到数十亿个，而且价格大幅下降，已成为最实惠的数字收发器类型。

关键思想不是 WiFi 适配器本身，而是使用它们的非常规方法：拒绝建立网络连接并切换到广播模式，此时一个适配器充当数字信号的发射器，而第二个适配器充当接收器。

建议使用经济型 SBC Raspberry Pi，通过 MIPI 接口和 USB WiFi 适配器（推荐的适配器之一）将摄像头连接到它，接收器的另一侧则使用类似的 WiFi 模块和 HDMI 显示器。在这种情况下，不是使用传统的 UDP 协议进行低延迟视频传输，而是使用较低级别的协议进行灵活的数据管理：带宽控制、发射器功率以及在传输过程中数据包 [部分损坏](https://en.wikipedia.org/wiki/Error_detection_and_correction) 时恢复数据的能力。其中一个技术问题是，并非每个 WiFi 适配器都能在这种低级模式下工作，此外：您几乎总是必须安装仅适用于 Linux 的特殊驱动程序。

在此模式下，接收器以所谓的“监控模式”运行，接收并向操作系统传输给定 WiFi 信道的每个数据包，然后决定是否对其进行解码或丢弃。WiFi 发射器适配器必须以“注入模式”运行，此时操作系统内核实际上并不参与 ISO 网络模型规定的网络数据包生成。有些适配器支持两种模式，有些仅支持其中一种，因此可能只位于发射器或接收器端。

<p align="center">
  <img src="https://befinitiv.files.wordpress.com/2015/04/dscf11161.jpg" alt="The first ever drone controlled by Wifibroadcast" />
</p>


## 给新手的建议 
<span style="color:red;"> 
OpenIPC FPV 仍在开发中。安装和使用/测试它需要 Linux 技能。如果没有这样的技能或不想学习，那么最好购买和使用开箱即用的专业 FPV 设备。 
</span>

您应该具备以下技能：
* 使用 SSH（安全 Shell）连接到 IPcamera/NVR
* 通过 SCP（安全复制）交换文件
* 使用 [VIM 编辑器](https://github.com/vim/vim)
* 使用 CAT 命令查看文件内容
* 改编或创建 bash 脚本的技能


有很多优秀的书籍、网站和视频可以用来学习。

* [Linux 基础课程](https://github.com/kodekloudhub/linux-basics-course)
* [视频：Linux 简介 - 初学者完整课程](https://www.youtube.com/watch?v=sWbUDq4S6Y8)



## OpenIPC 的革命

[OpenHD](https://openhdfpv.org/) 项目（以及其他类似项目）中的经典设置包括连接到 Raspberry Pi 的 MIPI 或 USB 摄像头，它充当机载系统的视频编码器和路由器，然后通过 USB 连接到 WiFi 适配器，通过 UART 连接到飞行控制器。地面站通常由相同的 WiFi 适配器、第二个 Raspberry Pi 或 x86 Linux 笔记本电脑以及高对比度显示器或护目镜组成。

有时，MIPI 或 USB 摄像头会换成 IP 摄像头，IP 摄像头功能更强大（有自己的硬件编码器），价格更便宜，但特性相似。大多数现代摄像机都是典型的搭载 Linux 的设备（但 RAM 和闪存大小与 Raspberry Pi 相比要小得多），这允许您在其上重新编译和运行几乎任何便携式软件。

在使用这项技术时，我萌生了简化飞行系统并将所有必要软件直接移植到 IP 摄像机的想法。从技术上讲，OpenIPC 项目的 FPV 固件是一种特殊的组件，其中包含两种流行的 WiFi 适配器驱动程序，Majestic Streamer（在发射机系统的经典方案中充当 GStreamer 的角色）和 [WFB-ng](https://github.com/svpcom/wifibroadcast)。

### 好处

* 降低系统成本（H.265 IP 摄像头与 Raspberry Pi 上的 H.264 MIPI 摄像头）
* 通过简化电路降低总体功耗并提高系统可靠性
* 降低视频延迟：在我们的 Glass-to-Glass 测试中，1080p@60 的延迟约为 80ms（在中等预算的摄像头上），720p@60 的延迟约为 60 ms，1080p@30 的延迟约为 100 ms（对于大多数预算摄像头）。
* 可以调整硬件编码器，例如更频繁地形成 I 帧（具体情况取决于 IP 摄像头的供应商）
* 社区在 [修复 IP 摄像头](https://t.me/ExIPCam) 方面积累了大量经验，这使得进一步降低系统运行成本成为可能。

