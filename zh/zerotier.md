# ZeroTier One  
[ZeroTier One](https://www.zerotier.com) 包用于终极构建。

### 开始
- 要启动服务，请执行以下命令：
```
/usr/sbin/zerotier-one -d &
```

### 设置
1. 在 [my.zerotier.com](https://my.zerotier.com) 注册。
2. 获取网络 ID，将网络配置为私有网络。
3. 从 ssh 控制台（或 Web 控制台）运行以下命令：

```
zerotier-cli join [network id]
```



4. 转到 [my.zerotier.com](https://my.zerotier.com) 的 Web 界面并授权新连接的摄像头，通过选中相应对等点旁边的框进行授权。

### Settings
- The configuration is stored in `/var/lib/zerotier-one`
- You can leave the network by running the following command:
```
zerotier-cli leave [network id]
```
