# OpenIPC 维基

[Table of Content](../README.md)

## OrangePi 5 Ubuntu 22.04 地面站快速设置

---

<p align="center">
  <img src="../images/pi5-plus.png?raw=true" alt="Logo" style="height:400px;"/> 
  <img src="../images/pi-5.png?raw=true" alt="Logo" style="height:400px;"/>
</p>

### Prepare

```
sudo apt update
sudo apt upgrade
```

### 下载并安装rockchip rk3588 的 Linux 内核头文件

[https://drive.google.com/drive/folders/1R7VmAeo3_LpFDQvYSEG9ymAC-DvaLt47](https://drive.google.com/drive/folders/1R7VmAeo3_LpFDQvYSEG9ymAC-DvaLt47)

```
sudo dpkg -i linux-headers-legacy-rockchip-rk3588_1.1.2_arm64.deb
sudo dpkg -i linux-image-legacy-rockchip-rk3588_1.1.2_arm64.deb
```

### Wifi 卡驱动

要禁用将其添加到黑名单：

```
sudo bash -c "cat > /etc/modprobe.d/wfb.conf <<EOF
# blacklist stock module
blacklist 88XXau
blacklist 8812au
blacklist rtl8812au
blacklist rtl88x2bs
EOF"
```

从源代码编译驱动程序：

```
git clone -b v5.2.20 https://github.com/svpcom/rtl8812au.git
cd rtl8812au/
sudo ./dkms-install.sh
```

### 安装 WFB-NG

使用"nmcli"命令，我们找出你的 wifi 适配器的名称，并用 $WLAN 代替

```
git clone -b stable https://github.com/svpcom/wfb-ng.git
cd wfb-ng
sudo ./scripts/install_gs.sh $WLAN
```

并启用自动上传

```
sudo systemctl enable wifibroadcast
```

### 频道配置

```
sudo vi /etc/wifibroadcast.cfg
```

### 从 IP 摄像机复制加密密钥

```
sudo scp root@192.168.1.10:/etc/drone.key /etc/gs.key
```

并重新启动 wfb-ng：

```
sudo systemctl restart wifibroadcast@gs
```

### 启动 WFB CLI

```
wfb-cli gs
```

### 视频解码

h265

```
gst-launch-1.0 udpsrc port=5600 caps='application/x-rtp, media=(string)video, clock-rate=(int)90000, encoding-name=(string)H265' ! rtph265depay ! h265parse ! mppvideodec ! xvimagesink sync=false
```

h264

```
gst-launch-1.0 udpsrc port=5600 caps='application/x-rtp, media=(string)video, clock-rate=(int)90000, encoding-name=(string)H264' ! rtph264depay ! h264parse ! mppvideodec ! xvimagesink sync=false
```

### GS 已准备就绪###

### DVR（数字视频录像机）

创建一个文件**gst_start**，内容如下，并赋予执行**chmod +x gst_start**的权限。

```
#!/bin/bash
current_date=$(date +'%Y%d%m_%H%M%S')
cd ~/Videos

if [[ $1 == "save" ]]
then
	gst-launch-1.0 -e udpsrc port=5600 caps='application/x-rtp, media=(string)video, clock-rate=(int)90000, encoding-name=(string)H265' ! rtph265depay ! h265parse ! tee name=t ! queue ! mppvideodec ! xvimagesink sync=false t. ! queue ! matroskamux ! filesink location=record_${current_date}.mkv
else
	gst-launch-1.0 udpsrc port=5600 caps='application/x-rtp, media=(string)video, clock-rate=(int)90000, encoding-name=(string)H265' ! rtph265depay ! h265parse ! mppvideodec ! xvimagesink sync=false
fi

```

当使用 **save** 选项运行时，视频将保存到 **/home/Video 文件夹/**

### 启动、停止、重启服务

```
systemctl status wifibroadcast@gs
systemctl stop wifibroadcast@gs
systemctl start wifibroadcast@gs
```

### 从服务获取最新日志

```
journalctl -u wifibroadcast@gs -f
journalctl -xu wifibroadcast@gs -n 100
```

### 有用的命令

```
# Checking the operation of the wfb-ng
/usr/bin/wfb_rx -p 0 -c 127.0.0.1 -u 5600 -K /etc/gs.key -i 7669206 $WLAN

# Find out the name of the wifi adapter
nmcli
ifconfig
iw

# Displays the possible parameters of the wifi adapter
iw list

# Displays the current settings of the Wifi adapter
iw dev

# Outputs the current frequency and power parameters
sudo iw reg get

# Set a new region
sudo iw reg set RU
https://hackware.ru/?p=17978 - Solves the problem of channel selection

# Viewing running wfb-ng processes
ps -aux | grep wfb

# Set the power
sudo ip link set $WLAN down
sudo iw dev $WLAN set txpower fixed 30mBm
sudo ip link set $WLAN up

# View available plugins for decoding
gst-inspect-1.0 | grep 265

# Shows a list of downloaded drivers/modules
lsmod

# Displays a list of connected USB devices and related drivers
usb-devices
```
