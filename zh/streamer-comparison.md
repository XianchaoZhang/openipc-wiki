# OpenIPC Wiki
[Table of Content](../README.zh.md)

Comparison of streamers
-----------------------

### 目录

- [主播的主要类型及其存在](/en/streamer-comparison.md#main-types-of-streamers-and-their-presence)
- [主播功能表](/en/streamer-comparison.md#table-of-streamer-functionity)
- [支持的平台和组表](/en/streamer-comparison.md#table-of-supported-platforms-and-groups)


### 主要的彩带类型及其存在

- [Divinus](https://github.com/OpenIPC/divinus) - 全新、开放且积极开发的流媒体，支持多个平台
- [Majestic](https://github.com/OpenIPC/majestic) - 在功能和支持各种平台方面无可争议的领导者
- [Mini](https://github.com/OpenIPC/mini) - 最古老的流媒体，所有其他流媒体的曾祖父
- [Venc](https://github.com/OpenIPC/silicon_research/tree/master/venc) - 适用于两个平台的 FPV/URLLC 微型流媒体


### 流媒体功能表

| Features and capabilities                               | Divinus  | Majestic | Mini     | Venc     |
|:--------------------------------------------------------|:--------:|:--------:|:--------:|:--------:|
| Development of the project in the active phase          | Yes      | Yes      | No       | No       |
| Open source project                                     | Yes      | No       | Yes      | Yes      |
| Limited number of platforms supported                   | No       | No       | Yes      | Yes      |
| IR filter and backlight control                         | Yes      | Yes      | Yes      | No       |
| Freeze detection and Watchdog support                   | Yes      | Yes      | No       | No       |
| Record video to SD card in MP4 format                   | ?        | Yes      | Yes      | No       |
| Getting a JPEG snapshot                                 | Yes      | Yes      | Yes      | No       |
| Sending JPEG snapshots to a remote server               | ?        | No       | Yes      | No       |
| Basic OSD support                                       | Yes      | Yes      | No       | No       |
| Expanded OSD support and dynamic data                   | Yes      | No       | No       | No       |
| MJPEG support                                           | Yes      | Yes      | Yes      | No       |
| Basic RTSP support, H264 UDP                            | Yes      | Yes      | Yes      | No       |
| Expanded RTSP support, H264/H265 and TCP/UDP            | ?        | Yes      | No       | No       |
| Two independent RTSP video streams                      | No       | Yes      | No       | No       |
| RTMP H264/H265 support                                  | No       | Yes      | No       | No       |
| Audio support in all streams                            | No       | Yes      | No       | No       |
| Support for sensor tuning profiles                      | No       | Yes      | No       | No       |
| Motion detection and script launch                      | No       | Yes      | No       | No       |
| Cloud video surveillance support [ipeye.ae](https://ipeye.ae) | No | Yes      | No       | No       |
| Sending steam to YouTube, Facebook, Telegram, etc       | No       | Yes      | No       | No       |
| HLS support                                             | No       | Yes      | No       | No       |
| WebRTC support                                          | No       | Yes      | No       | No       |
| NetIP protocol support for DVRs                         | No       | Yes      | No       | No       |
| Basic ONVIF support, resource search                    | No       | Yes      | No       | No       |
| Support for custom plugins                              | No       | Yes      | No       | No       |
| Setup via WebUI                                         | No       | Yes      | No       | No       |
| Custom ISP settings, a large number of                  | No       | Yes      | No       | No       |
| Custom RTP stream, via UDP post or socket               | No       | Yes      | No       | Yes      |
| Sending telemetry to developers                         | No       | Yes      | No       | No       |
|                                                         |          |          |          |          |
| To be continued, material is being written              |          |          |          |          |


### 支持的平台和群组表

### Table of supported platforms and groups

| Features and capabilities                               | Divinus  | Majestic | Mini     | Venc     |
|:--------------------------------------------------------|:--------:|:--------:|:--------:|:--------:|
| HiSilicon HS3516AV100 group                             | No       | Yes      | No       | No       |
| HiSilicon HS3516CV100 group                             | ?        | Yes      | Part     | No       |
| HiSilicon HS3516CV200 group                             | ?        | Yes      | Yes      | No       |
| HiSilicon HS3516CV300 group                             | Yes      | Yes      | Yes      | No       |
| HiSilicon HS3516CV500 group                             | ?        | Yes      | No       | No       |
| HiSilicon HS3516EV200 group                             | Yes      | Yes      | No       | Yes      |
| HiSilicon HS3519V101 group                              | No       | Yes      | No       | No       |
|                                                         |          |          |          |          |
| To be continued, material is being written              |          |          |          |          |
