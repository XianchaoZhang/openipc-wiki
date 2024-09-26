# OpenIPC Wiki
[Table of Content](../README.zh.md)

Foscam X5 / Acculenz R5 / Assark X3E 
---

#### 准备 
将 SD 卡格式化为 FAT32，并将以下档案内容复制到卡中：
- [foscam-openipc.zip][1]

在 autostart.sh 上更新您的无线凭据：
```diff
#!/bin/sh
+WLAN_SSID="Router"
+WLAN_PASS="12345678"
```

#### Flashing
- 确保摄像头已关闭，将准备好的 SD 卡放入设备。
- 打开设备电源并等待至少 4 分钟。
- 不久之后，OpenIPC 摄像头应该会出现在您的 WLAN 上。

---

### GPIO 
IRLed | IRCut | Speaker | Reset | IRSensor
-|-|-|-|-
GPIO0 | GPIO3 | GPIO14 | GPIO66 | GPIO80

```
cli -s .nightMode.irSensorPin 80
cli -s .nightMode.irCutPin1 3
cli -s .nightMode.irCutSingleInvert true
cli -s .nightMode.backlightPin 0
cli -s .audio.speakerPin 14
cli -s .audio.speakerPinInvert true
```

---

### Wireless
```
fw_setenv wlandev rtl8188fu-ssc337de-foscam
fw_setenv wlanssid Router
fw_setenv wlanpass 12345678
```

---

### 摄像头拆解 
<details>
<summary>Expand pictures</summary>
<img src="../images/device-foscam-01.webp" width=50% height=50%>
<img src="../images/device-foscam-02.webp" width=50% height=50%>
<img src="../images/device-foscam-03.webp" width=50% height=50%>
<img src="../images/device-foscam-04.webp" width=50% height=50%>
<img src="../images/device-foscam-05.webp" width=50% height=50%>
<img src="../images/device-foscam-06.webp" width=50% height=50%>
<img src="../images/device-foscam-07.webp" width=50% height=50%>
<img src="../images/device-foscam-08.webp" width=80% height=80%>
<img src="../images/device-foscam-09.webp" width=80% height=80%>
</details>

---

### 其他
- Labels: X3/R3/R5/X3E
- https://fccid.io/ZDER3

[1]: https://github.com/openipc/wiki/files/13301107/foscam-openipc.zip
