# OpenIPC Wiki
[目录](../README.zh.md)

## 基本信息

OpenIPC 是 IP 摄像机的替代 [固件](https://github.com/OpenIPC)，也是我们系统组件中其他项目的一种保护伞。

OpenIPC 是一个基于 Buildroot/OpenWrt 项目的 Linux 操作系统，针对来自不同供应商的芯片组的 IP 摄像机，首先是 Goke GK72xx、HiSilicon Hi35xx、SigmaStar SSC33x 和 XiongmaiTech XM5xx。

欢迎任何人以他们认为有用的任何方式使用/贡献该项目！

我们将非常感激任何反馈和建议。


## 官方链接

* [OpenIPC on Wiki](https://github.com/openipc/wiki) - 各种文档和说明的集合。
* [OpenIPC on GitHub](https://github.com/OpenIPC/) - GitHub 组织，大多数项目都托管在这里。
* [OpenIPC on OpenCollective](https://opencollective.com/openipc) - OpenCollective 社区。
* [OpenIPC on Twitter](https://twitter.com/openipc) - 我们的主要新闻。
* [OpenIPC on YouTube](https://www.youtube.com/channel/UCaXlbR2uGTRFh8jQ2lCFd2g) - 我们的说明和流程（在计划中）。


## 电报聊天组

* [OpenIPC](https://t.me/openipc) (EN) - 讨论我们项目开发的国际频道，[*](https://combot.org/c/1166652144)
* [OpenIPC 改装](https://t.me/openipc_modding) (RU) - 修改 IPCam 设备固件的一般问题，[*](https://combot.org/c/-1001247643198)
* [OpenIPC 开发](https://t.me/openipc_software) (RU) - HiSilicon、XM 在 OpenWrt 中的移植和编程器问题，[*](https://combot.org/c/-1001196905312)
* [OpenIPC 建议](https://t.me/openipc_advice) (RU) - 问题、寻找解决方案、长时间对话， [*](https://combot.org/c/1385065634)
* [OpenIPC Iranian](https://t.me/joinchat/T_GwQUBTJdfXJrFb) (IR) - 伊朗用户特别群组 / تیم OpenIPC برای کاربران ایرانی, [*](https://combot.org/c/-1001341239361)
* [OpenIPC demo](https://t.me/openipc_demo) (EN/RU) - 带按钮的测试机器人，连接后，给出"/menu", [*](https://combot.org/c/1414887196)
* [OpenIPC ExIPCam](https://t.me/ExIPCam) (RU) - ExIPCam 程序和硬件/软件修复设备, [*](https://combot.org/c/1213889378)
* [OpenIPC 更新](https://t.me/s/openipc_updates) (RU) - 固件和软件更新信息频道
* [OpenIPC dev](https://t.me/s/openipc_dev) - 固件和软件开发频道


## 团队发展

### 固件

* [openipc-2.x](https://github.com/openipc/firmware) - 基于 Buildroot 的固件开发和创建系统。
* [openipc-1.0][chaos_calmer] - 基于 OpenWrt 15.05 的固件开发和创建系统。
* [coupler][coupler] - 摄像机固件之间的无缝过渡。

### Streamers

* [majestic](https://openipc.org/majestic-endpoints) - 通用 IPCam 流媒体。
* [mini][mini] - 开源迷你 IP 摄像头流媒体。

### 工具

* [ipctool](https://github.com/openipc/ipctool) - 用于检查 IP 摄像头硬件的工具（和库）。
* [yaml-cli][yaml-cli] - 用于在 CLI 中更改设置的工具。
* [glutinium](https://github.com/ZigFisher/Glutinium) - 附加 OpenWRT 软件包。

### Windows 软件

* [exipcam](http://team.openipc.org/exipcam) - 用于修复 IPCam 的超酷实用程序 (适用于 Windows，可通过 Wine 在 Linux 上运行)。
* [ipcam_dms](http://team.openipc.org/ipcam_dms) - IPCam 设备管理系统 (适用于 Windows，可通过 Wine 在 Linux 上运行)。



## Developers

| Name                                                             | Role                                                   | Participation                                                            |
|------------------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------------------------|
| [Dmitry Ilyin](https://web.telegram.org/#/im?p=@widgetii)        | OpenIPC 项目联合创始人及主要参与者	 | [ipctool][ipctool], [majestic][majestic], [mini][mini], [motors][motors] |
| [Dmitry Ermakov](https://web.telegram.org/#/im?p=@dimerrr)       | 主要参与者                                      | [coupler][coupler], [firmware][firmware], [ipctool][ipctool]             |
| [Igor Zalatov](https://web.telegram.org/#/im?p=@FlyRouter)       | **项目创始人和开发协调员**        | [chaos_calmer][chaos_calmer], [firmware][firmware], [wiki][wiki]         |
| [Ivan Pozdeev](https://web.telegram.org/#/im?p=@John)            | developer                                              | [microbe-web][webui], [yaml-cli][yaml-cli]                               |
| [Konstantin](#)                                                  | developer                                              | [hisi-trace][hisi-trace], [yaml-cli][yaml-cli]                           |
| [Maksim Patrushev](https://web.telegram.org/#/im?p=@maxi380)     | developer                                              | [motors][motors]                                                         |
| [Maxim Chertov](https://web.telegram.org/#/im?p=@mAX3773)        | OpenIPC 项目联合创始人                      | [chaos_calmer][chaos_calmer], [ipctool][ipctool], [mini][mini]           |
| [Paul Philippov](https://web.telegram.org/#/im?p=@themactep)     | 主要参与者                                       | [microbe-web][webui]                                                     |
| [Sergey Sharshunov](https://web.telegram.org/#/im?p=@USSSSSH)    | OpenIPC 项目联合创始人                      | [chaos_calmer][chaos_calmer], [burn][burn]                               |
| [Temirkhan Myrzamadi](https://web.telegram.org/#/im?p=@hirrolot) | 主要参与者                                       | [smolrtsp][smolrtsp]                                                     |
| [Vasiliy Yakovlev](https://web.telegram.org/#/im?p=@#)           | 总协调员                                    |                                                                          |


OpenIPC 提供两个级别的支持。

- 通过社区提供免费支持（通过 [聊天](https://openipc.org/#telegram-chat-groups) 和 [邮件列表](https://github.com/OpenIPC/firmware/discussions)）。
- 付费商业支持（来自开发团队）。

如果您打算将我们的产品用于商业用途，请考虑订阅付费商业支持。作为付费客户，您将直接从我们技术精湛的团队获得技术支持和维护服务。您的错误报告和功能请求将得到优先关注和快速解决方案。这是双方共赢的策略，有助于您的业务稳定，并帮助核心开发人员全职从事项目工作。

如果您对我们的项目有任何具体问题，请随时[联系我们](mailto:flyrouter@gmail.com).

### 参与与贡献

如果您喜欢我们的工作，并且愿意加强发展，请考虑参与。

您可以改进现有代码并向我们发送补丁。您可以添加我们的代码中缺少的新功能。

您可以帮助我们编写更好的文档，校对和修改我们的网站。

您只需捐献一些钱来承担开发和长期维护的成本，我们相信这将为像您这样的用户提供最稳定、最灵活、最开放的 IP 网络摄像机框架。

您可以在 [Open Collective](https://opencollective.com/openipc/contribute/backer-14335/checkout) 为该项目提供资金捐助。

谢谢。

<p style="text-align:center">
<a href="https://opencollective.com/openipc/contribute/backer-14335/checkout" target="_blank"><img src="https://opencollective.com/webpack/donate/button@2x.png?color=blue" width="375" alt="Open Collective donate button"></a>
</p>


[burn]: https://github.com/OpenIPC/burn
[chaos_calmer]: https://github.com/OpenIPC/chaos_calmer
[coupler]: https://github.com/OpenIPC/coupler
[firmware]: https://github.com/OpenIPC/firmware
[hisi-trace]: https://github.com/OpenIPC/hisi-trace
[ipctool]: https://github.com/OpenIPC/ipctool
[majestic]: https://github.com/OpenIPC/majestic
[mini]: https://github.com/OpenIPC/mini
[motors]: https://github.com/OpenIPC/motors
[smolrtsp]: https://github.com/OpenIPC/smolrtsp
[webui]: https://github.com/OpenIPC/microbe-web
[wiki]: https://github.com/wiki
[yaml-cli]: https://github.com/OpenIPC/yaml-cli
