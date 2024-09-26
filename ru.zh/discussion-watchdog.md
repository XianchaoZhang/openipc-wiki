# OpenIPC Wiki 
[内容](../README.zh.md)

关于watchdog和rtc的讨论
------------------------------------------------


| Группа процессоров    | Имя модуля в системе | Заметки и комментарии               |
|-----------------------|----------------------|-------------------------------------|
| Goke GK7205V200       | open_wdt             | Открытый модуль из OpenHiSilicon    |
| HiSilicon HI3516CV100 | wdt                  | Используем бинарный модуль из SDK ? |
| HiSilicon HI3516CV200 | wdt                  | Используем бинарный модуль из SDK ? |
| HiSilicon HI3516CV300 | hi3516cv300_wdt      | Используем бинарный модуль из SDK ? |
| HiSilicon HI3516EV200 | open_wdt             | Открытый модуль из OpenHiSilicon, в старых прошивках использовался hi3516ev200_wdt |
|                       |                      |                                     |
| список дополнить      |                      |                                     |



### 看门狗链接

- [Linux 内核模块_别名](https://lwn.net/Articles/47412/)
- [看门狗：iTCO_wdt：添加平台驱动程序模块别名](https://scm.linefinity.com/common/linux-stable/commit/e5de32e3ec9d4d5a355659760d5045b80c0a05d8)
- [海思外部看门狗驱动](https://blog.karatos.in/a?ID=00550-03a9ba75-fb52-4f63-a238-56e2c66a5c26)
- [嵌入式系统看门狗定时器指南](https://interrupt.memfault.com/blog/firmware-watchdog-best-practices)
- [看门狗定时器设计师指南](https://www.digikey.ca/en/articles/a-designers-guide-to-watchdog-timers)

