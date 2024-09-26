# OpenIPC Wiki
[Table of Content](../README.md)

路线图 
-------

### 快速访问热门存储库的 Git 日志：

- [固件](https://github.com/OpenIPC/firmware/commits/master)、[生成器](https://github.com/OpenIPC/builder/commits/master)、[耦合器](https://github.com/OpenIPC/coupler/commits/main)
- [ipctool](https://github.com/OpenIPC/ipctool/commits/master)、[webui](https://github.com/OpenIPC/webui/commits/master)、[webui-next](https://github.com/OpenIPC/webui-next/commits/master)、[wiki](https://github.com/OpenIPC/wiki/commits/master)

### 计划变更：

- 添加 webui-next 作为默认界面。
- 更好地与 Majestic 和 webui-next 集成。
- 继续重构固件脚本和包。
- 扩展构建器支持。
- 扩展文档支持。
- 扩展 majestic-plugins 支持。
- 将 buildroot 更新为即将推出的 2024 版本。
- 更新 wiki 构建命令。
- 更新 wiki majestic 命令。

### 19.02.2024:
- [hisilicon/goke] 修复多路复用不需要的 GPIO 问题，这些问题会烧坏 XM 板上的 IRCUT

### 12.02.2024：
- 添加了一项增强功能，允许 Majestic 运行与 sdk 分离的 cgi 脚本。
- 修复了阻止重新绑定 rtsp 端口的问题。
- 修复了 Majestic Web 服务器主目录的问题。
- 修复了编解码器选择上的异常。
- 如果 majestic.yaml 不可用，则提高稳定性。
- 缩短了 Ingenic 设备上 Majestic 关闭的时间范围。

### 05.02.2024:
- 为 webui-next 添加了 mjpeg 预览。
- 为 Ingenic 添加了 mjpeg 支持。
- 添加了到 Majestic webserver 的端口 85 重定向。
- 为 webui-next 调整了几个 Majestic 配置设置。
- 修复了 webui-next 上的夜间模式控制。
- 修复了反转单个 ircut 的问题。
- 将 Majestic 设置为 webui-next 的唯一 webserver。
- 将 majestic-webui 设置为 Sigmastar 设备的默认设置。
- 注意：majestic-webui 扩展目前不可用。

### 2024 年 1 月 29 日：
- 为各种新的索尼传感器添加了 ipctool 支持。
- 在启动和停止 majestic-plugins 时添加了其他命令。
- 添加了各种新的别名命令 (show_help)
- 添加了实验性的 Majestic IP6 支持。
- 添加了检查以防止重复执行夜间模式设置。
- 用 makefile 替换 building.sh。
- 重构了几个固件脚本和软件包 makefile。

### 2024 年 1 月 22 日：
- 添加了 T40/T41 芯片组检测。
- 添加了对 Sigmastar fpv 的音频支持。
- 添加了可选的夜间色彩模式。
- 修复了几个 Majestic 稳定性问题。
- 修复了 Ingenic 在 sighup/reload 上运动检测的问题。
- 提高了 Majestic 网络服务器的稳定性。
- 将 gkrcparams 包含在 majestic 配置中。
- 更新了 libevent 库和工具链。
- 更新了 Ingenic 供应商库。

### 15.01.2024：
- 为 ircut 和 light 添加了单独的控制切换。
- 添加了基于传感器的日/夜检测。
- 为各种端点添加了身份验证。
- 添加了对 Ingenic 注册信息扫描的初始支持。
- 修复了 Majestic 无法正确重新打开 udp 套接字的问题。
- 修复了 Ingenic T10/T20 rtsp 流的问题。
- 修复了设置正确的海思编码器比特率的问题。

### 08.01.2024：
- 为 Ingenic 设备添加了 Majestic sighup 支持。
- 为 rtmp 添加了 h265 支持。
- 添加了 rtmp 重新连接选项。
- 添加了对 Sigmastar 注册信息扫描的支持。
- 修复了几个 rtmp 身份验证问题。
- 修复了一些 Sigmastar 传感器驱动程序上的旋转问题。
- 删除了基于 Ingenic 软件的日/夜检测。

