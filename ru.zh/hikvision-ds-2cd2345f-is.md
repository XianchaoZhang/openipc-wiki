# OpenIPC Wiki 
[内容](../README.md)

海康威视 DS-2CD2345F-IS 
--------------

**注意，目前这些只是工作笔记，而不是完整的操作说明！**

Rostelecom 销售的摄像机中有 Hikvision DS-2CD2345F-IS 型号。它与大多数其他产品的不同之处在于，它显然没有原始固件。但硬件受 OpenIPC 支持，这意味着并非一切都丢失了。

## 目前情况
- 您需要使用 hi3516av100 处理器的组件，因为只是它支持 NAND 内存，其他一切都与 hi3516dv100 的组件相同。
- 日/夜切换是通过附加脚本实现的。在一天中的黑暗和光明时间的边界存在某些问题 - 在黄昏时它会反复切换到一种或另一种模式。 
- 麦克风不起作用。
- 无法录制到存储卡。 
- 进入和退出不起作用。

## 平台
- 处理器 hi3516dv100
- 传感器ov4689
- 内存容量128MB
- ROM容量128MB
- NAND ROM类型

## 固件
### 环境变量
```
setenv soc hi3516av100
setenv sensor ov4689
setenv totalmem 128M
setenv osmem 48M
setenv baseaddr 0x82000000

setenv bootargs 'mem=48M console=ttyAMA0,115200 panic=20 init=/init root=ubi0:rootfs rootfstype=ubifs ubi.mtd=3,2048 mtdparts=hinand:256k(boot),768k(wtf),3072k(kernel),-(ubi) of_mdio.higmacphy=0'
setenv bootcmd 'nand read ${baseaddr} 0x100000 0x300000; bootm ${baseaddr}'

setenv ethaddr 00:12:34:56:78:90    //задать MAC-адрес камеры, если не задан
setenv ipaddr 192.168.1.10          //задать IP-адрес камеры, если не задан
setenv serverip 192.168.1.2         //задать адрес компа с TFTP-сервером

saveenv
```
### 内核和文件系统映像 
```
mw.b ${baseaddr} ff 0x1000000
tftp ${baseaddr} uImage.${soc}
nand erase 0x100000 0x300000
nand write.i ${baseaddr} 0x100000 0x300000

mw.b ${baseaddr} ff 0x1000000
tftp ${baseaddr} rootfs.ubi.${soc}
nand erase 0x400000 0x7c00000
nand write.i ${baseaddr} 0x400000 ${filesize}

reset
```
您可以使用 **;** 在一行中输入命令： 
```
mw.b ${baseaddr} ff 0x1000000; tftp ${baseaddr} uImage.${soc}; nand erase 0x100000 0x300000; nand write ${baseaddr} 0x100000 0x300000

mw.b ${baseaddr} ff 0x1000000; tftp ${baseaddr} rootfs.ubi.${soc}; nand erase 0x400000 0x7c00000; nand write ${baseaddr} 0x400000 ${filesize}

reset
```
#### 首次启动 
我们不中断启动加载并观察系统启动日志。如果一切按预期进行，并且所使用的程序集中没有任何损坏，那么几秒钟后我们将看到登录提示。以不带密码的 root 用户身份登录，然后输入 ifconfig eth0 命令以查看生成的 IP 地址。
## Web 界面 
Web 界面默认在 85 端口上可用。登录名：admin，密码：12345。首次登录时，会要求您设置新的复杂密码，该密码也将成为登录时的 root 密码通过 UART 或 SSH 的控制台。该系统的主要部分是 Majestic 流光。它执行捕获和广播图像的功能以及与其相关的所有其他操作。我们需要设置它。
### Majestic -> 图像信号处理器 (ISP)
- 在传感器配置文件路径字段中，选择 /etc/sensors/ov4689_i2c_1080p.ini（对于 2MP 分辨率）或 /etc/sensors/ov4689_i2c_4M.ini（对于 4MP 分辨率）。
### Majestic -> 主流视频 (Video0)
- 确保启用Video0开关已打开
- 在Video0编解码器字段中，选择h265选项
### Majestic -> 子流视频 (Video1)
- 打开启用视频1开关
- 在Video1编解码器字段中，选择h265选项
### 设置 -> 重置...
- 单击“重新启动相机”以重新启动。

