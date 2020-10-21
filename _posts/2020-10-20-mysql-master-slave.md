---
layout: post
title: "MySQL Master Slave Replication"
date: 2020-10-20 16:30 +0800
categories: tech
---

在实际生产环境中，数据库一般都要进行读写分离，提高系统的性能，实时对数据备份。本编文章详细介绍如何搭建MySQL主从，实现数据库的读写分离。

注意MySQL8.x以下的版本不支持`bind-address`多IP地址的绑定，本文中所使用的MySQL版本信息为： 
```
mysql  Ver 14.14 Distrib 5.7.31, for Linux (x86_64) using  EditLine wrapper
```

# 准备: 

IP: master -> 172.16.200.27; slave: 172.16.200.31.

[远程连接Linux下MySQL服务器](https://zhuanlan.zhihu.com/p/51455783)

# 配置Master和Slave
## Master
```
cd /etc/mysql/mysql.conf.d/
vim mysqld.cnf
```
编辑 `mysqld.cnf`: 
```
# bind-address            = 172.16.200.31  # only one slave or comment if for all slaves; Mysql8.x support multiple ip addresses.
server-id               = 1 # unique id you want to set
log_bin                 = /var/log/mysql/mysql-bin.log
binlog_do_db            = database_you_wanna_sync
```

重启 mysql: 
```
service mysql restart
```

```
GRANT REPLICATION SLAVE ON *.* TO 'slave'@'%' IDENTIFIED BY 'password';
FLUSH PRIVILEGES;
```

锁定数据库表防止写: 
```
USE database_you_wanna_sync;
FLUSH TABLES WITH READ LOCK;
```

Show master status & remember following records for further using: 
```
mysql> show master status;
+------------------+----------+----------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB   | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+----------------+------------------+-------------------+
| mysql-bin.000001 |      590 | zdz_filcompute |                  |                   |
+------------------+----------+----------------+------------------+-------------------+
1 row in set (0.00 sec)
```
数据库 `zdz-filcompute` 是要同步到slave的数据库.

备份数据库: 
```
mysqldump -u root -p --opt your_database > your_database_bak.sql
```

解锁数据库表;
```
mysql> UNLOCK TABLES;
mysql> QUIT;
```

## Slave
```
mysql> CREATE DATABASE database_you_wanna_sync;
mysql> EXIT;
```

在slave上导入数据库: 
```
scp database_you_wanna_sync ray@172.16.200.31:/home/ray
mysql -u root -p database_you_wanna_sync < /path/to/database_you_wanna_sync.sql
```
 
Edit `mysqld.cnf`
```
server-id               = 1 # unique id you want to set
log_bin                 = /var/log/mysql/mysql-bin.log
binlog_do_db            = database_you_wanna_sync
```

重启mysql服务: 
```
sudo service mysql restart
```

Slave变更master: 
```
mysql> change master to master_host='172.16.200.27', master_user='slave', master_password='zdz', master_log_file='mysql-bin.000001', master_log_pos= 590;
```
This command accomplishes several things at the same time:
It designates the current server as the slave of our master server.
It provides the server the correct login credentials
Last of all, it lets the slave server know where to start replicating from; the master log file and log position come from the numbers we wrote down previously.

启动Slave:
```
mysql> START SLAVE;
mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 172.16.200.27
                  Master_User: slave
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000001
          Read_Master_Log_Pos: 590
               Relay_Log_File: rayvm02-relay-bin.000002
                Relay_Log_Pos: 320
        Relay_Master_Log_File: mysql-bin.000001
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB: 
          Replicate_Ignore_DB: 
           Replicate_Do_Table: 
       Replicate_Ignore_Table: 
      Replicate_Wild_Do_Table: 
  Replicate_Wild_Ignore_Table: 
                   Last_Errno: 0
                   Last_Error: 
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 590
              Relay_Log_Space: 529
              Until_Condition: None
               Until_Log_File: 
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File: 
           Master_SSL_CA_Path: 
              Master_SSL_Cert: 
            Master_SSL_Cipher: 
               Master_SSL_Key: 
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error: 
               Last_SQL_Errno: 0
               Last_SQL_Error: 
  Replicate_Ignore_Server_Ids: 
             Master_Server_Id: 1
                  Master_UUID: 41979d5f-0d23-11eb-84a3-0c9d92bf710d
             Master_Info_File: /var/lib/mysql/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Slave has read all relay log; waiting for more updates
           Master_Retry_Count: 86400
                  Master_Bind: 
      Last_IO_Error_Timestamp: 
     Last_SQL_Error_Timestamp: 
               Master_SSL_Crl: 
           Master_SSL_Crlpath: 
           Retrieved_Gtid_Set: 
            Executed_Gtid_Set: 
                Auto_Position: 0
         Replicate_Rewrite_DB: 
                 Channel_Name: 
           Master_TLS_Version: 
1 row in set (0.00 sec)
```


# 参考
[远程连接Linux下MySQL服务器](https://zhuanlan.zhihu.com/p/51455783)

[How To Set Up Master Slave Replication in MySQL](https://www.digitalocean.com/community/tutorials/how-to-set-up-master-slave-replication-in-mysql)