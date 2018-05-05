---
layout: post
title: "Build a RaspberryPi straming server"
date: 2018-05-01 02:11 +0800
categories: tech
---

# Hardwares needed
```
RaspberryPi 3B
RaspberryPi Camera
```

# Softwares needed
```
python-picamera
```
install the picamera lib
```
sudo apt-get install python-picamera
```

# Enable picamera
```
sudo raspi-config
```
choose interfacing options -> enable the camera, then reboot the pi:
```
sudo reboot
```

# Check the picamera is working
create a camera.py file, and copy following code snippet in the file.
```
#! /usr/bin/python
from picamera import PiCamera
from time import sleep

camera = PiCamera()
camera.start_preview()
sleep(5)
camera.capture('/home/pi/file/image001.jpg')
camera.stop_preview()
```
If there is a image called image001.jpg has been taken in the directory, it shows the
camera is working as expected.

# vlc
## Compile vlc with hardware acceleration
https://www.raspberrypi.org/forums/viewtopic.php?f=66&t=195221
## install vlc on client
```
sudo apt-get install vlc
```
# Run vcl streaming on raspberrypi
```
raspivid -o - -t 0 -n | cvlc -vvv stream:///dev/stdin --sout '#rtp{sdp=rtsp://:8554/}' :demux=h264
```
## Create a shell script for future use
```
#!/bin/bash
raspivid -o - -t 0 -n | cvlc -vvv stream:///dev/stdin --sout '#rtp{sdp=rtsp://:8554/}' :demux=h264
```
## Specifying camera resoluation
```
raspivid -o - -t 0 -n -w 600 -h 400 -fps 12 | cvlc -vvv stream:///dev/stdin --sout '#rtp{sdp=rtsp://:8554/}' :demux=h264
```

# Watching streaming videos on client
Open the vlc player -> Media -> Network streaming -> Network, input the rtsp streaming
address in the input box: rtsp://192.168.0.200:8554/, notice that you cannot drop the slash, otherwise
you cannot connect to the straming server; it will show error like this:
```
Your input can't be opened:
VLC is unable to open the MRL 'rtsp://192.168.0.200:8554'. Check the log for details.
```
If anthing goes well, now, you can watch the real time video camputred by the pi camera though the latency
is a big issue. The following image shows my straming server is working properly:
[![](/assets/images/2018-05-01/in-streaming.png)](/assets/images/2018-05-01/in-streaming.png)

## The real time streaming
Though there's about 5 seconds latency, the streaming seems works just fine.
<video controls autoplay>
    <source src="/assets/videos/2018-05-01/rasp-vlc-streaming-sample.mp4" type="video/mp4"/>
</video>

# Notice: Check the power is enough, 5V and 2.5A are needed to run the straming server