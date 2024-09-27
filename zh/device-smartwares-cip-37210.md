# OpenIPC Wiki
[目录](../README.zh.md)

智能软件 CIP-37210 
--------------------

### 设备信息

| System | Description                          |
|--------|--------------------------------------|
| SoC    | HI3518EV200                          |
| Sensor | OmniVision OV9732                    |
| Flash  | 16Mb (Winbond 25Q128JVSQ)            |
| WiFi   | RTL8188FU                            |

### 一步步刷机指南

除了相机之外，您还需要以下工具：
- PH0 螺丝刀或钻头
- 小型螺丝刀，例如 0.6 × 3.5 毫米
- 用于 UART 通信的 USB 转 TTL 适配器。（我使用了基于 CP2102 的设备，但还有 [许多其他不错的选择](https://github.com/OpenIPC/wiki/blob/master/en/equipment-flashing.md))
- microSD 卡（我使用了旧的 2 GB 卡）
- 一些跳线
- 万用表
- 运行 GNU/Linux 的计算机

烧录 OpenIPC 相机的首选方法是通过 tftp，但 CIP-37210 没有以太网端口。另一个问题是，原装 u-boot 受密码保护，fatload（读取 FAT 文件系统的权限）不起作用。

因此，我们需要结合两种不同于标准程序的方法来烧录 Smartwares CIP-37210：[刻录实用程序](https://github.com/OpenIPC/burn) 直接启动到由 OpenIPC 项目编译的 u-boot 引导加载程序，以便能够从 microSD 卡进行烧录，当然还有 [从 microSD 卡进行烧录](https://paulphilippov.com/articles/flashing-ip-camera-with-full-openipc-binary-firmware-from-sd-card)。

#### 打开设备

使用 PH0 螺丝刀拧下相机支架背面的可见十字螺丝。![unscrew](/images/cip-37210_open_001.jpg "拧下十字螺丝")

使用一字螺丝刀撬开相机外壳（支架就安装在外壳上）：![pry_open](/images/cip-37210_open_002.jpg "撬开相机")

#### 建立 UART 连接

打开设备后，就该建立 UART 连接了。使用随附的微型 USB 电源为打开的相机供电。现在是时候检查 PCB 顶部可疑的 4 个针孔了：使用万用表测量针孔的电压，方法是将它们连接到 GND（我使用了中间螺丝周围的一个垫片）。

我发现了两个 3.3 V 的针孔、一个略低于 3.3 V 的针孔和一个 0 V 的针孔。现在是时候在启动期间观察 3.3 V 引脚了——具有振荡电压的是 TX 引脚，而稳定的 3.3 V 引脚是 Vcc。

**总结：**靠近黑色螺丝的针孔是 RX，旁边的是 TX，再旁边的是 GND。将 GND 连接到 GND，将 TX 连接到 RX，将 RX 连接到 TX。

![uart_cip-37210](/images/uart_cip-37210_cropped.jpg "CIP-37210 PCB 上标记的 UART 针孔")

我使用简单的公对母杜邦跳线连接到针孔。当然还有更好的解决方案，比如将连接器焊接到针孔上或使用测试钩，但只要跳线不接触，它就可以正常工作。

![uart_cip-37210_action](/images/uart_cip-37210_action.jpg "已建立 UART 连接。"

#### 保存库存固件

在烧录 OpenIPC 之前，最好先保存一下原厂固件，以防您不喜欢 OpenIPC 并想回滚或破坏某些东西。由于无法使用 tftp，我们将把闪存的内容保存到 microSD 卡中。由于设备运行 Linux，您现在无需担心格式化 microSD 卡。在连接到 USB 转 TTL 适配器时启动相机并启动屏幕：

```sh
sudo screen -L /dev/ttyUSB0 115200
```

现在是时候使用我在互联网上找到的密码"I81ou812"以 root 身份登录了。microSD 卡已自动安装到安装点"/mnt/sd/"，因此让我们在 SD 卡上创建一个新目录并转储闪存的内容：

```sh
mkdir /mnt/sd/image
for mtd in $(ls /dev/mtdblock*); do dd if=${mtd} of=/mnt/sd/image/${mtd##/*/}.bin; done
```
您可能需要对另一个文件夹重复此步骤，并比较二进制文件的 md5 校验和以确保转储成功。使用"C-a"然后按"d"退出屏幕，将 microSD 卡重新插入计算机并备份二进制文件。

#### 烧录 OpenIPC

是时候格式化 microSD 卡了，这样 u-boot 才能加载镜像。这些步骤可能因您的 Linux 发行版而异。[已经有一个适用于 Debian Sid 的脚本](https://gist.github.com/themactep/d0b72f4c5d5f246e2551622e95bc9987)，但遗憾的是在我的计算机上不起作用。（不同的 fdisk 版本和不同的设备和分区命名）。这些是我运行的命令：

```sh
# create the partition table
sudo parted /dev/mmcblk0 mklabel msdos
# create first partition
sudo parted /dev/mmcblk0 mkpart primary fat32 1MB 64MB
sudo mkfs.vfat -F32 /dev/mmcblk0p1
# create second partition
sudo parted /dev/mmcblk0 mkpart primary fat32 64MB 128MB
sudo mkfs.vfat -F32 /dev/mmcblk0p2
```

现在是时候安装第一个分区并[下载正确的固件](https://openipc.org/cameras/vendors/hisilicon/socs/hi3518ev200/download_full_image?flash_size=16&flash_type=nor&fw_release=ultimate) 并将其复制到已安装的分区上。卸载分区并将 microSD 卡插入相机。

Next, the burn utility needs to be set up:
```sh
git clone https://github.com/OpenIPC/burn
cd burn
sudo pip install -r requirements.txt
```
Now we need to download the correct uboot-binary
```sh
wget -P ./u-boot/ https://github.com/OpenIPC/firmware/releases/download/latest/u-boot-hi3518ev200-universal.bin
```

Make sure, that no process is blocking your USB to TTL adapter:
```sh
sudo lsof /dev/ttyUSB0
```
Kill the process if necessary:
```sh
sudo kill 230002
```
Power off the camera and also disconnect and reconnect your USB to TTL adapter. Now enter the following command and power on the camera:
```sh
./burn --chip hi3518ev200 --file=./u-boot/u-boot-hi3518ev200-universal.bin --break && screen -L /dev/ttyUSB0 115200
```
Hit any key to stop autoboot and you are greeted by the OpenIPC u-boot shell!
```sh
OpenIPC #
```
First we need to check, if our microSD card is ready to use:
```sh
fatls mmc 0
```
The following output is expected:
```text
 16777216   openipc-hi3518ev200-ultimate-16mb.bin

1 file(s), 0 dir(s)
```
Nice! Now it's time to load the binary into the memory. The variables are environment variables the OpenIPC u-boot knows to resolve, so you just need to copy and paste:
```sh
mw.b ${baseaddr} 0xff 0x1000000; fatload mmc 0:1 ${baseaddr} openipc-${soc}-ultimate-16mb.bin
```
This should result in the following output:
```text
reading openipc-hi3518ev200-ultimate-16mb.bin

16777216 bytes read
```
Now it's time to write and keep your fingers crossed:
```sh
sf probe 0; sf erase 0x0 0x1000000; sf write ${baseaddr} 0x0 ${filesize}
```
The expected output looks like this:
```text
16384 KiB hi_fmc at 0:0 is now current device
Erasing at 0x1000000 -- 100% complete.
Writing at 0x1000000 -- 100% complete.
```

It anything goes wrong here, don't power off the device and ask the mentioned [Telegram group](https://t.me/openipc) for help! Otherwise enter `reset` and get into the freshly flashed u-boot by hitting any key to stop autoboot. Run the following command and you are done:
```sh
run setnor16m
```
Now remove the SD cards and reboot, by entering `reset` again and you'll be greeted like this:
```text
Welcome to OpenIPC
openipc-hi3518ev200 login: root

    /######                                    /######  /#######    /######
   /##__  ##                                  |_  ##_/ | ##__  ##  /##__  ##
  | ##  \ ##   /######    /######   /#######    | ##   | ##  \ ## | ##  \__/
  | ##  | ##  /##__  ##  /##__  ## | ##__  ##   | ##   | #######/ | ##
  | ##  | ## | ##  \ ## | ######## | ##  \ ##   | ##   | ##____/  | ##
  | ##  | ## | ##  | ## | ##_____/ | ##  | ##   | ##   | ##       | ##    ##
  |  ######/ | #######/ |  ####### | ##  | ##  /###### | ##       |  ######/
   \______/  | ##____/   \_______/ |__/  |__/ |______/ |__/        \______/
             | ##
             | ##                              build
             |__/                             master+01a1348a, 2023-03-05

 Please help the OpenIPC Project to cover the cost of development and
 long-term maintenance of what we believe is going to become a stable,
 flexible Open IP Network Camera Framework for users worldwide.

 Your contributions could help us to advance the development and keep
 you updated on improvements and new features more regularly.

 Please visit https://openipc.org/sponsor/ to learn more. Thank you.
```

接下来，需要设置刻录实用程序： 现在我们需要下载正确的 uboot 二进制文件 确保没有进程阻止您的 USB 转 TTL 适配器： 如有必要，请终止该进程： 关闭相机电源，并断开并重新连接 USB 转 TTL 适配器。 现在输入以下命令并打开相机电源： 按任意键停止自动启动，您将看到 OpenIPC u-boot shell！ 首先，我们需要检查我们的 microSD 卡是否可以使用： 预期输出如下： 很好！现在是时候将二进制文件加载到内存中了。这些变量是 OpenIPC u-boot 知道要解析的环境变量，因此您只需复制和粘贴： 这应该会导致以下输出： 现在是时候写下并祈祷了： 预期输出如下所示： 如果这里出现任何问题，请不要关闭设备电源并向提到的 [Telegram 组](https://t.me/openipc) 寻求帮助！否则，输入"reset"并按任意键进入刚刚烧录的 u-boot 以停止自动启动。运行以下命令即可完成：现在移除 SD 卡并重新启动，再次输入"reset"，您将看到如下信息：root 密码为"12345"。第一次登录后，不要忘记使用"passwd"更改密码！

如果您在学习本教程时遇到困难但仍想在 Smartwares CIP-37210 上尝试 OpenIPC，您可以[在 open collective 购买预装了 OpenIPC v2.2 固件的产品](https://opencollective.com/openipc/contribute/wifi-camera-showme-by-openipc-44355)。

## 连接到 wifi 
现在是时候将相机连接到您的 2.4 GHz Wi-Fi 网络了。首先，确保固件环境变量设置正确。

首先设置网络驱动程序：

```sh
fw_setenv wlandev rtl8188fu-generic
```

Then the correct values according to your needs, for example:
```sh
fw_setenv wlanssid guest
fw_setenv wlanpass guest-password
```

You can check the settings as folows:
```sh
fw_printenv wlandev
fw_printenv wlanssid
fw_printenv wlanpass
```

The last step is to configure the wlan0 interface:
```sh
cat <<EOF > /etc/network/interfaces.d/wlan0
auto wlan0
iface wlan0 inet dhcp
    pre-up echo 3 > /sys/class/gpio/export
    pre-up echo out > /sys/class/gpio/gpio3/direction
    pre-up echo 1 > /sys/class/gpio/gpio3/value  # GPIO3 is the WIFI power
    pre-up modprobe mac80211
    pre-up sleep 1
    pre-up modprobe 8188fu
    pre-up sleep 1
    pre-up wpa_passphrase "\$(fw_printenv -n wlanssid)" "\$(fw_printenv -n wlanpass)" > /tmp/wpa_supplicant.conf
    pre-up sed -i 's/#psk.*/scan_ssid=1/g' /tmp/wpa_supplicant.conf
    pre-up ifconfig wlan0 up
    pre-up wpa_supplicant -B -i wlan0 -D nl80211,wext -c /tmp/wpa_supplicant.conf
    pre-up sleep 3
    post-down killall -q wpa_supplicant
    post-down echo 0 > /sys/class/gpio/gpio3/value
    post-down echo 3 > /sys/class/gpio/unexport
EOF
```

Now it's time to check whether it's working:
```sh
ifup wlan0
ip addr
```
然后根据您的需要输入正确的值，例如：您可以按如下方式检查设置：最后一步是配置 wlan0 接口：现在是时候检查它是否正常工作了：检查您是否可以 ping 和 ssh 进入相机。重新启动并检查，相机是否自动连接到您的 wifi 网络。重新组装相机，现在是时候告别 UART 并使用 ssh 和 Web 界面了。（凭据是 root 和您之前设置的密码。）

最后，您应该查看"/etc/majestic.yaml"，特别是按如下所示设置此部分，以便为夜间模式和音频提供正确的 GPIO 映射。

```yaml
audio:
  enabled: true
  volume: 70
  srate: 8000
  codec: alaw
  outputEnabled: true
  outputVolume: 30
  speakerPin: 51
  speakerPinInvert: true
nightMode:
  enabled: true
  irSensorPin: 62
  irSensorPinInvert: true
  irCutPin1: 64
  backlightPin: 63
  dncDelay: 30
  nightAPI: true
  irCutSingleInvert: false
```