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