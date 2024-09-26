# OpenIPC Wiki
[Table of Content](../README.md)

Sigmastar 解砖 
---

**找到 Sigmastar UART 输出并将其连接到 CH341A I2C：**
- $\color{dimgray}{\texttt{GND -> GND (PIN 1-4)}}$
- $\color{deepskyblue}{\texttt{TX -> SDA I2C (PIN 5)}}$
- $\color{orange}{\texttt{RX -> SCL I2C (PIN 6)}}$

<img src="../images/sigmastar-ch341a.webp">
<img src="../images/sigmastar-uart.webp">
<img src="../images/sigmastar-example.webp">

---

**下载 snander-mstar：** 
- https://github.com/openipc/snander-mstar/releases

<details>
<summary>Install Windows driver</summary>
<img src="../images/sigmastar-driver.webp">
</details>

---

**使用 snander 检查设备闪存：**
- 设备连接到编程器时必须通电。
- 如果无法检测到设备，电源循环可能会有所帮助。
```
snander -i -q
```

<img src="../images/sigmastar-check.webp">

**擦除启动分区：** 
```
snander -l 0x200000 -e
```

<img src="../images/sigmastar-erase.webp">

**编写新的 uboot 文件：**
- https://github.com/openipc/firmware/releases/tag/latest
- 将文件放入与程序相同的文件夹中 
```
snander -w u-boot-ssc338q-nand.bin
```

<img src="../images/sigmastar-write.webp">

---

**I2C 设备：**
- 0x49 -> MStar ISP
- 0x59 -> MStar Debug

---

- [MarioFPV 的替代 Raspberry 方法](https://youtu.be/88C8UvyKQlQ)

