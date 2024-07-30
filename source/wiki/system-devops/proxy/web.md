---
wiki: system-devops
title: 应用代理服务器
rightbar: toc
---

## 期望目标

对于应用代理服务器而言，期待可以实现高可用和负载均衡。对于最外层有硬件支持的情况下，可以由硬件进行处理，如果没有硬件支持的情况下，使用 Keepalived 和 Nginx 来实现高可用，同时对于每一个 Nginx 来说实现负载均衡。

## 安装需要的软件

### Nginx 部分

#### 安装模块依赖

#### 编译安装

### Keepalived 部分

#### 编译安装

1. 安装依赖
    ```bash
    yum install psmisc libnl-devel -y
    ```
2. 下载安装包并解压
    ```bash
    cd /usr/local/src

    # 如果 SSL 证书失效，结尾添加 --no-check-certificate
    wget https://www.keepalived.org/software/keepalived-2.2.8.tar.gz

    tar -zxvf keepalived-2.2.8.tar.gz
    ```
3. 编译安装
    ```bash
    cd /usr/local/src/keepalived-2.2.8
    
    ./configure --prefix=/usr/local/keepalived2.2
    
    make && make install
    ```

#### keepalived 配置文件详解

##### 简介

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

##### 全局配置

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

## 实现高可用

1. 创建 Nginx 活性检测脚本
   ```bash
   mkdir -pv /data/scripts/keepalived
   
   vim /data/scripts/keepalived/check-nginx.sh
   
   chmod +x /data/scripts/keepalived/check-nginx.sh
   ```
   
   文件内容如下：

   ```shell
   #!/bin/bash

   # 若 nginx 进程不存在，则停止 keepalived 服务
   killall -0 nginx &>/dev/null

   # 判断上一条指令的状态码，若状态码为0则服务存活，否则服务未启动
   if [ $? -ne 0 ];then
      systemctl start nginx
      killall -0 nginx &>/dev/null
      if [ $? -ne 0 ];then
         exit 1
      fi
   fi
   ```

2. 修改 Master 节点的 keepalived 的配置文件

   ```bash
   vim /usr/local/keepalived2.2/etc/keepalived/keepalived.conf
   ```
   
   写入内容如下：

   ```bash keepalived.conf
   ! Configuration File for keepalived

   vrrp_script chk_nginx {
      script "/data/scripts/check-nginx.sh"
      interval 2
      fall 2
      rise 1
      weight -20
   }

   vrrp_instance VI_1 {
      state MASTER
      interface ens33
      virtual_router_id 51
      priority 100
      advert_int 1
      authentication {
         auth_type PASS
         auth_pass 1111
      }

      virtual_ipaddress {
         192.168.0.250
      }
   
      # 防止主备节点的上联交换机禁用了组播，采用 vrrp 单播通告的方式
      unicast_src_ip 192.168.0.11 #本机 IP 地址
      unicast_peer {
         192.168.0.12 #对端节点 IP 地址
      }

      # 追踪 vrrp_script 块中定义的脚本
      track_script {
         chk_nginx
      }
   }
   ```

## 配置 Nginx

### 配置 proxy.conf

### 配置跨域

### 禁止部分请求方式

### 禁止相关爬虫

对于爬虫应该爬取 robots.txt 文件中所规定的内容，这个一个业内遵守的默认规定，但是依旧有部分厂商不遵守并且爬取频率过高，对于服务器的内存和流量有较大影响。或者，并不想业务能够被浏览器收录，需要进行爬虫的阻止。

```nginx agent_deny.conf
# 禁止 scrapy 爬虫
if ($http_user_agent ~* "Scrapy|Sogou web spider|Baiduspider") {
    return 403;
}

# 禁止指定 UA 及 UA 为空的访问
if ($http_user_agent ~ "FeedDeemon|JikeSpider|Indy Library|Alexa Toolbar|AskTbFXTV|AhrefsBot|CrawlDaddy|CoolpadWebkit|Java|Feedly|UniversalFeedParser|ApacheBench|Microsoft URL Control|Swiftbot|ZmEu|oBot|jaunty|Python-urllib|lightDeckReports Bot|YYSpider|DigExt|YisouSpider|HttpClient|MJ12bot|heritrix|EasouSpider|LinkpadBot|Ezooms|^$") {
    return 403;
}

if ($http_user_agent ~ "Mozilla/4.0\ \(compatible;\ MSIE\ 6.0;\ Windows\ NT\ 5.1;\ SV1;\ .NET\ CLR\ 1.1.4322;\ .NET\ CLR\ 2.0.50727\)") {
    return 404;
}
```

### 配置请求转发

## 【可选】配置文件同步
