---
wiki: system-devops
title: Kubernetes 介绍
rightbar: toc
references:
  - '[Kubernetes 一小时入门](https://geekhour.net/2023/12/23/kubernetes/)'
---

## 什么是 Kubernetes

Kubernetes 是一个开源的容器编排引擎，可以用来管理容器化的应用，包括容器的自动化部署、扩容、缩容、升级、回滚等。是 Google 在 2014 年开源的一个项目，前身是 Google 内部的 Borg 系统。

## 为什么要使用 Kubernetes

在 Kubernetes 出现之前，一般是使用 Docker 来管理容器化应用，但是 Docker 只是一个单机的容器管理工具，它只能管理单个节点上的容器，当应用程序运行在多个节点上时，就需要使用一些其他的工具来管理这些节点。

Docker Swarm 作为 Docker 官方提供的一个容器编排引擎，功能较为简单，适合一些小型的、简单的情景。而 Mesos 和 Kubernetes 则适合比较复杂的项目，而 Kubernetes 基本上已经成为容器编排引擎的事实标准了。

