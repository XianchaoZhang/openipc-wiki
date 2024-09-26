# OpenIPC Wiki 
[内容](../README.zh.md)

我想帮忙！我应该怎么办？ 
----------------------------

我们不会拒绝任何帮助，即使是最小的帮助。该项目既需要那些能够阅读原理图、知道如何反转封闭驱动程序的人，也需要那些能够看到并纠正拼写错误、编写说明、画出美丽的图画，甚至可能创作一首优美的歌曲的人:)

### 我对代码进行了更改。我怎样才能把它发送给你？

为了使用代码，我们使用版本控制系统 [Git][gitdoc] 和位于 GitHub 服务上的存储库。

如果您在 GitHub 上有帐户，那么一切都很简单：在您的帐户中复制我们的代码（fork）。

![GitHub Fork](../images/gh-fork.webp) 对您的副本进行编辑，然后提交更改的拉取请求。

![GitHub 贡献](../images/gh-contribute.webp)

如果由于某种原因您不使用 GitHub，您仍然可以向我们发送您的代码更改。为此，请制作相应项目存储库的本地副本。

```
git clone git@github.com:OpenIPC/firmware.git
```
传输您对其所做的更改。将您所做的更改写入存储库的本地副本。然后创建一个补丁文件并将其发送给我们 <dev@openipc.org>。

```
git format-patch master
```

### 小更正和拼写错误。

使用 GitHub 处理小修复甚至更容易。  注意到错字了吗？你想出了更好的措辞吗？注意到链接损坏了吗？只需单击这个类似铅笔的按钮即可进行更正。

![GutHub 校正](../images/gh- Correction.webp)

[gh-signup]: https://github.com/signup
[gitdoc]: https://git-scm.com/book/ru/v2
