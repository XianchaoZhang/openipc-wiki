# OpenIPC Wiki
[目录](../README.zh.md)

## HomeAsistant PTZ 集成

此集成基于 ssh 命令到开放 ipc 固件以集成 ptz 控制，适用于不支持 onvif 的摄像机

## 相机配置
使用参数加载模块（您可能需要针对特定​​相机试验 hmaxstep 和 vmaxstep 值）

复制 motor_sample.ko ---> 内部存储器（ssh 复制） 
```
scp "C:\Users\Downloads\sample_motor.ko" root@192.168.1.29:/sample_motor.ko
```
在相机上编辑新的自动启动脚本 
```
  $ vi /etc/rc.local
```
添加以下行
```
# addmod
insmod /sample_motor.ko vstep_offset=0 hmaxstep=2130 vmaxstep=1600
# go to 0 position 
t31-kmotor -d   h -r
```
更改文件模式（添加执行权限）
```
 $ chmod +x /etc/rc.local
```

## Home Assistant 配置
```
 docker ps
 docker exec -it <"ID_HA_container"> /bin/bash
```
install sshpass
```
apk add sshpass
```

  
**注意：**您可以使用以下方式测试 sshpass

```
sshpass -p '123456' ssh root@192.168.1.29
```


将这些行添加到 configuration.yaml 
```
shell_command:
  c101_x_down: /bin/bash c101_x_down.sh
  c101_x_up: /bin/bash c101_x_up.sh
  c101_y_down: /bin/bash c101_y_down.sh
  c101_y_up: /bin/bash c101_y_up.sh
  c101_r: /bin/bash c101_r.sh
```
将这些行添加到 scripts.yaml 
```
c101_x_down:
  alias: c101_x_down
  sequence:
  - service: shell_command.c101_x_down
    data: {}
  mode: single
c101_x_up:
  alias: c101_x_up
  sequence:
  - service: shell_command.c101_x_up
    data: {}
  mode: single
c101_y_down:
  alias: c101_y_down
  sequence:
  - service: shell_command.c101_y_down
    data: {}
  mode: single
c101_y_up:
  alias: c101_y_up
  sequence:
  - service: shell_command.c101_y_up
    data: {}
  mode: single
c101_r:
  alias: c101_r
  sequence:
  - service: shell_command.c101_r
    data: {}
  mode: single
```

**SCRIPT** 
此脚本位于此 repo https://github.com/OpenIPC/motors/tree/4c7dc45e5e877f38c076343f361159844374920a/t31-kmotor

在 /config 目录中创建此脚本

```
vi camara_scrip.sh
```
Paste this file 
```
#!/bin/bash

echo "Enter the camera user:"
read user
echo "Enter the camera password:"
read password
echo "Enter the camera IP:"
read ip
echo "Camera_Name_"
read name

echo "#!/bin/bash
# Conctate al servidor remoto utilizando sshpass y la contraseÃ±a
sshpass -p '"$password"'  ssh -o StrictHostKeyChecking=accept-new  "$user"@"$ip" <<EOF
# Dentro del servidor remoto, ejecuta el comando t31-kmotor con los argumentos
t31-kmotor -d   h -r
EOF 
"> "$name"_r.sh
echo "#!/bin/bash
# Conctate al servidor 
sshpass -p '"$password"'  ssh -o StrictHostKeyChecking=accept-new  "$user"@"$ip" <<EOF
# Dentro del servidor remoto, ejecuta el comando t31-kmotor
t31-kmotor -d g -x -300 -y 0
EOF 
"> "$name"_x_down.sh
echo "#!/bin/bash
# Conctate al servidor 
sshpass -p '"$password"'  ssh -o StrictHostKeyChecking=accept-new "$user"@"$ip" <<EOF
# Dentro del servidor remoto, ejecuta el comando t31-kmotor
t31-kmotor -d g -x 300 -y 0
EOF 
"> "$name"_x_up.sh
echo "#!/bin/bash
# Conctate al servidor 
sshpass -p '"$password"'  ssh -o StrictHostKeyChecking=accept-new  "$user"@"$ip" <<EOF
# Dentro del servidor remoto, ejecuta el comando t31-kmotor
t31-kmotor -d g -x 0 -y -300
EOF 
"> "$name"_y_down.sh
echo "#!/bin/bash
# Conctate al servidor 
sshpass -p '"$password"'  ssh -o StrictHostKeyChecking=accept-new  "$user"@"$ip" <<EOF
# Dentro del servidor remoto, ejecuta el comando t31-kmotor
t31-kmotor -d g -x 0 -y 300
EOF 
"> "$name"_y_up.sh

# Change mod 
chmod +x "$name"_r.sh "$name"_x_down.sh "$name"_x_up.sh "$name"_y_down.sh "$name"_y_up.sh

```
Execute the following lines
```
chmod +x camara_scrip.sh
./camara_scrip.sh
```
粘贴此文件执行以下行输入您的相机数据（之前的示例名称为 c101）

**注意：**需要为其他文件（configuration.yml 和 scrips.yml）制作脚本



## Lovelace 示例 
手动将此摘录添加到新卡片中（yaml 格式）
```
camera_view: live
type: picture-elements
image: http://192.168.1.29/image.jpg
entities:
  - entity: script.c1_r
  - entity: script.c1_x_down
  - entity: script.c1_x_up
  - entity: script.c1_y_down
  - entity: script.c1_y_up
camera_image: camera.192_168_1_29
elements:
  - type: icon
    icon: mdi:arrow-left-drop-circle
    tap_action:
      action: call-service
      service: script.c1_x_down
    style:
      bottom: 45%
      left: 5%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
  - type: icon
    icon: mdi:arrow-right-drop-circle
    tap_action:
      action: call-service
      service: script.c1_x_up
    style:
      bottom: 45%
      right: 5%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
  - type: icon
    icon: mdi:arrow-up-drop-circle
    tap_action:
      action: call-service
      service: script.c1_y_up
    style:
      top: 10%
      left: 46%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
  - type: icon
    icon: mdi:arrow-down-drop-circle
    tap_action:
      action: call-service
      service: script.c1_y_down
    style:
      bottom: 10%
      left: 46%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
  - type: icon
    icon: mdi:arrow-expand-all
    tap_action:
      action: more-info
    entity: camera.192_168_1_29
    style:
      top: 5%
      right: 5%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)

```

Example of view

![GUI_Interface](../images/GUI_Interface.png)
# Enjoy the stream.
