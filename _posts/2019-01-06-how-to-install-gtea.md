---
layout: post
title: "如何安装Gitea"
date: 2019-01-06 19:00 +0800
categories: tech
---

# 官方安装方法
https://docs.gitea.io/en-us/install-from-binary/

# Gitea作为系统服务
https://docs.gitea.io/en-us/linux-service/

> /etc/systemd/system/gitea.service

```
[Unit]
Description=Gitea (Git with a cup of tea)
After=syslog.target
After=network.target
#After=mysqld.service
#After=postgresql.service
#After=memcached.service
#After=redis.service

[Service]
# Modify these two values and uncomment them if you have
# repos with lots of files and get an HTTP error 500 because
# of that
###
#LimitMEMLOCK=infinity
#LimitNOFILE=65535
RestartSec=2s
Type=simple
User=git
Group=git
WorkingDirectory=/var/lib/gitea/
ExecStart=/usr/local/bin/gitea web -c /etc/gitea/app.ini
Restart=always
Environment=USER=git HOME=/home/git GITEA_WORK_DIR=/var/lib/gitea
# If you want to bind Gitea to a port below 1024 uncomment
# the two values below
###
#CapabilityBoundingSet=CAP_NET_BIND_SERVICE
#AmbientCapabilities=CAP_NET_BIND_SERVICE

[Install]
WantedBy=multi-user.target
```
如果要用到MySQL，就打开After=mysqld.service，其他保持默认值。

> 启动服务
```
sudo systemctl enable gitea
sudo systemctl start gitea
```

> 查看服务状态

```
sudo service gitea status
```

下面是按照官方文档操作，服务没起来时的状态：
```
● gitea.service - Gitea (Git with a cup of tea)
   Loaded: loaded (/etc/systemd/system/gitea.service; enabled; vendor preset: enabled)
   Active: activating (auto-restart) (Result: exit-code) since Sun 2019-01-06 09:36:23 UTC
  Process: 12701 ExecStart=/usr/local/bin/gitea web -c /etc/gitea/app.ini (code=exited, st
 Main PID: 12701 (code=exited, status=1/FAILURE)

Jan 06 09:36:23 ahezime systemd[1]: gitea.service: Main process exited, code=exited, statu
Jan 06 09:36:23 ahezime systemd[1]: gitea.service: Unit entered failed state.
Jan 06 09:36:23 ahezime systemd[1]: gitea.service: Failed with result 'exit-code'.
lines 1-9/9 (END)
```

通过journalctl查看进程日志
```
journalctl -b 0 -u gitea
```

发现错误如下：
```
Jan 06 09:43:15 ahezime gitea[18618]: 2019/01/06 09:43:15 [W] Custom config '/etc/gitea/app.ini' not found, ignore this if you're running first time
Jan 06 09:43:15 ahezime gitea[18618]: 2019/01/06 09:43:15 [...s/setting/setting.go:808 NewContext()] [E] Failed to create '/home/git/.ssh': mkdir /home/git: permission denied
Jan 06 09:43:15 ahezime systemd[1]: gitea.service: Main process exited, code=exited, status=1/FAILURE
Jan 06 09:43:15 ahezime systemd[1]: gitea.service: Unit entered failed state.
Jan 06 09:43:15 ahezime systemd[1]: gitea.service: Failed with result 'exit-code'.
Jan 06 09:43:17 ahezime systemd[1]: gitea.service: Service hold-off time over, scheduling restart.
Jan 06 09:43:17 ahezime systemd[1]: Stopped Gitea (Git with a cup of tea).
Jan 06 09:43:17 ahezime systemd[1]: Started Gitea (Git with a cup of tea).
```

原来是少了一个文件和目录，创建文件和目录并将权限赋给git用户：
```
chown root:git /etc/gitea
chmod 770 /etc/gitea

touch /etc/gitea/app.ini
chown root:git /etc/gitea/app.ini
chmod 770 /etc/gitea/app.ini
```

再来启动下gitea系统服务：
```
systemctl start gitea
```

启动后的正常状态：
```
● gitea.service - Gitea (Git with a cup of tea)
   Loaded: loaded (/etc/systemd/system/gitea.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2019-01-06 10:57:05 UTC; 42s ago
 Main PID: 19362 (gitea)
    Tasks: 7
   Memory: 29.5M
      CPU: 211ms
   CGroup: /system.slice/gitea.service
           └─19362 /usr/local/bin/gitea web -c /etc/gitea/app.ini

Jan 06 10:57:05 ahezime gitea[19362]: 2019/01/06 10:57:05 [I] Gitea v1.5.0 built with: bi
Jan 06 10:57:05 ahezime gitea[19362]: 2019/01/06 10:57:05 [I] Log Mode: Console(Info)
Jan 06 10:57:05 ahezime gitea[19362]: 2019/01/06 10:57:05 [I] XORM Log Mode: Console(Info
Jan 06 10:57:05 ahezime gitea[19362]: 2019/01/06 10:57:05 [I] Cache Service Enabled
Jan 06 10:57:05 ahezime gitea[19362]: 2019/01/06 10:57:05 [I] Session Service Enabled
Jan 06 10:57:05 ahezime gitea[19362]: 2019/01/06 10:57:05 [I] SQLite3 Supported
Jan 06 10:57:05 ahezime gitea[19362]: 2019/01/06 10:57:05 [I] Run Mode: Development
Jan 06 10:57:05 ahezime gitea[19362]: 2019/01/06 10:57:05 Serving [::]:3000 with pid 1936
Jan 06 10:57:05 ahezime gitea[19362]: 2019/01/06 10:57:05 [I] Listen: http://0.0.0.0:3000
Jan 06 10:57:18 ahezime systemd[1]: Started Gitea (Git with a cup of tea).
```
如果Gitea是安装在本机，在浏览器中输入地址http://127.0.0.1:3000，便可以看到首页了。
