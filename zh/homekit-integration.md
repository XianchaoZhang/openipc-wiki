[Table of Content](../README.zh.md)

将 OpenIPC 集成到 HomeKit 中 
---

目前 OpenIPC 尚未原生支持 HomeKit，集成由第三方软件包 [go2rtc](https://github.com/AlexxIT/go2rtc "go2rtc") 完成，感谢 [@gtxaspec](https://github.com/gtxaspec "@gtxaspec") 将此软件包添加到 OpenIPC

### 硬件要求

由于编译后的二进制文件大小为 3.3MB，因此闪存芯片大小至少应为 16MB，DDR 大小至少应为 128MB

### 编译 go2rtc 包

首先安装 go 和 upx
```
apt update
apt install golang upx
```
- #### 方法 1：编译整个固件 
在 `firmware/br-ext-chip-xxxx/configs` 目录中编辑板配置文件，添加以下行以启用 go2rtc 包，
```
BR2_PACKAGE_GO2RTC=y
```
然后运行
```
make distclean
make all BOARD=board_config_name
```
- #### 方法 2：仅编译包 
```
make distclean
make br-go2rtc-rebuild BOARD=board_config_name
```


编译后的 go2rtc 二进制文件将位于 `firmware/output/per-package/go2rtc/target/usr/bin`，默认配置文件位于 `firmware/output/per-package/go2rtc/target/etc`

### 编辑配置文件

将配置文件"go2rtc.yaml"放入"/etc/"目录，示例配置：

```
log:
  level: info  # default level
  api: trace
  exec: debug
  ngrok: info
  rtsp: warn
  streams: error
  webrtc: fatal

rtsp:
  listen: ":8553"

webrtc:
  candidates:
    - stun:8555

streams:
  openipc: rtsp://admin:12345@127.0.0.1/stream=0

homekit:
  openipc:                   # same stream ID from streams list
    pin: 19550224           # custom PIN, default: 19550224
    name: openipc-ssc30kq      # custom camera name, default: generated from stream ID
    device_id: openipc       # custom ID, default: generated from stream ID
```

### 运行 go2rtc

```
/usr/bin/go2rtc -config /etc/go2rtc.yaml &
```

在您的 Apple 设备上打开 Home 应用程序，单击右上角的"+"按钮，OpenIPC 摄像头应该会自动出现在那里，在配置文件中输入引脚号码以与其配对。

### 启动时自动运行

在 /etc/rc.local 中添加以下行

```
/usr/bin/go2rtc -config /etc/go2rtc.yaml &
```

### 限制

- 尚不支持 HomeKit 安全视频
- 尚不支持运动传感器
- 尚不支持双向音频

