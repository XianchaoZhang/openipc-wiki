# OpenIPC Wiki
[Table of Content](../README.md)

远程系统日志 
-------------

有时需要远程从多个 OpenIPC 设备获取日志。

这个没有什么困难，您需要通过启用接收信息的选项来配置服务器，并在对摄像机上的处理程序的调用中写入其 IP 地址。

将以相机启动。添加选项 -R server-ip:514，地址如示例所示，然后重新启动设备。

```bash
root@openipc-hi3516ev300:~# differ /etc/init.d/S01syslogd
```

```diff
--- /rom/etc/init.d/S01syslogd
+++ /etc/init.d/S01syslogd
@@ -3,7 +3,7 @@
 DAEMON="syslogd"
 PIDFILE="/var/run/$DAEMON.pid"
.
-SYSLOGD_ARGS="-C64 -t"
+SYSLOGD_ARGS="-C64 -t -R 172.19.32.17:514"
.
 # shellcheck source=/dev/null
 [ -r "/etc/default/$DAEMON" ] && . "/etc/default/$DAEMON"
```

在服务器配置文件中，写下要监听的端口号和协议的选项，然后重新启动服务。

```diff
--- rsyslog.conf.orig 2022-09-30 16:41:52.081353630 +0300
+++ rsyslog.conf 2023-05-01 12:44:06.098032982 +0300
@@ -14,12 +14,12 @@
 #module(load="immark") # provides --MARK-- message capability
.
 # provides UDP syslog reception
-#module(load="imudp")
-#input(type="imudp" port="514")
+module(load="imudp")
+input(type="imudp" port="514")
.
 # provides TCP syslog reception
-#module(load="imtcp")
-#input(type="imtcp" port="514")
+module(load="imtcp")
+input(type="imtcp" port="514")
.
 # provides kernel logging support and enables non-kernel klog messages
 module(load="imklog" permitnonkernelselfacility="on")
```

欢迎评论和补充。再见！

