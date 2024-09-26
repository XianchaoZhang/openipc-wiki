# OpenIPC Wiki 
[内容](../README.zh.md)

Qtech QVC-IPC-136W & 开关摄像机 HS-303-V3 
------------------------------------- ---

> [!提示]
> 此页面可以填写有用的信息，以换取您的 GitHub 个人资料中的 OpenIPC 徽章；）

### 关于比赛、信息、参赛者、奖品

<h1align="left"> 
<a href="https://foss.kruzhok.org/"><img src="https://raw.githubusercontent.com/TheDayG0ne/wiki/master/images/project- kruzhok-2024/foss_kruzhok-logo.png" alt="FOSS Kruzhok" width="500"></a> 
</h1>

该项目面向学童和学生。该计划分为加速器部分和竞赛部分。来自领先 IT 公司的专家在 GitHub/GitLab 上评估项目。\ 最好的人将收到来自领先技术公司专家的 CodeReview，并为发表有关该项目的文章做准备。

开源文化允许您创建自己的项目并参与其他项目的改进，并为新手 IT 专家提供协作和接收经验丰富的开发人员反馈的机会。

该竞赛是为学生和中小学生提供进入开源领域的平台。参与者将被要求发布自己的项目或为现有开源开发项目之一的开发做出重大贡献。

参赛者、获奖者和获奖者名单可在[竞赛网站](https://foss.kruzhok.org/results_2024)上查看

### 吸引项目的目的和目标

<!-- Не знаю, что тут можно указать... (TheDayG0ne) -->

### 将相机连接到 Wi-Fi 网络

Для подключения камеры к WiFi точке доступа:
1. Сгенерируйте QR код со следующими параметрами:
    ```
    wlanssid=HotSpotName
    wlanpass=mypassword
    ```

要将相机连接到 WiFi 接入点：
1. 生成二维码，参数如下：
> [!提示]
> 要生成代码，您可以使用[OpenIPC 网站上的](https://openipc.org/tools/qr-code-generator) 生成器，或任何其他[QR 代码生成器](https://www.openipc.org/tools/qr-code-generator)。 google.com/搜索？q=QR+代码+生成器）

> [!警告]
> 注意！ Wi-Fi 网络的名称不得包含空格或西里尔字符。\
>否则相机将无法连接网络

>[！注意]
> TheDayG0ne 的评论：\
> *我的相机拒绝连接到`#TDG NET`网络，但同时它很容易连接到`tdgnet.local`网络。\
> 我必须重命名我的家庭网络，然后重新连接所有智能设备:)*

2. 连接相机电源
3.开机后1.5分钟内，在15-30厘米的距离处向相机展示之前生成的二维码
4. 等待语音通知"设备已就绪"，然后等待相机重新启动

**准备好！相机已连接到网络**

相机的 IP 地址可以在路由器的设置中找到 <h1align="left"> <a><img src="https://raw.githubusercontent.com/TheDayG0ne/wiki/master/images/project -kruzhok-2024/cam -ip-router.png" alt="在路由器设置中查看摄像机的IP地址" width="800"></a> </h1>

或者通过网络扫描程序（例如，Advanced IP Scanner） <h1align="left"> <a><img src="https://raw.githubusercontent.com/TheDayG0ne/wiki/master/images/project- kruzhok-2024/cam-ip-aips.png" alt="通过网络扫描程序查看摄像机的IP地址" width="800"></a> </h1>

### 登录相机网络界面

Для входа в веб-интерфейс камеры:
1. Введите в браузер IP-адрес камеры (полученный одним из способов выше)
2. В появившемся окне входа укажите следующий логин и пароль:
  ```
  Логин: root
  Пароль: 123456
  ```
登录相机网络界面：
1. 在浏览器中输入摄像机的IP地址（使用上述方法之一获取）
2. 在出现的登录窗口中，输入以下登录名和密码： <h1align="left"> <a><img src="https://raw.githubusercontent.com/TheDayG0ne/wiki/master/images/ project-kruzhok- 2024/web-login.png" alt="摄像头 Web 界面中的授权窗口" width="800"></a> </h1>
3. 授权成功后，您将进入相机Web界面的主页 <h1align="left"> <a><img src="https://raw.githubusercontent.com/TheDayG0ne/wiki/master /images/project- kruzhok-2024/web-main.png" alt="相机网络界面" width="800"></a> </h1>

> [!提示]
> 登录Web界面后，建议更改默认密码。

### 通过网络界面更改您的密码

要通过摄像机 Web 界面更改 root 密码：
1. 单击 Web 界面菜单中的"固件"项
2. 从下拉菜单中选择接口
3. 在打开的页面中的"访问"部分中，输入 root 用户的新密码
4. 要保存密码，请单击保存更改按钮 <h1align="left"> <a><img src="https://raw.githubusercontent.com/TheDayG0ne/wiki/master/images/project-kruzhok -2024/ web-passchange.png" alt="通过相机Web界面更改密码" width="800"></a> </h1>

> [!重要]
> 修改密码后，您需要重新登录Web界面。

### 通过 SSH 连接到相机

> [!警告]
> 本手册中描述的操作是使用标准 Windows 操作系统工具（命令行或 Windows 终端）执行的

> [!注意]
> 如果需要，可以通过使用 SSH 客户端（例如 PuTTY、Termius 或 MobaXterm）添加连接来修改这些说明

通过 SSH 连接到相机：
1. 打开命令提示符
2. 在出现的窗口中输入`ssh root@<摄像机IP地址>`并按Enter键
3. 针对无法验证主机的消息，输入 `yes` 并按 Enter <h1align="left"> <a><img src="https://raw.githubusercontent.com/TheDayG0ne/wiki /master /images/project-kruzhok-2024/ssh-connect.png" alt="通过 SSH 连接到相机" width="800"></a> </h1>

4.输入root用户密码（`123456`），然后按Enter键

> [!警告]
> 请注意，由于 *nix 系统的性质，您输入的密码字符不会显示:)

<h1align="left"> <a><img src="https://raw.githubusercontent.com/TheDayG0ne/wiki/master/images/project-kruzhok-2024/ssh-connected.png" alt="连接中通过 SSH 连接到摄像头" width="800"></a> </h1>

**准备好！您通过 SSH 连接连接到相机**

