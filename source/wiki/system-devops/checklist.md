---
wiki: system-devops
title: 注意事项
rightbar: toc
---

## 前置检查

- 服务器的架构
  - arm64
  - x86_64
- {% mark color:red 服务器之间的横向 SSH 是否联通 %}
- 是否允许升级 SSL
  - 升级的版本要求
- 是否允许升级 SSH
  - 升级至 9.8 版本及以上
  - {% mark color:red 升级 SSH 之后可能导致堡垒机部分功能不可用 %}

## 网络关系

- 服务器是否允许出网
- 应用相关
  - 后端服务请求的第三方接口
  - 请求前端服务的 IP 地址
- 运维服务器
  - 到自建的 Squid 服务器

## 初始化服务器

- 虚拟机管理平台配置高可用选项
- 修改为国内源
- 防火墙
  - 关闭
  - 放开相应端口
- 关闭 selinux
- 升级 OpenSSL
- 升级 OpenSSH
- 配置 LVM 磁盘
- 修改文件最大打开数
- 配置服务器内核和网络设置
- 测试服务器硬件是否满足生产环境需求

## Proxy 服务器

- Nginx
  - Lua 模块
- Keepalived（是否可以由硬件支持）
  - VIP 提供
- Ansible
- Rsync
- Scripts
  - 用于 Nginx 控制文件访问的 Lua 脚本
  - 文件同步脚本

## Web 应用服务器

- Nginx
- Java
  - jdk1.8（写入系统 PATH）
  - jdk11 or higher（绝对路径使用）
- Supervisord
- Keepalived
- Ansible
  - {% mark color:red 服务器之间是否允许 SSH 协议通信 %}
  - Ansible 的 Hosts 文件配置
- NFS 或其他存储方式
- Scripts
  - 文件同步脚本

## 中间件服务器

- Redis
  - 架构
    - 单点
    - 集群
- RabbitMQ
  - 架构
    - 单点
    - 集群
  - {% mark color:red 版本（与 erlang 版本对应）%}
- MinIO
- NacOS

## 数据库服务器

- Mysql
  - 版本
  - 架构
    - 单独
    - 双主双从
    - 集群
  - 备份策略
    - MysqlDump
    - XtraBackup
- Keepalived
  - VIP 确定

## 监控服务器

- Kibana
- ElasticSearch
- Logstash
- Zabbix Server
  - 版本
  - {% mark color:red 数据库和业务数据库分离 %}
- Prometheus
- Php
  - 版本

## 运维服务器

- Jenkins
  - Git 安装
  - 配置 SSH Token
- Scripts
  - Jenkins 远程执行的脚本文件
- Nvm
  - Nodejs 的版本
  - Pnpm 的版本
  - Nodejs 仓库配置
- Java
  - Jdk1.8（与业务使用的 Java 版本一致）
  - Jdk11 or higher（绝对路径使用，仅 Jenkins 运行需要，不参与业务）
  - Maven
    - 修改 Maven 仓库
    - 访问自建仓库
- Ansible
    - {% mark color:red 服务器之间是否允许 SSH 协议通信 %}
    - Ansible 的 Hosts 文件配置

## 跳板机

- Windows
  - RDP 协议
- Linux
  - VNC 协议

## K8S 服务器

- K8S 版本
- Kubesphere 版本
- Root 用户安装
- harbor 存储位置
- dockerhub 拉取镜像代理

## 其他事项

- squid 代理服务器配置
- 备份文件的 MD5 校验
