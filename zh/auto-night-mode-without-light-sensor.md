# OpenIPC Wiki
[Table of Content](../README.md)

没有光传感器的设备上自动夜间模式 
================================

并非所有设备都配有板载光传感器来确定是否应激活夜间模式。对于这些设备，我们可以使用图像传感器的模拟增益值进行切换。在低光条件下，此值会很高，表示图像传感器正在应用增益来提高亮度。在光线充足的条件下，此值会很低。

#### 步骤 1：确定红外截止滤镜是否设置正确 
本文假设您已找到并输入了红外截止滤镜的正确 GPIO 引脚，并且能够使用预览中的“红外截止滤镜”按钮切换滤镜。在日光条件下，在预览中，图像不应为粉红色。如果是粉红色，则很可能是您的引脚顺序错误，需要在“Majestic > 夜间模式”中交换它们。

#### 第 2 步：安装夜间模式脚本 
我们需要 2 个脚本：实际的夜间模式脚本和在启动时启用夜间模式脚本的启动脚本。

[autonight.sh]（https://raw.githubusercontent.com/OpenIPC/device-mjsxj02hl/master/flash/autoconfig/usr/sbin/autonight.sh）

[autonight.sh](https://raw.githubusercontent.com/OpenIPC/device-mjsxj02hl/master/flash/autoconfig/usr/sbin/autonight.sh)

Copy `autonight.sh` to `/usr/sbin`

[S96autonight](https://raw.githubusercontent.com/OpenIPC/device-mjsxj02hl/master/flash/autoconfig/etc/init.d/S96autonight)

Copy `S96autonight` to `/etc/init.d/` and make it executable with `chmod +x /etc/init.d/S96autonight`

#### 步骤 3：调整传感器模拟增益值
在“autonight.sh”中，您将发现 3 个设置：
```
again_high_target=14000
again_low_target=2000
pollingInterval=5
```

“again_high_target”是启用夜间模式的增益值。同样，“again_low_target”是关闭夜间模式的值。您可以更改这些数字以优化您的特定设置。“pollingInterval”表示脚本检查传感器模拟增益值的频率。较低的值将导致更快的响应，但可能会导致对短暂的闪光等的更“紧张”的切换行为。

**注意：**要重新启动 `autonight.sh` 脚本（例如，如果您更改了设置），请使用 `/etc/init.d/S96autonight restart`。要停止脚本（例如，如果您想在不切换 IR 滤镜的情况下观察模拟增益值），请使用 `/etc/init.d/S96autonight stop`。停止脚本后，您可以在终端中手动运行 `/usr/sbin/autonight.sh` 以获取日志输出。

#### 额外：查看传感器模拟增益值和当前夜间模式状态
指标显示在 Web 界面的 `/metrics` 端点。

_The current analog gain value is displayed in `isp_again`:_
```
# HELP isp_again Analog Gain
# TYPE isp_again gauge
isp_again 2880
```

_The current night mode setting displayed in `night_enabled`:_
```
# HELP night_enabled Is night mode enabled
# TYPE night_enabled gauge
night_enabled 0
```
