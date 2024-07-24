---
wiki: system-devops
title: 应用代理服务器
rightbar: toc
---

## 期望目标

对于应用代理服务器而言，期待可以实现高可用和负载均衡。对于最外层有硬件支持的情况下，可以由硬件进行处理，如果没有硬件支持的情况下，使用 Keepalived 和 Nginx 来实现高可用，同时对于每一个 Nginx 来说实现负载均衡。




