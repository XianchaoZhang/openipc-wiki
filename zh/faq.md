# OpenIPC Wiki
[目录](../README.zh.md)

常见问题 
---------------------------

### 我的相机不在受支持的设备列表中。你们能帮我解决吗？

简短的回答是"不"。

如果您有技能并且希望让 OpenIPC 在新硬件上运行，我们可以分享我们的知识。如果没有，请购买一台受支持的相机。

### 在购买相机之前，如何知道相机里面有什么硬件？

大多数情况下您无法找到。尤其是如果它是廉价的中国仿制品，仿制的是重新命名的相机。对于经过硬件认证的知名品牌，有机会在认证文件中查看相机内部。在相机的盒子或外壳上查找 FCC ID，然后在 [FCC ID 数据库](https://fccid.io/) 中查找。

### 精简版和旗舰版之间的区别

- 支持亚马逊服务器
- 支持二维码识别（基本）
- 支持 iptables（防火墙）
- 支持 ZeroTier 隧道
- 支持 MQTT（遥测）
- 支持 WiFi
- 支持 lame（mp3）和 libwebsockets
- 支持实验性的 WebRTC（仅限最近的 Hisi/Goke）


### 如何从二进制镜像中剥离 U-Boot 镜像包装器标头

有时，供应商的固件由二进制镜像组成，这些镜像旨在与 U-Boot 镜像加载器一起使用，并以 [U-Boot 镜像包装器格式](https://formats.kaitai.io/uimage/) 开头。在将此类镜像用作原始二进制文件之前，应先删除标头。以下是删除文件中前 64 个字节的方法：

```bash
dd if=inputfile.img of=outputfile.bin bs=64 skip=1
```

或者

```bash
tail -c +65 inputfile.img > outputfile.bin
```

### 如何进入引导加载程序 shell？

[有几种方法可以访问锁定的引导加载程序 shell](help-uboot.md#bypassing-password-protected-bootloader)

### 如何从 U-Boot 重置相机设置

有时，不正确的设置会使相机不稳定，以至于无法登录或在重新启动之前没有足够的时间来修复设置。以下是如何从引导加载程序 shell 中完全擦除 OpenIPC 固件中的覆盖分区，以使相机恢复到原始状态：

> __仅适用于 8MB 闪存分区__

```
sf probe 0; sf erase 0x750000 0xb0000; reset
```

> __仅适用于 16MB 闪存分区__

```
sf probe 0; sf erase 0xd50000 0x2b0000; reset
```

### 如何通过 SSH 登录相机？

`ssh root@<camera_ip_address>`

默认密码是_12345_。

### 如何登录相机 Web UI？

打开http://<camera_ip_address>，使用默认用户名_root_和默认密码_12345_登录。登录成功后，系统会要求您更改密码。

__请注意，它还会更改您的 ssh root 密码！__

### 如何重置 SSH/Web UI 密码？

使用 UART 适配器和终端程序创建与相机的串行连接。打开相机后，按 Ctrl-C 中断启动序列并进入引导加载程序 shell。

对于带有 8MB 闪存芯片的相机，运行

```bash
sf probe 0; sf erase 0x750000 0xb0000; reset
```

对于带有 16MB 闪存芯片的相机，运行

```bash
sf probe 0; sf erase 0xd50000 0x2b0000; reset
```

### 如何查找相机硬件和软件的信息？

通过"ssh"登录相机并运行"ipctool"。

### 我在摄像头上看不到 ipctool。为什么？

您看不到它，因为它最初不存在，但有一个同名的 shell 命令。

_（由于它是一个 shell 命令，因此它无法从 Web UI 中的 Web 控制台运行。通过 SSH 登录到摄像头并在其中运行该命令。）_

运行此命令时，最新版本的"ipctool"实用程序将下载到"/tmp/"目录中并从那里运行。由于该实用程序位于"/tmp/"，因此重启后它将失效，因此之后不会占用相机上的任何有用空间。

如果您需要知道命令中的内容，请在"/etc/profile"文件中搜索"ipctool"。

### 从 Linux 替换引导加载程序

每行命令单独执行，并等待执行结束。替换引导加载程序的全名及其可用性可在 [此处][3] 中查看

在运行命令之前，不要忘记输入正确的引导加载程序名称！

```
FILE=u-boot-SOC-TYPE.bin
curl -k -L https://github.com/OpenIPC/firmware/releases/download/latest/${FILE} -o /tmp/${FILE}
flashcp -v /tmp/${FILE} /dev/mtd0
flash_eraseall /dev/mtd1
```

Save wireless credentials:
```
FILE=/usr/share/openipc/wireless.sh
echo "#!/bin/sh" > ${FILE}
echo "fw_setenv wlandev $(fw_printenv -n wlandev)" >> ${FILE}
echo "fw_setenv wlanssid $(fw_printenv -n wlanssid)" >> ${FILE}
echo "fw_setenv wlanpass $(fw_printenv -n wlanpass)" >> ${FILE}
chmod 755 ${FILE}
```

保存无线凭证：
### 如何更新古老的 OpenIPC 固件？

每行命令单独执行，等待执行结束。第一个命令更新实用程序，该实用程序的算法在 2023 年 2 月发生了更改。如果您需要在 T31 处理器上更新实用程序，请在下载的实用程序的 URL 中添加 -mips 后缀。第二个命令更新固件组件本身。

```
curl -L -o /tmp/ipcinfo https://github.com/OpenIPC/ipctool/releases/download/latest/ipcinfo && chmod +x /tmp/ipcinfo; /tmp/ipcinfo -csF
curl -s https://raw.githubusercontent.com/OpenIPC/firmware/master/general/overlay/usr/sbin/sysupgrade | sh -s -- -k -r -n
```

### 是否可以通过"无线"方式从"精简版"切换到"终极版"？

在 Ingenic 和 Sigmastar 上，可以分割最终的 rootfs.squashfs 并将其刷入 rootfs（mtd3）和覆盖（mtd4）分区。

```
dd if=rootfs.squashfs of=mtd3.bin bs=1k count=5120
dd if=rootfs.squashfs of=mtd4.bin bs=1k skip=5120
flashcp mtd3.bin /dev/mtd3 -v
flashcp mtd4.bin /dev/mtd4 -v
```

### 如何将完整固件转储到 NFS 共享

如果你足够幸运的话，这个方法就可以奏效，你可以访问库存固件上的 Linux shell，并且它确实支持 NFS 挂载：

```bash
fw=$(mktemp -t)
nfs=$(dirname $fw)/nfs
mkdir -p $nfs
mount -t nfs -o tcp,nolock 192.168.1.123:/path/to/nfs/share $nfs
cat /dev/mtdblock? > $fw
mv $fw ${nfs}/firmware_full.bin
```

确保使用您自己的 IP 地址和 NFS 共享路径！

### 如何在固件转储中找到原始 MAC 地址

```bash
strings dumpfile.bin | grep ^ethaddr
```

### 如何配置通过密钥进行 ssh 会话授权

__在相机上__：通过相机的 85 端口登录 Web UI。

```bash
passwd
```

__在桌面上__：使用上面创建的密码登录，将公钥复制到相机。

```bash
ssh-copy-id root@192.168.1.66
```

__在相机上__：在根用户的主目录中创建一个 `.ssh` 文件夹，并将包含授权密钥库的文件复制到其中。

```bash
mkdir ~/.ssh
cp /etc/dropbear/authorized_keys ~/.ssh/
```

__在桌面上__：打开一个新会话来验证授权是否使用公钥通过而不需要密码。

```bash
ssh root@192.168.1.66
```

### Majestic

#### 如何获取内存转储以进行调试？

在菜单 **Majestic** > **Majestic Debugging** 中启用并配置 Core Dump。

#### 相机图像带有粉红色调

您需要指定 GPIO 引脚来控制红外滤光片。某些相机的设置可在 [本表][1] 中找到。如果您的相机不在表中，则需要使用 [ipctool 实用程序][2]。

第一次调用"ipctool"时，OpenIPC 固件将自动将最新版本的实用程序下载到 /tmp 目录。

在库存固件中，您需要使用系统中可用的任何工具自行将实用程序下载到相机：wget、curl、tftp 等。

例如，将 ipctool 实用程序下载到本地网络上的 TFTP 服务器，然后将其下载到相机：

```bash
tftp -g -r ipctool -l /tmp/ipctool 192.168.1.1
chmod +x /tmp/ipctool
/tmp/ipctool
```

如果摄像机可以访问互联网，您可以尝试安装公共 NFS 共享并从中运行该实用程序，而无需下载到摄像机：

```bash
mkdir -p /tmp/utils
mount -o nolock 95.217.179.189:/srv/ro /tmp/utils/
/tmp/utils/ipctool
```

将实用程序下载到相机后，在终端中运行"ipctool gpio scan"命令，并用手掌打开和关闭相机镜头几次。

观察 ipctool 的输出以确定负责控制红外滤光帘的引脚。

输入在夜间模式 Majestic 的设置中获得的值。如果粉红色调仍然存在，则可能需要启用传感器信号反转。

不要忘记将相机型号和找到的 GPIO 值添加到表中！

#### 是否可以在标准/metrics 中显示设置镜头自动​​对焦的数据而不是当前的 sample_af？

不，这是一个困难的算法，以这种方式运行它没有意义。

#### 将文件从 Linux 系统复制到相机

Sometimes you need to transfer files to the camera. In addition to the above
method using NFS (Network File System) you can use the standard Linux `scp`
command to copy files over an SSH connection:
```bash
scp ~/myfile root@192.168.1.65:/tmp/
```
有时您需要将文件传输到相机。除了上述使用 NFS（网络文件系统）的方法外，您还可以使用标准 Linux `scp` 命令通过 SSH 连接复制文件：此命令将 `myfile` 从主目录复制到相机上的 `/tmp/` 目录。

On recent Linux systems the following error may occur:
```console
sh: /usr/libexec/sftp-server: not found
scp: Connection closed
```
In this case, add `-O` option to the command:
```bash
scp -O ~/myfile root@192.168.1.65:/tmp/
```


在最近的 Linux 系统上可能会出现以下错误：在这种情况下，在命令中添加 `-O` 选项：

[1]: https://openipc.org/wiki/en/gpio-settings.html
[2]: https://github.com/OpenIPC/ipctool/releases/download/latest/ipctool
[3]: https://github.com/OpenIPC/firmware/releases/tag/latest
