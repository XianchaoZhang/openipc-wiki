# OpenIPC Wiki 
[内容](../README.md)

设置 OpenIPC。 
------------------

### 外壳访问。

进入新闪光相机的外壳非常容易。为此，您需要一个 ssh 客户端（[PuTTY](https://putty.org/) 适用于 Windows）。打开终端并运行“ssh”，指定相机的 IP 地址（默认为 192.168.1.10，但 DHCP 服务器可能会为相机提供不同的地址）和用户名 __root__。默认情况下，该用户没有密码。
```
ssh root@192.168.1.10
```


### 设置环境。

安装新固件后，您需要立即清洁覆盖区域。为此，请在 shell 中运行“firstboot”命令。相机将清除覆盖并重新启动。

现在您需要配置引导加载程序环境变量。这通常是从引导加载程序控制台完成的，但也可以使用“fw_setenv”命令从 Linux 完成。

设置原始固件的 MAC 地址、DHCP 请求的首选 IP 地址以及其他网络连接参数。

```
fw_setenv ethaddr D2:19:5E:EF:6A:61
fw_setenv ipaddr 192.168.1.99
fw_setenv netmask 255.255.255.0
fw_setenv gatewayip 192.168.1.1
```
设置网络上的 TFTP 服务器地址。
```
fw_setenv serverip 192.168.1.254
```


### 网络设置。

如果您打算使用无线连接，但您将需要设置网络。打开网络接口设置文件并进行必要的更改。

```
vi /etc/network/interfaces
```
使用静态 IP 地址设置 MT7601 无线卡的示例。
```
auto wlan0
iface wlan0 inet static
    hwaddress ether $(fw_printenv -n ethaddr || echo 00:24:B8:FF:FF:FF)
    address 192.168.1.99
    netmask 255.255.255.0
    gateway 192.168.1.1
    pre-up modprobe mac80211
    pre-up modprobe mt7601sta
    pre-up echo -e "nameserver 192.168.1.53\nnameserver 208.67.222.222\n" >/tmp/resolv.conf
    pre-up echo -e "server 0.time.openipc.org iburst\nserver 1.time.openipc.org iburst\nserver 2.time.openipc.org iburst\nserver 3.time.openipc.org iburst" >/tmp/ntp.conf
    pre-up wpa_passphrase "MyWirelessNetwork" "SuperSecurePassword123" >/tmp/wpa_supplicant.conf
    pre-up sed -i '2i \\tscan_ssid=1' /tmp/wpa_supplicant.conf
    pre-up wpa_supplicant -B -D wext -i wlan0 -c/tmp/wpa_supplicant.conf
    post-down killall -q wpa_supplicant
```

### 访问网络界面。

要配置固件，请在浏览器中打开相机端口 85 上的 Web 界面。默认登录名和密码分别为 __admin__ 和 __12345__。第一次登录时，系统会要求您设置自己的密码，然后才能访问控件。

### 时区。

首先设置正确的时区。在菜单中找到“NTP 设置”部分，在“区域名称”字段中，开始输入您所在时区的主要城市的名称，然后从打开的可用城市列表中选择所需的值。确保 _Zone string_ 值从 GMT0 更改为默认值。保存您的设置更改。重新启动相机以应用更改。

### 网络设置。

设置您的网络。默认情况下，摄像机使用从 DHCP 服务器接收的设置。如果您需要为摄像机分配静态 IP 地址、更改主机名或 DNS 服务器地址，请在_网络设置_页面上执行此操作。

### Majestic 流光设置。

#### RTSP

要配置 RTSP 协议，请指定流传输器将响应的端口号。默认情况下，这是端口 554。访问 RTSP 流的 URL 类似于 _rtsp://camera-host:port/stream={0,1}_，其中 _camera-host_ 是您的摄像头在网络上的地址（完整域名或 IP 地址），_port_ 是 RTSP 流媒体的端口，_{0,1}_ 表示您要接收哪个流，主 _(0)_ 或附加 _(1) _。

此类链接的示例：_rtsp://192.168.1.123:554/stream=0_。

#### 网络IP

要配置 NETIP 协议，请指定摄像机将提供访问的用户名、密码和端口号。请注意，密码必须以清晰的形式输入。系统本身将计算其哈希值并将其保存在设置中。因此，如果您忘记了密码，您将无处可查。将您的密码更改为新密码并尽量不要忘记。

 -  待续  - 

