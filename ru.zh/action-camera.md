# OpenIPC Wiki 
[内容](../README.md)

使用OpenIPC作为廉价运动相机
--------------------------------

<p align="center">
  <img src="https://github.com/OpenIPC/wiki/blob/master/images/actioncam-helmet.webp" alt="Logo"/>
</p>

长期以来，相机和摄像机一直将多种设备结合在一起。因此，连接到计算机的相机可以用作网络摄像头。车内的电话可以充当录像机。因此，将网络摄像机用作运动摄像机的想法自然而然地诞生了。

要组装，这样的模块必须具有：
* microSD 存储卡插槽
* USB 端口用于 WiFi 适配器
* 麦克风和扬声器的音频输入/输出
* 良好的灵敏传感器

### 相机组装

多家制造商的主板均满足以下要求：
*卡姆嗨：
  * GK7205V200 + IMX307
  * GK7205V300 + IMX335
*SMTSEC：
  * GK7205V200 + IMX307
  * GK7205V300 + IMX307
  * GK7205V300 + IMX335

5 兆像素 IMX335 传感器将允许您在进一步的视频处理过程中使用数字稳定功能。

“球”非常适合作为身体，因为......它们具有最小的尺寸和流线型的形状。塑料的重量轻，但有许多通风孔，因此不能在雨天或靠近溅水源的地方使用。金属的较重，但不透气。 50x50的配电箱看起来很有趣，可以加倍形成一个立方体。

<palign="center"> 
<img src="https://github.com/OpenIPC/wiki/blob/master/images/actioncam-housing-front.webp" alt="外壳前视图"/> 
<img src="https://github.com/OpenIPC/wiki/blob/master/images/actioncam-housing-side.webp" alt="外壳侧视图"/> 
</p>

要启动相机，需要至少提供 4V 电源，因此无法直接使用单节锂电池供电。您需要使用任何升压电压转换器，例如MT3608，设置为5V或更高。您还需要一个电池充电模块，合适的版本 TP4056。

为了指示操作，使用了一个两针、两色 LED，通过限流电阻器连接到 Irkat 连接器。电阻的阻值根据所需的亮度和升压模块上配置的电压来选择。

<palign="center"> 
<img src="https://github.com/OpenIPC/wiki/blob/master/images/actioncam-inside.webp" alt="内部视图"/> 
</p>

选择合适尺寸的电池。合身性好：
* 来自手机的正方形
* 电子烟圆形
* 各种电子设备的小包装

3Ah电池搭配gk7205v200 + imx307板可持续使用约4小时。

<palign="center"> 
<img src="https://github.com/OpenIPC/wiki/blob/master/images/actioncam-battery.webp" alt="电池"/> 
</p>

不要忘记连接麦克风。为了减少干扰，建议使用屏蔽线。塑料外壳内的相机重量约为 100 克。该安装座可以直接用螺钉固定在机身上，也可以通过摄影设备的标准 1/4" 螺母固定在机身上。

<palign="center"> 
<img src="https://github.com/OpenIPC/wiki/blob/master/images/actioncam-mount.webp" alt="安装"/> 
<img src="https ://github.com/OpenIPC/wiki/blob/master/images/actioncam-weight.webp" alt="重量"/> 
</p>

### 相机设置

禁用 u-boot 延迟：

`fw_setenv 启动延迟 0`

禁止启动未使用的会降低工作速度的服务：

**注意力！网络访问将不再可能！**

```
cd /etc/init.d
chmod -x S40network S49ntpd S50dropbear S50httpd S50snmpd S50telnet S60crond S92motion S93telegrambot
```

并创建一个新的“S94actioncam”：

```
#!/bin/sh

mount | grep mmc || exit

cd /mnt/mmcblk0p1
number=`ls -dl */ | wc -l`
number=$((number+1))

mkdir $number

mv *.mp4 $number/
```

还有“S96led”：

```
#!/bin/sh

/usr/bin/led_blink &
```

文件`/usr/bin/led_blink`：

```
#!/bin/sh

gpio_0=14
gpio_1=15

# pin_mux
echo "$gpio_0" >/sys/class/gpio/unexport
echo "$gpio_1" >/sys/class/gpio/unexport
echo "$gpio_0" >/sys/class/gpio/export
echo "$gpio_1" >/sys/class/gpio/export

# dir
echo "out" >/sys/class/gpio/gpio$gpio_0/direction
echo "out" >/sys/class/gpio/gpio$gpio_1/direction

while true
do
  sleep 1
  echo "0" >/sys/class/gpio/gpio$gpio_0/value
  echo "0" >/sys/class/gpio/gpio$gpio_1/value

  sleep 1
  echo "0" >/sys/class/gpio/gpio$gpio_0/value
  echo "1" >/sys/class/gpio/gpio$gpio_1/value

done
```

不要忘记使所有文件可执行：

```
chmod +x S94actioncam S96led
chmod +x /usr/bin/led_blink
```

`gpio_0` 和 `gpio_1` 根据所选板进行设置。接通电源时，黄色LED灯亮起；流光启动后，颜色变为绿色，并继续以1秒的间隔闪烁。加载时间约为10秒。

我们在yaml中编写相同的gpio：

```
cli -s .nightMode.irCutPin1 14
cli -s .nightMode.irCutPin2 15
```

启用录音：

```
cli -s .audio.enabled true
cli -s .audio.srate 24000
cli -s .audio.codec aac
```

将闪存卡插入相机并查看其安装位置：

```
mount | grep mnt
```

必须在流媒体设置中指定此路径：

```
cli -s .records.enabled true
cli -s .records.path /mnt/mmcblk0p1/%Y-%m-%d-%H.mp4

```

现在，加载相机后，旧视频将被移动到新文件夹，最新视频将位于闪存驱动器的根目录中。

### 已知问题

最好使用快速闪存驱动器，因为...在动态变化的场景中，视频比特率会增加，并且音频可能会出现长达数秒的丢帧。

每个视频都以几秒钟的静态镜头开始。这是由于流光器对传感器的自动调整造成的。

因为视频录制被错误中断，然后计算机上的某些文件可能会长时间打开。

### 改进

* wifi用于复制录制的视频和设置
* 用于复制录制视频的 USB 大容量存储
* 扬声器，通过信号或声音通知放电和其他事件
* RGB LED 用于操作指示
* 使用按钮进行电源控制以将最后编码的块正确写入闪存驱动器
* 基于AXP173（AXP176）的电源系统，用于监控电池消耗
* 使用具有不同镜片的“两只眼睛”板
* RTC 用于正确的文件命名
* GPS用于记录拍摄坐标（在元数据或字幕中）
* 陀螺仪用于视频稳定
* 带触摸屏/操纵杆的显示屏用于设置
* PQTools 调整图像质量

<palign="center"> 
<img src="https://github.com/OpenIPC/wiki/blob/master/images/actioncam-box-1.webp" width="25%" alt="立方体 1 "/> 
<img src="https://github.com/OpenIPC/wiki/blob/master/images/actioncam-box-3.webp" width="25%" alt="Cube 3"/> 
<img src="https://github.com/OpenIPC/wiki/blob/master/images/actioncam-box-2.webp" width="25%" alt="Cube 2"/> 
</p>

