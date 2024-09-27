# FPV 设置的分步安装指南

当考虑安装 OpenIPC 用于 FPV 时，我们基本上可以将该过程分为 6 个关键步骤。

1. 使用 OpenIPC 烧录摄像头和地面站
2. 连接其他硬件（wifi 适配器和 BECS）
3. 生成并安装 WFB-NG 的密钥配对
4. 编辑 wfb.conf 以设置正确的 wifi 频道
5. 在地面站上配置 vdec.conf
6. 在摄像头上配置 majestic.yaml 文件
7. 测试配置
8. 视频教程和后续步骤

第 1 部分和第 2 部分介绍了硬件的设置 - 尽管涉及许多子步骤，因此您可以将其视为"难点"。虽然软件方面（步骤 3 - 6）有更多步骤，但它本质上是编辑一些文件，因此我们可以将其视为"简单部分"

### 硬件要求
对于本分步指南，我使用的是特定硬件，尽管烧录相机和地面站的程序大致相同，但就您可以使用的 UART 连接而言，它们可能有很大差异，并且芯片组和内存也不同 - 因此请确保仔细检查您的设备。从基本层面上讲，您需要一个地面站、一个相机、2 个 wifi 适配器、最多 4 个 UBECS 和一个 FTDI 板。

作为我的相机的参考，我订购了一块基于 SSC338 的带有索尼 IMX415 传感器的主板。

![Camera](../images/sbs-Camera.jpg)

