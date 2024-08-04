---
wiki: system-devops
title: Keepalived 软件介绍
rightbar:
   - toc
---

## keepalived 配置文件详解

### 简介

keepalived 配置文件是按层级和模块划分的，每层由`{}`来进行界定。在主配置文件中可以使用`include`来使用多个子配置文件。

配置文件中的语法说明：

- `BOOL`：布尔类型，值为 `[on | off | true | false | yes | no]`
- `TIMER`：时间，以秒为单位，包括小数秒，例如值为：`[3 | 1.414]`
- 关于脚本引用：
    - 双引号 `"` 字符串中嵌入其他双引号或空格，那么字符串将仅在带引号的字符串结束之后结束，例如：`"abcd" edg hijk "lmno"` 等价于 `"abcd edg hijk lmno"`
    - 对于使用参数指定脚本，不带引号的空格将会分割参数，如果需要传递带空格的参数，将这个参数包含在 `'` 之中

配置文件包含以下三个区域

1. GLOBAL 配置
2. VRRPD 配置
3. LVS 配置

### GLOBAL 配置

全局配置又包括两个子配置

1. 全局定义
2. 静态路由配置

具体配置和内容解释如下：

```bash keepalived.conf
! Configuration File for keepalived

# 用于标识全局定义部分
global_defs {
   # 设置邮件报警地址，可以设置多个
   notification_email {
      test1@mail.14bytes.com
      test2@mail.14bytes.com
   }
   
   # 设置发件人
   notification_email_from server@mail.14bytes.com
   
   # 设置 smtp server 地址，可以是 ip 或域名，端口号是可选的，默认为 25
   smtp_server smtp.14bytes.com[:25]
   # 设置 smtp server 的超时时间
   smtp_connect_timeout 30
   
   # 主机标识，用于邮件通知，可以设置为主机名
   router_id <host-name>
   
   # 严格执行 VRRP 协议规范，此模式不支持节点单播
   vrrp_strict
   
   # 指定允许脚本的用户名和组名。默认使用用户的默认组，如果未指定，默认为 keepalived_script 用户，如果没有这个用户，则为 root 用户
   script_user <nginx> [nginx]
   
   # 禁止使用 root 用户执行
   enable_script_security
}
```

### VRRPD 配置

VRRPD 配置模块包含以下子模块

1. vrrp_script
2. vrrp_sync_group
3. garp_group
4. vrrp_instance

```bash keepalived.conf
# vrrp_script 区域

```

### LVS 配置

LVS 配置模块包含以下子模块

1. virtual_server
2. real_server
3. real_server 中的健康检查
    - HTTP_GET 或者 SSL_GET
    - TCP_CHECK
    - DNS_CHECK