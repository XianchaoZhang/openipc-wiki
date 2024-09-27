# OpenIPC Wiki
[目录](../README.zh.md)

Majestic Streamer
-----------------

### 序言

Majestic 是一款视频流应用程序，是我们固件的核心（与摄像头/视频监控功能相关）。Majestic 可通过 /etc/majestic.yaml 文件进行配置，默认情况下启用了许多功能/服务。可以关闭不需要的选项以获得更好的安全性和性能。有关配置选项，请参阅 /etc/majestic.full。

### 控制信号

```
-HUP restart Majestic (Except Ingenic T21).
-SIGUSR2 SDK Shutdown (For all platforms).
```

### 固件中相机相关的 URL

Majestic 支持多种音频、视频和静态图像格式等。您可以在[此页面](https://openipc.org/majestic-endpoints) 上找到可用端点的完整列表。

长 JPEG 控制参数不适合网站上的示例，我们在此处发布它：

`/image.jpg?width=640&height=360&qfactor=73&color2gray=1`

### 通过 cli 更改参数

目前，可以通过 CLI 实用程序更改配置文件中的参数。

这样，在某些平台上，只需强制重新读取配置文件，就可以在伪动态模式下用一行代码更改参数。

```
cli -s .video0.codec h264 ; cli -s .video0.fps 10 ; killall -HUP majestic 
```

### 实验性控制功能（尚未在端点中描述）

```
/metrics/isp
/metrics/venc
/metrics/motion
```
```
/night/ircut
/night/light
```


### 自动白天/夜晚检测

如果使用这些变量，则可以替换使用的沙盒脚本。仅适用于配置最少且 majestic.yaml 配置文件中未提及 irSensorPin 的简单日/夜方案。如果设置了光传感器 gpio，它将使用默认模式。

The settings work like this:
```day < [minThreshold] | hysteresis | [maxThreshold] < night```

If the sensor gain is 1024 on a bright day the minThreshold could be set to 2000,
if the sensor gain is 32000 on a dark night the maxThreshold could be set to 10000.

```
cli -s .nightMode.minThreshold 10
cli -s .nightMode.maxThreshold 50
```

### Motion detection

Motion detect is supported for Hisilion/Goke, Ingenic and Sigmastar.
When a motion event is detected, `majestic` invokes a predefined script `/usr/sbin/motion.sh` with a parameter specifying the object count:

```
/usr/sbin/motion.sh [count]
```

Enable motion detection in `majestic` configuration:

```
cli -s .motionDetect.enabled true
cli -s .motionDetect.debug true
```

Reboot the camera and restart `majestic` in the foreground:

```
killall majestic; sleep 3; majestic
```

You should see the script running after motion detection events:

```
20:37:02  <SED_IVE_DETCTOR> [  motion] motion_update@155             Motion detected: [1163x0] -> [690x475]
20:37:02  <SED_IVE_DETCTOR> [   tools] motion_event@615              Execute motion script: /usr/sbin/motion.sh
```

### Broadcasts using RTMP

To instantly launch a YouTube broadcast, run these commands in the console:
```
cli -s .video0.codec h264
cli -s .audio.enabled true
cli -s .outgoing.enabled true
cli -s .outgoing.server rtmp://a.rtmp.youtube.com/live2/you-key-here
reboot
```

Examples of other addresses for different services:
- YouTube
    - rtmp://a.rtmp.youtube.com/live2/---KEY---
- Telegram
    - rtmps://dc4-1.rtmp.t.me/s/---KEY---
- RuTube
    - rtmp://upload.rutube.ru/live_push/---KEY---
- OK and VK
    - rtmp://ovsu.mycdn.me/input/---KEY---

We ask that you add information about other popular services here, thank you.

RTMP reconnection and timeout logic works as follows:

```
    0-200 tries = 10 seconds timeout
  200-500 tries = 60 seconds timeout
 500-1000 tries = 300 seconds timeout
    1000+ tries = 600 seconds timeout
```

### Other outgoing options

```
outgoing:
  enabled: true
  server: udp://192.168.1.10:5600
  naluSize: 1200
  - udp://IP-1:port
  - udp://IP-2:port
  - unix:/tmp/rtpstream.sock
  - rtmps://dc4-1.rtmp.t.me/s/mykey
```

### ONVIF

For basic ONVIF to work correctly, you need to enable it and add a user to the system as shown in the example:

```
cli -s .onvif.enabled true
adduser viewer -s /bin/false -D -H
echo viewer:123456 | chpasswd
```

### JPEG and MJPEG

For the purpose of unification and standardization for all platforms, as well as to increase the stability of the streamer, the image size will always be equal to the size on the Video0 channel and a separate setting is not provided.

###  ROI

Detection zones of two types:

`motionDetect.roi: 1854x1304x216x606,1586x1540x482x622`

`motionDetect.skipIn: 960x540x1920x1080`

**roi** - region of interest, when we specify one or more regions whose movements we are interested in.

**skipIn** - on the contrary, if we are interested in movements on the whole screen, except for some areas (for example, there is a tree in the frame, which is swaying in the wind).

Coordinate format is the same as in osd.privacyMasks: x,y of the top left point, length and width in pixels.

### How to convert YUV image to a more common image format

Use `convert` command from ImageMagick software. Run it like this:
```
convert -verbose -sampling-factor 4:2:0 -size 1920x1080 -depth 8 image.yuv image.png
```
where `1920x1080` is the picture resolution of video0, and `.png` is the target
image format.

### How to play audio stream

Use [ffplay][ffplay] utility from [ffmpeg][ffmpeg] package.
```
ffplay -ar 48000 -ac 1 -f s16le http://192.168.1.10/audio.pcm
ffplay -ar 48000 -ac 1 -f alaw http://192.168.1.10/audio.alaw
ffplay -ar 48000 -ac 1 -f mulaw http://192.168.1.10/audio.ulaw
ffplay -ar 8000 -ac 1 -f alaw http://192.168.1.10/audio.g711a
```

### How to create an audio file to play on camera's speaker over network

Using [sox][sox] program convert any source audio file to [PCM][pcm] 8kbps audio:
```
sox speech.mp3 -t raw -r 8000 -e signed -b 16 -c 1 test.pcm
```

### How to play audio file on camera's speaker over network

```
curl -u root:12345 --data-binary @test.pcm http://192.168.1.10/play_audio
```

[aac]: https://en.wikipedia.org/wiki/Advanced_Audio_Coding
[alaw]: https://en.wikipedia.org/wiki/A-law_algorithm
[dng]: https://en.wikipedia.org/wiki/Digital_Negative
[g711]: https://en.wikipedia.org/wiki/G.711
[heif]: https://en.wikipedia.org/wiki/High_Efficiency_Image_File_Format
[hls]: https://en.wikipedia.org/wiki/HTTP_Live_Streaming
[jpeg]: https://en.wikipedia.org/wiki/JPEG
[mjpeg]: https://en.wikipedia.org/wiki/Motion_JPEG
[mp3]: https://en.wikipedia.org/wiki/MP3
[mp4]: https://en.wikipedia.org/wiki/MPEG-4_Part_14
[opus]: https://en.wikipedia.org/wiki/Opus_(audio_format)
[pcm]: https://en.wikipedia.org/wiki/Pulse-code_modulation
[raw]: https://en.wikipedia.org/wiki/Raw_image_format
[rtsp]: https://en.wikipedia.org/wiki/RTSP
[ulaw]: https://en.wikipedia.org/wiki/%CE%9C-law_algorithm
[yuv]: https://en.wikipedia.org/wiki/YUV
[ffplay]: https://ffmpeg.org/ffplay.html
[ffmpeg]: https://ffmpeg.org/
[sox]: https://en.wikipedia.org/wiki/SoX
