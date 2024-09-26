# OpenIPC Wiki
[Table of Content](../README.md)

对于 LifeSmart 相机：LS179

## BOM

SoC: HI3518EV200  
Flash: ENQH127A (16MB NOR)  
Sensor: soif23 (???)

## 安装

按照 [说明](help-uboot.md#bypassing-password-protected-bootloader) 访问 U-boot。

备份闪存并根据[说明](https://openipc.org/cameras/vendors/hisilicon/socs/hi3518ev200)安装 OpenIPC。

选择 16MB NOR 闪存，如果要使用 WiFi，请使用 Ultimate 固件。

## 无线上网

为了连接到 WiFi，您需要使用 `rtl8188fu-hi3518ev200-lifesmart` 配置 [无线设备](wireless-settings.md#initial-configuration)

```
fw_setenv wlandev rtl8188fu-hi3518ev200-lifesmart
# also configure your WiFi
fw_setenv wlanssid "MySSID"
fw_setenv wlanpass "password"
# and then...
reboot
```

这将启动 USB WiFi 模块（GPO #54），并加载适当的内核驱动程序（“8188fu”）。

## LEDs

LED 由 GPO #2 控制。

```bash
# turn on
gpio set 2

# turn off
gpio clear 2
```

