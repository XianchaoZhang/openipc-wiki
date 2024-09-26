# OpenIPC Wiki
[Table of Content](../README.md)

Overview
--------

Each SoC manufacturer has its own software to adjust picture quality:

* HiSilicon - PQTools
* Novatek - isptool
* Fullhan - Coolview

## HiSilicon based boards

### EV300 family

#### Run server module on OpenIPC boards

```console
$ pqtools

.....

dev mounted
libs mounted
pqtools:begin to run
the s32Result is 1
g_bUseSpecAWB is 0
port is : 4321

<HI_PQT_Network_Init>(1486)bind ok!
pqtools:server(port:4321)begin to listen
pqtools:Waiting for connection from client!
```

#### 在 XM 板上运行服务器模块

为了简单起见，我们使用公共 NFS 服务器：

```console
$ mount -o nolock 95.217.179.189:/srv/ro /utils/
$ cd /utils/ittb_ev300_V1.0.1.2/
$ LD_LIBRARY_PATH=lib ./ittb_control

...
pqtools:Waiting for connection from client!
```

#### 在 Windows 上运行客户端软件

下载并安装[MATLAB 编译器运行时][mcr]。

下载 [PQTools][pqt] 并将 zip 文件解压到电脑的某处。

启动"HiPQTools.exe"，选择"Hi3516EV200_V1.0.1.2"，输入相机的 IP 地址，然后单击"OK"。

使用[本手册][man]作为参考。

[mcr]: https://ssd.mathworks.com/supportfiles/MCR_Runtime/R2012a/MCR_R2012a_win32_installer.exe
[pqt]: https://drive.google.com/file/d/1c4XZRbJKXjMBwfMJaLl5jUPcVqMbO936/view?usp=sharing
[man]: https://drive.google.com/file/d/1mY1lXMZVNz2Ia5CPvTF-K-907eIioSYU/view?usp=sharing
