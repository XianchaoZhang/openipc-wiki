# OpenIPC Wiki
[Table of Content](../README.md)

Ingenic SoC 研究和调试笔记 
----------------------------------------

#### 使用 OpenIPC 附带的"ingenic-pwm"实用程序控制 ingenic 设备上的 PWM 通道：

```
INGENIC PWM Control Version: Oct 19 2023_18:01:16_latest-2294-g72f266e7
Usage: ingenic-pwm [options]

Options:
  -c, --channel=<0-7>            Specify PWM channel number
  -q, --query                    Query channel state
  -e, --enable                   Enable channel
  -d, --disable                  Disable channel
  -p, --polarity=<0|1>           Set polarity (0: Inversed, 1: Normal)
  -D, --duty=<duty_ns>           Set duty cycle in ns
  -P, --period=<period_ns>       Set period in ns
  -r, --ramp=<value>             Ramp PWM (+value: Ramp up, -value: Ramp down)
  -x, --max_duty=<max_duty_ns>   Set max duty for ramping
  -n, --min_duty=<min_duty_ns>   Set min duty for ramping
  -h, --help                     Display this help message
```

示例命令：
打开 LED，调暗：设置 PWM 通道 3 启用，周期为 1000000，最小占空比为 0，最大占空比为 1000000，斜率 + 调暗，- 调暗

`ingenic-pwm-c 3-e-p 1-P 1000000-n 0-x 1000000-r 50000` `ingenic-pwm-c 3-e-p 1-P 1000000-n 0-x 1000000-r -50000`

---

#### 启用完整工程调试

启用：运行"switch_debug on"以启用调试 
禁用：运行"switch debug off"或"switch_debug"以抑制调试输出

启用将在 dmesg 中启用**FULL**工程调试输出。

---

#### 动态调试

Ingenic 平台的 Linux 内核已启用动态调试，以抑制过多的工程调试。

https://www.kernel.org/doc/html/v4.14/admin-guide/dynamic-debug-howto.html

首先挂载 debugfs：`mount -t debugfs none /sys/kernel/debug`

检查条目：'cat /sys/kernel/debug/dynamic_debug/control'

示例输出：

```
../ingenic-opensdk/kernel/avpu/t31/avpu_main.c:314 [avpu]write_reg =_ "Out-of-range register write: 0x%.4X\012"
../ingenic-opensdk/kernel/avpu/t31/avpu_main.c:304 [avpu]write_reg =_ "Reg write: 0x%.4X: 0x%.8x\012"
../ingenic-opensdk/kernel/avpu/t31/avpu_main.c:302 [avpu]write_reg =_ "Reg write: 0x%.4X: 0x%.8x\012"
../ingenic-opensdk/kernel/avpu/t31/avpu_main.c:290 [avpu]read_reg =_ "Reg read: 0x%.4X: 0x%.8x\012"
../ingenic-opensdk/kernel/avpu/t31/avpu_main.c:234 [avpu]wait_irq =_ "Unblocking channel\012"
../ingenic-opensdk/kernel/avpu/t31/avpu_ip.c:128 [avpu]avpu_hardirq_handler =_ "ENOMEM: Missed interrupt\012"
../ingenic-opensdk/kernel/avpu/t31/avpu_ip.c:117 [avpu]avpu_hardirq_handler =_ "bitfield is 0\012"
/mnt/mips/external_utilities/OpenIPC/openingenic/kernel/sensors/t31/gc2053/gc2053.c:1860 [sensor_gc2053_t31]gc2053_probe =p "probe ok ------->gc2053\012"
/mnt/mips/external_utilities/OpenIPC/openingenic/kernel/sensors/t31/gc2053/gc2053.c:1422 [sensor_gc2053_t31]gc2053_s_stream =p "gc2053 stream off\012"
/mnt/mips/external_utilities/OpenIPC/openingenic/kernel/sensors/t31/gc2053/gc2053.c:1415 [sensor_gc2053_t31]gc2053_s_stream =p "gc2053 stream on\012"
/mnt/mips/external_utilities/OpenIPC/openingenic/kernel/sensors/t31/gc2053/gc2053.c:1288 [sensor_gc2053_t31]gc2053_detect =p "-----%s: %d ret = %d, v = 0x%02x\012"
/mnt/mips/external_utilities/OpenIPC/openingenic/kernel/sensors/t31/gc2053/gc2053.c:1282 [sensor_gc2053_t31]gc2053_detect =p "-----%s: %d ret = %d, v = 0x%02x\012"
```

