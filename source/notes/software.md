---
wiki: explore
header: false
menu_id: explore
robots: noindex, nofollow
sitemap: true
title: 软件使用相关问题
h1: '软件使用相关问题'
breadcrumb: true
---

{% folders %}

<!-- folder  MinIO 文件太大上传错误 -->

修改 Nginx 配置文件

```bash
vim /usr/local/nginx/conf/nginx.conf
```

将 `client_max_body_size` 的值设为为 0 （不限制）或者较大的值

{% endfolders %}
