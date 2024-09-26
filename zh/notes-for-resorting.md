# OpenIPC Wiki
[Table of Content](../README.zh.md)

安装：如何让OpenIPC在某些硬件上运行
------------------------------------------------------------

[openipc.org](https://openipc.org/firmware/) 网站上提供了支持的硬件和安装程序。

请按照网站上的说明操作，了解您的 CPU 和视频芯片！

以下是一些示例：

[带 IMX291 的 Hi3516cv300](https://openwrt.org/docs/techref/hardware/soc/soc.hisilicon.hi35xx/ivg-hp203y-ay)

[[Hi3516Ev300-IMX335]]

## 如何升级 OpenIPC

目前不支持OpenWRT中类似[sysupgrade][openwrtsysupgrade]的全自动系统升级，请改用部分手动更新。

### 部分手动更新

**注意！** 仅在特殊情况下才建议对 U-boot 和内核进行部分升级，并且应谨慎进行。_

该过程在主站点的 [固件页面](https://openipc.org/firmware/#update-parts-of-the-firmware) 上进行了描述。

## 常见问题

* OpenIPC 可以安装在 Raspberry Pi 或其他硬件上吗？

不。目前仅支持 HiSilicon HI35xx SoC。但理论上可以支持其他架构和主板。不过这需要付出很多努力，因此这不是该项目近期的重点。

* 我可以在不使用 UART 控制台和 TFTP 的情况下刷新 OpenIPC 映像吗？

不可以。目前还不可能，不过我们正在努力。

* 基于BuildRoot的OpenIPC和基于OpenWRT的OpenIPC有什么区别？

Buildroot 是新平台的初始开发速度更快的工具，因为它简单且没有依赖项。OpenWRT 作为最终产品对用户来说很方便，但它存在很多复杂性和依赖性，如果没有社区，开发就没有意义。

### 其他常见问题

* [OpenIPC Wiki (ru)](https://github.com/OpenIPC/camerasrnd/blob/master/docs/FAQ-ru.md)


## 网络相关的东西

常见的网络配置可以在 Luci GUI 中完成：

`http://<你的相机 IP>`

[[3G_modems]] USB 调制解调器支持 hilink 和 ppp 模式

## 图形用户界面

OpenIPC目前有两个分支：

### 基于 OpenWRT

GUI 基于 Luci。有用于相机特定设置的菜单部分。

### 基于 Buildroot

计划采用不同的界面...仍在开发中。

## 硬件相关建议

### 互联网供电 PoE

建议使用带 RJ-45 连接器的 48V 电源适配器，而不是 12V 电源适配器。使用 12V 适配器时，电流会高出 4 倍。大电流会烧毁 RJ-45 连接器和电线。

## 如何将视频流式传输到互联网

* __MiniHttp__ ➤ 基于 OpenIPC 系统的主要音频/视频流媒体。

* __Мajestic__ ➤ 基于 OpenIPC 系统的新型（正在开发中）音频/视频流媒体。

* __YouTube 流媒体__

### MiniHttp 是基于 OpenIPC 系统的主流流媒体

通过关闭不需要的协议和功能来调整 MiniHttp 的配置以获得更好的安全性和性能非常重要。

配置文件位于 `/etc/minihttp.ini`

### 调试模式：

```killall -sigint minihttp; sleep 1; export SENSOR=`ipctool --sensor_id`; minihttp```

### Production mode:

```killall -sigint minihttp; sleep 1; export SENSOR=`ipctool --sensor_id`; minihttp 2>&1 | logger -p daemon.info -t minihttp &```


## OpenIPC OS 中使用的自定义构建工具

[工具存储库](https://github.com/OpenIPC/packages/tree/main/utils)

[ipctool](https://github.com/OpenIPC/ipctool) - 获取硬件信息并以通用格式输出。还可用于备份和恢复相机软件（仍为实验性功能）。

## 与数字视频录制系统的集成示例

使用各种实用程序在本地记录流。

YouTube 作为 DVR 黑客。

## 监控 OpenIPC 系统的技巧和窍门

### 如何从芯片的内部传感器获取温度（如果支持）：

`ipctool --temp`

其他用于[[监测温度]]的命令

[[snmp]]

### Prometheus 监控

[[prometheus 节点]]

### 监控模板

* [[Zabbix 监控模板]]

## Prometheus 节点配置

[Prometheus](https://prometheus.io/) 是一个开源系统监控和警报工具包。

OpenIPC 已将 prometheus node exporter 作为软件包。可以在 http://192.168.1.10:9100/metrics 上以纯文本形式查看结果输出

或者如果你安装了 [proemetheus 服务器和 graphana](https://prometheus.io/docs/visualization/grafana/) 则可视化：

[[images/preometheus_node_graphana_example.jpg]]

您可以在"/etc/config/prometheus-node-exporter-lua"中配置节点。

### 元包

<https://github.com/ZigFisher/Glutinium/tree/master/prometheus-node-exporter-lua>

## 在 Hi3518EV200 上使用 I2C 进行实验

### 通过设备树设置 i2c-x

如果引脚之前已设置为 dts，则标准 i2c-hisilicon 驱动程序不会提供将引脚设置为 i2c 模式的选项。要自动将所需引脚设置为 i2c 模式，您只需将以下代码添加到 hi_i2c_hw_init (linux/drivers/i2c/busses/i2c-hisilicon.c) 的开头即可

```
#ifdef CONFIG_ARCH_HI3518EV200 // Might be the same for other hardware devices
if(pinfo->mem->start = 0x200d0000 /* address i2c-0 */) {
    writel(0x2, 0x200f0040);
    writel(0x2, 0x200f0044);
}
if(pinfo->mem->start = 0x20240000 /* address i2c-1 */) {
    writel(0x1, 0x200f0050);
    writel(0x1, 0x200f0054);
}
if(pinfo->mem->start = 0x20250000 /* address i2c-2 */) {
    writel(0x1, 0x200f0060);
    writel(0x1, 0x200f0064);
}
#endif
```

## 研发相关信息

### 如何[[登录内部]]原始固件

### [[Majestic Log]] 评论

### [[开发工具]]

### [[关于不同 IP 摄像机的文档]]

### Telegram 中与开发相关的群组：


## 研究和开发中使用的工具

[hisi-trace](https://github.com/OpenIPC/hisi-trace) --> 在 OpenIPC 中运行 Sofia 的工具。允许将 Sofia 的原始功能移植到目标系统，而无需加载原始固件。

[一些反汇编工具](https://github.com/TekuConcept/ArmElfDisassembler)

## 有关当前不支持的 SoC 的文档：

[Novatek NV98515 SoC]（https://github.com/hn/reolink-camera）

与 IP 摄像机论坛相关的不同黑客和修改：

<https://www.goprawn.com/>

以下是一些如何使用各种实用程序录制视频流的示例。

### gstreamer

* rtsp h264流：

`gst-launch-1.0 rtspsrc 位置=rtsp://192.168.1.10:554/stream=0！rtpjitterbuffer！rtph264depay！h264parse！mp4mux！filesink 位置=stream0_h264.mp4 -e`

* rtsp h265流：

`gst-launch-1.0 rtspsrc 位置=rtsp://192.168.1.10:554/stream=0！rtpjitterbuffer！rtph265depay！h265parse！mp4mux！filesink 位置=stream0_h265.mp4 -e`

### ffmpeg

### 可变电流

## 如何登录原版固件

此信息仅适用于基于 XM 的相机固件。

### 启用 telnet 服务器

在 U-Boot 控制台中：

```
setenv telnetctrl 1; saveenv
```

### 使用 telnet 连接

```
LocalHost login: root
Password: xmhdipc
Welcome to HiLinux.
```

另外，可以尝试[其他对]（https://gist.github.com/gabonator/74cdd6ab4f733ff047356198c781f27d）

### 可选：启用 Linux 内核详细启动（armbenv 存在的地方）

```
# armbenv -s xmuart 0
# reboot
```

或者如果 XmEnv 存在：

```
# XmEnv -s xmuart 0
# reboot
```

### 无需打开相机即可启用 telnet（远程）

* 使用[链接](https://translate.google.com/translate?hl=en&sl=ru&tl=en&u=https%3A%2F%2Fwww.cctvsp.ru%2Farticles%2Fobnovlenie-proshivok-dlya-ip-kamer-ot-xiong-mai) 找到包含最新固件更新的正确 zip 文件并下载。

* 解压缩并从几个选项中选择适当的"bin"文件。

* 建议使用此库存固件更新您的相机，不要对其进行修改。这将有助于了解可能出现的问题。如果不确定要使用哪个选项，请使用"常规..."。

* 解压"bin"文件，就像解压普通的 zip 档案一样。

* 从存储库的"utils"目录复制"add_xmuart.sh"到解压文件的目录中。

* 运行`./add_xmaurt.sh`，然后确保`u-boot.env.img`在文件末尾附近有`xmuart=1telnetctrl=1`。

* 重新打包 `bin` 文件并添加修改后的 `u-boot.env.img`，如下所示：`zip -u General_IPC_HI3516EV200_85H30AI_S38.Nat.dss.OnvifS.HIK_V5.00.R02.20200507_all.bin u-boot.env.img`

* 使用新的"bin"文件升级相机。

文档来源为[这里]（https://github.com/OpenIPC/camerasrnd/blob/master/get_telnet.md）

## 用于测量各种 SoC 上的芯片温度的命令

`Hi3516CV200 / Hi3518EV200 / Hi3518EV201`
```sh
devmem 0x20270110 32 0x60FA0000 ; devmem 0x20270114 8  | awk '{print "CPU temperature: " ((($1)*180)/256)-40}'
```

`Hi3516CV300 / Hi3518EV100`
```sh
devmem 0x1203009C 32 0x60FA0000 ; devmem 0x120300A4 16 | awk '{print "CPU temperature: " (((($1)-125.0)/806)*165)-40}'
```

`Hi3516EV200 / Hi3516EV300`
```sh
devmem 0x120280B4 32 0xC3200000 ; devmem 0x120280BC 16 | awk '{print "CPU temperature: " (((($1)-117)/798)*165)-40}'
```

`Hi3536D`
```sh
himm 0x0120E0110 0x60320000 > /dev/null; himm 0x120E0118 | awk '{print $4}' | dd skip=1 bs=7 2>/dev/null | awk '{print "0x"$1}' | awk '{print "CPU temperature: " (($1*180)/256)-40}'
```

`Hi3536C`
```sh
himm 0x0120E0110 0x60320000 > /dev/null; himm 0x120E0118 | awk '{print $4}' | dd skip=1 bs=7 2>/dev/null | awk '{print "0x"$1}' | awk '{print "CPU temperature: " (($1-125)/806)*165-40}'
```

`HI3520DV200`
```sh
devmem 20060020 32
```

`Hi3516AV200`
```sh
#PERI_PMC68 0x120a0110 (disable-->enable)
himm 0x120a0110 0 > /dev/null;
himm 0x120a0110 0x40000000 > /dev/null;

usleep 100000
#PERI_PMC70 0x120a0118 read temperature
DATA0=$(himm 0x120a0118 0 | grep 0x120a0118)
DATA1=$(printf "$DATA0" | sed 's/0x120a0118: //')
DATA2=$(printf "$DATA1" | sed 's/ --> 0x00000000//')

let "var=$DATA2&0x3ff"
if [ $var -ge 125 -a $var -le 931 ];then
    echo `awk -v x="$var" 'BEGIN{printf "chip temperature: %f\n",(x-125)*10000/806*165/10000-40}'`
else
    echo "$var ---> invalid. [125,931]"
fi
```

`Hi3516CV200 / Hi3518EV200 / Hi3518EV201` `Hi3516CV300 / Hi3518EV100` `Hi3516EV200 / Hi3516EV300` `Hi3536D` `Hi3536C` `HI3520DV200` `Hi3516AV200`
## 直接流式传输到 YouTube

YouTube 不仅提供 LiveStreaming，还可以录制此流。

最多可录制 12 小时的 LiveStream。

可以直接流式传输到 YouTube，但目前 OpenIPC 不支持。

### 可以使用 MiniHttp 直接进行流式传输

可以借助 RTMP 直接向 YouTube 进行流式传输，但目前没有计划将此协议添加到主流流媒体 MiniHttp。

### 可以使用 FFMPEG 进行直接流式传输

有两种模式可用：旧模式支持通过 RTMP 传输 H264，新模式支持通过 HLS 传输 H265。

这两种方法都未在生产中测试，目前仍处于开发模式。详情请参阅以下链接：

### H264 通过 RTMP

导航到已编译的包 [H264 over RTMP](https://github.com/ZigFisher/Glutinium/tree/master/hi35xx-ffmpeg/files)

将文件"silence.aac"复制到"/usr/lib/"，将文件"ffmpeg"复制到"/usr/sbin/"

同时设置执行权限：

`chmod +x /usr/sbin/ffmpeg`

使用以下参数运行"ffmpeg"：

`ffmpeg -stream_loop -1 -i /usr/lib/silence.aac -rtsp_transport udp -thread_queue_size 64 -i rtsp://127.0.0.1:554/stream=0 -c:v copy -c:a copy -f flv rtmp://a.rtmp.youtube.com/live2/<你的密钥>`

[基于 HLS 的 H265](https://gist.github.com/widgetii/ec275524dd621cd55774c952bee4c622)

一些构建说明：

<https://github.com/ZigFisher/Glutinium/blob/master/hi35xx-ffmpeg/0_Build.sh>

[openwrtsysupgrade]: https://github.com/openwrt/openwrt/blob/master/package/base-files/files/sbin/sysupgrade
