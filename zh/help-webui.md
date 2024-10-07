# OpenIPC Wiki
[目录](../README.zh.md)

帮助：Web UI 
------------

### 从 Web UI 更新 Web UI。

在某些情况下，尤其是当您认为某些功能无法正常工作时，请尝试再次更新 Web UI，并在第二次更新时覆盖版本检查。这可能是因为我们可能对更新例程进行了一些更改而必须这样做，因此您应在第一次更新时检索更新例程代码，然后将其用于后续更新。

### Web UI 开发。

如果您想帮助我们开发固件的 Web 界面，以下是您需要事先了解的内容。相机在空间和性能方面非常有限。目前固件中唯一适合动态生成 HTML 页面的选项是 `haserl`，它是 `ash` 的花哨表亲，用于充当 CGI 包装器。我说的是 `ash` 吗？没错，因为我们的 Linux 中没有 `bash`、`tcsh`、`zsh`。它就是 Ash。就像 A shell，全名为 Almquist shell。小巧、轻便，而且有点受限。系统也很受限 —— 在大多数情况下它是 `busybox`。因此，如果您仍然愿意尝试 80 年代风格的 Web 开发，那么欢迎加入。

最近的界面是围绕 [Bootstrap](https://getbootstrap.com/) CSS 框架构建的，虽然有点过度，但让我们能够从最初的微生物网络快速发展到现在的样子。我们希望精简原始的 Bootstrap 包，并创建一个仅包含我们使用的功能的自定义包。如果你拥有这样的技能，那就来和我们一起工作吧。

此外，我们正在考虑切换到客户端 Web 界面构建器的可能性，只将数据抛给服务器。Vue.js 或类似。有什么要补充的吗？说出来。

还有其他想法吗？我们也想听听。

### 运行 Web UI 的开发版本。

要开始改进 Web 界面，请在本地克隆其 GitHub 存储库，并在相机上设置 NFS 挂载到本地副本的根目录：

```bash
mkdir -p /tmp/dev
mount -t nfs -o nolock,tcp 192.168.1.123:/full/path/to/web/files /tmp/dev
```

> _（将 192.168.1.123 和 /full/path/to/web/files 替换为你自己的 IP 和路径）_

然后启动 httpd 守护进程的另一个实例，在摄像机的另一个端口（比如说端口 86）上为您的 Web UI 版本提供服务：

```bash
httpd -p 86 -h "/tmp/dev/var/www" -c /dev/null
```

现在，您可以在工作站上您最喜欢的 IDE 或文本编辑器中处理 Web UI 源代码，并立即使用指向相机上端口 86 的 Web 浏览器测试更改。_（例如 http://192.168.1.10:86/）_

请记住，您仅替换 Web 服务器内容，但 Web 目录之外还存在支持脚本。如果您对这些脚本进行更改，则可能需要在相机上更新这些脚本。要更新相机上的脚本，请打开相机的 ssh 会话，并将脚本的更新版本从"/tmp/dev/usr/sbin/"复制到"/usr/sbin/"。
