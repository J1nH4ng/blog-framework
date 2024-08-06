---
wiki: system-devops
title: 后端应用服务器
rightbar: toc
---

## JDK 部分

### 安装配置

由于 Oracle 公司为了推广新的 Java 版本，旧版本无法直接下载，需要登陆下载，所以在下载之后手动上传至服务器的 `/usr/local/src` 目录下

1. 进入 `/usr/local/src` 目录，并解压
    ```bash
    cd /usr/local/src
   
    tar -zxvf jdk-8u371-linux-x64.tar.gz
    ```
2. 移动位置并配置环境变量
    ```bash
    mv jdk1.8.0_371 /usr/local/jdk8
   
    echo 'PATH=$PATH:/usr/local/jdk8/bin
    export PATH' >> /etc/profile

    echo 'JAVA_HOME=/usr/local/jdk8' >> /etc/profile

    # 刷新环境变量
    source /etc/profile
    ```

{% note color:cyan 其他的&nbsp;Java&nbsp;版本 对于其他的 Java 版本除了解压以及重命名的包名不同，其他并无任何不同，并且对于不参与业务使用的 Java 版本，不应写入系统 PATH %}

## Supervisord 部分

### 安装配置

### 管理 Java 进程

编写管理 Java 进程的 ini 文件

```bash
vim /etc/supervisord/<programme-name>.ini
```

`ini` 配置文件中相关配置如下

```ini
[program:<programme-name>]
; 无任何配置的启动命令
command=/usr/local/jdk8/bin/java -jar -Xms512m -Xmx512m -Dspring.profiles.active=prod -Dserver.port=9090 /data/releases/<programme-name>/<jar-name>.jar
; 含 Java 优化的启动命令
; command=/usr/local/jdk8/bin/java -jar -Xms512m -Xmx512m -XX:MaxMetaspaceSize=256G -XX:+UseG1GC -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/data/dumps/<programme-name> -Dspring.profiles.active=prod -Dserver.port=9090 /data/releases/<programme-name>/<jar-name>.jar
; 含 JMX 监控的启动命令
; command=
; 含多种监控的启动命令
; command=
directory=/data/releases/<programme-name>
autostart=true
autorestart=true
startsecs=10
startretries=3
user=root
priority=999
redirect_stderr=true
stopasgroup=false
killasgroup=false
stdout_logfile_maxbytes=20MB
stdout_logfile_backups=20
stdout_logfile=/data/logs/<programme-name>/<jar-name>.log
```

## NFS 部分

### 配置 NFS 服务

对于 NFS 服务的配置，不应该存放在后端服务器上，而是应该有一台单独的存储服务器。这里将搭建教程存放于中间件服务器部分。

{% link /wiki/system-devops/middleware/nfs NFS&nbsp;服务器搭建 %}

### 挂载已经存在的 NFS 服务
