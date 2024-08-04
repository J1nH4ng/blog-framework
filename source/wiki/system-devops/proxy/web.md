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
4. 修改 Keepalived 日志输出信息
   ```bash
   vim /usr/local/keepalived2.2/etc/sysconfig/keepalived
   ```
   修改如下参数
   ```bash
   # KEEPALIVED_OPTIONS="-D"
   KEEPALIVED_OPTIONS="-D -d -S 0"
   ```
5. 配置 Keepalived 日志输出位置
   ```bash
   vim /etc/rsyslog.conf
   ```
   添加下列配置
   ```bash
   # keepalived log
   local0.*       /var/log/keepalived.log
   ```
6. 配置 keepalived 的 service 文件
   ```bash
   cp /usr/local/src/keepalived-2.2.8/keepalived/keepalived.service \
      /etc/systemd/system/keepalived.service
   
   systemctl daemon-reload
   ```
7. 重启 rsyslog 和 keepalived 服务
   ```bash
   systemctl restart rsyslog
   systemctl restart keepalived
   ```

#### Keepalived 配置详解

具体内容可参考[附录 A：软件介绍 -- Keepalived 软件介绍](/wiki/system-devops/extra/software/keepalived.html)

## 实现高可用 

{% hashtag color:red 当没有硬件支持时使用 %}

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
