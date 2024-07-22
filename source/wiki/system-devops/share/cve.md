---
wiki: system-devops
title: CVE 漏洞列表
rightbar: toc
banner: https://w.wallhaven.cc/full/2k/wallhaven-2k67om.jpg
---

## 服务器漏洞

{% quot 高危 icon:hashtag el:h3 %}

{% button CVE-2024-6387&nbsp;漏洞报告 https://www.tenable.com/cve/CVE-2024-6387 icon:https://www.tenablecloud.cn/themes/custom/tenable/img/favicons/favicon.ico %}

{% folders %}
<!-- folder CVE-2024-6387 -->
影响版本：
  - OpenSSH < 4.1p1、8.5p1 <= OpenSSH < 9.8p1

解决办法：
  - 防火墙限制用户进行 SSH 访问
  - 升级 OpenSSH 至 9.8p1 版本及以上
{% endfolders %}

{% quot 中危 icon:hashtag el:h3 %}

{% quot 低危 icon:hashtag el:h3 %}

## 软件漏洞

{% quot 高危 icon:hashtag el:h3 %}

{% button CVE-2016-2183&nbsp;漏洞报告 https://www.tenable.com/cve/CVE-2024-6387 icon:https://www.tenablecloud.cn/themes/custom/tenable/img/favicons/favicon.ico %}

{% folders %}
<!-- folder CVE-2016-2183 -->
影响版本：
解决办法：
  1. 升级 OpenSSL 到最新版本
  2. 对于 Nginx 等服务器禁止使用 DES 加密算法
{% endfolders %}

{% quot 中危 icon:hashtag el:h3 %}

{% quot 低危 icon:hashtag el:h3 %}

