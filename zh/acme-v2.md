# OpenIPC Wiki
[Table of Content](../README.md)

如何在相机上安装 HTTPS 证书 
------------------------------------------------

确保您的相机可通过端口 80（HTTP）和端口 443（HTTPS）从互联网访问。您可能需要在路由器上设置端口转发。

### 创建 ACME 帐户：

__在镜头前：__

```bash
uacme -y -v new
```

### 为你的相机指定一个 FQDN

安全 HTTP（安全超文本传输​​协议，HTTPS）不能发给裸 IP 地址，您需要为您的相机设置完全限定域名 (FQDN)。这样您的相机才能通过 HTTPS 访问。

使用任何域名注册商创建一个帐户并注册一个域名，例如 _mysuperduperdomain.com_。

为该域名设置一个 DNS 区域，并在该域区域中为您的相机创建记录。

```console
DNS Records
mysuperduperdomain.com
---------------------------------------
Type    Host       IP Address       TTL
A       ipc-001    75.123.45.555    600
```

其中 `75.123.45.555` 是您的公共 IP 地址。

### 如果您的相机位于 NAT 后面，请设置端口转发。

添加从 WAN 接口的端口 80 到摄像机本地 IP 地址的端口 80 的端口转发。

```console
75.123.45.555:80 => 192.168.1.10:80
```

如果您的网络上有多个设备提供公共 HTTP 请求，则将您的相机域名添加到 HTTP 代理。

### 为您的域名颁发证书：

__在镜头前__：

```bash
uacme -y -v -h /usr/share/uacme/uacme.sh -t EC issue ipc-001.mysuperduperdomain.com
```

### 设置本地 DNS 记录覆盖

您可以在计算机上的“/etc/hosts”文件中添加覆盖记录

```bash
echo "192.168.1.10  ipc-001.mysuperduperdomain.com" >> /etc/hosts
```

或者您可以在本地 DNS 服务器（如 [pi.hole](https://pi-hole.net/)）上创建记录，以便使用该 DNS 服务器的任何人也可以安全地访问摄像头。

### 重启 majestic 并测试访问

打开您最喜欢的网络浏览器并转到 <https://ipc-001.mysuperduperdomain.com/>

