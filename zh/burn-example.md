# OpenIPC Wiki
[Table of Content](../README.md)

使用 Burn 安装 OpenIPC 的注意事项 
--------------------------------------

### 引导程序刷新 GK7205V210，并锁定引导程序

#### 序言

```
My opinion is that - instructions for beginners should be written by beginners.
As soon as a gentleman end flashing 2-3 boards, his skills increase to 50 level 
and he does not want to remember the little things that are important for beginners.
```

开始工作之前，请观看我们 [YouTube](https://www.youtube.com/@openipc/playlists) 频道上的视频

- 下载 [Burn](https://github.com/OpenIPC/burn)
- 安装 [PUTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) 和 [TFTP](https://pjo2.github.io/tftpd64/) 服务器
- 关闭相机电源，将 USB com FTDI 连接到相机，指定 PC 上的 COM 端口
- 进入 burn 文件夹并运行以下 cmd（仅聚焦串行端口，在我的情况下是 COM4，其他参数无关紧要，它适用于我的 GK7205V210）：
- **仅**使用 Burn 存储库中的 U-Boot 加载程序！
```
python burn --chip hi3516ev200 --file=u-boot/gk7205v200.bin -p COM4 --break && putty.exe -serial COM4 -sercfg 115200,8,n,1,N
```

- 用电源打开相机，等待 putty 退出控制台
- 按 Enter，然后您将在控制台中看到“goke”
- 运行 TFTP 服务器，指定 bin 文件的路径
- 现在按照 OpenIPC 站点生成的说明进行操作：

```
# Enter commands line by line! Do not copy and paste multiple lines at once!
setenv ipaddr 192.168.0.10; setenv serverip 192.168.0.40
mw.b 0x42000000 0xff 0x800000
tftpboot 0x42000000 openipc-FULL-FIRMWARE-IMAGE.bin
sf probe 0; sf lock 0;
sf erase 0x0 0x800000; sf write 0x42000000 0x0 0x800000
reset
Ctrl + c quickly during booting
# Enter commands line by line! Do not copy and paste multiple lines at once!run setnor8m
```
