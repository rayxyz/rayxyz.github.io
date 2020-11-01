---
layout: post
title: "搭建基于Elastic Stack的日志系统"
date: 2030-01-01 16:30 +0800
categories: tech
---

本文章展示如何在Ubuntu Linux 20.04下安装Elastic Stack，搭建基于Elastic Stack的日志分析系统。为了方便展示，我用VirtualBox新建了虚拟机，安装好干净的Ubuntu Linux 20.04 Server。

# VirtualBox上Ubuntu20.04的配置
## 硬件配置信息

![https://rayxyz.github.io/assets/images/general/elk-vm-ubuntu20.04.png](https://rayxyz.github.io/assets/images/general/elk-vm-ubuntu20.04.png)

## 网络配置
网络配置使用`Bridge Adaptor`方式，使虚拟机上的Linux系统和本机在同一个网络，方便SSH。

![https://rayxyz.github.io/assets/images/general/elk-vm-ubuntu20.04-network-bridge.png](https://rayxyz.github.io/assets/images/general/elk-vm-ubuntu20.04-network-bridge.png)


# 安装Java8
由于Elastic Stack基于Java环境，所以先安装Java。

## 命令行安装：
```
sudo apt install openjdk-8-jdk
```
安装好后不用配置环境变量。

## 手动.deb包安装:
下载地址: [Java SE Development Kit 8u271](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html) 

版本: `jdk-8u271-linux-x64.tar.gz`

安装: 
```
sudo tar -xzvf jdk-8u271-linux-x64.tar.gz -C /usr/local 
```

配置Java环境变量: 

系统级环境变量
```
sudo vim /etc/profile 
```
添加
```
export JAVA_HOME=/usr/local/jdk1.8.0_271
export PATH=$JAVA_HOME:$PATH
```
重启，是环境变量生效: 
```
reboot 
```

用户级环境变量: 
```
sudo vim ~/.profile
```
添加: 
```
export JAVA_HOME=/usr/local/jdk1.8.0_271
export PATH=$JAVA_HOME:$PATH
```
source以下，是环境变量生效: 
```
source ~/.profile
```

# 安装Elastic Stack

这里使用官方安装方式，地址: [Getting started with the Elastic Stack](https://www.elastic.co/guide/en/elastic-stack-get-started/current/get-started-elastic-stack.html)

## 安装Elastic
```
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.9.2-amd64.deb
sudo dpkg -i elasticsearch-7.9.2-amd64.deb
sudo service elasticsearch start 
```
将elasticsearch安装为系统服务：
```
sudo systemctl enable elasticsearch
```

## 安装kibana
```
curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-7.9.2-linux-x86_64.tar.gz
tar xzvf kibana-7.9.2-linux-x86_64.tar.gz
```

运行kibana: 
```
cd ~/kibana-7.9.2-linux-x86_64/bin
ray@rayvm01:~/kibana-7.9.2-linux-x86_64/bin$ sudo ./bin/kibana --allow-root
```

## 安装Nginx，向外提供kibana访问 
重新开一个终端安装。
```
sudo apt-get install nginx
```

创建kibana服务配置文件kibana.conf
```
ray@rayvm01:~$ cat /etc/nginx/conf.d/kibana.conf 
server {
    listen 80;

    server_name kibana;

#    auth_basic "Restricted Access";
#    auth_basic_user_file /etc/nginx/htpasswd.users;

    location / {
        proxy_pass http://localhost:5601/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

注释掉Nignx默认服务器配置: 
```
ray@rayvm01:/etc/nginx/sites-enabled$ ls
default
ray@rayvm01:/etc/nginx/sites-enabled$ cat default 
##
# You should look at the following URL's in order to grasp a solid understanding
# of Nginx configuration files in order to fully unleash the power of Nginx.
# https://www.nginx.com/resources/wiki/start/
# https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/
# https://wiki.debian.org/Nginx/DirectoryStructure
#
# In most cases, administrators will remove this file from sites-enabled/ and
# leave it as reference inside of sites-available where it will continue to be
# updated by the nginx packaging team.
#
# This file will automatically load configuration files provided by other
# applications, such as Drupal or Wordpress. These applications will be made
# available underneath a path with that package name, such as /drupal8.
#
# Please see /usr/share/doc/nginx-doc/examples/ for more detailed examples.
##

# Default server configuration
#
#server {
#	listen 80 default_server;
#	listen [::]:80 default_server;
#
#	# SSL configuration
#	#
#	# listen 443 ssl default_server;
#	# listen [::]:443 ssl default_server;
#	#
#	# Note: You should disable gzip for SSL traffic.
#	# See: https://bugs.debian.org/773332
#
.
.
.
.
.
```
注释掉`Default server configuration`部分。如果默认配置没注释，对服务器的访问会首先使用默认配置，以至于我们的`kibana.conf`配置不起作用。

重载Nignx配置:
```
sudo nginx reload
```

# 在本机浏览器上访问kibana服务
```
http://172.16.200.37
```

首页界面: 
![https://rayxyz.github.io/assets/images/general/elk-kibana-home-page.png](https://rayxyz.github.io/assets/images/general/elk-kibana-home-page.png)


# 安装Metricbeat
Metricbeat是用来作系统状态监控，将系统状态发送到Elastic后在kibana可视化展示系统状态。

安装： 
```
curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.9.2-amd64.deb
sudo dpkg -i metricbeat-7.9.2-amd64.deb
```

设置初始环境: 
```
sudo metricbeat setup -e
```

大概1分钟左右才设置好。

查看启动的模块列表: 
```
ray@rayvm01:~$ sudo metricbeat modules list
Enabled:
system

Disabled:
activemq
aerospike
.
.
.
```

system模块已启动，可用以下命令启动:
```
sudo metricbeat modules enable system
```

启动metricbeat服务： 
```
sudo service metricbeat start
```

一切都顺利安装好后，可以到kibana Web界面查看可视化的系统状态信息: 
到kibana Dashboard查看，查找选择要查看的dashboard: 

![https://rayxyz.github.io/assets/images/general/elk-kibana-metricbeat-system-dashboard-select.png](https://rayxyz.github.io/assets/images/general/elk-kibana-metricbeat-system-dashboard-select.png)

可视化的数据: 
![https://rayxyz.github.io/assets/images/general/elk-kibana-metricbeat-system-metrics.png](https://rayxyz.github.io/assets/images/general/elk-kibana-metricbeat-system-metrics.png)


# 集成Logstash