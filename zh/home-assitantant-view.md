# OpenIPC Wiki
[Table of Content](../README.md)


## HomeAsistant 通用摄像头 
#### 选项 1 
转到设置 -> 设备和服务 -> 添加集成 --> 通用摄像头

#### 选项 2 
[HomeAssistan_Generic](https://my.home-assistant.io/redirect/config_flow_start/?domain=generic)

#### 选项 3 
<"IP-HomeAsisstantServer">/config/integrations/dashboard

添加集成 --> 通用相机

### 配置参数

<div style="display: flex;">
  <div style="max-width: 80%; overflow-x: auto;">
    <img src="../images/howto-streaming-homeassistant.png" alt="Example configuration generic camera home assitant (IP_Camera 192.168.1.29)" width="300"/>
  </div> 
  <div style="max-width: 80%; overflow-x: auto;">
    <pre>
      <code>
Still Image URL -> http://<"IP-OIPC_Camera">/image.jpg
Stream Source URL  -> rtsp://<"User">:<"PASS">@<"IP-OIPC_Camera">:554/stream=0
RTSP transport protocol -> TCP
Authentication -> basic
Username -> <"User">
Password -> <"PASS">
Frame Rate -> 15
Verify SSL certificate -> empty
Limit refetch to url change ->empty
      </code>
    </pre>
  </div>
</div>

**注意：**默认用户和 PASS OpenIPC -> ("root" & ¨12345¨)

**注意**：小心帧速率（目前家庭助理不支持超过 20 的闪烁问题）

## Lovelace 示例 
手动将此摘录添加到新卡片中（yaml 格式）
```
camera_view: live
type: picture-glance
image: http://192.168.1.29/image.jpg
entities: []
entity: camera.192_168_1_29
view_layout:
  position: main
camera_image: camera.192_168_1_29
```
**其他注意事项：**
## 相机配置 
![](../images/HA_CameraConfig.png)

## 路由器设置 
请记住从路由器为您的相机设置一个固定的 IP 地址，否则它可能会在重启时发生变化。



# Enjoy the stream.

