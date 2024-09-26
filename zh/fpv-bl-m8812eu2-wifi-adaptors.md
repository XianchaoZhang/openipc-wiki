# OpenIPC Wiki
[Table of Content](../README.zh.md)

在 FPV 固件中使用 BL-M8812EU2（或其他基于 RTL8812EU）Wi-Fi 模块的说明 
--- 
[LB-LINK 的 BL-M8812EU2 模块](https://www.lb-link.com/product_36_183.html) 
另一款具有高 TX 功率（>29dBm）且低成本的 FPV Wi-Fi 适配器选择。 
![image](https://github.com/libc0607/openipc-wiki/assets/8705034/8aed1797-8f58-4e8f-95d7-b8d055c3519a)


### 硬件 
#### 在哪里购买 
由于 RTL8812EU 芯片比较新，目前 Aliexpress 上还没有卖家。但考虑到它与广泛销售的 BL-R8812AF1 来自同一供应商，它上市只是时间问题。不过，你仍然可以找到任何淘宝代理商并从 [这里](https://item.taobao.com/item.htm?id=764510955987) 购买该模块。

#### 接线 
![image](https://github.com/libc0607/openipc-wiki/assets/8705034/0511de9a-bd3a-42c1-8f35-0f5ec72a1121)  

将```USB2.0+DP```、```USB2.0-DM```用双绞线连接到IPC的USB。还需要将IPC连接到GND。
将```GND```和```VDD5.0```连接到5V/>3A电源为模块供电。
在传输之前，将两个5GHz天线连接到IPEX连接器J0和J1。
引脚9~18为GND，可以悬空。

参考：https://oshwhub.com/libc0607/bl-m8812eu2-demoboard-v1p0

### 将驱动程序添加到 OpenIPC 固件中 
由于闪存空间非常有限，驱动程序默认是禁用的。您需要从源代码编译固件才能启用它。

#### 步骤 1. 准备 
按照 [source-code.md](https://github.com/libc0607/openipc-wiki/blob/master/en/source-code.md) 中的指南进行操作，直到成功构建固件。

#### 步骤 2. 将 BR2_PACKAGE 添加到 Board Config 
在 ```br-ext-chip-*/config``` 中找到您的目标板配置，然后向其中添加 ```BR2_PACKAGE_RTL88X2EU_OPENIPC=y```。如果您的 IPC 有 8M/16M NOR Flash，您可能需要禁用其他驱动程序（例如 RTL8812AU）以节省空间。

#### 步骤3. 检查内核配置中的CONFIG_WIRELESS_EXT 
驱动需要在内核配置中开启```CONFIG_WIRELESS_EXT```，可以在```br-ext-chip-*/board/*/kernel/*-fpv.config```中找到。这个宏已经在SigmaStar中设置，但在使用Hisilicon或GOKE时需要检查它。

#### Step 4. Build the firmware 
```
make
```
#### 步骤 4. 构建固件 
然后您可以将 ```output/images/rootfs.squashfs.*``` 和 ```output/images/uImage.*``` 与 ```sysupgrade``` 一起使用。

### 用法 
它几乎与 RTL8812AU 适配器相同。

#### 设置TX功率 
有两种方法可以设置TX功率。
- ```/etc/wfb.conf``` 中的 ```driver_txpower_override```。范围是 ```0~63```
- ```iw dev <wlan0> set txpower fixed <mBm>```。范围是 ```0~3150```，可以在发射时动态设置。

对于 BL-M8812EU2 模块，我建议将"driver_txpower_override"设置为"40~50"，因为更高的值会导致放大器饱和。当"driver_txpower_override > ~40"时，BL-M8812EU2 模块的功耗可以达到 5V/2.xA。使用合适的 5V 电源和带风扇的散热器。