`=_` 表示调试输出被 `禁用`，而 `=P` 则表示调试输出被 `启用`。

检查 `dmesg` 的输出

Note:  Some old kernel modules may complain about missing symbols relating to dynamic debugging:
```
[    4.357160] sample_core: Unknown symbol __dynamic_dev_dbg (err 1)
[    4.361299] sample_hal: Unknown symbol __dynamic_dev_dbg (err 1)
```
注意：一些旧的内核模块可能会抱怨缺少与动态调试相关的符号：要解决此问题，请确保将整个 OpenIPC 安装更新到 2023 年 10 月 20 日之后的最新版本，或尝试更新遇到问题的各个内核模块。作为最后的补救措施，您还可以在内核配置中禁用"CONFIG_DYNAMIC_DEBUG"，但广泛的测试并未表明这是一个问题。

---

#### 动态改变传感器时钟速率

`echo "30000000" > /proc/jz/clock/cgu_cim/rate` 这可用于更改图像传感器的 MCLK 时钟速率设置。您可以使用它来获得更多带宽，以获得更高的 FPS 速率分辨率。

---

#### 动态插入或移除 SDIO 设备

在系统启动后使用这些命令来启用或禁用 SDIO 设备。

`echo "插入" > /sys/devices/platform/jzmmc_v1.2.X/present` `echo "删除" > /sys/devices/platform/jzmmc_v1.2.X/present`

其中 X = 您想要控制的 MMC 设备 MSC0=0 MSC1=1

---

### IMP 控制

IMP-Control 是一款多功能工具，用于控制和配置 Majestic 流媒体中 Ingenic IMP 库中的各种参数。此工具允许对音频和视频设置进行微调，以在各种环境中实现最佳性能。

#### 主要特性和功能
1. **音频调整：**
   - `aihpf`：高通滤波器切换（开/关）。
   - `aiagc`：自动增益控制，具有可调增益级别和补偿。
   - `ains`：具有强度级别的噪音抑制。
   - `aiaec`：回声消除切换。
   - `aivol` 和 `aovol`：分别调整音频输入和输出音量。
   - `aigain` 和 `aogain`：设置音频输入和输出增益。
   - `aialc`：音频输入自动电平控制增益设置。

2. **视频增强功能**：
   - `flip`：调整图像方向。
   - `contrast`、`brightness`、`saturation`、`sharpness`：调整基本图像质量。
   - `sinter`、`temper`：通过调整烧结和回火强度来增强图像。
   - `aecomp`：自动曝光补偿。
   - `dpc`、`drc`：控制 DPC 和 DRC 强度。
   - `hilight`：调整高光强度。
   - `again`、`dgain`：设置模拟和数字增益。
   - `hue`：修改色调。
   - `ispmode`：在白天和夜间模式之间切换。
   - `flicker`：防闪烁设置。

3. **高级控制：**
   - `whitebalance`：使用模式、红色和蓝色增益调整白平衡。
   - `sensorfps`：配置传感器每秒帧数。
   - `backlightcomp`：背光补偿强度。
   - `defogstrength`：控制除雾强度以获得更清晰的图像。
   - `framerate`、`gopattr`：管理帧速率和图片组 (GOP) 属性。
   - `setbitrate`、`setgoplength`、`setqp`、`setqpbounds`、`setqpipdelta`：详细的编码器设置。
   - `rcmode`：设置或获取速率控制模式。
   - `aemin`：设置自动曝光最小参数。
   - `autozoom`、`frontcrop`：调整缩放和裁剪设置。
   - `mask`：设置用于隐私或强调的屏蔽参数。

4. **OSD 和分析：**
   - `getosdattr`、`getosdgrpattr`：检索屏幕显示 (OSD) 属性。
   - `getgamma`、`getevattr`、`getaeluma`、`getawbct`、`getafmetrics`、`gettotalgain`、`getaeattr`：获取各种图像处理指标。

5. **系统信息：**
   - `getimpversion`：获取 IMP 版本。
   - `getcpuinfo`：检索 CPU 信息。

6. **演示和测试：**
   - `full_demo`：全面演示各种功能。

#### 如何使用 
一般使用语法是： 
```
imp-control [command] [parameters]
```
参数根据命令而有所不同，在某些情况下，它们是可选的，以检索当前值。

要获取帮助并查看可用命令列表，请使用：
```
imp-control help
```
---

