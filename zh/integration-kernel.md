# OpenIPC Wiki
[Table of Content](../README.zh.md)

内核配置的额外要求 
------------------------------------------------

```
CONFIG_BLK_DEV=y
CONFIG_BLK_DEV_LOOP=y

CONFIG_IP_MULTICAST=y

CONFIG_IP_PNP=y
CONFIG_IP_PNP_DHCP=y

CONFIG_ROOT_NFS=y
```

## 补丁文件要求

- 补丁文件名应遵循格式"<number>-<description>.patch"。

- 补丁文件的名称中不应包含对软件包版本的任何引用。

- 补丁文件名中的 `<number>` 部分表示从 1 开始的应用顺序。最好在数字左侧添加零，最多为四位数字，就像 `git-format-patch` 所做的那样。_0001-foobar-the-buz.patch_

- 补丁文件应该在其标题中包含一条注释，解释此补丁的作用以及为什么需要它。

- 在每个补丁文件的头部添加一个_Signed-off-by_声明，以帮助跟踪更改并证明补丁是在与其修改的软件相同的许可证下发布的。

