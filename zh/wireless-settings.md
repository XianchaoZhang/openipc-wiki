# OpenIPC Wiki
[Table of Content](../README.md)

无线设置
---

### 初始配置

- 基于 uboot 变量提供无线设置：

```diff
# HI3516EV300 CamHi
+if [ "$1" = "mt7601u-hi3516ev300-camhi" ]; then
	devmem 0x100C0080 32 0x530
```

> [!IMPORTANT] 
> 某些驱动程序未包含在预制的 OpenIPC 固件二进制文件中，您可能需要自行编译。

- 变量 [列于此处][1] 并且可以通过以下方式设置：
```shell
fw_setenv wlandev mt7601u-hi3516ev300-camhi
```

### 输入无线凭据

- 可以添加以下凭证：

```shell
fw_setenv wlanssid MySSID
fw_setenv wlanpass MyPassword
```

---

### 采用自定义设置

- 可以将 [现有的 wlan0 设置](../en/network-settings.md) 转换为 [新配置][1]：

```diff
auto wlan0
iface wlan0 inet dhcp
+	pre-up devmem 0x100C0080 32 0x530
+	pre-up echo 7 > /sys/class/gpio/export
+	pre-up echo out > /sys/class/gpio/gpio7/direction
+	pre-up echo 0 > /sys/class/gpio/gpio7/value
+	pre-up modprobe mt7601u
	pre-up wpa_passphrase "SSID" "password" >/tmp/wpa_supplicant.conf
	pre-up sed -i '2i \\tscan_ssid=1' /tmp/wpa_supplicant.conf
	pre-up sleep 3
	pre-up wpa_supplicant -B -D nl80211 -i wlan0 -c/tmp/wpa_supplicant.conf
	post-down killall -q wpa_supplicant
	post-down echo 1 > /sys/class/gpio/gpio7/value
	post-down echo 7 > /sys/class/gpio/unexport
```

```diff
# HI3516EV300 CamHi
if [ "$1" = "mt7601u-hi3516ev300-camhi" ]; then
+	devmem 0x100C0080 32 0x530
+	set_gpio 7 0
+	modprobe mt7601u
	exit 0
fi
```

[1]: https://github.com/OpenIPC/firmware/blob/master/general/overlay/etc/wireless
