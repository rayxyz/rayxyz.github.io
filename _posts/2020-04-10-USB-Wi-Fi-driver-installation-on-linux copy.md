---
layout: post
title: "Ubuntu Linux 下 USB Wi-Fi 适配器驱动安装"
date: 2020-04-10 13:01 +0800
categories: tech
---

The working USB Wi-Fi adapter detects the active networks

![https://rayxyz.github.io/assets/images/general/USB-WiFi-Adapter-Networks-Detected.png](https://rayxyz.github.io/assets/images/general/USB-WiFi-Adapter-Networks-Detected.png)

## Before
When I download the official Tenda U12 USB Wi-Fi Adapter linux driver, I cannot compile the driver, it always says:
```
Authentication requested [root] for make driver:
make ARCH=x86_64 CROSS_COMPILE= -C /lib/modules/4.15.0-50-generic/build M=/home/ray/softs/U12_linux_v5.1.5_19247.20160830/driver/rtl8812AU_linux_v5.1.5_19247.20160830  modules
make[1]: Entering directory '/usr/src/linux-headers-4.15.0-50-generic'
arch/x86/Makefile:245: *** You are building kernel with non-retpoline compiler, please update your compiler..  Stop.
make[1]: Leaving directory '/usr/src/linux-headers-4.15.0-50-generic'
Makefile:1838: recipe for target 'modules' failed
make: *** [modules] Error 2
##################################################
Compile make driver error: 2
Please check error Mesg
##################################################
```
I was desperately working on the offical, updated my gcc to higher or downgraded it, the errors occured each time. So I searched for long time and finally found the working solution below.

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

The blue-working-light on the USB adapter is not working

![https://rayxyz.github.io/assets/images/general/usb-wifi-adapter-is-not-working.png](https://rayxyz.github.io/assets/images/general/usb-wifi-adapter-is-not-working.png)

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

When the USB Wi-Fi adapter is working, the blue light is blinking

![https://rayxyz.github.io/assets/images/general/usb-wifi-adapter-is-not-working.png](https://rayxyz.github.io/assets/images/general/usb-wifi-adapter-is-working.png)


## Reference
[https://rehmann.co/blog/drivers-wifi-usb-adapter-osxmac-linux-windows/](https://rehmann.co/blog/drivers-wifi-usb-adapter-osxmac-linux-windows/)