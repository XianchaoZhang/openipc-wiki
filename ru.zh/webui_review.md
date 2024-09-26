# OpenIPC webui 审查

OpenIPC 中可用的 Web 界面简史

## 旧版_webui

存储库：https://github.com/openipc/webui

它被创建为一个基本的、非常简单的界面，其中基本设置不仅可以在控制台中应用。它主要与内置的简单 httpd 相关，该 httpd 包含在 busybox 软件包中并在端口 85 上工作。对于 CGI 脚本，使用 haserl，因为它简单且微型。

Majestic中出现了很多功能之后，也由于不同处理器上发射能力的扩展，需要使用可以直接从流光器获取的参数。在 Legacy 中，您经常需要解析变量的输出并启用某些功能。

另外，这个界面包含了大量许多用户喜欢的插件，但这也起到了不好的作用，界面变得有缺陷和笨拙。决定制作另一个已经与流媒体绑定的界面。

## majestic_webui

存储库：https://github.com/openipc/majestic-webui

下一版本的界面已经与流光器紧密配合，许多参数在流光器、配置和显示部分之间自动读取和调整。同样在这种模式下，HTTP服务器的所有功能都只交给了majestic，它可以使用ssl和许多其他功能。

唯一的缺点是没有需要清理、修改和转移的插件。这是正确的方法，但一个接口运行在另一个接口之上的事实只是爱好者的一种解决方案。

## FancyWeb

存储库：https://github.com/openipc/fancyweb

我们还有一个华丽的界面，但没有实现，只是因为当时它有点"胖"，而且流媒体中缺乏 API 功能。用 ReactJS 编写。

有一个出色的演示文稿可以让您熟悉各种可能性：https://github.com/OpenIPC/fancyweb/blob/master/presentation/OpenIPC_fancyweb_interface_rus.pdf

## 4th_generaton_webui

存储库：尚未准备好供公众访问

该界面正在开发中，结合了之前三个界面的功能，将紧凑、时尚、稳定、基于现代框架等。