我订购了这款相机，配有 2.8mm lens （对于 FPV 来说似乎有点窄，但它是当时最宽的镜头）和 12V Lan 电缆。这根电缆对于相机的闪光非常重要。之所以选择这款相机，是因为它可以实现一系列帧速率和分辨率，并且还配备了不错的传感器。我从 AliExpress 购买了这款相机，链接的具体 URL 是 [此处](https://www.aliexpress.com/item/1005004350557805.html)

对于地面站，我选择了 Nvr 板 - 这在 OpenIPC 社区中似乎相当流行，而且成本非常低。同样，我购买的具体型号是 [这里](https://www.aliexpress.com/item/1005004023376532.html)

![Nvr Board](../images/sbs-Nvr.JPG)

相机和地面站都需要一个 wifi 适配器才能无线发送 FPV 信号，因此另一个低成本解决方案是 RTL8812AU。具体来说，我订购了 2 个 [这些](https://www.aliexpress.com/item/1005005638445796.html)

![Wifi board](../images/sbs-wifi.png)

由于我们需要以 12v 运行摄像头和 NVR，并以 3.3v 运行 wifi 适配器，因此我订购了一些简单的 BECS，可以配置为输出 3.3v、12v 或介于两者之间的任何电压。这里有很多选择。我从当地的 eBay 网站购买了 4 个 [这些](https://www.ebay.co.uk/itm/254153188189)。

![BECs](../images/sbs-BECS.jpg)

FTDI 适配器对于在设备上安装控制台以启动烧录过程至关重要。这些适配器在 eBay 上随处可见，价格相当便宜 [这里是一个例子](https://www.ebay.co.uk/itm/203581591537?hash=item2f66688ff1) 不过，您应该确保您选择的 FTDI 适配器有一个可以在 5v 和 3.3v 之间移动的跳线

![](../images/sbs-ftdi.jpg)

除此之外，显然还有一些一般要求 - 连接电线、烙铁、您选择的供电连接器（最有可能是 XT60 - 但选择权在您手中）

### 步骤 1：使用 OpenIPC 烧录摄像头和地面站 我们将把这一步分解为几个子步骤，并将摄像头和地面站分开。所以让我们从比较棘手的部分开始。

### 步骤 1.1：闪光灯相机

首先，让我们检查一下相机及其各个连接点。

![相机引脚分配](../images/sbs-Camera-Pinout-v2.jpg)

要烧录 OpenIPC，我们需要使用 FTDI 适配器打开相机上的控制台连接，然后进入引导加载程序。这听起来比实际要复杂得多 - 我们要做的就是焊接几根电线并在正确的位置按下 RETURN 键。

对于这款相机来说，有一件事使得这更具挑战性，那就是我们需要连接的引脚的位置。当然，它是电路板右侧的两个非常小的焊盘（如图所示）。您可以非常小心地直接焊接到这些焊盘上 - 但要非常小心，不要使用太多的热量，因为这些焊盘很容易被掀掉！

如果您对焊接没有信心，可以使用一些弹簧针在进行闪烁时建立临时连接。我设计了一个简单的 3D 可打印工具来实现这一点，可以从 Thingiverse [此处](https://www.thingiverse.com/thing:6358225) 下载。您还需要一些弹簧针来完成此工具。我从亚马逊订购了[这些](https://www.amazon.co.uk/dp/B08NT88C3G)（仅限 100 个！）注意：在将电线推入 3D 打印件之前，先将电线焊接到弹簧针上。如果在打印过程中焊接它们，PLA 会熔化，再次将针脚放回原位会很麻烦。

您需要将弹簧针推入足够的距离，这样您必须稍微弯曲工具才能将其放置在电路板上，当您松开工具时，针脚应该会落下并与焊盘良好接触（注意：这张照片是在我测试适合性时拍摄的 - 在这个阶段，您应该已经将电线焊接到弹簧针上了）

![pogo 工具](../images/sbs-pogo-tool.jpg)

好的，无论您是焊接还是使用工具，现在您都需要连接到 FTDI 适配器。首先，将 FTDI 适配器上的跳线设置为 3.3v，然后将相机上的 TX 焊盘连接到 FTDI 适配器上的 RX 引脚，将相机上的 RX 焊盘连接到 FTDI 适配器上的 TX 引脚，并将相机上的任何接地连接到 FDTI 适配器上的接地引脚。

如果你现在在想"等等，哪个接地引脚？"。让我向你展示我略加改进的相机引脚分布图，我称之为"有用引脚"

![有用的图钉](../images/sbs-camera-userful-pins-v2.jpg)

这里有一些引脚，它们在连接此相机时实际上与我们有些关联。我们已经连接了 TX/RX，因此如果您有一个来自 FPV 相机的旧 3 针连接器，那么它可能正好适合插入图中电路板左侧包含 GND 的插座。至少我就是这么做的。如果您没有这样的 JST 连接器 - 请买一些。如果可能的话，我希望尽可能避免在这些微小的组件周围焊接。

您还需要通过随附电缆中的以太网端口将摄像机连接到有线网络（因此需要订购 12V Lan 电缆），并且您还需要在这里为其供电 - 使用 12v 圆柱形插头。

连接好所有东西后，它看起来应该像这样。但在我们继续插入 12v 电源之前，需要有软件来打开串行控制台，这样我们才能进入引导加载程序并实际运行一些命令。

![准备闪光相机](../images/sbs-flash-camera.jpg)

在 Mac（或 Linux）上，这非常简单，因为我们内置了所有命令。我将在分步文档中介绍 MacOS 和 Windows。Linux 应该与 MacOS 非常相似，但如果您以 Linux 作为主要操作系统，那么我希望您已经知道这些命令！

### 步骤 1.2 设置串行终端仿真

**MacOS**

首先，插入 FTDI 适配器（但暂时保持相机电源关闭），这样系统才能识别 FDTI 适配器。首先，我们需要打开一个终端。您可以在"实用程序"子目录中的"应用程序"文件夹中找到它。打开此终端后，我们需要找出 FTDI 适配器是哪个设备。为此，请键入

```
$ ls -l /dev/tty.usbserial*
crw-rw-rw-  1 root  wheel    9,  10 20 Dec 10:31 /dev/tty.usbserial-A50285BI
```

如您所见，我的设备名为 /dev/tty.usbserial-A50285BI。但您的设备可能不同，我们需要此设备的名称，以便在下一个命令中使用它，该命令实际上会在该设备上打开一个串行模拟器，并允许我们与相机通信。要做到这一点，我们可以使用 screen 命令。在命令行中，输入

`$ 屏幕 /dev/tty.usbserial-A50285BI 115200`

115200 是我们使用的波特率。好的，我们现在应该有一个空白屏幕，顶部有一个光标，等待出现某些内容。您可以跳到第 1.3 节

**视窗**

Windows 需要安装一些额外的软件，因为它的基本操作系统中没有可以完成工作的东西。对于串行终端仿真以及 ssh 和 scp（我们稍后会使用后两者），我建议使用 Putty，您可以在此处下载（https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html）下载、安装并运行后 - 插入 FTDI 适配器，但暂时保持相机电源关闭。首先，我们需要找到 Windows 为 FTSI 适配器分配了哪个 COM 端口，要检查这一点，您可以进入设备管理器来查找（只需在搜索栏中输入设备管理器）

![设备管理器](../images/sbs-com-port.jpg)

在我的例子中，您可以看到 FTDI 适配器配置为 COM6。返回 Putty 屏幕，将连接类型更改为串行，将您看到的 FTDI 适配器的 COM 端口放入串行线路文本框中，并将速度设置为 115200。保存它很有用，这样以后只需双击即可打开此终端。因此，如果您在已保存的会话文本框中键入 Serial OpenIPC（或任何您想要调用的名称），然后单击保存。

![Putty 串行连接](../images/sbs-putty-serial.jpg)

现在，如果我们双击已保存的会话，它将打开一个新屏幕，准备与摄像头对话。

### 步骤 1.3 设置 tftp 服务器

tftp 代表"简单文件传输协议"。您可能过去使用过功能更全面的 ftp 协议。tftp 过去传统上用于"网络启动"远程工作站。在启动时，所有这些客户端都知道如何操作，即向特定 IP 地址上的服务器请求启动文件，然后它会下载并运行该文件。不需要密码或任何其他命令，只需"给我这个文件"，因此很简单。人们在系统上运行任何类型的服务器时，通常会担心它会危及系统的安全性吗？答案是否定的，它不应该。您只需要在本地网络上接收连接，此外 - 该协议非常古老且易于理解。任何可能存在的安全漏洞都已在几年前发现并修补。

**MacOS**

MacOS 有自己的 tftp 服务器可供运行，但默认情况下不启动。要启动它，请输入

`$ sudo launchctl load -F /System/Library/LaunchDaemons/tftp.plist `

sudo 命令表示以 root 身份运行，因此系统会提示您输入 root 密码，以便命令成功运行。MacOS 将使用 /private/tftpboot 目录来提供文件，因此我们稍后会将启动镜像放在此目录中。

**视窗**

正如您所料，Windows 需要安装更多软件才能运行 tftp 服务器。有很多选择，我使用了 Solarwinds 公司的产品，您可以从 [此处](https://www.solarwinds.com/free-tools/free-tftp-server?) 获取。虽然它是免费下载的，但该公司要求提供注册信息。当然，您选择在注册信息中输入什么完全取决于您 - 因为它与实际获取下载链接以获取软件无关。获得软件并安装和运行它后，您应该会看到这样的窗口。

![Windows 上的 tftp](../images/sbs-tftp-win.jpg)

服务器将使用 C:\TFTP-Root 目录来提供文件，因此我们稍后会将启动镜像放在此目录中。

### 步骤 1.4 获取正确的固件镜像

此特定相机的 SOC 是 SigmaStar SSC338Q。显然，识别相机上的具体 SOC 非常重要，因为固件特定于每个 SOC。幸运的是，就我的相机而言（如果您查看上面的引脚分布图），您可以看到它写在电路板上。要找到相机的固件，请从主 OpenIPC [网页](https://openipc.org/) 转到预编译二进制文件链接，它将带您进入此 [页面](https://openipc.org/supported-hardware/featured) 从这里，我们可以在特色页面上看到 SigmaStar SSC338Q，但根据您拥有的 SOC 型号，您可以在页面顶部的链接中选择合适的制造商。无论您拥有哪种，下一步都是单击生成安装指南。在这种情况下，它会带我们进入[此]（https://openipc.org/cameras/vendors/sigmastar/socs/ssc338q）页面

该图显示的是**之后**我更改了所需的特定固件版本的选项的页面。

![固件生成](../images/sbs-firmwae-gen.jpg)

关于这些更改的一些说明。当您第一次进入此页面时，MAC 地址字段将为空白 - 因此请单击生成有效的 MAC 地址来填充此字段。对于相机 IP 地址，我们需要为其提供一个未使用的地址，该地址位于我们 PC 正在运行的同一子网上。在大多数情况下，您的家庭网络将位于 192.168.0.x 或 192.168.1/x 网络上。如果您不确定您的子网是什么，那么我们还需要找到我们 PC 的 tftpserver 地址，所以这是一种查找方法。

在 MacOS 上我可以简单地使用

```
$ ifconfig en0
en0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
	options=50b<RXCSUM,TXCSUM,VLAN_HWTAGGING,AV,CHANNEL_IO>
	ether 3c:cd:36:5b:d4:80 
	inet6 fe80::c78:ab18:b66d:b615%en0 prefixlen 64 secured scopeid 0x4 
	inet 192.168.0.10 netmask 0xffffff00 broadcast 192.168.0.255
	nd6 options=201<PERFORMNUD,DAD>
	media: autoselect (1000baseT <full-duplex,flow-control,energy-efficient-ethernet>)
	status: active
```

在 Windows 上，你可以在命令提示符中使用类似的命令（在搜索栏中输入 cmd）

```
C:\>ipconfig

Windows IP Configuration


Ethernet adapter Ethernet0:

   Connection-specific DNS Suffix  . : localdomain
   Link-local IPv6 Address . . . . . : fe80::e34e:48bb:9e79:90b2%12
   IPv4 Address. . . . . . . . . . . : 192.168.0.10
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 192.168.0.1

Ethernet adapter Bluetooth Network Connection:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :
```

从这个命令中，我们可以看到我的电脑的 IP 地址是 192.168.0.10，我的子网是 192.168.0。所以我可以填写我的 TFTP 服务器 IP 地址字段，对于相机 IP 地址字段，我只需选择一个尚未使用的地址。我使用了 192.168.0.123，因为它未被使用。如果你不确定，你可以尝试 ping 这个地址来检查。

```
$ ping 192.168.0.123
PING 192.168.0.123 (192.168.0.123): 56 data bytes
Request timeout for icmp_seq 0
Request timeout for icmp_seq 1
Request timeout for icmp_seq 2
```

这些超时通常表明该地址没有主机，并且可以安全使用。

对于其余选项，此相机具有 16MB 的闪存（我们稍后可以看到如何验证这一点），我已将固件版本更改为 FPV，并且网络接口和 SD 卡插槽是默认值。现在，让我们单击"生成安装指南"。它将向您显示这样的指南。

![安装指南](../images/sbs-install-guide.jpg)

本安装指南的理念是，一旦打开相机上的控制台，您就可以简单地将命令剪切并粘贴到控制台窗口中以执行备份和固件烧录。这只有一个问题，那就是指南中有几个错误。公平地说，自从我烧录后，它已经有所改进，但仍有几个错误需要解决。

首先，保存原始固件的整个部分将不起作用。这是因为在相机有限的操作系统上既没有 tftpput 命令也没有 tftp 命令，所以我们无法将相机的备份从相机本身移走。这意味着我们必须忽略整个部分。我听说开发人员在某个地方有这个备份 - 以防你需要恢复它。如果你真的想创建备份，有一个[工作方法](help-uboot.md#saving-original-firmware-without-using-tftp)，但传输数据需要几个小时。

幸运的是，实际的烧录应该可以正常工作，但在开始之前，我们需要单击"下载 OpenIPC 固件 (Fpc) 镜像"链接。在本例中，我们将下载 openipc-ssc338q-fpv-16mb.bin 文件。根据 tftp 服务器指南，在 MacOS 上，您需要将此文件放在 /private/tftpboot 中，在 Windows 上，将其放在 C:\TFTP-Root 目录中。在 MacOS 上，当您尝试以自己的用户身份复制此文件时，操作系统会拒绝您的权限，因此您很可能需要再次使用 sudo 命令。

`$ sudo cp $HOME/Downloads/openipc-ssc338q-fpv-16mb.bin /private/tftpboot/`

Windows 默认使用其 Microsoft Defender 防火墙阻止所有传入连接，因此我们需要暂时禁用它。如果您在搜索栏中输入 Windows 安全并运行 Windows 安全应用，然后选择防火墙和网络保护。下一部分取决于您如何配置 Windows。如果您已将本地网络配置为私有网络，则可以单击"私有网络"并禁用 Microsoft Defender 防火墙。我将自己的 Windows 10（虚拟机）保留为默认设置，因此它没有配置私有网络并将所有内容视为公共网络，因此在我的情况下，我单击"公共网络"并启用防火墙。

![Windows 防火墙](../images/sbs-Win-Firewall.jpg)

### 步骤 1.5 打开控制台并进行闪存！

现在终于该给这个相机刷机了。所以，如果您的 FTDI 适配器仍连接到 PC 和相机，并且串行控制台已打开，那么您就可以开始了（如果没有，请返回 1.2 并打开串行终端）。您需要做的就是将 12v 电源插入相机，然后您应该会看到串行控制台上出现许多启动消息。这里的想法是，一旦出现启动消息，您就多次按下回车键。如果您错过了它并且相机继续启动，您最终会看到这样的登录提示。

![登录提示](../images/sbs-bootloader-missed.jpg)

虽然这确实意味着你按回车键的速度太慢，但这确实表明控制台正在工作，并且摄像头正在正常启动。不用担心，只需重新启动摄像头并重试即可。如果你这次速度足够快，你应该会看到类似这样的画面。

![引导加载程序提示](../images/sbs-bootloader-int.jpg)

好的，现在我们开始烹饪！但是等一下，如果您在控制台上什么都没得到，而您看到的是一个完全空白的屏幕怎么办？好吧，这里最容易出错的地方是 FTDI 板上的 TX/RX 连接交叉。只要相机似乎启动了（有一个或两个 LED，我的那个在启动时甚至发出了一点噪音），然后尝试在 FTDI 板上交换 TX/RX 导线，看看是否能解决问题。对于我们其他人来说，是时候开始输入安装指南中的命令了。

这些是指南中告诉我们一次运行的行。我会告诉你可能得到的响应以及哪些部分不应运行。

```
# Enter commands line by line! Do not copy and paste multiple lines at once!
setenv ipaddr 192.168.0.123; setenv serverip 192.168.0.10
mw.b 0x21000000 0xff 0x1000000
tftpboot 0x21000000 openipc-ssc338q-fpv-16mb.bin
# if there is no tftpboot but tftp then run this instead
tftp 0x21000000 openipc-ssc338q-fpv-16mb.bin
sf probe 0; sf lock 0;
sf erase 0x0 0x1000000; sf write 0x21000000 0x0 0x1000000
reset
```

让我们运行前几行。在我的相机上，我知道 tftpboot 命令确实存在，所以我们可以使用 tftp 忽略下一个命令

```
Anjoy # setenv ipaddr 192.168.0.123; setenv serverip 192.168.0.10
Anjoy # mw.b 0x21000000 0xff 0x1000000
Anjoy # tftpboot 0x21000000 openipc-ssc338q-fpv-16mb.bin
Using sstar_emac device
TFTP from server 192.168.0.10; our IP address is 192.168.0.123
Filename 'openipc-ssc338q-fpv-16mb.bin'.
Load adress: 0x21000000
Loading: #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #################################################################
         #######################################
         2.3 MiB/s
         
done
Bytes transferred = 16777216 (1000000 hex)
```

运行 tftpboot 命令后，您应该会看到文件从服务器中拉出时出现一行行 #。但如果它不起作用怎么办？在 Mac 上，最常见的问题是文件权限 - 文件是否具有全局读取权限，以便可以使用 tftp 守护程序读取。我们可以通过运行来设置文件的打开权限

`$ sudo chmod 777 /private/tftpboot/openipc-ssc338q-fpv-16mb.bin`

在 Windows 上，SolarWinds TFTP 服务器将记录连接和任何尝试在其窗口中获取文件的尝试，因此您可以检查它以帮助确定问题。如果其中没有任何内容，则请求不会到达服务器。你关闭了那个讨厌的防火墙吗？（如果此图像中的 IP 地址看起来很奇怪，那是因为我在虚拟机中运行 Windows，它会创建自己的网络连接来桥接主机）

![tftp 调试消息](../images/sbs-tftp-log.jpg)

根据说明，下一个命令是运行

`sf 探测 0；sf 锁 0；`

这不会起作用，因为命令的"锁定"部分尚未实现。因此，我们在这里需要运行的只是命令的第一部分。

```
Anjoy # sf probe 0
Flash is detected (0x0B05, 0xC8, 0x40, 0x18)
SF: Detected nor0 with total size 16Mib
```

如果您不确定相机上的闪光灯有多大，那么运行 sfprobe0 是检查它的好方法。如果它与您在"创建安装指南"页面中输入的内容有任何不同，那么您可以简单地返回该页面，输入正确的信息以生成新的说明和新的安装指南。重新启动相机并重新开始。

```
Anjoy # sf erase 0x0 0x1000000; sf write 0x21000000 0x0 0x1000000
_spi_flash_erase: addr 0x0, len 0x10000000 100%(cost 25076 ms)
SF: 16777216 bytes @ 0x0 Erased: OK
_spi_flash_write to 0x0, len 0x1000000 from 0x21000000 100%(cost 14084 ms)
SF: 16777216 bytes @ 0x0 Written: OK
```

此操作需要几分钟，但这是真正令人兴奋的闪烁部分。如果一切顺利，您现在需要做的就是通过输入最后一条命令重新启动主板。

`享受#重置`

此时，相机将重新启动几次。您将在控制台上看到很多消息，直到全部停止，其中可能包含以下消息

"未检测到 USB wifi 卡。请检查 wifi 棒连接、USB 电源或可能的焊接不良。"

这是意料之中的。我们在这里没有收到登录提示，因为 OpenIPC 安装的一部分关闭了通过此串行连接登录的功能（尽管重新打开它很简单）。但是，现在更好的登录方式是通过 SSH，因为我们应该已经向 DHCP 服务器（通常是路由器）询问了 IP 地址。如果您在控制台上查找以 uhdcpc 开头的行，您应该能够看到摄像机分配了哪个 IP 地址。就我而言，我可以看到

```
udhcpc: started, v1.36.1
udhcpc: broadcasting discover
udhcpc: broadcasting select for 192.168.0.50, server 192.168.0.1
udhcpc: lease of 192.168.0.50 obtained from 192.168.0.1, lease time 86400
```

这告诉我们我们的 IP 地址是 192.168.0.50，所以现在让我们通过网络连接到它。在 MacOS 上使用

`$ ssh root@192.168.0.50`

在 Windows 上，使用 Putty。创建一个已保存的会话是一个有用的想法，这样您只需双击它即可打开 ssh 窗口 - 如下图所示。

![Putty SSH](../images/sbs-putty-ssh.jpg)

当你通过 ssh 连接到相机时，系统会提示你输入 root 密码，密码是 12345。输入密码后，一切正常，你应该看到这样的屏幕

![ssh 登录](../images/sbs-ssh-openipc.jpg)

您首先需要做的是更改 root 密码。您可以在命令中执行此操作，例如输入命令 passwd。这将提示您输入当前 root 密码，然后要求您输入新密码并确认。或者，您可以使用 Web 界面。为此，请转到 PC 上的浏览​​器并输入摄像机的 IP 地址，因此对于我的摄像机，地址为 192.168.0.50:85。系统将提示您输入用户名（root）和密码（如果您尚未更改密码，则输入 12345）

如果你还没有更改密码，那么它会要求你这样做 - 只需在"保存更改"中输入你的新密码即可

![网络密码更改](../images/sbs-web-pass.jpg)

更改密码后（或者如果已经通过命令行更改密码，则 Web 界面将会更改。您会注意到更改 MAC 地址的选项 - 您可以继续执行此操作，但会看到有关您的 IP 地址可能更改的警告。再次，您可以在控制台启动消息中查找此信息。此时，OpenIPC 软件到相机的烧录已完成 - 做得好。我们需要返回相机进行更多配置更改，我们可以通过 ssh 会话进行更改，但现在，您可以拔下相机并断开 FTDI 适配器，因为我们现在必须对地面站重复此过程。

### 步骤 1.6 烧录地面站

现在您已经完成了烧录相机的操作，相比之下，您应该会发现 Nvr 板要简单得多。首先，我们已经运行了 tftp 服务器，并且现在熟悉使用串行仿真软件，因此我们真正需要做的就是重复获取控制台登录的过程，以便通过 tftp 传输新固件并安装它！

在 Nvr 板上，控制台的连接更加容易操作，甚至还为我们贴上了标签。

![Nvr UART 连接](../images/sbs-nvr-uart.jpg)

这些连接比相机上的焊盘要坚固得多，所以我觉得不需要任何使用弹簧针的工具。它们仍然很小，很容易将焊盘短接在一起 - 但很容易焊接几根电线。这正是我们需要做的。从这些连接焊接 GND/TX/RX，以便将它们连接到您的 FTDI 板上 - 再次记住交叉电线，以便板上的 TX 转到 FTDI 上的 RX，反之亦然。

此时，我们即将准备就绪。您需要插入 FTDI 适配器，连接我们之前使用的以太网电缆，并准备连接 12v 电源。如果您愿意，您还可以将 HDMI 连接器插入 Nvr 板并将其连接到显示器。您无法通过 HDMI 连接看到控制台消息，但它会在屏幕上显示一些内容。准备就绪后，您需要打开我们用于烧录相机的相同串行终端仿真器（MacOS 上的屏幕命令，Windows 上的 Putty）

![Nvr 闪烁](../images/sbs-nvr-flash.jpg)

插入电源后，您需要开始在控制台屏幕上按 CTRL-C（而不是像我们在相机上那样按 RETURN - 我第一次尝试时就被抓住了）。如果您太晚了，您将在控制台上看到的最后一条消息是"正在启动内核"，但如果您正确捕捉到它，您应该会看到类似

![NVR 引导加载程序](../images/sbs-nvr-bootloader.jpg)

Nvr 闪存记录在单独的文档中 [此处](https://github.com/OpenIPC/wiki/blob/master/en/fpv-nvr.md) 虽然它没有详细介绍，但所有命令都有效，并且有一个图像可供下载，因为 Nvr 板是众所周知的具有单一配置的硬件。下载固件文件 [此处](https://openipc.org/cameras/vendors/hisilicon/socs/hi3536dv100/download_full_image?flash_size=16&flash_type=nor&fw_release=fpv) 或从我之前链接到的页面获取它并将其放在您的 tftpserver 目录中（MacOS 上的 /private/tftpboot 和 Windows 上的 C:\TFTP-Root）

OpenIPC网站上的说明如下。

```
# Сhanging the ip address of the NVR board and the ip address of your TFTP server
setenv ipaddr 192.168.1.10; setenv serverip 192.168.1.254
mw.b 0x82000000 0xff 0x1000000
tftp 0x82000000 openipc-hi3536dv100-fpv-16mb.bin
sf probe 0; sf lock 0;
sf erase 0x0 0x1000000; sf write 0x82000000 0x0 0x1000000
reset
```

它提到更改 Nvr 板的 IP 地址和 tftp 服务器的 IP 地址，因此我将使用与之前用于相机的相同地址。显然，用您自己的地址替换这些地址。接下来我将向您展示的是输入的命令，以及运行所有这些命令后应得到的响应。

```
hisilicon # setenv ipaddr 192.168.0.123; setenv serverip 192.168.0.10
hisilicon # mw.b 0x82000000 0xff 0x1000000
hisilicon # tftp 0x82000000 openipc-hi3536dv100-fpv-16mb.bin
Hisilicon ETH net controller
MAC:   00-0B-3F-00-00-01
eth0 : phy status change : LINK=DOWN : DUPLEX=FULL : SPEED=100M
eth0 : phy status change : LINK=UP : DUPLEX = FULL : SPEED=100M
TFTP from server 192.168.0.10; our IP address is 192.168.0.123
Download Filename 'openipc-hi3536dv100-fpv-16mb.bin'.
Download to address: 0x82000000
Downloading: #################################################
done
Bytes transferred = 16777216 (100000 hex)
hisilicon # sf probe 0; sf lock 0;
16384 KiB hi_fmc at 0:0 is now current device
unlock all block
at XmSpiNor_disableWps() <Enter>.
@XmSpiNor_printWps(), WPS Not Enabled!
Current level[0], lock_level_max:7.
unlock all.
hisilicon # sf erase 0x0 0x1000000; sf write 0x82000000 0x0 0x1000000
Erasing at 0x10000 --   0% complete.stMaxRect.u32Width:1024, stMaxRect.u32Height:768.
DVR_HDMI_ProdCrgAllResetSet udelay(20000).
HDMI_INFO:DispFmt2HdmiTiming[419] ,Non CEA video timing:17
HDMI_INFO:Hdmi_PixelFreqSearch[163] ,u32Fmt17.
Erasing at 0x1000000 -- 100% complete.
Writing at 0x1000000 -- 100% complete.
hisilicon # reset
```

重置命令后，Nvr 板将重新启动并向您提供登录提示。您应该能够使用用户 root 和密码 12345 再次登录。还请记下控制台消息中的 udhcpc 行，因为您将能够看到 Nvr 板现在的 IP 地址。在下图中，您可以看到它是 192.168.0.51

![Nvr 控制台登录](../images/sbs-nvr-login.jpg)

此时您应该做的是更改 root 密码。输入"passwd"命令，系统将提示您输入当前密码、新密码，然后确认新密码。如果您通过 HDMI 连接连接到显示器，您还应该看到令人兴奋的 OpenIPC OSD 显示

![空白OSD](../images/sbs-blank-osd.jpg)

好的，Nvr 已烧录！！此时，您不再需要 tftp 服务器，因此 Windows 用户可以重新打开防火墙。


### 第 2 步 连接其他硬件（wifi 适配器和 BEC）

我们的相机和地面站需要 wifi 连接才能相互通信，因此在此步骤中，我们将连接小型 wifi 模块。这些模块的重点是它们由 3.3v 供电，而不是 5v。这意味着我们不能简单地通过 Nvr 上的 USB 连接器为 wifi 模块供电，尽管相机上有一个 3.3v 引脚，但它显然不提供电源，所以我们必须使用 BEC 来做到这一点

### 步骤 2.1 配置 BEC

如果您已经购买了适用于 12v 和 3.3v 的特定 BEC，那么这里无需执行任何操作 - 除了添加适当的电线/连接器，以便您可以添加电源并连接到 wifi 板。如果您继续购买我在硬件要求中列出的 BEC，那么在使用它们之前需要进行一些配置。

您需要将地线和电源线焊接到输入和输出端子上。将电池连接到输入端，将万用表连接到输出端。BEC 上有一个小螺丝，可以旋转以将 BEC 配置为特定电压。它非常灵敏，但只要有一点耐心，您就可以获得相当准确的电压。我让它们运行了 20 分钟，然后第二天尝试通电以查看电压是否有任何差异，但它们确实保持了相当准确的值。

![配置 becs](../images/sbs-bec-config.jpg)

我们总共需要 4 个 BEC，摄像机和地面站都需要一个 12v 的 BEC 和另一个 3.3v 的 BEC - 因此最好在此阶段配置所有这些 BEC。

### 步骤 2.2 连接 Wifi 模块

如果我们再看一下我们的 wifi 板图片，这次有一些引脚排列被标记了。 ![wifi 模块引脚排列](../images/sbs-wifi-pinout.jpg) 您将能够看到接线相当简单，焊接也相当容易，我们将 3.3v 和 GND 从 BEC 连接起来。为了将其连接到相机，如果您查看第 1.1 节中的相机引脚排列（特别是我称之为"有用引脚"的引脚排列），您会在板的底部看到我们有一个 USB D+、USB D- 和一个 GND。这些是我们在 wifi 板上剩余的引脚，因此您需要一个 jst 连接来插入相机，然后将相机上的 USB D+ 连接到 wifi 板上的 USB D+，将相机上的 USB D- 连接到 wifi 板上的 USB D-，并将 2 个接地连接在一起。

下图显示了我已完成的空中侧与 wifi 适配器的连接（尽管这是为了在工作台上进行测试，但我使用了与 XT60 适配器的临时连接）您将看到我的 3.3v BEC 连接到 wifi 适配器，USB 接线连接到 JST 连接器，随时可以插入摄像头板。另请注意，打开电源时必须将天线连接到 wifi 板。否则可能会损坏 wifi 板。我刚刚找到了几个带有 UFL 连接器的 5.8Ghz 天线。在这张图片中，我还将 12V BEC 添加到电源，该电源连接到另一个已连接的 jst，随时可以为摄像头供电。

![空中 wifi 板](../images/sbs-air-wifi.JPG)

在接地方面，接线方面的情况非常相似 - 但因为 Nvr 板有实际的 USB 连接器，所以我使用了我身边的旧 USB 电缆的一部分（在我似乎积累的 100 多条 USB 电缆中），然后简单地剪掉末端进行接线。这种电缆还有一个优点，那就是屏蔽效果很好。如果您不知道 USB 引脚排列，这里有一张图片可以帮助您。通常，您会发现电缆的颜色很合理，所以红色是 5v，黑色是接地，所以只需使用万用表来确定 D+ 和 D- 的颜色即可

![USB 引脚排列](../images/sbs-USB-pinout.jpg)

如上所述，wifi 板使用 3.3v 电源，因此我们需要确保剪断 5v 线，只需连接 wifi 板和 USB 连接器之间的接地/D+/D-，并通过 3.3v BEC 单独为 wifi 板供电。如下图所示。与前面的示例不同，我没有连接 12V BEC 来为 Nvr 板供电。因为我是在工作台上进行的，所以我继续使用 12V 电源适配器，尽管很明显，当我进行"真实"测试时，情况会发生变化。

![地面 Wifi](../images/sbs-ground-wifi.JPG)

### 步骤 2.3 检查 WiFi 模块是否被识别

不言而喻，在第一次开机之前，请检查连接，再检查一次，确保为正确的设备提供正确的电源，并确保 wifi 板上有天线。我还建议使用小型台扇对着 wifi 板吹，因为如果没有气流，这些板很容易变得相当热。

好的，此时您可以打开电源。现在，如果您将串行连接保留在摄像头或 Nvr 板上，您可能会注意到有关电路板的消息飞过。但检查一切是否正常工作的另一种简单方法是 ssh 进入并运行一些命令（显然您需要为此连接物理以太网电缆）。 ssh 进入后，输入以下命令。

`root@openipc# wifibroadcast stop`

根据您的卡是否被识别将显示一些不同的消息，因此请忽略这些消息并关注下一个命令的输出。

`root@openipc#wifibroadcast启动`

如果你得到类似如下的输出

```
Loading modules and wifi card driver...
Detecting wifi card vendor...
Detected: realtek
Awaiting interface wlan0 in system...
Preparing interface wlan...
drone key exist...
Starting Wifibroadcast service...
Done.
Using data frames
Listen on 5600 for wlan0
Loading MAVLink telemetry server...
Done.
Using data frames
Listen on 14550 for wlan0
```

然后电路板被检测到并且正在工作（这是来自空中侧的输出，尽管地面侧非常相似）。无论哪种情况，如果电路板未被检测到，错误都是相同的，因此如果您有类似的输出。

```
Loading modules and wifi card driver...
Detecting wifi card vendor...
No usb wifi card detected.  Check wifi stick connection, usb power or possible bad soldering.
```

那么肯定是哪里出了问题。通常的罪魁祸首是 D-/D+ 交叉，因此如果您确信 wifi 模块有电，那么首先要尝试切换电线。

### 步骤 3 生成并安装 WFB-NG 的密钥对

什么是 WFB-NG？好吧，WFB 是 wifibroadcast...您可能还记得上一步中的命令。这是真正启动 OpenSoure HD FPV 的项目。WFB-NG 是 Wifibroadcast Next Generation - wifibroadcast 的全新改进版本，然后 OpenIPC 用于 FPV。WFB-NG 在地面/空中之间发送/接收数据时也使用加密，因此我们有必要生成一些密钥以供使用，然后将它们移动到正确的位置以使 WFB-NG 工作。

地面站和摄像头烧录后将自动安装默认密钥，因此视频链接无需生成新密钥对即可工作。但是默认密钥并不安全（可以将其用于台架测试或如果您不关心加密）。如果您想设置单独的真实加密，请按照下一个说明进行操作。

通过 ssh 登录到您的地面站并运行命令

`root@openipc# wfb_keygen`

这应该给你输出

```
Drone keypair (drone sec + gs pub) saved to drone.key
GS keypair (gs sec + drone pub) saved to gs.key
```

我们需要用这些密钥将 drone.key 移动到相机的 /etc 目录，将 gs.key 移动到地面站的 /etc 目录。由于我们已经在地面站上，因此只需输入命令即可

`root@openipc# cp gs.key /etc`

（除非您输入错误，否则此命令不会有任何响应）问题是我们如何将 drone.key 文件传输到相机。这就是 scp 命令的作用所在。如果我们让地面站保持开机状态，然后返回 PC 输入命令

使用 scp root@192.168.0.51:/root/drone.key 命令执行此操作。

我在这里使用 192.168.0.51 作为示例，请不要忘记将此 IP 地址替换为地面站的 IP 地址。scp 是 MacOS 用户的内置命令。在 Windows 上，安装 Putty 也会安装 scp 命令，因此这只是打开 CMD 窗口并输入命令的情况。它将提示您输入地面站 root 用户的 root 密码。在这两种情况下，命令末尾的点 (.) 表示 drone.key 将复制到您当前的目录 - 以防您想知道它存储在哪里。保持该窗口打开，因为我们要将其从 PC 复制回相机。但要做到这一点，您需要打开相机并将其连接到以太网电缆（此部分无需为地面站通电）

相机通电后（您可以通过 ssh 连接进行检查），回到您的电脑上使用命令

`scp ./drone.key root@192.168.0.50:/etc`

再次强调，此处使用的 IP 地址只是示例 - 系统将提示您输入相机 root 用户的密码。要仔细检查文件是否在正确的位置，您可以 ssh 到相机和地面站并运行

`root@openipc# ls -l /etc`

并在相机上查找 drone.key，在地面站上查找 gs.key

### 步骤 4 编辑 wfb.conf 以设置正确的 wifi 频道

除了正确设置密钥之外，wifibroadcast 配置还有更多内容。在相机和地面站上都有一个配置文件需要编辑。为此，需要使用名为 vi 的文本编辑器。对于 Windows 用户来说，这可能有点新奇和陌生，因为它不是典型的编辑器，并且有几个命令供您删除字符、插入、附加内容和保存文件。[这里](https://www.guru99.com/the-vi-editor.html) 有一个基本的 vi 教程，希望可以帮助新用户。幸运的是，我们不需要在这个文件中进行大量编辑。

```
You need this vi commands
press i on keyboard to enter edit mode
press esc on keyboard to exit edit mode after edited parameters
press shift+zz to save file and exit vi
```

您将使用以下命令在相机和地面站上打开文件进行编辑

`root@openipc# vi /etc/wfb.conf`

此时，我不得不说，关于这个文件，有些事情我不知道，但我想了解一下 - 所以我认为这是一个正在进行的工作。这个文件中其他看起来有趣的东西与传输功率有关，比如 txpower 和 driver_txpower_override。我想要（并想在这里介绍）的是解释每行的用途以及我们可能想要更改的相关内容。我们肯定要更改的是频道号，您会在文件中看到它被列为其默认值

`频道=14`

频道是指我们将使用的 wifi 频道。频道 14 位于 2.4Ghz 频谱中。虽然我在本例中用作示例的 wifi 模块确实支持 2.4Ghz 和 5.8Ghz，但我想使用 5.8 频道，因为我有支持这个的天线。我选择的频道是频道 161，相当于频率 5805Mhz - 应该可以很好地调整为您可能闲置的 5.8 天线。因此，在地面站和相机的情况下，我将这一行更改为

`频道=161`

#### 配置 TX 功率

在同一个 wfb.conf 文件中，您将看到 2 个带有 txpower 设置的参数。此参数的可用范围是 20-58。在工作台上测试时，请注意不要设置最大功率，因为​​这可能会烧坏您的 wifi 卡！使用风扇进行测试。

txpower - 用于 atheros wifi 卡

tx_power_owerride - 用于 8812au 卡

### 步骤 5 在地面站上配置 vdec.conf

此时，如果您要测试视频流，您将在地面站上看到收到的 RX 数据包数量以及数据速率。您看不到的是来自相机的实际图片。原因是我们必须编辑更多文件才能按我们喜欢的方式设置相机并告诉地面站会发生什么。我们在地面站上使用 vdec.conf 文件执行第一个操作。

这是一个相当小的文件，其中大多数选项的含义非常明显。虽然这里有一些关于 osd 的选项，但我想了解如何设计我的自定义 osd。对于我们在这里使用的相机，只需进行一项更改。默认情况下，您会看到以下行

`编解码器=h264`

将其更改为

`编解码器=h265`

您还将看到一些视频模式，以期望传入的流处于其中。我们将在这里坚持

`模式=720p60`

好的，这个文件完成了。继续下一个。


### 步骤 6 在相机上配置 majestic.yaml 文件

Majestic Streamer 是 OpenIPC 使用的视频流应用程序。如果您通过 ssh 连接到摄像头并查看 /etc/majestic.full 文件，您将找到一长串的配置选项。虽然其中很多选项都有意义，但还有很多选项需要更详尽的文档，也许还需要一个列表来让我们知道哪些选项与 FPV 更相关。我们实际编辑的文件名为 majestic.yaml 因为它不是太大，所以我将向您展示我的文件版本以及所做的所有更改。这至少可以让我们将视频从摄像头传输到地面站。

```
system:                                                                                                                                                                                                       
  webAdmin: disabled                                                                                                                                                                                          
  buffer: 1024                                                                                                                                                                                                
image:                                                                                                                                                                                                        
  mirror: false                                                                                                                                                                                               
  flip: false                                                                                                                                                                                                 
  rotate: none                                                                                                                                                                                                
  contrast: 70                                                                                                                                                                                                
  hue: 50                                                                                                                                                                                                     
  saturation: 70                                                                                                                                                                                              
  luminance: 50                                                                                                                                                                                               
osd:                                                                                                                                                                                                          
  enabled: false                                                                                                                                                                                              
  template: "%a %e %B %Y %H:%M:%S %Z"                                                                                                                                                                         
nightMode:                                                                                                                                                                                                    
  enabled: false                                                                                                                                                                                              
records:                                                                                                                                                                                                      
  enabled: false                                                                                                                                                                                              
  path: /mnt/mmcblk0p1/%F/%H.mp4                                                                                                                                                                              
  maxUsage: 95                                                                                                                                                                                                
video0:                                                                                                                                                                                                       
  enabled: true                                                                                                                                                                                               
  bitrate: 12288                                                                                                                                                                                              
  codec: h265                                                                                                                                                                                                 
  rcMode: cbr                                                                                                                                                                                                 
  gopSize: 1.5                                                                                                                                                                                                
  size: 1280x720                                                                                                                                                                                              
  fps: 60                                                                                                                                                                                                     
video1:                                                                                                                                                                                                       
  enabled: false                                                                                                                                                                                              
jpeg:                                                                                                                                                                                                         
  enabled: false                                                                                                                                                                                              
mjpeg:                                                                                                                                                                                                        
  size: 640x360                                                                                                                                                                                               
  fps: 5                                                                                                                                                                                                      
  bitrate: 1024                                                                                                                                                                                               
audio:                                                                                                                                                                                                        
  enabled: false                                                                                                                                                                                              
  volume: auto                                                                                                                                                                                                
  srate: 8000                                                                                                                                                                                                 
rtsp:                                                                                                                                                                                                         
  enabled: false                                                                                                                                                                                              
  port: 554                                                                                                                                                                                                   
hls:                                                                                                                                                                                                          
  enabled: false                                                                                                                                                                                              
youtube:                                                                                                                                                                                                      
  enabled: false                                                                                                                                                                                              
motionDetect:                                                                                                                                                                                                 
  enabled: false                                                                                                                                                                                              
  visualize: true                                                                                                                                                                                             
  debug: true                                                                                                                                                                                                 
ipeye:                                                                                                                                                                                                        
  enabled: false                                                                                                                                                                                              
watchdog:                                                                                                                                                                                                     
  enabled: true                                                                                                                                                                                               
  timeout: 10                                                                                                                                                                                                 
isp:                                                                                                                                                                                                          
  exposure: 60                                                                                                                                                                                                
  drc: 350                                                                                                                                                                                                    
  IPQPDelta: -8                                                                                                                                                                                               
outgoing:                                                                                                                                                                                                     
  enabled: true                                                                                                                                                                                               
  server: udp://127.0.0.1:5600                                                                                                                                                                                
```

如果您查看原始文件和此文件之间的差异，您会发现最重要的变化是在 video0 部分，我们在其中定义了来自摄像机的流设置 - 并与我们在上一节中告诉地面站的期望内容相联系。

### 步骤 7 测试配置

好的，此时我们已经完成了所有基本配置，因此我们应该能够从相机发送视频并让地面站在屏幕上显示它。要测试它，我们只需插入所有东西并打开电源即可。因此，我们需要将地面站连接到其 wifi 模块并通过 HDMI 连接到屏幕，并将相机连接到其 wifi 模块。应该就是这样。

如果您一直遵循与我相同的配置，那么您桌上的赔率现在看起来像这样 - 虽然我应该说没有必要将 FTDI 适配器连接到摄像头，或者将以太网电缆连接到 Nvr 板，这些纯粹是为了调试。

![接线](../images/sbs-wiring-mess.jpg)

您现在应该看到的是实时视频图像 - 希望其中没有我的脸，如下面的屏幕截图所示。

![连接完成](../images/sbs-connection-working.jpg)

好的，但如果没有呢？好吧，这张图片中的一些东西可能会让我们检查不同的东西。例如，如果您没有图片但看到 RX 数据包增加，那么很可能是 majestic.yaml 和/或 vdec.conf 文件的问题。如果数据包数量根本没有增加，那么请退后一步并检查步骤 3 和 4。如果检查无误，请继续按照此处提供的步骤向后移动 - 登录设备并检查是否检测到 wifi 模块。

### 第 8 步 视频教程和后续步骤

我在拍摄 YouTube 教程后编写了这份分步指南，您可以在 [此处](https://www.youtube.com/watch?v=libsusKy6zc&lc=Ugx2sDfGe3gd_vaeqXZ4AaABAg) 看到这个视频。这个视频中我犯了很多错误，所以其中也有一些解决问题的方法，但观看起来相当冗长。但对于某些人来说，同时使用这两个视频并查看本指南无法提供的额外视觉效果可能会很有用。

这是为您提供流媒体视频的基本设置。它尚未准备好用于 FPV - 我们需要连接遥测以便 OSD 填充，并考虑如何将大型摄像头塞入模型中。我将在以后解决这个问题。


### 通过 5 伏电源为相机供电

要通过 5 伏电源为您的相机供电，您应该按照下图所示焊接电线。

![5v 电源](../images/camera-5v.jpg)


### 利用第二个 UART 进行遥测

使用这些小焊盘进行遥测不太方便。以下是如何启用第二个 UART 端口的说明。

1. 编辑 /etc/init.d/S95majestic，插入如下字符串（如屏幕截图所示）

```
devmem 0x1F207890 16 0x8
stty -F /dev/ttyS2 115200 raw -echo -onlcr
```
![第一步](../images/uart2-1.png)

2. 编辑 /etc/telemetry.conf 将 ttyS0 更改为 ttyS2（见截图） ![第二步](../images/uart2-2.png)

确保在 FC 中将 mavlink 遥测设置为 115200 波特率

