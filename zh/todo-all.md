# OpenIPC Wiki
[Table of Content](../README.zh.md)

待办事项 
----

## 错误修复

* 需要修复导致 syslogd 和 logread 在 hi35{16ev200,16ev300,18ev300} 配置文件上停止工作的问题


## 改进和功能

* 紧急添加对 Goke 处理器的支持
* 检查 majestic、mini_snmpd、telnetd 和其他脚本中是否存在二进制组件
* 构建新的 [motors-openipc](https://github.com/OpenIPC/motors/tree/master/XM) 包
* 根据 OpenIPC 标准集成新版本的 [libjson](https://github.com/json-c/json-c/tree/json-c-0.15) 并删除旧的符号链接
* 为所有平台创建 Initramfs 和 cpio 构建
* 优化 Busybox 小程序并禁用所有不必要的小程序
* 添加 Crond 守护进程的启动
* 从旧的 OpenIPC 项目中添加 Telegram 机器人脚本
* 优化 show_modules 脚本以显示动态消耗的内存
* ~
* 集成Hi3516Av300/Hi3516Cv500/Hi3516Dv300
* 集成Hi3516Cv200/Hi3518Ev200/Hi3518Ev201
* 集成Hi3516Av100/Hi3516Dv100
* 连接初始化模块及启动系统脚本


## 监控和管理

* 将使用 Ansible 的示例上传到存储库
* 安装本地 prometheus 服务器
* 从 ZFT Lab 设备获得稳定的遥测响应


## 文档

* 创建一个 Wiki 页面，用于输入带有电机和协议细节的电路板数据

