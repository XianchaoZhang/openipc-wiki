# OpenIPC Wiki
[Table of Content](../README.zh.md)

在 Docker 中运行 TFTP 服务器 
-------------------------------

运行 TFTP 服务器的最简单方法是在容器化环境中运行。无论您运行的是 Linux、Windows 还是 Mac，只需执行以下步骤：

### 安装 Docker Composer 按照[Docker 安装说明][1]进行操作。

### Create Docker image files
创建 Docker 镜像文件 创建一个名为"Dockerfile"的文件，内容如下： 
```dockerfile
FROM debian:latest

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install tftpd-hpa -y -qq && \
apt-get clean && rm -rf /var/lib/apt/lists/*

CMD echo -n "Starting $(in.tftpd --version)" && \
in.tftpd --foreground --create --secure --ipv4 --verbose --user tftp \
--address 0.0.0.0:69 --blocksize 1468 /srv/tftp
```

在同一目录中，创建一个名为"docker-compose.yml"的文件，内容如下：
```yaml
version: '3.9'
services:
  tftpd-hpa:
    build: .
    container_name: tftp
    network_mode: 'host'
    restart: unless-stopped
    volumes:
      - ./tftp:/srv/tftp
```

### 启动容器
```bash
docker-compose up -d
```
Docker 将在必要时构建镜像并在后台启动它。在构建容器期间，Docker 还将创建"tftp/"子目录，从中提供您的文件。Docker 充当组"input"中的用户"systemd-network"来访问该目录。如果您想允许保存通过 TFTP 发送到您的机器的文件，您需要更改该目录的所有权：或者，您可以放宽该目录的权限：使用您计算机的 IP 地址从本地网络上的其他机器访问 TFTP 服务器。

```bash
sudo chown systemd-network:input ./tftp
```
Alternatively, you may loosen permissions on that directory:
```bash
sudo chmod 777 ./tftp
```
### 停止容器
要停止容器并释放内存，只需运行该容器将保存在您的计算机上，直到下次需要启动它。
```bash
docker-compose stop
```

[1]: https://docs.docker.com/compose/install/
