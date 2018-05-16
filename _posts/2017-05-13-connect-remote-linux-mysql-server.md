---
layout: post
title: 远程连接Linux下MySQL服务器
date: 2017-05-13
categories: tech
---

# 1. 工具
```
ubuntu 16.04
mysql-server
iptables
ufw
netstat
```

# 2. 安装
sudo apt-get install mysql-server
可以修改root用户的密码：
```
alter user ‘root’@’%’ identified by ‘xyz’;
```
其中‘%’表示连接到mysql服务器的所有机器，设置后将使用xyz作为登录密码。
登录MySQL服务器：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/AtJZKlnExUAtw7yZ3yPA*..grf*gKaoWxDj85dDe*SM!/o/dG4BAAAAAAAA&bo=0AITAdACEwEDEDU!)

# 3. 配置
要想让远程客户端访问MySQL服务器，一是要确定客户端访问没系统拦截；二是要配置mysql，授权用户访问。其中访问被拦截，客户端提示代码为2003-10060的错误，可以通过配置iptables或开启防火墙并添加mysql访问来解决。对于mysql没授权会提示代码1130的错误，可以通过授权用户解决。

## 3.1 iptables
未配置iptables是访问发生错误：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/GjdKdvJ2PkgY3fbPtVN488KQK7Wi4ISjL9gYfYPmrvU!/o/dGwBAAAAAAAA&bo=WwJ4AFsCeAADEDU!)

添加iptables访问规则:

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/1FnI0nzZOJ4ZO16rrroM3yO1pCiN5SECwh.CtwDi1MI!/o/dIMBAAAAAAAA&bo=gAIwAIACMAADEDU!)

服务器接受访问3306的端口，将数据流转移到网卡eth0。
可以查看iptables的配置：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/KIbvmciy0ssyu4C4UAOs5CMpn2JqETeGD6xYKnzzJKo!/o/dGwBAAAAAAAA&bo=FgMiARYDIgEDEDU!)

## 3.2 ufw
当防火墙打开时，没有开放mysql访问端口时的错误：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/GjdKdvJ2PkgY3fbPtVN488KQK7Wi4ISjL9gYfYPmrvU!/o/dGwBAAAAAAAA&bo=WwJ4AFsCeAADEDU!)

打开ufw:

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/e*mmg0Py.7pYuOSQVM5BamD03Cna0GgKEhzFij3Wq6U!/o/dGwBAAAAAAAA&bo=xQI2AMUCNgADEDU!)

在打开时，提示是否及继续，继续的话ssh访问会受到影响，选择y。然后添加允许ssh访问。此外，当ufw diable后，将不能访问iptables配置，只有enable时才可以访问：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/Ct1Pb4mberakse4g3ZFA7xyzSWeHky2kIIbzxvy6ciw!/o/dN8AAAAAAAAA&bo=CgO7AAoDuwADEDU!)

```
sudo ufw allow ssh
```
添加ssh后查看状态:

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/Bz.YLZjgnn1bRAKJs0ZHHT1as2wrZrWsscaORb9LB1o!/o/dG4BAAAAAAAA&bo=DwKuAA8CrgADEDU!)

此时，ufw只允许ssh访问，其他如ftp，mysql，http等访问将被拦截。添加其他访问后的状态：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/bnauM.xToxWghnLmghzOow5WJsPniZl.dhxodcbLBu8!/o/dGoBAAAAAAAA&bo=OALzADgC8wADEDU!)

可以删除不想开放的服务端口：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/4F0cmbaOaiCGP2XN3DqN4AoBNshsxrFYoVg1.*zLA8E!/o/dGwBAAAAAAAA&bo=EQJQABECUAADEDU!)

## 3.3 mysql
远程连接MySQL服务器时，如未配置mysql访问权限，那么访问错误：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/oVG3QhQjG2G7DSWLSF1A7vdvjYySj9W7H5cUBjH1zLQ!/o/dGwBAAAAAAAA&bo=kAK3AJACtwADEDU!)

