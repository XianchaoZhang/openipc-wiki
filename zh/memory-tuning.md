# OpenIPC Wiki
[Table of Content](../README.zh.md)

海思开发板 
----------------

### 禁用不使用的子系统

供应商内核模块占用大约 5 兆字节的 RAM（带有用于缓冲区的动态内存的代码），其中一些非常无用，您需要特定的功能，如 OSD、运动检测、音频支持或 H264/265/JPEG 编解码器。

| Feature                               | Modules                                                                                                 | Size |
|---------------------------------------|---------------------------------------------------------------------------------------------------------|------|
| Audio output                          | hi3516ev200_ao, hi3516ev200_adec                                                                        |      |
| Audio input                           | hi3516ev200_ai, hi3516ev200_aenc                                                                        |      |
| Audio support (both input and output) | hi3516ev200_acodec, hi3516ev200_adec, hi3516ev200_aenc, hi3516ev200_ao, hi3516ev200_ai, hi3516ev200_aio |      |
| I2C sensor support                    | hi_sensor_i2c                                                                                           |      |
| SPI sensor support                    | hi_sensor_spi                                                                                           |      |
| PWM support                           | hi_pwm                                                                                                  |      |
| Motion detection                      | hi3516ev200_ive                                                                                         |      |
| JPEG snapshots                        | hi3516ev200_jpege                                                                                       |      |
| H.264 codec support                   | hi3516ev200_h264e                                                                                       |      |
| H.265 codec support                   | hi3516ev200_h265e                                                                                       |      |
| OSD support                           | hi3516ev200_rgn                                                                                         |      |

