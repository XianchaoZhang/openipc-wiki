# OpenIPC Wiki 
[内容](../README.zh.md)

升级 USB2TT_004 适配器以进行电源管理。 
--------------------------------

此修改将允许您以编程方式控制连接到适配器的设备的电源。

### 方案1：沿5V线开路。

![usb2tt_004_up_cut1](../images/usb2tt_004_up_cut1.webp)

_需要切断通向引脚5B的走线_

![usb2tt_004_pchannel](../images/usb2tt_004_pchannel.webp)

_焊接一个 sot-23 封装的 P 沟道 MOSFET，例如 APM2307A 和一个跳线_

相机公共线接GND，电源接5V引脚。

### 选项 2：通过公共线断开电路。

这种方法比较复杂，但允许您使用 12V 为相机供电。 5V时，2N7002三极管导通电阻较高，相机无法启动。任何 NPN 晶体管都可以。

![usb2tt_004_up_cut2](../images/usb2tt_004_up_cut2.webp)

_切割通向引脚 3.3V 和 5V 的走线_

![usb2tt_004_down_cut1](../images/usb2tt_004_down_cut1.webp)

_切断电阻器 R40 和晶体管 Q1 之间的走线_

![usb2tt_004_nchannel](../images/usb2tt_004_nchannel.webp)

_我们焊接一个 N 沟道 MOSFET 来代替 Q1，并焊接一个双极 NPN 型来代替 Q6。将一根跳线从公共线焊接到 Q1 的源极，将第二根跳线从 Q6 的集电极焊接到 Q1_ 的栅极

摄像头公共线接5V。电源取自外部电源，或取自通过跳线连接到 5V USB 端口的 3.3V 输出。

### 在 Linux 操作系统下使用适配器。

安装minicom并授予用户权限：

```
sudo apt install minicom
sudo usermod -a -G dialout USERNAME
```

要使用电源重新启动设备，请按"Ctrl+A H"。不关闭电源即可退出`Ctrl+A Q`。

将适配器连接至 USB 端口时，电源将关闭。要启用它，您可以运行以下程序：

```
#include <sys/ioctl.h>
#include <fcntl.h>
int main()
{
  int fd;
  fd = open("/dev/ttyUSB0", O_RDWR | O_NOCTTY);  
  int DTR_flag;
  DTR_flag = TIOCM_DTR;
  ioctl(fd, TIOCMBIS, &DTR_flag);
  close(fd);
}
```

### 在 Windows 操作系统下使用适配器。

安装允许您控制各个 COM 端口引脚的终端程序，例如 Br@y。按 DTR 按钮切换电源状态。

#### 添加

晶体管Q5的基极连接至RTS引脚。如果将晶体管焊接到其上，则可以控制另一个设备的电源或提供数字 0/1 信号，例如用于调试。

Q5-Q1 线连接至 RTS 引脚并上拉至 3.3V。 Q6-Q7 线连接至 DTS 引脚并上拉至 5V。

