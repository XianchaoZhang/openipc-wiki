# OpenIPC Wiki
[目录](../README.zh.md)

软件包 Vtun 
------------

### 介绍

此软件包旨在组织 IP 摄像机和服务器之间的基于 Vtun 的 L2 隧道。为了减少 NOR 闪存占用的空间、减少 RAM 消耗并增加隧道吞吐量，加密和压缩被完全禁用。

### 客户端部分

所有官方 OpenIPC 固件中都包含客户端部分。要连接到服务器，请转到扩展选项卡，指定服务器的 IP 地址或域并保存设置

### 服务器部分

- 创建桥接接口，例如 br-openipc
- 在 vtund.conf 配置中将所有摄像头连接添加到桥接
- 在 br-openipc 接口上启动任何 DHCP 服务器
- 通过 MAC 绑定连接设备的 IP 地址

### 服务器 vtun 编译示例

安装 Debian/Ubuntu 的组件和依赖项

```
apt install -y bison bridge-utils build-essential curl flex bridge-utils
```

### 自动编译脚本

```
#!/bin/bash
#
# OpenIPC.org | v.20240908
#

LANG=C

vtun_version="3.0.2"
vtun_download="http://prdownloads.sourceforge.net/vtun/vtun-${vtun_version}.tar.gz"

prepare() {
    curl -L -o vtun-${vtun_version}.tar.gz ${vtun_download}
    tar xvfz vtun-${vtun_version}.tar.gz
    rm vtun-${vtun_version}.tar.gz
    cd vtun-${vtun_version}
}

compile() {
    ./configure --build=x86_64-linux-gnu --disable-lzo --disable-zlib --disable-ssl --prefix=''
    make && strip vtund
}

install() {
    mkdir -p /usr/local/sbin
    mv -v vtund /usr/local/sbin/vtund
    cd -
    rm -rf vtun-${vtun_version}
}

prepare && compile && install
```

### 服务器的 /etc/network/interfaces.d/br-openipc 示例

```
# Bridge OpenIPC
#
auto br-openipc
iface br-openipc inet static
    address 192.168.11.1
    netmask 255.255.255.0
    bridge_ports zero
    up mkdir -p /var/lock/vtund /var/log/vtund
    up iptables -A FORWARD -j ACCEPT -i br-openipc -o br-openipc
    #up iptables -A FORWARD -t mangle -p tcp --tcp-flags SYN,RST SYN -j TCPMSS --clamp-mss-to-pmtu
    #up iptables -A POSTROUTING -t mangle -p tcp --tcp-flags SYN,RST SYN -j TCPMSS --clamp-mss-to-pmtu
    #up iptables -A POSTROUTING -t nat -s 192.168.11.0/24 -j MASQUERADE
    #up iptables -A FORWARD -j ACCEPT -i ens2 -o br-openipc -d 192.168.11.0/24
    #up iptables -A FORWARD -j ACCEPT -o ens2 -i br-openipc -s 192.168.11.0/24
    #up iptables -A PREROUTING -t nat -j DNAT -p TCP -i ens2 --dport 10180 --to-destination 192.168.11.101:80
    #up iptables -A POSTROUTING -t nat -j SNAT -p TCP -o br-openipc -d 192.168.11.101 --to-source 192.168.11.1

```

### 服务器的 /etc/vtund.conf 示例

```
options {
  syslog daemon;
  timeout 60;
  ip /bin/ip;
}
default {
  type tun;
  proto tcp;
  persist yes;
  keepalive yes;
  timeout 60;
  compress no;
  encrypt no;
  speed 512:512;
  multi killold;
}
#
### Cam-1
#
E60BFB000001 {
  type ether;
  speed 0:0;
  password bla-bla-pass;
  device v-E60BFB000001;
  up {
    ip "link set %% up multicast off mtu 1500";
    program "brctl addif br-openipc %%";
  };
  down {
    program "brctl delif br-openipc %%";
    ip "link set %% down";
  };
}
#
### Cam-2
#
729051000001 {
  type ether;
  speed 0:0;
  password bla-bla-pass;
  device v-729051000001;
  up {
    ip "link set %% up multicast off mtu 1500";
    program "brctl addif br-openipc %%";
  };
  down {
    program "brctl delif br-openipc %%";
    ip "link set %% down";
  };
}
#
```

### 选择

```
#!/bin/sh -x

curl -L -o vtun_3.0.4.orig.tar.gz http://archive.ubuntu.com/ubuntu/pool/universe/v/vtun/vtun_3.0.4.orig.tar.gz
tar xvfz vtun_3.0.4.orig.tar.gz
curl -L -o vtun_3.0.4-2build1.debian.tar.xz http://archive.ubuntu.com/ubuntu/pool/universe/v/vtun/vtun_3.0.4-2build1.debian.tar.xz
tar xvfJ vtun_3.0.4-2build1.debian.tar.xz
cd vtun-3.0.4
cat ../debian/patches/*.patch | patch -p 1
./configure --build=x86_64-linux-gnu --disable-lzo --disable-zlib --disable-ssl --prefix=''
make && strip vtund && cp vtund ../ && cd -
rm -rf vtun_3.0.4.orig.tar.gz vtun_3.0.4-2build1.debian.tar.xz debian vtun-3.0.4
```
