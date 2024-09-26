# Digitus DN-16048 Optizoom PTZ 
平移、倾斜、聚焦和缩放（通过命令行 ssh）

## 刷新 OpenIPC：

打开外壳后，发现 SoC 是 **hi3518cv100**、**16MB**，相机传感器是 **mt9p006**。在 PC 上安装 TFTP 服务器并按照安装说明进行操作：https://github.com/OpenIPC/wiki/blob/master/en/installation.md 从此处 https://openipc.org/cameras/vendors/hisilicon/socs/hi3518cv100 下载正确的镜像（16MB，精简版）并将其放入 TFTP 服务器的目录中（如果镜像未打包，则可以跳过 "unpacking" 步骤）。如所述，将 UART 适配器连接到 SoC 板上的引脚并在 PC 上启动终端：

```sh
screen -L -Logfile ipcam-$(date +%s).log /dev/ttyUSB0 115200
```

按照 OpenIPC 网页生成的说明进行操作。要访问 UBOOT，请在插入电源后立即按 Ctrl-C。要使网络稍后在 Linux 中工作：在 UBOOT 中的最后一个"重置"命令之前，请输入以下命令，如下所示：https://github.com/OpenIPC/wiki/blob/master/en/network-perversions.md

```sh
setenv extras 'hieth.phyaddru=3 hieth.mdioifu=0'
saveenv
```

最后输入：

```sh
reset
```

使用 "root" and "12345" 登录 Linux。在 Linux 中输入：

```sh
firstboot
```

再次登录 Linux。传感器不会被自动检测到。因此，在相机的 Linux 中输入以下命令：

```sh
fw_setenv soc hi3518cv100
fw_setenv sensor mt9p006
```

通过以下方式查找摄像机的 IP 地址

```sh
ip a
```

## 更改密码和 MAC 地址：
通过 85 端口使用浏览器访问 Web 界面并更改密码和 MAC 地址。

## 配置夜间模式切换：
通过 ipctool（ipctool gpio scan）可以轻松发现，光传感器输入（用于白天和夜晚的自动切换）是数字 2。红外截止由 1 和 0 控制。

* Preview-NightMode-Settings:
 
```
Enable Night mode: on
GPIO pin of signal from IR sensor: 2
GPIO pin1 of signal for IRcut filter: 1
GPIO pin2 of signal for IRcut filter: 0
```

## 配置相机分辨率：

* Mainstream Video (Video0):
 
```
Video resolution: 1280x720
Video framerate: 10
```
使用 RTSP 时，您可能希望将 Video0 的帧速率提高到 15。更高的帧速率可能是可行的，但超过 15 内存会不足，并且 Web GUI 可能突然无法再访问（并且可能会发生其他不良影响）。
* RTSP：

```
RTSP enable output: on
```

* MJPEG：

```
Video resolution: 1280x720
```

* JPEG：

```
Snapshot size: 1280x720
```

## 配置图像信号处理器：

* 图像信号处理器（ISP）：

```
Path to sensor configuration file: /etc/sensors/mt9p006_i2c_dc_720p.ini
Block count: 4
```

## 配置网站管理员：

* 系统：

```
Serve Web Admin via Majestic: off
```

## 配置看门狗：

* 看门狗：

```
Enable watchdog: on
Watchdog timeout: 120
```

## 测试 MJPEG 流：
例如，使用浏览器或 Android 应用程序 "IPCamViewer"（将 IP 替换为您当前的 IP）。

```sh
192.168.1.188/mjpeg
```

## 测试 RTSP 流：
例如，在 Linux 上使用 "mpv" 或 Android 应用"IPCamViewer"。使用 "IPCamViewer" 时，选择 "Generic RTSP over UDP" （将密码和 IP 替换为您当前的密码和 IP）。

```sh
mpv rtsp://root:12345@192.168.1.188:554/stream=0
```

## 测试平移、倾斜、缩放和对焦：
事实证明，摄像机通过串行端口 ttyAMA1 使用 pelco-d 协议，因此通过 ssh 登录到摄像机（使用您的 ip）：

```sh
ssh root@192.168.1.188
```

可以通过以下 stty 命令配置串行端口：

```sh
stty -F /dev/ttyAMA1 2400
```

使用编辑器 "vi" 创建以下脚本或通过 tftp 复制文件。使用 chmod 使文件可执行。例如：

