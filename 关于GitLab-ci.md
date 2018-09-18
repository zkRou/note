---
title: 关于GitLab ci
author: Kairou Zeng
date: 2018/9/17
tags: [git, 持续集成]
---

## 持续集成

> 持续集成(Continuous Integration,简称CI)，指频繁将代码集成到主干。

- 目的：

    更快速地发布更新，让产品快速迭代，同时还能保持高质量，提高开发效率。

- 核心措施：

    代码集成到主干前，必须通过自动化测试。只要有测试用例不通过，就不能集成。

- 组成：

    - 一个自动构建的过程，包括自动编译、分发、部署和测试等；
    - 一个代码存储库，即需要版本控制来保障代码的可维护性，同时作为构建过程的素材库；
    - 一个持续集成服务器
    
## Gitlab CI

> GitLab CI是GitLab提供的持续集成服务，只要在仓库根目录创建一个.gitlab.yml文件(定义GitLab runner要做哪些操作)，并为该项目指派一个Runner，当有Merge Request或push的时候就会触发build.

- GitLab  Runner 

    `GitLab Runner`是`.gitlab-ci.yml`脚本的运行器。`GitLab Runner`不需要和`GitLab`安装在同一台机器上，但是考虑到`GitLab Runner`的资源消耗问题和安全问题， 也不建议这两者安装在同一台机器上。

    `GitLab Runner`分为两种：

        - Shared Runners:可以运行所有开启`Allow shared runners`选项的项目

        - Specific Runners:只能被指定的项目使用

- Pipelines

    `Pipelines`是定义于`.gitlab-ci.yml`中的不同阶段的不同任务。每个`Pipelines`包含有多个`Stages`,每个`Stages`包含有一个火多个`jobs`。每一次Merge Request或push都要经过流水线之后才可以合格出厂。而`.gitlab-ci.yml`正是定义了`Pipelines`有哪些`Stages`，每个`Stages`要做什么事。

