# OpenIPC Wiki
[目录](../README.zh.md)

OpenIPC FPV 地面站 
--------------------------

<p align="center">
  <img src="https://github.com/OpenIPC/wiki/blob/master/images/fpv-logo.jpg?raw=true" alt="Logo"/>
</p>


如果你不熟悉 Radza，这里是一个很好的[入门](https://wiki.radxa.com/Zero/getting_started)。

### 烧录

* SDCard 
[链接](https://github.com/OpenIPC/sbc-groundstations/releases) 更新至最新版本

* EMMC 
[如何将图像刷入板载 emmc](https://github.com/OpenIPC/sbc-groundstations/blob/master/radxa_pi_zero_3w/flashing_to_the_onboard_memory.md)


### Wifi

您可以[设置板载 wifi](https://github.com/OpenIPC/sbc-groundstations/blob/master/radxa_pi_zero_3w/headless_setup.md#setup-of-autoconnect-on-boot) 以进行 SSH 连接。（udev 规则和网络管理器已在此镜像中配置，您只需执行 nmcli 命令）

注意：对于 RubyFPV，你要么需要一个 USB 网络适配器（如下所示）

 ![Picture](../images/fpv-radxa-usbc-lan.png)

或者访问串行控制台，请查看[此处](https://wiki.radxa.com/Zero/dev/serial-console)了解如何操作。

### 带 FPV 固件的 DVR

DVR 功能；它需要在物理引脚 25 和 27 之间的 gpio 接头上安装一个按钮，如下所示：


![image](../images/fpv-radxa-gpio.png)

要录制 DVR，请按一次按钮。流媒体将开始，DVR 将开始录制。完成后，按一次按钮停止录制并保存文件。

DVR 保存到您主目录中的 Videos 文件夹中。DVR 可​​通过 /home/radxa/Videos 或媒体服务器访问。将您的地面站连接到您的家庭网络，然后可通过网络浏览器访问 x.x.x.x:8080 - 将 x.x.x.x 替换为您的地面站的本地 IP 地址。

将 Led 长引线连接到 +5v，将 Led 短引线通过 1k 电阻连接到 GPIOAO_2（Radxa 上的另一个蓝色引脚），

```bash
sudo gpioset gpiochip4 11=0      # turn LED on
sudo gpioset gpiochip4 11=1      # turn LED off (actually it is very                             # simply lit because i guess logic level 0 is not 0 volts)
```

电路接线：+5v —> +Led- —-> 1k 电阻 —> Radxa z3w 上的第 28 针（又名另一个蓝色针）

<小时>

关于此图像中的 DVR 录制的说明。为了减轻处理器的压力，我们录制到 ts 文件而不是 mp4 或 mkv。因此，没有录制"拖影"效果，未捕获的帧只是被丢弃。您可能会注意到录制中没有帧信息的地方出现跳跃。

### 一些有用工具的链接

* [Windows 工具](https://dl.radxa.com/zero/tools/windows/)
* [其他，所有操作系统](https://dl.radxa.com/tools/)

### RubyFPV 
请参阅 [RubyFPV 硬件](https://rubyfpv.com/hardware.php)