授权访问：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/gMYWQCZ8pB32QOf2A2xywzh0l65TYuv1jO*NiTY*0P4!/o/dFUBAAAAAAAA&bo=CwNxAAsDcQADEDU!)

这里给任何机器授权以root用户和密码为root的访问。当然，在实际应用中这样的授权是不安全的，可以限制特定ip来访问mysql服务器，如：
```
grant all privileges on *.* to ‘root’@’127.0.0.1’ identified by ‘root’ with grant option
```

最后，要想让其他任何机器连接MySQL服务器，修改mysql的bind-address配置：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/*Cyj.r2HV.rM8u4pq2EzsJOsIf8ZwjGXXaLg2ZBNpOs!/o/dGwBAAAAAAAA&bo=eQJKAHkCSgADEDU!)

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/VMhxQwM5yb8wiompiEfLnz060qxoQwvYuk1UTcvr8IU!/o/dGwBAAAAAAAA&bo=BgOtAQYDrQEDEDU!)

bind-address = 127.0.0.1 是的网络状态：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/TAymDF.CqlFGgVQNAofnqJKVcbos04krRuVSmPFJR.o!/o/dGwBAAAAAAAA&bo=zwJHAM8CRwADEDU!)

bind-address = 127.0.0.1时的远程连接错误：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/rlJ2X96Puzo0Ww0AhN28B3gBRAXzw1bUFRydKyxLcsM!/o/dGsBAAAAAAAA&bo=WwJ4AFsCeAADEDU!)

bind-address现在绑定的是本机的环回接口127.0.0.1，要想让其他机器访问，那么就要修改ip地址。这里直接注释这条语句，表示任何机器都可以远程连接MySQL服务器。
重启mysql服务，让配置生效：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/boUosRYbjYR5lnn6jNucXDqXgxmI27xMyv1gI97f61Q!/o/dGwBAAAAAAAA&bo=ygIpAMoCKQADEDU!)

重启后的网络监听状态：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/k7Sn38DjypBc8j7FMjOe4M2lMUr74QTXI4ql20Y9JOA!/o/dGwBAAAAAAAA&bo=*gJbAP4CWwADEDU!)


此时，mysql服务监听任何连接mysql服务器的3306端口。
到现在，所有的配置已经完成。

# 4. 连接
本地用navicat连接：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/ACY81ZCjeF2CHBq.42e*NLv7o*R0ZEFN4ldjZpZv11Y!/o/dGkBAAAAAAAA&bo=LAMwASwDMAEDEDU!)

如图，可以成功连接到远程MySQL服务器了。

# 5. 参考资料
[https://www.linux.com/blog/installing-and-using-mysql-ubuntu](https://www.linux.com/blog/installing-and-using-mysql-ubuntu)

[http://blog.csdn.net/qq_16885135/article/details/53096451](http://blog.csdn.net/qq_16885135/article/details/53096451)

[https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-14-04](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-14-04)

[http://stackoverflow.com/questions/13208614/restricting-mysql-connections-from-localhost-to-improve-security](http://stackoverflow.com/questions/13208614/restricting-mysql-connections-from-localhost-to-improve-security)

[https://www.cyberciti.biz/tips/linux-iptables-18-allow-mysql-server-incoming-request.html](https://www.cyberciti.biz/tips/linux-iptables-18-allow-mysql-server-incoming-request.html)

[https://theos.in/desktop-linux/tip-that-matters/how-do-i-restart-mysql-server/](https://theos.in/desktop-linux/tip-that-matters/how-do-i-restart-mysql-server/)

[http://stackoverflow.com/questions/5864242/how-to-test-which-port-mysql-is-running-on-and-whether-it-can-be-connected-to](http://stackoverflow.com/questions/5864242/how-to-test-which-port-mysql-is-running-on-and-whether-it-can-be-connected-to)
