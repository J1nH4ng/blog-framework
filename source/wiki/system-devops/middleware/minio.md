---
wiki: system-devops
title: MinIO 部分
rightbar: toc
---

## 软件安装

### 单节点

### 多节点

## 数据备份

### 基于 MinIO Client 备份

使用 MinIO Client （mc）进行业务级备份

#### 下载并安装 MinIO Client

```bash
cd /usr/local/src
wget https://dl.min.io/client/mc/release/linux-amd64/mc
chmod +x mc
cp mc /usr/sbin/
```

#### 设置 MinIO 别名

```bash
mc alias set <minio-nickname> http://<minio-source-ip>:<minio-source-port> <minio-username> <minio-password>

# 设置完可以使用下列命令查看
mc alias list
```

#### 手动执行备份

{% tabs active:1 %}

<!-- tab 备份所有桶 -->

```bash
mc cp --recursive <minio-nickname> /data/backup/minio
```

<!-- tab 备份某个桶 -->
```bash
mc cp --recursive <minio-nickname>/<backup-bucket>/ /data/backup/minio/<backup-bucket>
```

{% note color:red 末尾带不带&nbsp;/&nbsp;的区别： 带&nbsp;/&nbsp;结尾时只会备份对应<backup-bucket>下的文件，而不会备份桶名，不带&nbsp;/&nbsp;时会连桶名一起备份 %}

{% endtabs %}

#### 脚本定时备份（全备份）

1. 创建脚本

   ```bash
   vim /data/scripts/minio-backup.sh
   ```

   写入如下内容：

   ```bash
   #!/bin/bash

   ```

2. 配置定时任务
   
   ```bash
   crontab -e
   ```

   写入如下内容：

   ```bash

   ```
### 直接拷贝 /data 目录备份
