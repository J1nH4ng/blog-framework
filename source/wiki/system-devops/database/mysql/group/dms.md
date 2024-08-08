---
wiki: system-devops
title: Mysql 双主集群搭建
rightbar: toc
---

## Mysql 配置

### 数据库前置设置

1. 创建用于同步的用户并给予权限
    ```bash
    mysql -uroot -p<root-password>
    ```

    ```sql
    create user 'repl'@'%' identified by '<repl-password>';
    
    grant replication slave on *.* to 'repl'@'%';
    ```
2. 确保 `my.cnf` 配置文件在的 `server_id` 是不一样的
   ```bash
   vim /etc/my.cnf
   ```

{% folders %}

<!-- folder Slave_IO_Running=connecting 问题解决 -->

查看 slave_sql_Running_state 字段，可以查看到错误信息：

```bash
error connecting to master 'repl@<ip>:3306'- retry-time: 60 retries: 6
message: Authentication plugin 'caching_sha2_password' reported error:
Authentication requires secure connection.
```
主要原因为 Mysql8.0 修改了默认的密码加密算法。

解决办法有两种：
1. 修改数据库的密码加密方式
   ```sql
   alter user 'repl'@'%' identified with mysql_native_passowrd by '<repl-password>';
   ```
2. 设置主库时命令添加 `GET_MASTER_PUBLIC_KEY=1` 参数 {% hashtag 推荐 color:green %}
   ```sql
   CHANGE MASTER TO
   MASTER_HOST='<master-ip>',
   MASTER_USER='repl',
   MASTER_PASSWORD='<repl-password>',
   MASTER_PORT=3306,
   MASTER_LOG_FILE='<MASTER_LOG_FILE>',
   MASTER_LOG_POS='<MASTER_LOG_POS>',
   GET_MASTER_PUBLIC_KEY=1;
   ```
{% endfolders %}

### 数据库内配置

#### 主数据库

1. 备份主数据库
   ```bash
   mysqldump -uroot -p'<root-password>' --routines --single-transaction --source-data=2 --no-autocommit -A >alldatas.sql
   ```
2. 查看备数据库的 `MASTER_LOG_FILE` 和 `MASTER_LOG_POS`
   ```sql
   show master status \G
   ```
   结果如下：
   ```sql
   *************************** 1. row ***************************
                File: ON.000001
            Position: 157
        Binlog_Do_DB:
    Binlog_Ignore_DB:
   Executed_Gtid_Set:
   1 row in set (0.02 sec)
   ```
   以这里的结果为例：
   - `MASTER_LOG_FILE` 的值为：`ON.000001`
   - `MASTER_LOG_POS` 的值为 `157`
3. 在主数据库上配置执行备数据库的同步
   ```bash
   mysql -uroot -p'<root-password>'
   ```
   停止同步，并重置同步配置
   ```sql
   stop slave;
   reset slave all;
   show slave status\G;
   ```
   使用下面的命令配置同步
   ```sql
   CHANGE MASTER TO
   MASTER_HOST='<backup-ip>',
   MASTER_USER='repl',
   MASTER_PASSWORD='<repl-password>',
   MASTER_PORT=3306,
   MASTER_LOG_FILE='<MASTER_LOG_FILE>',
   MASTER_LOG_POS='<MASTER_LOG_POS>',
   GET_MASTER_PUBLIC_KEY=1;
   ```
4. 查看同步状态
   ```sql
   show slave status \G
   ```
   结果如下：
   ```sql
   *************************** 1. row ***************************
   Slave_IO_State: Waiting for source to send event
   Master_Host: 192.168.150.107
   Master_User: repl
   Master_Port: 3306
   Connect_Retry: 60
   Master_Log_File: ON.000001
   Read_Master_Log_Pos: 530
   Relay_Log_File: host-192-168-150-106-relay-bin.000010
   Relay_Log_Pos: 319
   Relay_Master_Log_File: ON.000001
   Slave_IO_Running: Yes      # 这里显示 Yes 表示成功
   Slave_SQL_Running: Yes      # 这里显示 Yes 表示成功，配置一台服务器时也会同时显示 Yes，并不是同时配置完成时才会显示
   ```

#### 备数据库

1. 导入主数据库数据
   ```bash
   mysql -uroot -p'<root-password>' < /usr/local/alldatas.sql
   ```
2. 剩下操作与主数据库一样

## Keepalived 配置

### 前置配置

1. 安装活性检测软件
   ```bash
   yum -y install psmisc 
   ```
   {% note color:cyan psmisc&nbsp;作用 提供 killall 指令 %}
2. 配置 Mysql 服务检查脚本
   ```bash
   mkdir -pv /data/scripts/keepalived
   touch /data/scripts/keepalived/check-mysql.sh
   chmod +x /data/scripts/keepalived/check-mysql.sh
   
   vim /data/scripts/keepalived/check-mysql.sh
   ```
   写入如下内容：
   ```shell
   #!/bin/bash
   
   # 若 mysql 进程不存在，则停止 keepalived 服务
   killall -0 mysqld &>/dev/null

   if [ $? -ne 0 ];then    # 判断上一条指令的状态码，若状态码为0则服务存活，否则服务未启动
      /etc/init.d/mysqld start
      sleep 1s    # mysql_safe启动mysqld进程需要一定的时间
      killall -0 mysqld &>/dev/null
      if [ $? -ne 0 ];then
         systemctl stop keepalived
      fi
   fi
   ```

### 配置 Keepalived 配置

1. 主数据库配置
   ```bash
   vim /usr/local/keepalived/etc/keepalived/keepalived.conf
   ```
   写入如下内容：
   ```makefile keepalived.conf
   ! Configuration File for keepalived

   global_defs {
      router_id Mysql-1-106 #参照服务器标识命名约定
   }
   
   vrrp_script chk_mysql {
      script "/usr/local/keepalived2.2/scripts/check-mysql.sh"
      interval 2
      fall 2
      rise 1
   }
   
   vrrp_instance VI_1 {
      state MASTER
      # 修改网卡名称为本机的网卡名称
      interface eth0
      virtual_router_id 51
      priority 100
      advert_int 1
      authentication {
         auth_type PASS
         auth_pass 1111
      }
      virtual_ipaddress {
         # VIP 地址
         192.168.33.201
      }
      # 防止主备节点的上联交换机禁用了组播，采用 vrrp 单播通告的方式
      unicast_src_ip 192.168.150.106 #本机 IP 地址
      unicast_peer {
         192.168.150.107 #对端节点 IP 地址
      }
      track_script {
         chk_mysql
      }
   }
   ```
2. 备数据库
   除网卡名称、本机 IP 和对端 IP 需修改外，其他的与主数据库配置一样
3. 启动 Keepalived
   ```bash
   systemctl start keepalived
   systemctl enable keepalived
   ```
4. 查看运行状态和 VIP 是否存在
   ```bash
   systemctl status keepalived
   
   # 查看 VIP 是否存在
   ip a
   ```
