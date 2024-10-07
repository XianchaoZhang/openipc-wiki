# OpenIPC Wiki
[目录](../README.zh.md)

使用 OpenIPC 作为 FPV 系统的预算视频链接 
---------------------------------------------------

<p align="center">
  <img src="https://github.com/OpenIPC/wiki/blob/master/images/fpv-logo.jpg?raw=true" alt="Logo"/>
</p>

2015 年，德国爱好者 Befi [提出了开源无人机的想法](https://befinitiv.wordpress.com/wifibroadcast-analog-like-transmission-of-live-video-data/)，他建议使用普通的 WiFi 适配器，这种适配器在全球范围内的数量已经达到数十亿个，而且价格大幅下降，已成为最实惠的数字收发器类型。

关键思想不是 WiFi 适配器本身，而是使用它们的非常规方法：拒绝建立网络连接并切换到广播模式，此时一个适配器充当数字信号的发射器，而第二个适配器充当接收器。

建议使用经济型 SBC Raspberry Pi，通过 MIPI 接口和 USB WiFi 适配器（推荐的适配器之一）将摄像头连接到它，接收器的另一侧则使用类似的 WiFi 模块和 HDMI 显示器。在这种情况下，不是使用传统的 UDP 协议进行低延迟视频传输，而是使用较低级别的协议进行灵活的数据管理：带宽控制、发射器功率以及在传输过程中数据包 [部分损坏](https://en.wikipedia.org/wiki/Error_detection_and_correction) 时恢复数据的能力。其中一个技术问题是，并非每个 WiFi 适配器都能在这种低级模式下工作，此外：您几乎总是必须安装仅适用于 Linux 的特殊驱动程序。

在此模式下，接收器以所谓的"监控模式"运行，接收并向操作系统传输给定 WiFi 信道的每个数据包，然后决定是否对其进行解码或丢弃。WiFi 发射器适配器必须以"注入模式"运行，此时操作系统内核实际上并不参与 ISO 网络模型规定的网络数据包生成。有些适配器支持两种模式，有些仅支持其中一种，因此可能只位于发射器或接收器端。

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

### 缺点

* 由于资源有限（已安装的 RAM 和永久存储），大多数流行的编程语言（如 Java、Python、NodeJS）都不受相机支持。如果您喜欢用这些语言编写（或希望将用这些语言编写的软件移植到相机），则必须另外使用 NanoPi，并将 OpenIPC 相机用作连接到 NanoPi 的常规 IP 相机（https://www.aliexpress.com/item/1005004679805441.html）。用更大的闪存替换闪存是另一种选择：
* 您必须进行一些焊接才能更换板载内存，但我相信 FPV 世界中的任何人都不会对此感到害怕
* 在大多数系统中，由于某些模块以二进制形式提供，因此 Linux 内核版本受到 IP 摄像机供应商的限制。为新的 WiFi 适配器或特定外围设备开发内核驱动程序可能非常耗时。
* 预算相机（FPV 固件主要为其开发）的资源非常有限，因此在撰写本文时，无法运行神经网络。这种情况在不久的将来应该会有所改变。

### 原料

* IP 摄像头。考虑到 [VEYE 307](http://www.veye.cc/en/product/cs-mipi-imx307/) 型号在 FPV 社区中的受欢迎程度，我们建议您购买雄迈公司生产的 IVG-G2S IP 摄像头板，它具有完全相同的传感器 IMX307，但 [价格更实惠](https://aliexpress.com/item/1005003386137528.html)（甚至 [更实惠](https://item.taobao.com/item.htm?id=660122799616)，如果有可能在淘宝上购买，例如 [通过中介](https://www.youcanbuy.ru/)）。订购板时，请指定卖家将为您的订单设置的镜头焦距（焦距越大 - 放大倍数越大，但视角越小）。将来，镜头可以换成另一个具有相同螺纹的镜头，或者立即购买几种型号以供选择（视频监控领域的标准是 3.6 毫米）。 IP 摄像头板由 12V 供电（实际上 5V 就足够了，如果您不将其用作带背光的摄像机），并且对于初始测试，最好使用组合电源以太网电缆，它有两种类型：12V 带 [通过插孔供电](https://aliexpress.com/item/32961238897.html) 和主动 PoE 48V（根据网络交换机的功能选择）。

* WiFi 适配器。目前，FPV 固件支持 RT8812au 和 AR9271 芯片的两种驱动程序（但原则上没有人阻止添加其他适配器）。强烈建议在链路的两侧使用相同的适配器（此外，成对地从同一批次中取出它们），并且由于 2.4GHz 完全过载，因此仅使用 5GHz 频率。固件使用 [RT8812au](https://aliexpress.ru/item/32664378094.html) 和 [AR9271](https://aliexpress.co/item/32884675724.html) 上的两个适配器进行测试（最后提到的芯片仅适用于 2.4GHz 频率）。

* [UART-USB 适配器](https://aliexpress.com/item/1005001625391776.html) 电压为 3.3V。请注意，使用 5V 适配器可能会烧坏您的相机。不要购买/使用基于 [PL2303](https://aliexpress.com/item/704553060.html) 的适配器，尽管它们更便宜，但它们不适用于此 SoC。上一段中提到的编程器可以与 UART 适配器一起使用，因此如果您有一个，则无需单独购买。

* [带有 JST 1.25 型连接器的连接器](https://aliexpress.com/item/32863841787.html) 采用“3 针”和“8 针”配置，用于连接到相机的 UART 端口并替换飞行版本中的标准电源以太网电缆。

选修的：

* 16 兆字节或更大的 SPI NOR 闪存芯片可替代标准的 8 兆字节芯片。我们建议使用 [W25Q128FVIQ](https://www.aliexpress.com/item/1005003093500630.html) 或 [任何其他](https://www.winbond.com/hq/product/code-storage-flash-memory/serial-nor-flash/?__locale=en&selected=128Mb#Density) 与固件兼容（新模块也可以通过芯片 ID 添加到项目中）。请注意，市场上有很多 Winbond 仿冒品，您应该谨慎选择卖家。

* [SPI NOR 闪存编程器](https://aliexpress.com/item/32902635911.html)。原则上，您可以使用项目 [burn][github_burn] 来实现这一点，它允许您将系统文件刷入空/已损坏的闪存中（请参阅部分 [将图像填充到空/已损坏的闪存中](https://github.com/OpenIPC/burn)。部分 [使用 burn 将图像刷入空闪存](#pour-the-image-onto-an-empty-flash-using-burn-if-you-don-have-a-programmer)）。请注意，尽管许多编程器套件中都有“衣夹”，但绝对不可能直接在电路板上转储/编程闪存，因为编程器除了芯片外还会为电路板的其余部分供电（有一种方法可以通过切断 VCC 脚来解决这个问题）。

* 欢迎使用 [SoC 散热器](https://aliexpress.com/item/32859349038.html)（由于价格低廉，制造商通常不安装）。

* [F0.95 快速镜头](https://aliexpress.com/item/32876034491.html)（其他选项为[一](https://aliexpress.com/item/32957334039.html)和[二](https://aliexpress.com/item/4000142214594.html)）可充分利用索尼 IMX307 传感器，享受夜间飞行。

* [变焦镜头 2.8-12mm](https://aliexpress.com/item/32809397197.html)。请注意，该套件附带一个过时的基于 HiSilicon 3516EV100 的 IP 摄像头板，也可以将其重新刷新到 OpenIPC。由于控制电机的电路板通过 UART 与主板通信，因此您必须创造性地通过多路复用两个 UART 端口或输出未焊接的引脚来解决问题。

* [16 针 FPC 连接器](https://aliexpress.com/item/33013766973.html) 用于更好地焊接与 USB 适配器和相应的 [电缆](https://aliexpress.com/item/32958943450.html) 的连接。

在考虑的板上使用 OpenIPC 固件有两种方式：用更大容量的闪存替换闪存（用于安装您自己的程序）和不替换闪存（这更容易，但在这种情况下，新系统的进一步扩展可能性将非常有限）。下面将逐步讨论这两个选项：

### 在相机上安装（常见启动）

* 将电缆连接到摄像头并检查其是否正常工作（默认 IP 地址为 192.168.1.10，VLC 链接为  `"rtsp://192.168.1.10/user=admin&password=&channel=0&stream=0"`）。
* 将三线 UART 连接器焊接到摄像头板上的空闲焊盘上

<p align="center">
<img src="https://github.com/OpenIPC/wiki/blob/master/images/fpv-imx307-uart.jpg?raw=true" alt="Logo"/>
</p>

* 将 UART-USB 适配器连接到计算机（端口速度 115200N1，流量控制禁用，适配器应设置为 3.3V，而不是 5V）并检查当相机打开时，数据是否正在输出，并且您可以通过“Ctrl-C”中止下载（RX 和 TX 线都在工作）。

### 使用 IP Cam DMS 安装（无闪存焊接）

* 下载、解压压缩包并运行 [IP Cam DMS](https://team.openipc.org/ipcam_dms/IPCam_DMS_20201121_EN.zip) 程序，该程序允许您使用摄像机制造商的协议控制摄像机。
* 下载 [特殊压缩包](https://github.com/OpenIPC/coupler/releases/download/latest/000659A7_fpv_IPC_GK7205V200_50H20AI_S38.bin) 并执行固件升级，这将有效地实现从原始固件到 OpenIPC 的无缝过渡。

> 注意！尽管这是刷新固件最简单的方法，但它也有一些缺点：
> * 它不会备份库存固件。您可能需要备份来从库存固件中提取一些重要参数。因此，即使您 100% 确定不需要库存固件，备份仍然是个好主意。
> * 事实上，只有 IPC_GK7205V200_50H20AI_S38 板才有预构建的 fpv 版本。对于其他板，您需要先找到并刷新精简版。因此，对于 IPC_GK7205V200_50H20AI_S38 板以外的板，使用此安装类型是没有意义的。

### 使用 burn 录安装（无闪存焊接）

即使您锁定了引导加载程序或将错误的引导加载程序刷入 SPI 闪存，此方法仍可行。
* 在工作站上安装 TFTP 服务器
* 在 [openIPC][supported_hardware] 上找到您的 SoC 并生成“安装指南”（注意：即使您有 16M 或 32M 芯片，也请选择 NOR 8M 内存芯片，因为这些配置没有 fpv 版本。不用担心，首次启动后覆盖 fs 将扩展。）
* [burn][github_burn] 实用程序有一个视频教程：[OpenIPC BURN Utility Playlist][youtube_burn] 。只需选择适合您操作系统的视频并按照指南操作即可。
* 在视频教程结束时，您将到达具有解锁引导加载程序的终端。不要关闭它，您将需要它。
* 确保 TFTP 服务器已运行，并且您已将“安装指南”中的 OpenIPC 固件下载到正确位置
* 按照“安装指南”的第二步操作：
- 保存原始固件
- 刷新完整的 OpenIPC 固件映像

### 安装在相机上（更换闪存）

* 关掉相机，拆焊原始的 8 兆字节 SPI NOR 闪存芯片，并用编程器将其转储以防万一。最好使用 [热风焊枪](https://aliexpress.com/item/32980690787.html) 拆焊芯片，但如果您真的想要，您可以使用普通烙铁 [如 Alexey Tolstov 建议的那样](https://www.youtube.com/watch?v=M69JiBtuqq8) 或 [像这样](https://www.youtube.com/watch?v=dspjVDv7hck)。拆焊芯片后，应使用一根铜线彻底清洁焊盘上的焊料残留物。使用热风焊枪时，最好取下镜头并隔离其他组件，尤其是用 [kapton 胶带](https://aliexpress.com/item/1005003563721341.html) 隔离塑料连接器（极端情况下使用巧克力棒箔）。切勿 [使用 Rose 合金](https://habr.com/ru/post/437778/)。如果您的手指不太灵巧，那么去最近的手机维修服务处并向师傅展示本手册的一部分会更方便。

<p align="center">
<img src="https://github.com/OpenIPC/wiki/blob/master/images/fpv-imx307-spinor.jpg?raw=true" alt="Logo"/>
</p>

* 将 [U-Boot](https://github.com/OpenIPC/firmware/releases/download/latest/u-boot-gk7205v200-universal.bin) 填充到新的 16 兆字节闪存中（在内存的开头），并将其焊接到电路板上。验证 U-Boot 是否启动，并得到提示符。
* 在工作站上，安装 TFTP 服务器，下载并解压到 [archive](https://github.com/OpenIPC/firmware/releases/download/latest/openipc.gk7205v200-nor-fpv.tgz) 目录中的 FPV 固件
* 在 U-Boot 中发出命令（其中 `192.168.1.17` 是您的 TFTP 服务器，`192.168.1.33` 是分配给相机的临时地址）：

```
  setenv ipaddr 192.168.1.33; setenv serverip 192.168.1.17; saveenv
  run setnor16m
  #
  run uknor16m; run urnor16m
```

### 在相机上安装（一般完成）

* 启动系统后，验证它是否已通过以太网获取 IP 地址（可以通过 root@<摄像头的 IP 地址> 通过 SSH 访问，无需密码或密码：12345）。使用现代操作系统和名称“openipc.local”，您可以在不知道其 IP 地址的情况下在本地网络中找到摄像头。
* 将 GND、DP 和 DM 焊接到 USB 焊盘上（由于适配器的功耗高，不应将 USB 5V 电源连接到电路板），并分别将 5V 和 GND 焊接到 WiFi 适配器上（可能通过额外的 DC-DC 转换器，具体取决于您的电路）。连接到电路板的 USB 线应使用电缆扎带固定，以免折断接触板上的针脚：

<p align="center"> <img src="https://github.com/OpenIPC/wiki/blob/master/images/fpv-pinout.jpg?raw=true" width="50%/"> <img src="https://github.com/OpenIPC/wiki/blob/master/images/fpv-usb-colors.jpg?raw=true" width="50%/"> </p>

* 通过 `lsusb` 命令检查启动后是否出现了新设备
* 检查 `free -m` 命令是否至少提供了 34 兆字节的系统 RAM（其余的是视频内存），否则你可能会在运行中遇到 OOM killer（可通过 `fw_printenv bootargs / fw_setenv` 并使用 osmem=40M 更正来修复）：

```
root@openipc-gk7205v200:~# free -m
              total        used        free      shared  buff/cache   available
Mem:             34          21           2           0           9           9
Swap:             0           0           0
```

### 检查地面站运行情况（桌面上）

* 将第二个适配器连接到桌面并编译一个在监控模式下工作的驱动程序（参见相关项目文档），并在必要时通过 `insmod` 加载它。
* 激活接口（在此示例中为 `wlan0` 并指定 [channel](https://en.wikipedia.org/wiki/List_of_WLAN_channels)）（在此示例中为 `14`）：
```
sudo ip link set wlan0 down
sudo iw wlan0 set monitor control
sudo iwconfig wlan0 channel 14
sudo ip link set wlan0 up
```
* 确保通过 `iwconfig` 命令在摄像头和桌面 WiFi 适配器上设置相同的频率，如有必要，可以通过编辑摄像头上的 `/etc/wfb.conf`（`channel` 参数）或桌面上的 `sudo iwconfig <适配器名称> 通道 <编号>` 来更改频率。
* 从源代码 [WFB-ng](https://github.com/svpcom/wifibroadcast) 编译，__必须使用 brunch stable__，将 `./etc/gs.key` 从 IP 摄像头复制到桌面并运行接收 `sudo ./wfb_rx -p 0 -u 5600 -K gs.key -i 7669206 wlan0`。
* 检查控制台输出是否更改为
```
32168228	PKT	0:0:0:0:0:0
32169229	PKT	0:0:0:0:0:0
32170230	PKT	0:0:0:0:0:0
32171231	PKT	0:0:0:0:0:0
32172232	PKT	0:0:0:0:0:0
32173233	PKT	0:0:0:0:0:0
```
change to
```
32178236	ANT	1	282:-54:-52:-50
32178236	ANT	0	282:-48:-46:-44
32178236	PKT	283:0:283:2:0:0
32179236	ANT	1	244:-54:-52:-50
32179236	ANT	0	244:-48:-45:-44
32179236	PKT	245:0:245:0:0:0
32180236	ANT	1	250:-54:-52:-50
32180236	ANT	0	250:-48:-45:-44
```
* 运行 Gstreamer `gst-launch-1.0 -vvvv udpsrc port=5600 ! application/x-rtp,encoding-name=H264,payload=96 ! rtph264depay ! h264parse !queue !avdec_h264 !auto​​videosink sync=false -e` 并检查图像质量

### 地面站开发、遥测

假设 Linux 机器已按照 [quick-start-using-ubuntu](https://github.com/svpcom/wfb-ng#quick-start-using-ubuntu-ground-station) 中的说明安装了 wfb-ng。以下示例使用 Hubuntu 18.04 LTS 和 wfb-ng 22.09。

* 运行 wfb-ng，启动 wfb-cli 控制台：
```
sudo systemctl restart wifibroadcast@gs
wfb-cli gs
```
* 确保视频数据包正在流式传输。在这里您还可以看到 WiFi 适配器天线的 RSSI 值：

![wfb-cli-视频](../images/wfb-cli_video_only.png)

如果 recv 值保持为零，而 d_err 值增加，则相机和地面站密钥可能不匹配。确保将 /etc/gs.key 复制到地面站。如果不存在任何软件包 - 确保相机上 /etc/wfb.conf 中的“channel=xx”和地面上 /etc/wifibroadcast.cfg 中的“wifi_channel=xx”具有相同的值。对于 RTL8812AU 适配器的 5.8 GHz 范围，建议使用 60 及以上的通道。

* 在 ArduPilot 下配置飞行控制器，以 mavlink1 格式输出遥测数据，速度为 115200，例如在端口 Serial1 上。如果是单向遥测（仅下行链路），请确保 FC 默认输出所需的遥测流，而无需连接到地面站。这可以通过设置 SR1_xxx 参数来实现，请参阅 [mavlink SR_ 参数]。[mavlink SR_ 参数](https://ardupilot.org/dev/docs/mavlink-requesting-data.html)。
* 将 FC 的 Serial1 连接到摄像头的 UART，rx 到 tx，tx 到 rx。对于 STM32F4/7 上的现代 FC，电压电平相同（3.3V），对于 5V APM，需要进行电平转换。微妙之处在于，在 UART 输入上收到任何字节后，摄像头 U-Boot 引导加载程序会在启动时停止。 PC 应通过设置 TELEM_DELAY 参数开始输出延迟几秒钟的遥测 c。在表格中，更容易在从 FC 到相机的线路中提供一个断点。
* 在相机的 /etc/datalink.conf 文件中设置 `telemetry=true` 参数，在 /etc/telemetry.conf 文件中分别设置 `one_way=true` 参数用于单向遥测或 `one_way=false` 用于双线遥测。在相机上编辑文件的最简单方法是 Midnight Commander 中的 Shell Link：
* ![mc_shell_link](../images/MC_shell_link.png)
* 重新启动相机和 wfb-ng 服务。第二个数据流 - 遥测 - 应出现在 wfb-cli 中：

![wfb-cli-video-telem](../images/wfb-cli_video_telem.png)

* 安装 QGroundControl。此处使用版本 4.0.11，因为最新版本无法在 18.04 LTS 中正确处理视频。无需创建新的通信链接。QGC 应该可以看到 PC 连接并显示遥测输入流：


![QGC-map](../images/QGC_telem.png)

![QGC-mavlink](../images/QGC_mavlink.png)

在双向遥测的情况下，QGC 应该下载参数、允许更改参数、允许切换飞行模式以及加载和卸载任务：

![QGC-参数](../images/QGC_params.png)

![QGC-任务](../images/QGC_mission.png)

您可以看到 QGS 也已开始显示视频。4.0.11 中的延迟非常明显，这是由于纯软件流处理造成的，因此最好在 Ubuntu 20.04 及更新版本下尝试现代版本。

### 故障排除

* 无需运行 WFG-ng，通过桌面上的命令“sudo tcpdump -i wlan0”，您可以验证发射器是否确实在通过无线方式发送数据包：

![Tcpdump](../images/fpv-tcpdump.jpg)

### 使用刻录机将图像倒入空闪存中（如果您没有编程器）

在开发板关闭的状态下运行 [burn](https://github.com/OpenIPC/burn)：
```
./burn --chip gk7205v200 --file=u-boot-gk7205v200-universal.bin -d ; screen -L /dev/ttyUSB0 115200
```

打开开发板电源，等待 U-Boot 填充并出现命令行。然后我们执行以下命令，其中 `192.168.0.8` 是 TFTP 服务器地址，`192.168.0.200` 是摄像头的临时 IP 地址。

```
setenv ipaddr 192.168.0.200
setenv serverip 192.168.0.8

sf probe 0; sf lock 0
mw.b 0x42000000 ff 1000000; tftpboot 0x42000000 u-boot-gk7205v200-universal.bin; sf probe 0
sf erase 0x0 0x50000; sf write 0x42000000 0x0 ${filesize}
reset

run setnor16m

setenv ipaddr 192.168.0.200
setenv serverip 192.168.0.8

run uknor16m ; run urnor16m

saveenv
reset
```

### 进一步完善

#### 转接板的开发

考虑到根据上面的文字，我们最终从普通的廉价视频监控摄像头中完成了电路板的制作，并且 USB 连接器的安装没有工厂连接器，建议制作一个额外的特殊电路板（类似于 [内置 WiFi 适配器的电路板](https://aliexpress.com/item/1005002369013873.html)，它将具有 USB 连接器（可能带有额外的集线器）和 SD 卡。这将允许以最小的延迟在 720p 下广播视频，同时以 1080p 录制原始视频以供以后在 YouTube 上发布）。如果您有能力设计这样的电路板并与社区分享电路，我们将不胜感激。

### 常问问题

#### 相机运行过程中的功耗是多少？

功耗取决于传感器是否开启（传感器本身就是功耗大户之一），根据我们的测量，在活动模式下功耗为 1.7W，在传感器关闭（但主系统正在运行）时功耗为 1.1W。由此我们可以得出结论，如果需要，我们可以通过编程方式关闭/打开流媒体，以在需要时进一步降低系统功耗。

另外值得考虑的是：
* 能够在系统启动后 N 分钟内以编程方式关闭以太网适配器（启动后立即编写操作以允许配置更改和调试）
* 将所有未使用的 GPIO 置于输入模式
* [检查数据表](https://drive.google.com/file/d/1zGBJ_SIazFqJ8d8bguURVVwIvF4ybFs1/view) 并使用寄存器禁用芯片的所有未使用的功能块。

#### 是否支持 WDR？

为了使 WDR 在 IP 摄像机上正常工作，主芯片和传感器都必须支持相同的 WDR 标准（有几种类型）。在这种情况下，传感器通常以双倍频率开始工作（例如，60FPS 而不是 30FPS），使用长快门速度制作一帧，使用短快门速度制作第二帧。然后 ISP（图像信号处理器）硬件产生两帧的粘合，从使用长快门速度的帧中取出暗区，使用短快门速度取出亮区，形成具有扩展色彩范围的图像。不幸的是，要获得 WDR 图像，整个系统必须至少以两倍的速度工作（或者换句话说，拥有更多的晶体管，在某一时刻完成两倍的工作），因此 Goke V200 处理器没有这种模式。如果 WDR 支持对您来说至关重要，请考虑 V300 处理器系列中的下一代产品，该项目也支持该产品。

#### 我可以使用 LTE 适配器代替 WiFi 吗？

可以，但需要修改固件。我们建议您在官方群组中询问有关适配特定硬件的问题。

#### 我可以连接 SD 卡进行视频录制吗？

是的，你可以。照片来自一位订阅者：

<p align="center">
<img src="https://github.com/OpenIPC/wiki/blob/master/images/fpv-sd-card.jpg?raw=true" width="50%"/>
</p>

辅助连接器的引脚排列表：

<p align="center">
<img src="https://github.com/OpenIPC/wiki/blob/master/images/fpv-usb-sd.jpg?raw=true" width="50%"/>
</p>

#### 可以连接哪些附加外围设备？

根据上面显示的未焊接 FC 连接器的引脚排列，您可以看到它用于带有 WiFi（通过 USB）和 SD 卡的附加扩展卡。未使用的引脚可以按如下方式重新分配：

|Function|Additional|
|---|---|
|SD_CLK|GPIO32|
|SD_CMD|GPIO33|
|SD_DATA0|GPIO34|
|SD_DATA1|GPIO35|
|SD_DATA2|GPIO36|
|SD_DATA3|GPIO37|
|ALARM2_GPIO82||
|KEY_SET||
|BAT||
|ALARM_OUT||

D/N（白天/夜晚）只能用作输入 GPIO15（由于安装了晶体管）。同一连接器上它的左侧是 GND 和 GPIO16（可用作双向模式下的 GPIO 或 PWM 端口）。

电源网络连接器上有两个 GPIO（ETH_STA - GPIO14、ETH_ACT - GPIO12），通常用于指示活动的以太网物理连接和数据传输活动。它们也可以用于普通的双向 GPIO，其中 GPIO12 可以设置为 UART2_RXD 模式并实现额外的单向 UART 端口（仅用于数据接收）。该板在这些引脚上有 330 欧姆电阻，但这不会影响 UART 操作。

考虑到 SoC 外壳采用 QFN88 格式制作，可以将细线焊接到芯片的几乎任何脚上并使用附加端口。芯片引脚排列和没有芯片的真实电路板的照片如下所示：

<p align="center"> <img src="https://github.com/OpenIPC/wiki/blob/master/images/fpv-v200-pinout.png?raw=true" width="50%"/> </p>

<p align="center"> <img src="https://github.com/OpenIPC/wiki/blob/master/images/fpv-pcb-part.jpg?raw=true" width="50%"/> </p>

请注意，该解决方案远非工业化（理想情况下，您应该制作自己的电路板），如果无法避免，建议锯掉芯片主体以获得更安全的接触。

#### 我可以使用另一个 IP 摄像机吗？

如果您具备高级 Linux 用户的技能，您可以加入我们的项目，并将 FPV 固件适配到 [OpenIPC](https://openipc.org/supported-hardware) 支持的任何处理器。在大多数情况下，不需要编程技能（或者您会在了解和学习系统时自然而然地获得这些技能）。

#### 我怎样才能进一步减少视频延迟？

为了获得尽可能低的延迟，我们的固件使用了 HiSilicon/Goke 处理器中提供的低延迟模式。具体值取决于 SoC 型号、传感器、其分辨率、当前帧曝光甚至运行期间的芯片发热。延迟的主要因素是系统的 FPS（未经任何调整的 60FPS 将比最大设置下的 30FPS 更好），因此如果您需要低延迟，请注意更昂贵的硬件。为了获得更低的延迟，可以禁用中间块，但代价是降低图像质量，或者切换到更现代的芯片组。

我们的团队在低延迟媒体传输方面拥有丰富的经验（一些项目的延迟数字已达到 45 毫秒）。如果您对商业服务（咨询、硬件和软件开发、逆向工程）感兴趣，请[联系我们]（mailto:d.ilyin@openipc.org）。

### 快速插入一些链接

**请使用翻译器，这里有很多关于 FPV 的有趣内容：**

- https://github.com/openipc/sandbox-fpv
- https://github.com/OpenIPC/silicon_research
- [OpenIPC 用于构建 FPV 系统，在电报信使中聊天](https://t.me/+BMyMoolVOpkzNWUy)
- [WFB-ng 数据传输标准（草案）](https://github.com/svpcom/wfb-ng/blob/master/doc/wfb-ng-std-draft.md)

**以及我们的一般资源：**

- https://OpenIPC.org - https://github.com/OpenIPC

### 有用的链接

#### 来自克日什托夫·库切克

- [构建 OpenIPC FVP Gears](https://qczek.beyondrc.com/building-openipc-fvp-gears/)
- [qczek 出品的 Goke Gk7205V200 相机 FPV 外壳](https://www.printables.com/model/579791-goke-gk7205v200-camera-fpv-case)

#### 淘宝SigmaStar设备示例板

- [Anjoy MC800S，SSC338Q+IM415，约 24 美元](https://demo.otcommerce.com/item?id=655383131557#0)
- [Anjoy MCL12，SSC30KQ+IMX335，约 11 美元](https://demo.otcommerce.com/item?id=600618143992)
- [Anjoy MC-A35，SSC337+?，约 4 美元](https://demo.otcommerce.com/item?id=708324402303#0)


[youtube_burn]: https://youtube.com/playlist?list=PLh0sgk8j8CfsMPq9OraSt5dobTIe8NXmw
[github_burn]: https://github.com/OpenIPC/burn
[supported_hardware]: https://openipc.org/supported-hardware/featured
