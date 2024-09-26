# OpenIPC Wiki
[Table of Content](../README.zh.md)

## 直播至 Telegram

打开您要翻译的频道。开始直播会话。

![](../images/howto-streaming-telegram-1.webp)

从设置中复制服务器 URL 和流密钥。

![](../images/howto-streaming-telegram-2.webp)

在相机上打开"/etc/majestic.yaml"，并将 URL 和密钥添加到配置的"outgoing"部分。

**注意：**它将流式传输 `video0`。**必须**将其配置为视频编解码器：`h264`。

**注意**：不要忘记在参数前添加"-"号！

**注意：**`outgoing` 部分可能会影响其他部分的添加。记住！

![](../images/howto-streaming-telegram-3.webp)
![](../images/howto-streaming-telegram-4.webp)

Restart majestic streamer.

![](../images/howto-streaming-telegram-5.webp)

Enjoy the stream.

![](../images/howto-streaming-telegram-6.webp)
