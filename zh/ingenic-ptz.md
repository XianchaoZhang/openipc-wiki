
# 君正PTZ配置指南

电机模块包含在默认安装的"openingogenic" repo 中，作为"motor.ko"内核模块。

## 加载模块

使用以下命令加载电机模块（根据硬件需要调整 GPIO，请参阅模块配置）：

```bash
modprobe motor hmaxstep=2540 vmaxstep=720 hst1=52 hst2=53 hst3=57 hst4=51 vst1=59 vst2=61 vst3=62 vst4=63
```

要在启动期间自动执行此过程，请将行"sample_motor hmaxstep=2540 vmaxstep=720 hst1=52 hst2=53 hst3=57 hst4=51 vst1=59 vst2=61 vst3=62 vst4=63"添加到"/etc/modules"。

## 模块配置

- `hstX`：水平电机相位 GPIO 引脚。
- `vstX`：垂直电机相位 GPIO 引脚。
- `hmaxstep` 和 `vmaxstep`：指定硬件可以处理的最大步数。

控制电机

使用"ingenic-motor"命令行实用程序进行电机控制。

## 关于 GPIO 处理的注意事项

- 请注意，根据您的硬件用于电机控制的特定 GPIO，您可能会因为 Ingenic 平台上的 GPIO 处理中断而遇到受限或无法正常工作的移动。
- 此问题可能会在未来的更新中得到解决。

