# OpenIPC Wiki
[Table of Content](../README.md)

CH341A编程器
-----------------

### 修复高压错误

廉价而流行的 CH341A 迷你编程器的早期版本（1.7 版之前）有一个严重的错误，尽管编程器使用跳线设置为 3.3V，但数据线上的电压水平仍保持在 5V。

@ddemos1963 提出了一个有趣的办法，以一种高效而艺术的方式解决了这个问题。

![](../images/hardware-ch341a-hack-1.webp)
![](../images/hardware-ch341a-hack-2.webp)

Here's what you do.

![](../images/hardware-ch341a-hack-6.png)

切断5V电源线与CH341A芯片之间的连接。用锋利的美工刀，切断编程器板背面的走线。

![](../images/hardware-ch341a-hack-3.webp)

将电压调节器的 3.3v 输出脚连接到 CH341A IC 的第 9 引脚，并将其桥接到附近电容器处的相应走线。

![](../images/hardware-ch341a-hack-4.webp)

通过接头上的 5V 引脚连接器将 3.3V 电压从 3v3 引脚重新路由至 CH341A IC 的引脚 28，恢复芯片电源。

![](../images/hardware-ch341a-hack-5.webp)

### 故障排除

```console
libusb: error [get_usbfs_fd] libusb couldn't open USB device /dev/bus/usb/001/003, errno=13
libusb: error [get_usbfs_fd] libusb requires write access to USB device nodes
```

如果在运行编程软件时收到这样的错误消息，则需要调整该设备的 USB 端口的权限。

创建 udev 规则文件

```bash
sudo vi /etc/udev/rules.d/99-ch341a-prog.rules
```

在文件中添加以下内容：

```bash
# udev rule that sets permissions for CH341A programmer in Linux.
# Put this file in /etc/udev/rules.d and reload udev rules or reboot to install
SUBSYSTEM=="usb", ATTRS{idVendor}=="1a86", ATTRS{idProduct}=="5512", MODE="0666"
```

保存文件，重新加载 udev

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

然后拔下编程器并将其重新插入 USB 端口。

### 软件

- [SNANDer](https://github.com/McMCCRU/SNANDer) 或 [此 fork](https://github.com/Droid-MAX/SNANDer)
- [microsnander](https://github.com/OpenIPC/microsnander) 来自 OpenIPC
- [ch341prog](https://github.com/setarcos/ch341prog/)
- [flashrom](https://www.flashrom.org/Flashrom)
- [DIY BCQ CH341A 论坛](http://www.diybcq.com/thread-144131-1-1.html) (中文，使用 Chrome 自动翻译)
- [CH341A 程序员](https://4pda.to/forum/index.php?showtopic=884713) (俄语，使用 Chrome 自动翻译)

