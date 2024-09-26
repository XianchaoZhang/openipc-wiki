## FPV 上的音频

### 概述 
所有内容都已使用 steamdeck (PC x86) 和 ssc338q-imx415 Anjoy 板进行了测试，解码器和类似设备的选择可能会反映这一点。目前延迟约为 50-100 毫秒，比第一次试验的约 200 毫秒有了很大的改善。改进主要来自于在客户端使用“pipewiresink”，但它要求您在 GS 上有一个有效的 pipewire 音频后端。也可以使用 jack 音频后端来获得类似或更好的结果。还有一个更新，在输出端口（标准 5600）上使用混合 RTP 视频/音频流，这需要对 gst 管道采用不同的方法（见下文）。管道需要断开连接，以免相互阻塞。一个小警告；如果启用音频并且不调整管道以整理 payload=97 (H265)，RTP payload=98 将导致视频中出现视觉伪影。 rtpjitterbuffer 将有助于管理无序数据包（没有它就无法工作）。使用 OPUS 16000 OPUS 采样率的视频：https://youtu.be/Z0KxSS24j7o

### Majestic 和常规设置
音频设置 (majestic.yaml):
```
cli -s .audio.enabled true
cli -s .audio.srate 8000 (8000 pretty crap, 16000 usable and 48000 really good)
```

### 工作声音、视频和保存到文件
```
gst-launch-1.0 udpsrc port=5600 ! tee name=videoTee ! queue ! tee name=t t. ! queue ! application/x-rtp,payload=97, clock-rate=90000, encoding-name=H265 ! rtpjitterbuffer latency=20 ! rtph265depay ! mpegtsmux name=ts ! filesink location=/run/media/deck/170a3e7f-325f-4567-8580-0e01dda76973/video/record-$(date +%y%m%d_%H%M%S).tsn sync=false t. ! queue leaky=1 ! tee name=audioTee ! queue ! application/x-rtp, payload=98, encoding-name=OPUS ! rtpjitterbuffer latency=22 do-lost=true drop-on-latency=true ! rtpopusdepay ! ts. audioTee. ! queue leaky=1 ! application/x-rtp, payload=98, encoding-name=OPUS ! rtpjitterbuffer latency=22 ! rtpopusdepay ! opusdec ! audioconvert ! audioresample ! pipewiresink blocksize=128 mode=render processing-deadline=0 sync=false async=false videoTee. ! queue ! application/x-rtp,payload=97, clock-rate=90000, encoding-name=H265 ! rtpjitterbuffer latency=20 ! rtph265depay ! vaapih265dec ! fpsdisplaysink fps-update-interval=200 video-sink=xvimagesink sync=false
```
无需再做任何事情，它只是工作 :-)

