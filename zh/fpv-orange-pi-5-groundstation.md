
<h3>* 预安装的图像现已在此处提供 - https://github.com/JohnDGodwin/OpenIPC_Groundstations/releases/tag/OrangePi5Plus</h3>

***

下载 Ubuntu Server ISO 并刷入设备——`https://github.com/Joshua-Riek/ubuntu-rockchip`

`sudo apt update`

`sudo apt upgrade`

继续拉取一些我们也需要的包。

`sudo apt install dkms python3-all-dev fakeroot cmake meson`

设置系统本地时区 - 用您的用例替换地区和城市

`ln -sf /usr/share/zoneinfo/<区域>/<城市> /etc/localtime`

设置主机名

`sudo nano /etc/hostname`


***

使用 MPP 设置 Gsteamer


下载并安装 gstreamer

`sudo apt --no-install-recommends install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev libgstreamer-plugins-bad1.0-dev gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-libav gstreamer1.0-tools gstreamer1.0-x gstreamer1.0-gl`



从包含的自定义 PPA 下载并安装 rockchip mpp、rga 和 gstreamer 插件，以实现硬件加速解码。

`sudo apt install gstreamer1.0-rockchip1 librga-dev librga2 librockchip-mpp-dev librockchip-mpp1 librockchip-vpu0 libv4l-rkmpp rockchip-multimedia-config libgl4es libgl4es-dev libdri2to3`



测试：

`gst-inspect-1.0 | grep 265`

`gst-inspect-1.0 | grep mpp`



***

rtl8812au 驱动程序安装


逐行输入以下内容

	sudo bash -c "cat > /etc/modprobe.d/wfb.conf <<EOF
	# blacklist stock module
	blacklist 88XXau
	blacklist 8812au
	blacklist rtl8812au
	blacklist rtl88x2bs
	EOF"



`git clone -b v5.2.20 https://github.com/svpcom/rtl8812au.git`

``cd rtl8812au/``

`sudo ./dkms-install.sh`


***


重启设备

TODO：或者弄清楚如何在不完全重启的情况下加载驱动程序。尝试"modprobe 88xxau"


***

WFB-ng 安装


使用 iwconfig 查找 wifi 设备名称，并在脚本安装中将其替换为 $WLAN

`git clone -b stable https://github.com/svpcom/wfb-ng.git`

`cd wfb-ng`

`sudo ./scripts/install_gs.sh $WLAN`


安装后：


`sudo systemctl 启用 wifibroadcast`


然后


`sudo nano /etc/wifibroadcast.cfg`


更改频道以匹配 vtx

将区域从"BO"更改为"00"




将 drone.key 从 wfb-ng 目录复制到 vtx 的 /etc 目录

例如使用 scp，将 x.x.x.x 替换为相机的 ip 地址

`scp drone.key root@x.x.x.x:/etc`


确保 gs.key 已自动放入 VRX 端的 /etc 中

`ls /etc/gs.key`



***


再次重启设备


***

测试连接：

在地面站运行

`sudo systemctl 启用 wifibroadcast@gs` `sudo systemctl 启动 wifibroadcast@gs`

`wfb-cli gs`


插入相机并观察数据包的传入，xlost 应该保持在接近零的水平，而 xrecv 应该上升


***


gstreamer 播放的图形环境


`sudo apt install --no-install-recommends xorg lightdm-gtk-greeter lightdm openbox`

然后编辑

`sudo nano /etc/lightdm/lightdm.conf`


插入：

	[Seat:*]
	autologin-user=ubuntu
	xserver-command = X -nocursor




***

TODO：
此时，重新启动，您将必须使用键盘登录至少一次，但之后它会自动将 ubuntu 用户登录到没有光标的 openbox 会话


***



让我们设置桌面壁纸并编写一些启动脚本

`sudo apt install --no-install-recommends libimlib2-dev libx11-dev libxinerama-dev pkg-config make`

`git clone https://github.com/himdel/hsetroot.git`

`cd hsetroot`

`make`

`sudo make install`



将您想要的背景图像保存到 /home/ubuntu/desktop.png

创建脚本目录：