预览只有幻灯片放映，但如果您想要视频流，查看它的最简单方法是在 VLC 中，从菜单中选择“打开 URL”并输入以下行之一：

- rtsp://admin:password@ip-address:554/stream=0 - 第一个流
- rtsp://admin:password@ip-address:554/stream=1 - 第二个流，其中：password - 您的密码，ip-address - 摄像机地址。

如果默认参数值不令人满意，并且看起来图像可以更好，您可以使用传感器和视频流设置，但有机会破坏Majestic。要恢复功能，请在“设置”->“重置...”菜单中找到“重置 Majestic 设置”项目。使用后，您还需要重新启动相机。
## 切换白天/夜晚 
通常，当天黑或光源关闭时，摄像机会切换到夜间模式。图像切换为黑白模式，红外滤镜关闭，红外照明打开。在相反的情况下，执行相反的操作。

该系统可以检测来自传感器或图像的光的缺失。目前，Majestic 只能与传感器配合使用。该相机型号没有它。这意味着您需要设置控制输出的参数，并且必须使用脚本来控制它们。
### 菜单项 Majestic -> 夜间模式：GPIO 设置
- 启用夜间模式
- 设置 IRcut 滤波器信号的 GPIO pin1：105
- 设置 IRcut 滤波器信号的 GPIO pin2：104
- 设置 GPIO 引脚以打开夜间模式照明：114

现在 **Majestic** 了解了 **GPIO**，您可以尝试通过 **API** 从命令行手动控制切换。您需要以 **root** 身份登录，无需密码。命令如下：
```
curl http://ip-address/night/on         //включить ночной режим.
curl http://ip-address/night/off        //выключить ночной режим.
curl http://ip-address/night/toggle     //переключить режим.
```
如果一切正常，我们继续 - 我们根据改变曝光时间自动控制模式切换的过程。

### 模式切换控制脚本 
创建脚本文件： 
```
cat > /usr/sbin/checkexp.sh
```
… 并通过剪贴板粘贴内容： 
```
#!/bin/sh
sleep 10
login=$(cat /etc/httpd.conf | grep cgi-bin | cut -d':' -f2)
pass=$(cat /etc/httpd.conf | grep cgi-bin | cut -d':' -f3)
chtime=300 #change time to check isp_again, default 300 sec
chexp=15 #change isp_again threshold (15-30)
day=1

while true; do

exp=$(curl -s http://localhost/metrics | grep ^isp_again | cut -d' ' -f2)
bri=`expr $exp / 1000`
logger "Analog gain $bri"

    if [ $bri -gt $chexp -a $day -eq 1 ] ;then
	day=0
	curl -u $login:$pass http://localhost/night/on
	logger "Night mode ON"
    fi
	
	if [ $bri -le $chexp -a $day -eq 0 ] ;then
	day=1
	curl -u $login:$pass http://localhost/night/off
	logger "Night mode OFF"
    fi

sleep $chtime
done
```
按组合键 **Ctrl+D** 保存文件并授予执行权限： 
```
chmod +x /usr/sbin/checkexp.sh
```
如果现在运行脚本，它将开始分析曝光并控制夜间状态。缺省情况下，检查时间间隔为300秒，即5分钟。

为了让脚本在系统启动时自动运行，我们创建一个启动文件： 
```
cat > /etc/init.d/S99rc.local
```
… 并粘贴内容： 
```
./usr/sbin/checkexp.sh > /dev/null 2>&1 &
exit 0
```
按组合键 *Ctrl+D* 保存文件并授予执行权限： 
```
chmod +x /etc/init.d/S99rc.local
```
现在您可以重新启动相机并很高兴切换模式，虽然不理想，但它有效。

