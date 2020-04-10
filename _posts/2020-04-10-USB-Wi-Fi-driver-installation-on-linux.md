---
layout: post
title: "Ubuntu Linux 下 USB Wi-Fi 驱动安装"
date: 2020-01-17 23:14 +0800
categories: tech
---

## Preparation
1. An USB Wi-Fi Adapter(here I use the Tenda U12 USB Wi-Fi Adapter)
2. A working network connection(we will use it to download the linux driver)

## Instructions
```
sudo apt purge rtl8812au-dkms
sudo apt install git
git clone https://github.com/gnab/rtl8812au.git
sudo cp -r rtl8812au /usr/src/rtl8812au-4.2.2
sudo dkms add -m rtl8812au -v 4.2.2
sudo dkms build -m rtl8812au -v 4.2.2
sudo dkms install -m rtl8812au -v 4.2.2
```
If you already have the git installed, skip the second step.

## ifconfig command infos
Before installation: 
```
ray@ray-pc:~$ ifconfig
enp4s0    Link encap:Ethernet  HWaddr 04:d9:f5:d1:bd:34  
          UP BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:18910184 errors:0 dropped:0 overruns:0 frame:0
          TX packets:18910184 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:4096144845 (4.0 GB)  TX bytes:4096144845 (4.0 GB)
```

After installation but not connected yet:
```
ray@ray-pc:~$ ifconfig
enp4s0    Link encap:Ethernet  HWaddr 04:d9:f5:d1:bd:34  
          UP BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

enx502b73dc4dff Link encap:Ethernet  HWaddr 50:2b:73:dc:4d:ff  
          UP BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:18910187 errors:0 dropped:0 overruns:0 frame:0
          TX packets:18910187 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:4096145573 (4.0 GB)  TX bytes:4096145573 (4.0 GB)
```
Here we notice the new network `enx502b73dc4dff`.

Connect to the newly created network:
```
enp4s0    Link encap:Ethernet  HWaddr 04:d9:f5:d1:bd:34  
          UP BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

enx502b73dc4dff Link encap:Ethernet  HWaddr 50:2b:73:dc:4d:ff  
          inet addr:192.168.1.113  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::b334:eb59:ebc6:bb5a/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:113 errors:0 dropped:0 overruns:0 frame:0
          TX packets:170 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:33926 (33.9 KB)  TX bytes:61532 (61.5 KB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:18910310 errors:0 dropped:0 overruns:0 frame:0
          TX packets:18910310 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:4096164517 (4.0 GB)  TX bytes:4096164517 (4.0 GB)
```
Once the connection established, we got the assigned IP `192.168.1.113`.



## Reference
https://rehmann.co/blog/drivers-wifi-usb-adapter-osxmac-linux-windows/