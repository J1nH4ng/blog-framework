---
wiki: system-devops
title: 应用代理服务器
rightbar: toc
---

## 期望目标

对于应用代理服务器而言，期待可以实现高可用和负载均衡。对于最外层有硬件支持的情况下，可以由硬件进行处理，如果没有硬件支持的情况下，使用 Keepalived 和 Nginx 来实现高可用，同时对于每一个 Nginx 来说实现负载均衡。

## 安装需要的软件

### 安装 Nginx 需要的模块依赖

### 安装 Nginx

### 安装 Keepalived

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

## 实现高可用

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
