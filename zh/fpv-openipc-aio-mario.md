# OpenIPC Wiki
[目录](../README.zh.md)

OpenIPC AIO "Mario"
-------------------

https://store.openipc.org/OpenIPC-AIO-Mario-v1-0-p633320808

![image](https://github.com/user-attachments/assets/ad675599-61ce-4cec-a9bf-5933d907c53a)

使用前请取下镜头保护膜

![image](https://github.com/user-attachments/assets/9ead08a6-f4eb-45a0-bc63-19d3abd3ec1e)



马里奥相机外壳配有 4 个 M1.2 螺丝。

侧面安装孔为 M2 x2 螺纹。

为 AIO 安装天线。

![image](https://github.com/user-attachments/assets/e10e6671-553f-4840-aacd-16816be0813b)



### LED 功能

红灯闪烁 电池供电

红色 有线连接

蓝闪 射频发射器

绿色 待定

电路板尺寸为 30mmx32mm，带有 4*M2 安装孔。

带有 20mmx20mm 安装孔的散热器。

![image](https://github.com/user-attachments/assets/1c7e34c1-76a9-45ee-9caf-ffd33261e154)




### 电源部分

电源输入：

2S-6S

板载双向 BEC 容量：

板载 RF BEC 最高输出 3A 5V

板载 MSIC BEC 最高输出 2A 5V

进入 uboot 使用 uart 设备连接到 AIO 板的顶部（R0，T0）焊盘。


### 连接 USB 调试端口

先插入 USB 线连接 AIO 和 PC，然后接通 DC 电源，或者仅接通 USB 电源。

如果电脑中有未知的USB设备，需要安装以下驱动。

[corechip-sr9900](https://github.com/user-attachments/files/16829005/corechip-sr9900-usb20-to-fast-ethernet-adapter-1750095.zip) 

Mac 操作系统上的操作

<img width="686" alt="Screenshot 2024-09-21 at 10 07 24 AM" src="https://github.com/user-attachments/assets/15fde52d-8f14-4377-87ed-cec6e05dc70a">

<img width="714" alt="Screenshot 2024-09-21 at 10 09 18 AM" src="https://github.com/user-attachments/assets/ce029aab-7505-41f8-81fc-283e41dfe84a">

<img width="544" alt="Screenshot 2024-09-21 at 10 11 31 AM" src="https://github.com/user-attachments/assets/97aff8d8-1a1b-4682-a304-4af58a29e68a">

#### Windows 中的自动设置

请下载  [Auto_Set_Mario_CDC.zip](https://github.com/user-attachments/files/17010487/Auto_Set_Mario_CDC.zip) 文件，解压并以管理员身份运行。

或手动设置如下：

进入控制面板-互联网-网络：

找到一个 usb2.0 转快速以太网适配器

将此卡的 ipv4 地址设置为 192.168.1.11 掩码：255.255.255.0

应用 

然后打开 ssh 连接 AIO 地址：192.168.1.10

用户：root 密码：12345
**如何为 AIO 获取互联网**

1. 在 Windows 上打开网络连接，右键单击主网络适配器属性---共享：允许其他网络用户选择 USB CDC

![image](https://github.com/user-attachments/assets/e3f41122-7601-4dff-b599-a325e0693b8c)

应用，CDC 以太网将自动获得 IP 地址 192.168.137.1

![image](https://github.com/user-attachments/assets/bac7350b-399e-419c-8a4a-557d4378cf79)

打开管理终端

![image](https://github.com/user-attachments/assets/7eaae7ca-16c6-4e59-a8de-9178c9e3b77c)

使用 arp -a 查找 AIO 的 IP
![image](https://github.com/user-attachments/assets/f6d2641e-a5e9-45cd-90cb-5328d423a6c7)


这里 192.168.137.147 是我的 AIO 的 IP 地址。

打开 ssh 连接到 AIO，完成。

![5ed177f3dc690f9e70452e3e4c93b17](https://github.com/user-attachments/assets/48f44a3d-ae04-4780-aac7-b878a043c3e6)

现在您可以使用 sysupgrade -r -k -n --force_ver 来更新固件。


### 升级固件

可以通过 SD 卡更新固件，或者直接使用 win scp 将 rootfs 和内核文件拖到 /tmp

```
sysupgrade -n -z --kernel=/tmp/uImage.ssc338q --rootfs=/tmp/rootfs.squashfs.ssc338q
```

您可以通过用于在线更新的链接[下载](https://github.com/OpenIPC/wiki/blob/master/en/fpv-openipc-aio-ultrasight.md#software)固件。

您还可以在此处阅读当前的讨论和建议：

- https://t.me/c/1809358416/98818/103632
- https://t.me/c/1809358416/98818/108052

或者直接使用配置器 - https://github.com/OpenIPC/configurator


### 射频部分

射频天线特性

![image](https://github.com/user-attachments/assets/d54050b4-2769-4942-95d7-8aad3b5e2e21)

![image](https://github.com/user-attachments/assets/0a709f70-ac8b-4880-93f5-49e1d958eb1b)


默认天线为 ANT1（1T1R），ANT0+ANT1（2T2R）

推荐 RF 设置

RF 功率最大为 18dbm，用于板载 PA 输入。
  对于 1T1R 射频设置范围：1-63 固件更新至最新版本！
  stbc=0,ldpc=0 建议 RF 功率值 < 45 
  
  MCS 索引 1,3（0-7 为 1T1R，8+ 为 2T2R）
   
  视频比特率：4096 /8192/12688（mcs 3+）

  当使用 stbc=1,ldpc=1 时，建议将 MCS3 的射频功率设置为 8-15 进行测试。

  台架测试时保持 RF 功率 < = 15（仅 USB 连接时）


### 用于航空摄像机记录的标清解决方案

在测试或调试时默认禁用记录功能

要启用录制功能，请在 majestic.yaml 中设置 (记录值) true


**板载散热器和冷却风扇：**

冷却风扇输出功率最大可达 500mA

所有散热器安装孔均为 M2 螺纹。


### 扩展连接器

![image](https://github.com/user-attachments/assets/af8124e3-539f-42c6-a757-a560eb93e3fe)


**NOTE**

USB 仅适用于调试模式，当仅使用直流电源时，cdc 以太网处于睡眠模式以节省能源。

仅 USB 供电模式功率限制为 5W 输入。

**升级固件至 Ruby FPV**

插入 USB 电缆并设置 cdc 以太网 ipv4：192.168.1.11 255.255.255.0

使用 winscp 将解压后的文件拖到 /tmp

使用 ssh 登录并复制以下命令：

sysupgrade --kernel=/tmp/uImage.ssc338q --rootfs=/tmp/rootfs.squashfs.ssc338q -z -n

更新并重启后

使用 ssh 登录并设置命令：

```
fw_setenv sensor imx335 && fw_setenv upgrade https://github.com/OpenIPC/firmware/releases/download/latest/openipc.ssc338q-nor-rubyfpv.tgz && reboot
```