`mkdir /home/ubuntu/scripts`

将显示分辨率设置为 1280x720 的脚本


`sudo nano /home/ubuntu/scripts/setdisplay.sh`


 insert:


	#/bin/bash
	export DISPLAY=:0

	#set your desired screen resolution here
	MODE=1280x720


	if [[ $(xrandr | awk '/HDMI-1/ {print $2}') == "connected" ]]; then
	        xrandr --output HDMI-1 --mode $MODE
	fi
	if [[ $(xrandr | awk '/HDMI-2/ {print $2}') == "connected" ]]; then
	        xrandr --output HDMI-2 --mode $MODE
	fi
	exit 0

用于启动/停止视频流并将 DVR 保存到 ~/Videos 目录的脚本

注意：此脚本依赖于连接在引脚 5 和 GND 之间的按钮。您必须安装一个按钮才能使其工作。

创建视频目录

`sudo mkdir /home/ubuntu/Videos`

Make the script

`sudo nano /home/ubuntu/scripts/dvr.sh`

 insert:

	#!/bin/bash

	export DISPLAY=:0

	xset s off -dpms

	GPIO_PIN=5
	RUNNING=0
	gpio mode $GPIO_PIN up

	cd /home/ubuntu/Videos

	while true; do
        if [ $(gpio read $GPIO_PIN) -eq 0 ]; then
        if [ $RUNNING -eq 0 ]; then
                current_date=$(date +'%m-%d-%Y_%H-%M-%S')
		
		gst-launch-1.0 -e \
                udpsrc port=5600 caps='application/x-rtp, media=(string)video, clock-rate=(int)90000, encoding-name=(string)H265' ! \
                rtph265depay ! \
                h265parse ! \
                tee name=t ! \
                queue ! \
                mppvideodec ! \
                videoconvert ! \
                autovideosink sync=false t. ! \
                queue ! \
                matroskamux ! \
                filesink location=record_${current_date}.mkv &
		
                RUNNING=$!
        else
                kill $RUNNING
                RUNNING=0
        fi
        sleep 0.2
        fi
        sleep 0.1
	done

使用 chmod +x 使脚本可执行。

`sudo chmod +x /home/ubuntu/scripts/dvr.sh /home/ubuntu/scripts/setdisplay.sh`


Finally:

`sudo nano /etc/xdg/openbox/autostart`
 
add:	

	bash /home/ubuntu/scripts/setdisplay.sh

	hsetroot -cover /home/ubuntu/desktop.png &

	sudo /home/ubuntu/scripts/dvr.sh &

为了将视频流无边框地显示到屏幕上，我们执行以下操作。

`sudo nano /etc/xdg/openbox/rc.xml`

找到行 `<keepBorder>yes</keepBorder>` 并将其替换为 `<keepBorder>no</keepBorder>`

然后在文件末尾添加：

	<applications>
	     <application class="*">
	         <decor>no</decor>
	    </application>
	</applications>


***

通过 IP 拉取 DVR——使用 nginx 的基本媒体服务器


`sudo apt install nginx-light`


授予文件树到我们的视频目录的权限


`sudo chmod o+x /home /home/ubuntu /home/ubuntu/Videos`



备份默认加载页面并替换为我们自己的

`sudo mv /etc/nginx/sites-available/default /etc/nginx/sites-available/default.old`

`sudo nano /etc/nginx/sites-available/default`

添加以下内容，但将 x.x.x.x 替换为系统的网络 IP 地址：


	server {
		listen 8080;
		listen [::]:8080;

		server_name x.x.x.x;

		root /home/ubuntu/Videos;
  		autoindex on;
	}



重新启动 nginx 以使更改生效

`sudo systemctl 重启 nginx`

现在可以通过浏览器从 x.x.x.x:8080 下载您的 DVR

***

自动将 mkv DVR 转码为 hevc mp4

如果你想从头开始做苹果派，你必须首先发明宇宙——我们的 gstreamer 包不处理 mp4 h265 视频的多路复用，我们的 apt-get ffmpeg 包不包含 rkmpp 硬件加速....所以我们将在 https://github.com/nyanmisaka/ffmpeg-rockchip 的帮助下构建支持 mpp 的 ffmpeg

