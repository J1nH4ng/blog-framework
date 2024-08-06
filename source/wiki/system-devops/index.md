---
wiki: system-devops
title: 内容简介
rightbar: toc
---

{% quot 为什么编写这个 Wiki el:h2 %}

在工作中，知识点较为零碎，没有系统化的构建文档，所以编写了这个 Wiki，根据服务器的使用不同，进行相对应的服务器设置。

{% quot Wiki 完成进度 el:h2 %}

{% okr o1 %}

实现目标：wiki 编写完成

<!-- okr kr1 percent:1 -->
完成 wiki 界面的基本框架

<!-- okr kr2 percent:1 -->
完成 checklist 界面的主要内容

<!-- okr kr3 percent:1 -->
完成公共部分的主要内容

<!-- okr kr4 status:off_track percent:0.15 -->
完成代理服务器的相关文档

<!-- okr kr5 percent:0.05 -->
完成应用服务器的相关文档

<!-- okr kr6 percent:0.15 -->
完成中间件服务器的相关文档

<!-- okr kr7 status:unfinished -->
完成数据库服务器的相关文档

<!-- okr kr8 status:unfinished -->
完成监控服务器的相关文档

<!-- okr kr9 status:unfinished -->
完成运维服务器的相关文档

<!-- okr krX status:unfinished -->
完成 K8S 服务器的相关文档

<!-- okr krXI status:unfinished -->
完成附录部分的相关文档

{% endokr %}

{% quot Wiki 时间线 el:h2 %}

{% timeline %}
<!-- node 2024 年 08 月 05 日 -->
- 修改文件目录，抽离软件介绍至附录
- 完善服务器初始化检查列表
<!-- node 2024 年 07 月 31 日 -->
- 完善目录结构
- 添加附件内容
<!-- node 2024 年 07 月 23 日 -->
- 基本完成公共部分-初始化服务器的内容
- 开始编写代理服务器-应用代理服务器部分
<!-- node 2024 年 07 月 20 日 -->
- 完成 wiki 首页的介绍内容
- 完成 checklist 页的内容
- 开始编写公共部分-初始化服务器的内容
<!-- node 2024 年 07 月 19 日 -->
- 完成 Wiki 的框架构成
{% image /wiki/system-devops/images/hello@1x.png width:300px %}
{% endtimeline %}

{% quot Wiki 维护周期 el:h2 %}

- 快速开始
  - 内容简介：{% hashtag 仅修复错误 color:yellow %}
  - 初始化注意事项：{% hashtag 仅修复错误 color:yellow %}
  - 迁移等注意事项：{% hashtag 仅修复错误 color:yellow %}

- 共用部分
  - CVE 漏洞列表：{% hashtag 长期维护 color:green %}
  - 初始化服务器：{% hashtag 重大更新维护 color:red %}
- 代理服务器部分
  - 应用代理服务器：{% hashtag 重大更新维护 color:red %}
  - 网络代理服务器：{% hashtag 重大更新维护 color:red %}
- 应用服务器部分
  - 前端应用服务器：{% hashtag 重大更新维护 color:red %}
  - 后端应用服务器：{% hashtag 重大更新维护 color:red %}
- 中间件服务器部分
  - NFS 服务搭建：{% hashtag 重大更新维护 color:red %}
  - MinIO 部分：{% hashtag 重大更新维护 color:red %}
- 容器化服务器部分：
  - Docker 安装配置：{% hashtag 仅修复错误 color:yellow %}
  - Kubernetes 安装配置：{% hashtag 仅修复错误 color:yellow %}
- 附录 A：
  - Keepalived 软件介绍 {% hashtag 仅修复错误 color:yellow %}
  - Docker 软件介绍 {% hashtag 仅修复错误 color:yellow %}
  - Kubernetes 软件介绍 {% hashtag 仅修复错误 color:yellow %}
- 附录 B：
  - 半自动部署脚本：{% hashtag 重大更新维护 color:red %}
  - 文件同步脚本：{% hashtag 重大更新维护 color:red %}

{% quot Wiki 部分内容说明 el:h2 %}

1. 对于 `[A | B]` 中内容表示为可选，可选择 A 或者 B
2. 对于 `<target>` 中内容表示为必填，必填内容为 target 所描述的内容，例如 `<target-ip>` 表示填入目标 IP 地址