```sh
chmod +x ./right
```


### Script ./right

```sh
#!/bin/sh 
stty -F /dev/ttyAMA1 2400 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1 
printf '\xFF\x01\x00\x02\x20\x00\x23' >/dev/ttyAMA1 
sleep 0.1 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1
```


### Script ./left

```sh
#!/bin/sh 
stty -F /dev/ttyAMA1 2400 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1 
printf '\xFF\x01\x00\x04\x20\x00\x25' >/dev/ttyAMA1  
sleep 0.1 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1
```


### Script ./up

```sh
#!/bin/sh 
stty -F /dev/ttyAMA1 2400 
printf '\xFF\x01\x00\x00\x00\x00\x01' > /dev/ttyAMA1 
printf '\xFF\x01\x00\x08\x20\x00\x29' > /dev/ttyAMA1 
sleep 0.1 
printf '\xFF\x01\x00\x00\x00\x00\x01' > /dev/ttyAMA1
```


### Script ./down

```sh
#!/bin/sh 
stty -F /dev/ttyAMA1 2400 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1 
printf '\xFF\x01\x00\x10\x20\x00\x31' >/dev/ttyAMA1 
sleep 0.1 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1
```


### Script ./in (Zoom)

```sh
#!/bin/sh 
stty -F /dev/ttyAMA1 2400 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1 
printf '\xFF\x01\x00\x20\x00\x00\x21' >/dev/ttyAMA1 
sleep 1  
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1
```


### Script ./out (Zoom)

```sh
#!/bin/sh 
stty -F /dev/ttyAMA1 2400 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1 
printf '\xFF\x01\x00\x40\x00\x00\x41' >/dev/ttyAMA1  
sleep 1 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1
```


### Script ./near (Focus)

```sh
#!/bin/sh
stty -F /dev/ttyAMA1 2400
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1
printf '\xFF\x01\x01\x00\x00\x00\x02' >/dev/ttyAMA1 
sleep 0.1 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1
```


### Script ./far (Focus)

```sh
#!/bin/sh
stty -F /dev/ttyAMA1 2400
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1
printf '\xFF\x01\x00\x80\x00\x00\x81' >/dev/ttyAMA1 
sleep 0.1 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1
```


### Script ./scanh

```sh
#!/bin/sh 
stty -F /dev/ttyAMA1 2400 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1 
sleep 0.5 
printf '\xFF\x01\x00\x04\x08\x00\x0d' >/dev/ttyAMA1
```


### Script ./scanv

```sh
#!/bin/sh 
stty -F /dev/ttyAMA1 2400 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1 
sleep 0.5 
printf '\xFF\x01\x00\x08\x08\x00\x11' >/dev/ttyAMA1
```


### Script ./stop
以下脚本停止所有操作（包括水平和垂直扫描）。这是一种解决方法，因为正常的 pelco-d  "stop" 命令并不总是有效。事实证明， "up", "down", "left", "right" 总是会停止扫描命令，因此此脚本中包含简短的左+右命令：

```sh
#!/bin/sh 
stty -F /dev/ttyAMA1 2400 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1 
printf '\xFF\x01\x00\x02\x20\x00\x23' >/dev/ttyAMA1 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1 
printf '\xFF\x01\x00\x04\x20\x00\x25' >/dev/ttyAMA1 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1 
printf '\xFF\x01\x00\x04\x20\x00\x25' >/dev/ttyAMA1 
printf '\xFF\x01\x00\x00\x00\x00\x01' >/dev/ttyAMA1
```


## 可能的进一步改进：

- 摄像机似乎有一个 PoE 板，但似乎不起作用。也许有两个版本（一个带 12V，一个带 PoE），主要区别在于电缆。所以也许可以修改摄像机以使用 PoE 板。

- 有一个 SDCard 插槽（但只有打开机壳时才能使用）。目前无法使用。虽然已有库存固件更新明确解决了 SDCard 功能，但不确定它是否曾经与库存固件配合使用。

- 相机配有 Wifi 模块。可能可以通过 OpenIPC 激活。

- 相机配有变焦和对焦板。此板的文档有中文版（可通过谷歌翻译翻译）。似乎有一个 "factory calibration" 程序来为变焦级别分配 "standard" 对焦设置。

