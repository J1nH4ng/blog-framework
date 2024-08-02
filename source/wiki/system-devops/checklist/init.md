---
wiki: system-devops
title: 初始化注意事项
rightbar: toc
---

## 前置检查

- 服务器架构
  - arm64
  - x86_64
- {% mark color:red 服务器横向 SSH 是否联通 %}
- 防火墙开放状态
- 物理磁盘是否挂载

## 网络关系

- 服务器是否允许出网
- 应用部分
  - 后端需要请求的第三方接口
  - 业务的跨网段请求
  - 互联网访问前端应用
- 运维服务器
  - 到自建的 Squid 服务器或其他正向服务器

## 初始化部分

- 虚拟机管理平台设置为高可用选项
- 修改为国内源
- 防火墙
  - 关闭防火墙
  - 放开特定端口并限制 IP 访问
- 关闭 SeLinux
- 升级 OpenSSL
  - 版本确定
- 升级 OpenSSH
  - {% mark color:red 升级后可能导致堡垒机部分功能不可用 %}
  - 9.8p1 版本及以上
  - 禁用特定算法，修复 CVE-2002-20001 漏洞
- 配置文件最大打开数
- 配置内核和网络参数
- 测试硬件是否符合要求
  - 硬盘是否为 SSD
- 配置 LVM 磁盘

## 应用安装部分

### 代理服务器

- Nginx
  - 版本确定
  - Lua 模块
- Keepalived
  - {% mark color:red 是否可以由硬件支持 %}
- Logrotate
  - 切割方式
    - 按日期
    - 按大小
- Ansible {% hashtag SSH&nbsp;联通 color:red %}
  - Ansible 的 Hosts 文件配置
- Rsync {% hashtag SSH&nbsp;联通 color:red %}
- 脚本内容
  - 用于 Nginx 访问控制的 Lua 脚本
  - 文件同步脚本

### Web 应用服务器

- Nginx
- Java
  - jdk1.8（写入系统变量）
  - jdk11 或者更高（绝对路径使用）
- Supervisord
- Keepalived
- Ansible {% hashtag SSH&nbsp;联通 color:red %}
  - Ansible 的 Hosts 文件配置
- Rsync {% hashtag SSH&nbsp;联通 color:red %}
- NFS 或其他存储
- Logrotate
  - 切割方式
    - 按时间
    - 按大小
- 脚本内容
  - 文件同步脚本
  - 半自动发布脚本（临时）{% hashtag Jenkins&nbsp;不可用时 color:red %}

### 数据库服务器

- Mysql
  - 版本
  - 架构
    - 单点
    - 双主双从
    - MGR 集群
  - 备份策略
    - MysqlDump
    - XtraBackup
- Keepalived
  - VIP 确定
- MongoDB


### 中间件服务器

- Redis
  - 架构
    - 单点
    - 集群
- RabbitMQ
  - 架构
    - 单点
    - 集群
  - {% mark color:red 与 erlang 版本对应 %}
- MinIO
  - 备份策略
- NacOS
- 脚本内容
  - MinIO 备份脚本

### 监控服务器

- Prometheus
- Grafana
- Zabbix
  - 版本
  - Server or Proxy
  - Agent 版本
  - {% mark color:red 数据库与业务数据库分离 %}
- Php 
  - 版本
  - 编译时禁用特定参数解决中文乱码
- ELK
  - 版本完全一致

### 运维服务器

- Jenkins
  - Git 配置
  - 配置 SSH Keygen
- 脚本内容
  - Jenkins 远程执行的脚本
- NVM
  - Nodejs 版本
  - Pnpm 版本
  - Nodejs 仓库配置
- Java
  - Jdk1.8（与业务 jdk 版本一致）
  - Jdk11（绝对路径使用，仅 Jenkins 运行使用，不参与业务）
- Maven
  - 修改仓库
- Ansible
  - Ansible 的 Hosts 文件配置
  - 配置免密执行 sudo
- Squid 代理配置或其他正向代理配置

### K8S 服务器

- Kubernetes
  - 版本
  - root 用户安装
- Kubesphere
  - 版本
  - root 用户安装
- Docker
  - 版本
  - root 用户安装
- harbor
- helm
- docker 代理配置

### 跳板机

- Windows （RDP 协议）
  - Navicat
  - Firefox or Chrome
- Linux （vnc 协议）
  - VNC 配置
  - Navicat
  - Firefox or Chrome

### 其他事项

- Squid 源服务器配置白名单
- 上传文件的 MD5 或 Hash 检查
- 备份文件
  - 保存周期
  - MD5 或 Hash 生成
  - 移动后的 MD5 或 Hash 检查
