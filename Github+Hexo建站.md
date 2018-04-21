---
title: Github+Hexo建站
author: Krirou Zeng
date: 2018/04/16
tags: git,hexo
---

# 安装前提

- git

- node环境
  
  使用`npm -v`测试是否安装

使用npm完成Hexo的安装：
```sh
npm install -g hexo-cli //-g：全局安装,不带说明安装在当前目录
```

为了能够使Hexo部署到GitHub上，需要安装一个插件：
```sh
npm install hexo-deployer-git --save
```

## Hexo命令

**init：** 新建一个网站。如果没有设置 folder ，Hexo 默认在目前的文件夹建立网站。

```sh
hexo init [folder]
```

该命令会在目标文件夹内建立网站所需要的所有文件。

**generate：** 生成静态文件

```sh
hexo generate
```

简写：

```sh
hexo g
```

参数 | 描述
--- | --- |
-d,--deployer |  文件生成后立即部署网站
-w,--watch | 监视文件变动

**servre：** 启动服务器

```sh
hexo server
```

简写：

```sh
hexo s
```

debug启动：

```sh
hexo s --debug
```

默认情况下，访问网址为： http://localhost:4000/

参数 | 描述
--- | --- |
-p,--port | 重设端口
-s,--static | 只使用静态文件
-l,--log | 启动日记记录，使用覆盖记录格式

**deploy：** 部署网站

```sh
hexo deploy
```

简写：

```sh
hexo d
```

参数 | 描述
--- | --- |
-g,--generate | 部署之前预先生成静态文件

**clean：** 清除缓存文件(db.json)和已生成的静态文件(public)

```sh
hexo clean
```

## npm命令

**npm install：** 安装依赖包

