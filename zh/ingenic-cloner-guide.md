### Ingenic 克隆工具

ingenic cloner 应用程序是一款 PC 端实用程序，可与 Ingenic SOC 内置的"USB 启动"模式交互。通过将 SOC 置于"USB 启动"模式，您可以使用 ingenic clonger 应用程序直接刷新固件芯片，而无需物理移除或连接闪存芯片。

本指南尚在编写中。


#### 短接闪存芯片上的引脚

首先要做的是找到相机电路板上的闪存芯片。通常，这是一个方形芯片，有 8 个引脚，标记为 25Q64 或 25Q128，很少标记为 25L64 或 25L128。如果您找不到芯片，请尝试从两侧拍摄电路板的照片。然后 [在我们的 Telegram 频道](https://t.me/openipc) 中寻求帮助。__不要尝试短路任何随机芯片！这很可能会烧毁您的相机电路。__

在引导加载程序启动之后但在调用 Linux 内核之前，用小金属物体、螺丝刀或镊子短路闪存芯片的引脚 5 和 6。

SOIC8 芯片的引脚 5 和 6 位于引脚 1 的对角，由旁边的浮雕或绘制的点表示。

![](../images/flash-pins.webp) ![](../images/flash-pins-2.webp)


![image](https://github.com/OpenIPC/wiki/assets/12115272/632e5cb9-0b5d-406b-a268-7c4b65781828)
![image](https://github.com/OpenIPC/wiki/assets/12115272/5b9fed70-031e-42ea-89b6-292cc2f34458)
![image](https://github.com/OpenIPC/wiki/assets/12115272/27f0d101-059d-41a1-a444-43bd137cf1b9)
![image](https://github.com/OpenIPC/wiki/assets/12115272/69c6f79d-1c88-45d9-b6a7-003345d72e56)

---

# 下载克隆器：[OpenIPC 实用程序]（https://openipc.org/utilities）

---

**OpenIPC Cloner 应用指南**

1. **访问 Cloner**： 
   - 导航到"cloner-2.5.xx-ubuntu_alpha"目录，其中"xx"表示您下载的 Cloner 版本。

2. **准备固件**： 
   - 在此目录中创建一个名为"0_OpenIPC_Firmware"的新文件夹。

3. **启动 Cloner**： 
   - 打开 `cloner` 应用程序。确保您使用的是 2.5.43 或更高版本以确保兼容性。

4. **初始设置**：
   - 单击"加载图像"并选择"openipc_cloner_bundle_xxx.zip"文件。
   - 如果锁定级别为"2"，请将其更改为"0"。输入"!@#"（感叹号、at 符号、数字符号，不带任何引号）作为密码。应会重新出现"配置"按钮。

5. **配置设置**： 
   - 点击右上角的"配置"按钮。

6. **导航配置**： 
   - 在配置窗口的"信息"选项卡下，访问各种配置菜单。

7. **设备特定设置**：
   - 选择"平台 T"。
   - 在平台"T"旁边为您的设备选择适当的 SOC 版本。
   - 在"主板"下，选择相关操作：
     - 8MB 闪存芯片设备使用"txxx_sfc_nor_reader_8MB.cfg"。
     - 16MB 闪存芯片设备使用"txxx_sfc_nor_reader_16MB.cfg"。
     - 写入单个分区使用"txxx_sfc_nor_writer.cfg"。
     - 刷新整个芯片使用"txxx_sfc_nor_writer_full.cfg"。
   - 单击"保存"返回主屏幕。

8. **启动程序**： 
   - 加载所需的配置文件后，单击主屏幕上的"启动"。

9. **设备识别程序**：
   - 将 USB 电缆插入设备，另一端不插。
   - 短路闪存芯片上的 5-6 针，而不是 SoC 或任何其他芯片，使用照片作为参考，如本文档前面所述。
   - 保持短路的同时，将 USB 电缆连接到计算机。等待 2 秒钟，然后松开短路。
   - Cloner 可能需要长达 30 秒才能识别设备。主屏幕上的进度条将显示正在进行的操作。

10. **完成**： 
   - 一旦所有进度条变为绿色，操作即完成。

---

仔细按照以下步骤操作，以确保克隆器应用程序设置正确并按预期运行。