我们可以使用 3 个简单的安装脚本来完成此操作。

首先，我们从源代码构建 MPP，因为我们当前的 MPP 包比较旧。

其次，我们出于同样的原因从源头构建 RGA。

第三，我们构建了支持 rkmpp 和 rkrga 的 ffmpeg。

`sudo nano buildMPP.sh`

	mkdir -p ~/MPP && cd ~/MPP
	git clone -b jellyfin-mpp --depth=1 https://github.com/nyanmisaka/mpp.git rkmpp
	pushd rkmpp
	mkdir rkmpp_build
	pushd rkmpp_build
	cmake \
	    -DCMAKE_INSTALL_PREFIX=/usr \
	    -DCMAKE_BUILD_TYPE=Release \
	    -DBUILD_SHARED_LIBS=ON \
	    -DBUILD_TEST=OFF \
	    ..
	make -j $(nproc)
	sudo make install
 

`sudo nano buildRGA.sh`

	mkdir -p ~/RGA && cd ~/RGA
	git clone -b jellyfin-rga --depth=1 https://github.com/nyanmisaka/rk-mirrors.git rkrga
	meson setup rkrga rkrga_build \
	    --prefix=/usr \
	    --libdir=lib \
	    --buildtype=release \
	    --default-library=shared \
 	   -Dcpp_args=-fpermissive \
 	   -Dlibdrm=false \
 	   -Dlibrga_demo=false
	meson configure rkrga_build
	sudo ninja -C rkrga_build install

`sudo nano buildFFMPEG.sh`

	mkdir -p ~/ffmpeg && cd ~/ffmpeg
	git clone --depth=1 https://github.com/nyanmisaka/ffmpeg-rockchip.git ffmpeg
	cd ffmpeg
	./configure --prefix=/usr --enable-gpl --enable-version3 --enable-libdrm --enable-rkmpp --enable-rkrga
	make -j $(nproc)

	./ffmpeg -decoders | grep rkmpp
	./ffmpeg -encoders | grep rkmpp
	./ffmpeg -filters | grep rkrga

	sudo make install

使脚本可执行

`sudo chmod +x buildMPP.sh buildRGA.sh buildFFMPEG.sh`

并一次运行一个：

`./buildMPP.sh`

`./buildRGA.sh`

`./buildFFMPEG.sh`

现在我们可以使用 ffmpeg 将 mkv 视频文件硬件转码为 hevc mp4。我们可以通过增强 dvr.sh 脚本在每次录制结束时自动执行此操作。打开 /home/ubuntu/scripts 目录中的 dvr.sh 脚本，找到行"kill $RUNNING"并在其下方添加以下两行。

	sleep 0.2
 	ffmpeg -hwaccel rkmpp -i record_${current_date}.mkv -c:v hevc_rkmpp record_${current_date}.mp4

完整脚本应如下所示：

	#!/bin/bash

	export DISPLAY=:0

	xset s off -dpms

	GPIO_PIN=5
	RUNNING=0
	gpio mode $GPIO_PIN up

	cd /home/ubuntu/Videos

	while true; do
	if [ $(gpio read $GPIO_PIN) -eq 0 ]; then
	if [ $RUNNING -eq 0 ]; then
		current_date=$(date +'%m-%d-%Y_%H-%M-%S')
		
		gst-launch-1.0 -e \
		udpsrc port=5600 caps='application/x-rtp, media=(string)video, clock-rate=(int)90000, encoding-name=(string)H265' ! \
		rtph265depay ! \
		h265parse ! \
		tee name=t ! \
		queue ! \
		mppvideodec ! \
		videoconvert ! \
		autovideosink sync=false t. ! \
		queue ! \
		matroskamux ! \
		filesink location=record_${current_date}.mkv &
	
 		RUNNING=$!
	else
		kill $RUNNING
		RUNNING=0
		sleep 0.2
		ffmpeg -hwaccel rkmpp -i record_${current_date}.mkv -c:v hevc_rkmpp record_${current_date}.mp4
	fi
	sleep 0.2
	fi
	sleep 0.1
	done

 ***
