---
layout: post
title: Linux下FTP服务器搭建
date: 2017-05-12
categories: tech
---

# 1 所需工具
操作系统: ubuntu 16.04
FTP服务器: vsftpd
FTP常规模式（Normal mode): xinetd

# 2 安装
安装vsftpd: sudo apt-get install vsftpd

![setup](http://b385.photo.store.qq.com/psb?/V11dEA6U3qTHQy/5llYPoYx5a66Kt7qON8oaE2sHXuFJnn780.M7Z9zMss!/b/dIEBAAAAAAAA&bo=GwOvAQAAAAADEIM!)

安装完成后，启动下，看是否成功：

![](http://b365.photo.store.qq.com/psb?/V11dEA6U3qTHQy/Uny.7YDfHfXlP74U9izwN*bUMnYVvRj.Bjiy7YMSua0!/b/dG0BAAAAAAAA&bo=HQOlAQAAAAADEI8!)
如上图，启动没问题，说明已成功安装vsftpd。

安装xinetd: sudo apt-get install xinetd

![](http://b365.photo.store.qq.com/psb?/V11dEA6U3qTHQy/tbxL3E0URxZ6Ez7sf6Px3kZJownrtT.PN0H3Erd08Hc!/b/dG0BAAAAAAAA&bo=GgOwAQAAAAADEJ0!)

因为我已经安装过了，所以不会再去下载安装。

# 3 配置
## 3.1 vsftpd的访问配置：
用vim打开vsftpd的配置文件：sudo vim /etc/vsftpd.conf

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/AIOA1xrtUxjcLmeNt*e*ECWc9PhiNBeZXxfl22jqvlI!/o/dG0BAAAAAAAA&bo=GwOsARsDrAEDEDU!)

按i键修改配置：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/ZtCjB5NiNk2sIPUTuG9PHldhW3P38Zsv32qRdErWPTw!/o/dG0BAAAAAAAA&bo=GwOtARsDrQEDEDU!)

将 listen=YES 和 listen_ipv6=YES 改为 NO，因为我们将用xinetd来管理vsftpd。
anonymous_enable=YES 即是否允许匿名访问。
这里主要注意 write_enable，默认是注释掉的并且值为NO。这里代打开注释，将值设为YES，表示可以允许授权用户进行写操作。write_enable值设为YES后，我们就可以用FTP客户端传输文件到FTP服务器了。

## 3.2 xinetd的配置：
在/etc文件夹下创建文件夹xinetd.d
```
sudo mkdir xinetd.d
```
跳到/etc/xinetd.d/下创建文件vsftpd
```
vim vsftpd
```
然后加入以下内容：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/YaxxJZpg1nO.byY7.DsDk*XcbNiHH8RADnDKhFN5l3w!/o/dGoBAAAAAAAA&bo=kAIDAZACAwEDEDU!)

其中no_access是要阻塞的主机，根据实际情况设置。
设置好后，按Esc键，键入:wq退出vim。
停止vstpd服务：
```
sudo service vsftpd stop
```

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/8mznMq8DPLjRvD*14kX0qldx0CJoehXNG1mvZ6n6xHw!/o/dIQBAAAAAAAA&bo=6QE5AOkBOQADEDU!)

现在我们就可一启动FTP服务器的Normal模式：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/SDZJ9b0rg2.kZ2xviIlDQtUbQ9V82mkCVRH9MvxgGL0!/o/dG4BAAAAAAAA&bo=cwJLAHMCSwADEDU!)

# 4 文件传输前的权限配置
在远程服务器上创建文件夹（这里是file）:

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/Xo4X*VRVWN*ldptmHfZ3SZSZrrY.Mn*Hh06xOX3M6lA!/o/dGwBAAAAAAAA&bo=5AKmAOQCpgADEDU!)

然后将文件夹的权限付给ftp用户，这里是ubuntu:
```
sudo chown -R ubuntu file
sudo chmod -R 777 file
```

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/JeX*9eEH3Zt9iC*wSfxJl0F7cH4bMhioGUnLryuGGBE!/o/dCUAAAAAAAAA&bo=9QLUAPUC1AADEDU!)

到这里，设置基本完成了，接下来检验下能不能传文件，来验证FTP服务器是否搭建成功。

# 5 文件传输
## 5.1 ftp命令
在本机用ftp命令连接远程FTP服务器：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/e6HJ3WI7jJ5.ezh4chm1*.2be1f35qN3N5yKX20hSpk!/o/dGwBAAAAAAAA&bo=3AK2AdwCtgEDEDU!)

输入用户名和密码后，我们成功登录了FTP服务器。
如果登录失败：
530 Login incorrect.
Login failed.
将 /etc/vsftpd.conf 下键值pam_service_name=vsftpd 改为pam_service_name=ftp.
用ls命令查看FTP根目录：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/1HHZrlKdeyY2PwXUYjcUAmcdYvTnm*O3cSjyUJ2yXNw!/o/dIMBAAAAAAAA&bo=3AK2AdwCtgEDEDU!)

我们可以看到我们创建的file文件夹。我们接下来用file文件夹存放要上传的文件。用put命令上传文件：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/Qo**wEp35d4NKJcUK6HDOEcImvXtJrYa7EzZ95eam9c!/o/dGwBAAAAAAAA&bo=pAMQAqQDEAIDEDU!)

把本地名为test001.txt的文件成功地上传到了远程服务器上。
用ssh看下文件内容：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/6c9h7RLDYN63xETC1etBpAeqeHvMyWjTA4eqEe7jkKE!/o/dN8AAAAAAAAA&bo=HQMKAR0DCgEDEDU!)

再看看本地的文件内容：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/BHSGGOrvy.GYLUnIywH91*wcaetdIgPLF*R006ms18k!/o/dGwBAAAAAAAA&bo=IQNpASEDaQEDEDU!)

两边内容一致，说明FTP服务器可以正常工作了！！！

## 5.2 nautilus文件管理器
我们可以用linux自带的文件管理器nautilus来作为FTP客户端。
用nautilus的connect to server连接远程FTP服务器：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/N0sBTpmV7wOmDqzYPawyVapRYfEcr48VA0fE1i*KQAA!/o/dGoBAAAAAAAA&bo=qAFxAagBcQEDEDU!)

连接成功后，和用ftp命令一样，打开了ftp的根目录，我们可以查看file文件夹下刚刚上传上去的文件：

![](http://r.photo.store.qq.com/psb?/V11dEA6U3qTHQy/EpFE5oW6Z.mD0KRwF7uNXb31Gsjad1lW9L9YWL2wG1I!/o/dG0BAAAAAAAA&bo=HwPBAB8DwQADEDU!)

用nautilus，就可以像在本地一样管理远程FTP服务器的文件了。

# 6. 参考文档
[How to setup and use FTP Server in Ubuntu Linux](https://linuxconfig.org/how-to-setup-and-use-ftp-server-in-ubuntu-linux)

[VSFTPD keeps giving me 550 Permission Denied](https://ubuntuforums.org/showthread.php?t=833829)

[Beginner’s guide to using ftp](http://www.tldp.org/HOWTO/FTP-3.html)