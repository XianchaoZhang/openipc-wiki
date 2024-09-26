# OpenIPC Wiki 
[内容](../README.md)

问题与解答
----------------

### 如何使用密钥设置 ssh 会话授权

__在相机上__：打开 ssh 会话并为 root 用户创建一个非空密码。默认情况下，在我们的固件中，root 用户没有密码。请记住，创建密码后，所有后续新的 ssh 会话，直到您使用公钥设置授权，以及尝试从没有此类密钥的计算机登录时，都将需要使用此授权特定密码。别忘了！ 

```
passwd
```

 __在桌面上__：将公钥复制到相机，使用上面创建的密码登录。
```
ssh-copy-id root@192.168.1.66
```

__在相机上__：在root用户的主目录中创建`.ssh`文件夹，并将授权密钥存储文件复制到其中。 
```
mkdir ~/.ssh
cp /etc/dropbear/authorized_keys ~/.ssh/
```

__在桌面上__：打开一个新会话以验证授权是否使用公钥，无需密码。
```
ssh root@192.168.1.66
```


### Majestic

#### 如何获取内存转储以进行调试？
在 Majestic > Majestic 调试中启用并配置核心转储发送。

#### 相机图像呈粉红色。
您需要指定 GPIO 引脚来控制红外滤光片。某些相机的设置可以在[表中](https://openipc.org/wiki/en/gpio-settings.html)找到。如果您的相机不在表中，那么您将需要 [ipctool](https://github.com/OpenIPC/ipctool/releases/download/latest/ipctool) 实用程序。

当您第一次调用"ipctool"时，OpenIPC 固件会自动将最新版本的实用程序下载到 /tmp 目录。在本机固件上，您需要使用系统中提供的工具自行将该实用程序下载到相机：wget、curl、tftp...例如，将 ipctool 实用程序下载到本地网络上的 TFTP 服务器，然后下载从那里到相机：

```
tftp -g -r ipctool -l /tmp/ipctool 192.168.1.1
chmod +x /tmp/ipctool
/tmp/ipctool
```
如果相机可以访问互联网，您可以尝试挂载公共 NFS 共享并从中运行该实用程序，而无需将其下载到相机：
```
mkdir -p /tmp/utils
mount -o nolock 95.217.179.189:/srv/ro /tmp/utils/
/tmp/utils/ipctool
```
将该实用程序下载到相机后，运行命令在终端中输入"ipctool gpio scan"，然后用手掌关闭和打开相机镜头几次。观察 ipctool 输出以确定负责控制 IR 滤光幕的引脚。将结果值输入 Majestic 夜间模式设置中。如果粉红色调没有消失，您可能需要启用传感器信号反转。

不要忘记将相机型号和找到的 GPIO 值添加到表中！

#### 是否可以在标准/指标中显示用于设置自动镜头对焦的数据，而不是当前的sample_af？
不，这是一个单独的重型算法，这样运行它是没有意义的。

#### 将文件从 Linux 系统复制到相机 
通常您需要将一些文件放到相机上。除了上述通过 NFS（网络文件系统）的方法外，您还可以使用标准 Linux 系统 scp 命令通过 SSH 连接复制文件：
```
scp ~/myfile root@192.168.1.65:/tmp/
```
该命令会将 myfile 从主目录复制到摄像机上的 /tmp 目录。在非常新的系统上可能会出现以下错误：在这种情况下写下：
```
sh: /usr/libexec/sftp-server: not found
scp: Connection closed
```

В таком случае пишите так:
```
scp -O ~/myfile root@192.168.1.65:/tmp/
```

### 如何更改相机上的MAC地址？

通过 SSH 或 UART 登录并执行命令``fw_setenv ethaddr AA:BB:CC:DD:EE:FF```，其中第三个参数对应所需的正确参数，即一个"有效"的 MAC 地址（最好是原厂地址），这一点非常重要！

当您首次通过摄像机的 85 端口登录 WEB 时，系统会要求您自动更改 MAC，您可以指定自己的 MAC，也可以根据所有规则自动生成 MAC。

### u-boot被删除或损坏后如何恢复相机

许多现代相机处理器都具有快速启动功能，即使闪存驱动器是空的或没有 u-boot，它也允许您刷新闪存驱动器。当您打开平台时，如果处理器收到特殊命令，则会打开快速启动模式，这将允许您写入固件。有几个程序可以用来恢复 u-boot：

* 对于基于海思处理器的相机：HiTool

* 对于采用国科处理器的相机：ToolPlatform

* 作为 OpenIPC 子项目的通用 Python 刻录实用程序：https://github.com/OpenIPC/burn

gk7205v300 平台的启动示例，其中 u-boot/gk7205v300 是带有路径的文件名：

```
./burn --chip gk7205v300 --file=u-boot/gk7205v300.bin --break; minicom -D /dev/ttyUSB0
```

通过 UART 连接时，该命令由以下算法启动：

1.关闭相机电源
2.运行刻录命令
3. 打开相机电源

如果burn抱怨缺少python模块，那么您需要使用以下命令安装这些模块的附加列表
```
pip install -r requirements.txt
```
### 从源头自组装固件

#### 我没有Linux。如何为 Windows 构建固件？

这有点复杂，但也是可能的。  首先，您需要安装适用于 Windows 的 Linux 子系统 (WSL)，例如，您可以在此处阅读如何执行此操作：https://docs.microsoft.com/ru-ru/windows/wsl/install。

然而，这还不够：您需要设置环境变量，否则脚本将失败并出现错误。  抱怨"$​​PATH"环境变量中存在无效字符。  原因很简单：Windows 推出自己的路径：

```diff
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/usr/lib/wsl/lib:/mnt/c/Program Files (x86)/VMware/VMware Workstation/bin/:/mnt/c/Program Files (x86)/Common Files/Oracle/Java/javapath:/mnt/c/Program Files (x86)/Common Files/Intel/Shared Files/cpp/bin/Intel64:/mnt/c/Program Files (x86)/Intel/iCLS Client/:/mnt/c/Program Files/Intel/iCLS Client/:/mnt/c/Windows/system32:/mnt/c/Windows:/mnt/c/Windows/System32/Wbem:/mnt/c/Windows/System32/WindowsPowerShell/v1.0/:/mnt/c/Program Files (x86)/NVIDIA Corporation/PhysX/Common:/mnt/c/Program Files (x86)/PuTTY/:/mnt/c/Program Files/Intel/Intel(R) Management Engine Components/DAL:/mnt/c/Program Files/Intel/Intel(R) Management Engine Components/IPT:/mnt/c/Program Files (x86)/Intel/Intel(R) Management Engine Components/DAL:/mnt/c/Program Files (x86)/Intel/Intel(R) Management Engine Components/IPT:/mnt/c/WINDOWS/system32:/mnt/c/WINDOWS:/mnt/c/WINDOWS/System32/Wbem:/mnt/c/WINDOWS/System32/WindowsPowerShell/v1.0/:/mnt/c/Program Files/LLVM/bin:/mnt/c/WINDOWS/System32/OpenSSH/:/mnt/c/Program Files/NVIDIA Corporation/NVIDIA NvDLISR:/mnt/c/WINDOWS/system32:/mnt/c/WINDOWS:/mnt/c/WINDOWS/System32/Wbem:/mnt/c/WINDOWS/System32/WindowsPowerShell/v1.0/:/mnt/c/WINDOWS/System32/OpenSSH/:/mnt/c/Program Files/Intel/WiFi/bin/:/mnt/c/Program Files/Common Files/Intel/WirelessCommon/:/mnt/c/Program Files (x86)/Common Files/Acronis/SnapAPI/:/mnt/c/Program Files/Docker/Docker/resources/bin:/mnt/c/ProgramData/DockerDesktop/version-bin:/mnt/c/Program Files (x86)/Intel/Platform Flash Tool Lite:/mnt/c/Program Files (x86)/Paragon Software/LinuxFS for Windows/:/mnt/c/Program Files/Git/cmd:/mnt/c/Program Files/WireGuard/:/mnt/c/Program Files/dotnet/:/mnt/c/Users/USER/Python/Scripts/:/mnt/c/Users/USER/Python/:/mnt/c/Users/USER/AppData/Local/Programs/Python/Python37-32/Scripts/:/mnt/c/Users/USER/AppData/Local/Programs/Python/Python37-32/:/mnt/c/Users/USER/AppData/Local/Microsoft/WindowsApps:/mnt/c/Users/USER/AppData/Local/atom/bin:/mnt/c/Program Files/Intel/WiFi/bin/:/mnt/c/Program Files/Common Files/Intel/WirelessCommon/:/mnt/c/Users/USER/AppData/Local/Microsoft/WindowsApps:/mnt/c/Program Files/Multipass/bin:/mnt/c/Users/USER/AppData/Local/Programs/Microsoft VS Code/bin:/snap/bin
```
正如你所看到的，Linux 不喜欢路径中存在空格。我们需要摆脱这样的遗产。

您需要创建一个文件 `/etc/wsl.conf` 
```diff
[automount]
enabled = true
root = /mnt
options = "metadata,umask=22,fmask=11"
mountFsTab = true
[network]
generateHosts = true
generateResolvConf = true
[interop]
enabled = false
appendWindowsPath = false
```
...并重新启动机器：

`exit` 

`wsl --shutdown`


`[interop]` 块包含了必要的设置

结果：
```diff
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/usr/lib/wsl/lib:/snap/bin
```
(c) SterX aka zalessky

### 基本固件版本（Lite）和扩展版本（Ultimate）有什么区别？

- NAND闪存支持
- UBI文件系统
- 无线网络支持
- 二维码扫描仪
- 防火墙
- MQTT（一些精简版也有这个）
- ZeroTier
- WireGuard

- 许多 Majestic 功能，包括。在 Youtube/Telegram 等上串流

### 安装后，u-boot和linux下网络不通

有时，安装 OpenIPC 后，网络无法在摄像机上运行，​​即使电缆和网络显然没有问题。

#### uboot 中无网络 
连接摄像机的端口的路由器/交换机上的网络连接状态 LED 不亮。这可能会伴随以下消息： 
```
OpenIPC # run uknor8m
Hisilicon ETH net controler
MAC:   00-12-12-xx-xx-xx
PHY not link.
```

在这种情况下，需要对 MII（媒体独立接口）子系统进行一些调整： 
```
setenv mdio_intf rmii
setenv phyaddru 0
setenv phyaddrd 1
saveenv; reset
```
以上是最常用的 MII 设置，但它们可能有所不同。 `mdio_intf` 的可能值：`rmii`、`rgmii`、`gmii`。获取这些值和地址的最简单方法是通过"ipctool"从库存固件中获取。如果它们没有保存，那么您只需检查所有组合即可。地址可以从"0"到"3"变化。

您可以在uboot中检查网络功能，如下所示：
```
OpenIPC # ping 192.168.1.1
Hisilicon ETH net controler
MAC:   00-12-12-xx-xx-xx
eth0 : phy status change : LINK=DOWN : DUPLEX=FULL : SPEED=100M
eth0 : phy status change : LINK=UP : DUPLEX=FULL : SPEED=100M
**host 192.168.1.1 is alive**

```

#### 在 uboot 中，网络已经出现，但在 Linux 中没有网络接口 
eth0 在 uboot for Linux 中设置 MII 后，您还需要配置： 
```
fw_setenv extras hieth.phyaddru=0 hieth.phyaddrd=1
```

在`中保存相机的正确值后extras`变量，您必须重新启动相机，之后界面应该出现。您可以使用"ifconfig"或"ip addr"命令进行检查。

#### 更换闪存驱动器后，u-boot 停止加载，只写入一两行，
我刷新了闪存驱动器并将其更换到相机上，而不是损坏的闪存驱动器。我没有加载，而是在 UART 中仅收到以下几行。可以看到，有尝试加载U-Boot，但在某个阶段相机冻结了。
```
U-Boot 2014.04 (Dec 17 2019 - 15:47:31)

CPU: XM530
```

此行为可能是由于更换了不同型号的闪存驱动器所致。当我用 25Q64BVSIQ/25Q64FVSIQ 替换 Winbond 25Q64JVSIQ 后，我就发生了这种情况。他们有不同的特点。当我从工厂退回与相机相同型号的闪存驱动器时，相机像故障前一样工作正常。

