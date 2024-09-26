# OpenIPC Wiki
[Table of Content](../README.zh.md)

在 Ubuntu 22.04 上运行 GroundStation 的分步命令 
---------------------------------------------------------

<p align="center">
  <img src="https://github.com/OpenIPC/wiki/blob/master/images/fpv-logo.jpg?raw=true" alt="Logo"/>
</p>

视频版本： - [OpenIPC - 准备地面站：Ubuntu + QGroundControl](https://www.youtube.com/watch?v=JMtRAsOm0Dc)

### Prepare
```
sudo apt update
```
```
sudo apt install dkms git python3-all-dev net-tools virtualenv fakeroot debhelper python3-twisted libpcap-dev python3-pyroute2 python3-future python3-all libsodium-dev
```

### Wifi card driver
```
git clone -b v5.2.20 https://github.com/svpcom/rtl8812au.git
cd rtl8812au/
sudo ./dkms-install.sh
```

### WFB-NG
```
git clone -b stable https://github.com/svpcom/wfb-ng.git
cd wfb-ng
sudo ./scripts/install_gs.sh wlan0
```

### Channel configuration
```
vi /etc/wifibroadcast.cfg
```

### Start WFB CLI
```
wfb-cli gs
```
###GS 已准备就绪###

### Start, stop, restart service
```
systemctl status wifibroadcast@gs
systemctl stop wifibroadcast@gs
systemctl start wifibroadcast@gs
```

### Qground 控制手册

- https://docs.qgroundcontrol.com/master/en/qgc-user-guide/getting_started/download_and_install.html

### Get last logs from service
```
journalctl -xu wifibroadcast@gs -n 100
```

### Useful commands
```
nmcli
ifconfig
iwconfig

```



