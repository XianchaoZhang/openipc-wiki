# OpenIPC Wiki
[Table of Content](../README.md)

如何通过 UART 发送文件 
---------------------------

如果 SD 卡读卡器出现故障并且摄像头上没有配置网络，您可能需要通过 UART 接口发送新固件来更新摄像头。

## 发件人：

1-首先我们对文件进行编码

```bash
base64 uImage > uImage.b
```

2- 现在确保您的 com send-file 命令是 ascii-xfr，这是我的连接命令行

```bash
picocom -f n -p n -d 8 -b 115200  --send-cmd "ascii-xfr -snv" /dev/ttyUSB0

```

通常我们希望接收端使用 ascii-xfr，但由于我们没有它，-n 可以通过维护正确的行尾来解决这个问题。



## 接收者：

3- Now that we've connected, go to the directory where you want the received file.
```bash
cd /tmp/
```

4- Start receiving the file by uart
```bash
cat > uImage.b
```
3- 现在我们已经连接，请转到您想要接收文件的目录。4- 开始通过 uart 接收文件 5- 在 picocom 上，我只需按 CTRL+a+s，然后输入我要发送的文件的完整路径。传输完成后，您需要按 CTRL+c 来打破那只猫。

6- Now we decode the file,
```bash
base64 -d uImage.b > uImage
```

6- 现在我们解码文件，7- 尽一切可能验证该文件与您发送的文件是否相同，因为 ASCII 传输没有校验和保护。Openipc 有 sha512sum，但任何校验和命令都可以。


```bash
sha256sum uImage
```
一旦您手动确认总数匹配，您就可以认为转账成功！

对 rootfs 重复步骤 4、5 和 6，现在你就可以使用 sysupgrade 进行升级了

```bash
sysupgrade --kernel=/tmp/uImage --rootfs=/tmp/rootfs.squashfs --force_ver -z
```

