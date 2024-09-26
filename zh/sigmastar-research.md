# OpenIPC Wiki
[Table of Content](../README.md)

SigmaStar SoC研究和调试笔记 
------------------------------------------

#### 控制 SigmaStar SSC338Q 上的 PWM 通道

Switch GPIO1 to PWM1 mode and set parameters
```
devmem 0x1F207994 16 0x1121 
echo 1 > /sys/class/pwm/pwmchip0/export
echo 10000 > /sys/class/pwm/pwmchip0/pwm1/period
echo 25 > /sys/class/pwm/pwmchip0/pwm1/duty_cycle
echo 1 > /sys/class/pwm/pwmchip0/pwm1/enable
```
