# OpenIPC Wiki
[Table of Content](../README.md)

配置要求 
--------------------------

为防止因临时断线而导致直播意外终止，请将直播的开始日期安排在较远的未来（例如当年的 12 月 31 日）。直播时，它会运行顺畅，断开摄像头连接，然后继续直播。

### HLS + H.265

关注[通过 HLS 交付内容](https://developers.google.com/youtube/v3/live/guides/hls-ingestion)以了解更多信息。

### 创建新流

- 导航至 <https://developers.google.com/youtube/v3/live/code_samples> 页面。
- 选择"liveStreams"作为资源，选择"插入"作为方法。
- 在下表中，单击"插入"用例。
- 在页面右侧：
  - 在"cdn"对象中，将"frameRate"从"60fps"更改为"variable"；
  - 将"resolution"从"1080p"更改为"variable"；
  - 将"ingestionType"从"rtmp"更改为"hls"：

```json
"cdn": {
  "ingestionType": "hls",
  "frameRate": "variable",
  "resolution": "variable"
}
```

- 在"凭据"部分，确保您已选择"Google OAuth 2.0"和"https://www.googleapis.com/auth/youtube"范围（使用"显示范围"）并取消选择"API 密钥"选项，然后按下面的"执行"按钮。
- 使用您的 YouTube 关联帐户授权自己。
- 确保您收到 200 响应，否则请检查错误并重复。当您的帐户中之前未启用 [live-streaming](https://support.google.com/youtube/answer/2474026?hl=en) 时，会出现小错误。
- 从响应中保存"channelId"（看起来像"UCPJRjbxYlq6h2cCqy8RCRjg"）。


### 创建新的广播：

- 导航至 <https://developers.google.com/youtube/v3/live/code_samples> 页面。
- 选择"liveBroadcast"作为资源，选择"insert"作为方法。
- 在下表中，单击"插入"用例。
- 在页面右侧：
- 广播的"title"字段，如"My Hometown Camera"
- "scheduledStartTime"如"2020-04-21T00:00:00.000Z"（确保此时间在未来），
- "scheduledEndTime"如"2020-04-21T01:00:00.000Z"（预定结束时间应在预定开始时间之后）
- 还请按下"snippet"块内的蓝色加号按钮，并使用给定的流步骤值添加"channelId"

```json
"snippet": {`
  `"title": "My Hometown Camera",`
  `"scheduledStartTime": "2021-04-12T00:00:00.000Z",`
  `"scheduledEndTime": "2021-04-13T00:00:00.000Z",`
  `"channelId": "MCpZqkqqEZw806aGGHUdepIl"`
`},
```

- 在"凭据"部分，确保您已选择"Google OAuth 2.0"和"https://www.googleapis.com/auth/youtube"范围（使用"显示范围"）并取消选择"API 密钥"选项，然后按下面的"执行"按钮。
- 使用您已连接的 YouTube 帐户授权自己。
- 确保您收到 200 响应，否则请检查错误并重复。


### 将广播绑定到流：

- 导航至 <https://developers.google.com/youtube/v3/live/code_samples> 页面。
- 选择"liveBroadcast"作为资源，选择"bind"作为方法。
- 在下表中，单击"将广播绑定到流"用例。
- 在页面右侧：
- "id" - 广播的 ID（可在步骤"创建新广播"的服务器响应中找到，字段"id"）
- "streamId" - 流的 ID（可在步骤"创建新流"的服务器响应中找到，字段"id"）
- 在"凭据"部分，确保您已选择"Google OAuth 2.0"和"https://www.googleapis.com/auth/youtube"范围（使用"显示范围"）并取消选择"API 密钥"选项，然后按下方的"执行"按钮
- 使用您的 YouTube 关联帐户授权自己。
- 确保您收到 200 响应，否则检查错误并重复。


### 开始直播！

导航至<https://studio.youtube.com/>。

在右侧，单击"创建"按钮，然后单击"上线"。


（c）Victor，来源：https://github.com/OpenIPC/camerasrnd/blob/master/streaming/youtube.md

