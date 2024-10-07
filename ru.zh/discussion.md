# OpenIPC Wiki 
[内容](../README.zh.md)

讨论主题 
------------------

### 计划

* 在文件中构建时自动指定处理器和配置文件名称
* `/etc/hostname` 并且始终可以通过 `/rom/..` 访问
  * 对于 `..._${platform}_unknown_defconfig` 板，未指定主机名。
  * `..._gk7205v200_fpv_defconfig` 有不同的主机名 (@p0isk)
* 添加开关到`sysupgrade`来升级web-ui和majesty。

### 安全

* 首次登录Web UI时，提示用户（持续）更改密码，以免遇到CVE。
  * 完成（@p0isk，@themactep）。
* 首次通过SSH登录时，提示用户（持续）更改密码，以免遇到CVE。
* 使用 /etc 中的标准 passwd/shadow 实现 httpd 授权的集成。

### 核心统一

* 在所有内核中启用 ROOT_NFS 和 PNP_DHCP 选项。

### 系统更新

#### 核：

* 通过 mkimage 添加处理器名称，例如 `Linux-4.9.37-hi3516ev200`
  * 完成（@p0isk）。
* 检查它和日期以检查是否适合续订。
  * 完成（@p0isk）。

### 雄伟更新

* 仅更新和检查二进制和缩短的配置。
* 有"恢复设置"按钮，是否需要"恢复流媒体"？
* 除了 ETag 之外，您还可以使用 Last-Modified。

#### 开发分支

* 显示提交编号字段。如果为空，则取最后一个。
  * 无关紧要？ （@p0isk）。

项目存储库设计建议集
------------------------------------------ ----- --------

### 来自@themactep 的建议

* 从源文件的自述文件中删除动态图形元素（徽章）的链接。
* 用于在终端中读取的 Markdown Markdown 文件，字段宽度不超过 80 个字符。
* 徽章可用于 wiki 上的各个项目页面。

### 来自 Telegram 聊天的建议

* 将 microbe-web 项目重命名为更短且含义相似的名称，例如 amoeba。


开发新的 Microbe Web UI 
--------------------------------

### 目标

* 降低对 SSH 和 UART 控制台了解甚少的人员进入 OpenIPC 项目的门槛。
* 提供从任何浏览器（包括移动设备）对设备的访问。

### 安全

* 制作一条关于需要更改默认密码的永久消息。
* 管理员用户（网络设置、日期和稳定版本更新）和根用户（具有大量诊断的完全访问权限）的单独访问级别。


特征
----

### 将配置重置为出厂设置

* 重置方法和选项？

### 收到的报价

* 创建固件设计器，例如 [wifi-iot](https://wifi-iot.com/) 和 [tasmocompiler](https://github.com/benzino77/tasmocompiler)。
* 创建公共 FTP/TFTP/NFS 服务器用于测试固件组件的组件。
