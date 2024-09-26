# OpenIPC Wiki
[Table of Content](../README.md)

## 故障排除：Majestic 无法工作，相机重新启动

要排除 Majestic 故障，您首先需要访问其日志，直到它崩溃的那一刻，这会导致看门狗重新启动系统并从摄像头中删除日志。因此，您需要在日志填充时将其复制到安全的地方。您可以使用 ssh 访问摄像头并将输出管道传输到桌面上的文件来实现这一点。

Run this command on your Linux desktop (do not forget to replace _192.168.1.10_ with camera's actual IP address)
and wait for the camera to reboot once again. The resulting log file will be in the same directory named and 
dated similar to _majestic-2022-12-14.log_
```
ssh root@192.168.1.10 "killall majestic; sleep 2; majestic" > majestic-$(date +"%F").log
```
