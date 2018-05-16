---
layout: post
title: "Ubuntu 16.04 network connection drops every few minutes or seconds"
date: 2018-01-15 22:45:04 +0800
categories: tech
---

# My network info
```
ray@ray-pc:~$ sudo lshw -C network
[sudo] password for ray:
  *-network               
       description: Ethernet interface
       product: RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller
       vendor: Realtek Semiconductor Co., Ltd.
       physical id: 0
       bus info: pci@0000:03:00.0
       logical name: enp3s0
       version: 0c
       serial: 08:62:66:2f:cf:1e
       size: 100Mbit/s
       capacity: 1Gbit/s
       width: 64 bits
       clock: 33MHz
       capabilities: pm msi pciexpress msix vpd bus_master cap_list ethernet physical tp mii 10bt 10bt-fd 100bt 100bt-fd 1000bt 1000bt-fd autonegotiation
       configuration: autonegotiation=on broadcast=yes driver=r8169 driverversion=2.3LK-NAPI duplex=full firmware=rtl8168g-2_0.0.1 02/06/13 ip=192.168.1.115 latency=0 link=yes multicast=yes port=MII speed=100Mbit/s
       resources: irq:26 ioport:e000(size=256) memory:f7c00000-f7c00fff memory:f0000000-f0003fff
```
I have r81618 Ethernet controller, but the driver is r8169, this causes my Ethernet connection drops every few minutes or seconds, the network is very unstable.

# Checkout my linux kernel
```
uname -a
Linux ray-pc 4.13.0-26-generic #29~16.04.2-Ubuntu SMP Tue Jan 9 22:00:44 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
```

# Checkout the r81618 driver:
```
ls /var/lib/dkms/r8168
8.042.00  kernel-4.4.0-21-generic-x86_64
```

My linux kernel is 4.13.0-26-generic, I cannot install a lower version of r81618, if I do, it show build errors.Then I open the r81618 driver list on http://mirrors.kernel.org/ubuntu/pool/universe/r/r8168/. I think the reason way can't I install the r81618 driver with lower version is that my linux kernel upgraded automatically couple days ago, then the Ethernet controller driver doesn't work properly.

So I installed the latest driver r8168-dkms_8.045.08-2_all.deb, and I installed smoothly.
```
wget http://mirrors.kernel.org/ubuntu/pool/universe/r/r8168/r8168-dkms_8.045.08-2_all.deb
--2018-01-15 11:58:57--  http://mirrors.kernel.org/ubuntu/pool/universe/r/r8168/r8168-dkms_8.045.08-2_all.deb
Resolving mirrors.kernel.org (mirrors.kernel.org)... 198.145.21.9, 2001:19d0:306:6:0:1994:3:14
Connecting to mirrors.kernel.org (mirrors.kernel.org)|198.145.21.9|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 94872 (93K) [application/octet-stream]
Saving to: ‘r8168-dkms_8.045.08-2_all.deb’

r8168-dkms_8.045.08 100%[===================>]  92.65K  30.3KB/s    in 3.1s    

2018-01-15 11:59:01 (30.3 KB/s) - ‘r8168-dkms_8.045.08-2_all.deb’ saved [94872/94872]
```

```
sudo dpkg -i r8168-dkms_8.045.08-2_all.deb
(Reading database ... 298486 files and directories currently installed.)
Preparing to unpack r8168-dkms_8.045.08-2_all.deb ...

------------------------------
Deleting module version: 8.042.00
completely from the DKMS tree.
------------------------------
Done.
Unpacking r8168-dkms (8.045.08-2) over (8.042.00-2) ...
Setting up r8168-dkms (8.045.08-2) ...
Loading new r8168-8.045.08 DKMS files...
Building only for 4.13.0-26-generic
Building initial module for 4.13.0-26-generic
Done.

r8168:
Running module version sanity check.
 - Original module
   - No original module exists within this kernel
 - Installation
   - Installing to /lib/modules/4.13.0-26-generic/updates/dkms/

depmod.......

Backing up initrd.img-4.13.0-26-generic to /boot/initrd.img-4.13.0-26-generic.old-dkms
Making new initrd.img-4.13.0-26-generic
(If next boot fails, revert to initrd.img-4.13.0-26-generic.old-dkms image)
update-initramfs....

DKMS: install completed.
Processing triggers for initramfs-tools (0.122ubuntu8.8) ...
update-initramfs: Generating /boot/initrd.img-4.13.0-26-generic
```

# Checkout the newly installed version of r81618:
```
ls /var/lib/dkms/r8168
8.045.08  kernel-4.13.0-26-generic-x86_64
```
Now, the driver update from 8.042 to 8.045. I'll reboot my computer to see if everything goes well.

After reboot, my driver is changed to r8168
```
sudo lshw -C  network
  *-network               
       description: Ethernet interface
       product: RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller
       vendor: Realtek Semiconductor Co., Ltd.
       physical id: 0
       bus info: pci@0000:03:00.0
       logical name: enp3s0
       version: 0c
       serial: 08:62:66:2f:cf:1e
       size: 100Mbit/s
       capacity: 1Gbit/s
       width: 64 bits
       clock: 33MHz
       capabilities: pm msi pciexpress msix vpd bus_master cap_list ethernet physical tp 10bt 10bt-fd 100bt 100bt-fd 1000bt-fd autonegotiation
       configuration: autonegotiation=on broadcast=yes driver=r8168 driverversion=8.045.08-NAPI duplex=full ip=192.168.1.115 latency=0 link=yes multicast=yes port=twisted pair speed=100Mbit/s
       resources: irq:25 ioport:e000(size=256) memory:f7c00000-f7c00fff memory:f0000000-f0003fff
```

The problem still exists. I tried everything I can think of, but still no luck, I even delete the latest Linux kernel image on my computer. The problem continues annoying me. I remove the network-manager completely, and append the code below:
```
allow-hotplug enp3s0
iface eth0 inet dhcp
```
into the file /etc/network/interfaces. and run the lines:
```
sudo ifdown enp3s0
sudo ifup enp3s0
```
Now, I am using dhcp. I can use static IP if I want, just append code below in the file /etc/network/interfaces:
```
iface eth0 inet static
address 192.168.1.22
netmask 255.255.255.0
gateway 192.168.1.1
``

When I connect to the internet, the speed seems faster than before and the connection frequently drops problem changes a little better, but it still there.
