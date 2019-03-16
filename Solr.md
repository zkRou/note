---
title: Solr
author: Kairou Zeng
date: 2018/05/01
---

#### 概述

Lucene是一个Java语言编写的利用倒排原理实现的文本检索类库。

Solr是Lucene为基础实现的文本检索应用服务。

Solr的部署方式有单机方式、多级Master-Slaver方式、Cloud方式。

SolrCloud是基于Solr和Zookeeper的分布式搜索方案，它的主要思想是使用Zookeeper作为SolrCloud集群的配置信息中心。当索引越来越大，一个单一的系统无法满足磁盘需求，查询速度缓慢，此时就需要分布式索引。在分布式索引中，原来的大索引，将会分成多个小索引，solr可以将这些小索引返回的结果合并，然后返回给客户端。

#### Solr7.1.0 + Tomcat单机部署(Windows)

> 如今的solr版本已经不再提供war包了，而是直接在solr/server/solr-webapp下直接提供了webapp文件

- 使用Solr本身集成的Jetty服务器

  在solr的bin文件下执行 

  ```shell
  solr start
  ```

  默认端口：8983

  访问路径：<http://localhost:8983/solr> 


- 在Tomcat部署：

  **坑** ：Solr7.1.0和Tomcat7不兼容，配置会失败

  相关链接：[solr在tomcat的部署](http://wowubuntu.com/markdown/basic.html)

#### SolrCloud特点功能：

1. 集中式的配置信息
2. 自动容错
3. 近实时搜索
4. 查询时自动负载均衡



#### 基本查询

|       | 说明                                       |
| ----- | ---------------------------------------- |
| q     | 查询字符串，必须                                 |
|       | 指定返回哪些字段内容，用逗号火或空格分隔多个                   |
| start | 返回第一天记录在完整找到结果中的偏移位置，0开始，一般分页用           |
| rows  | 指定返回结果最多有多少条记录，配合start来实现分页              |
| sort  | 排序，格式 ：sort=<field name>+<desc\|asc>[,<field name>+<desc\|asc>]…                                示例：（inStock desc, price asc）表示先 “inStock” 降序, 再 “price” 升序，默认是相关性降序 |
| wt    | 指定输出格式，可以有xml,json,php                   |
| fq    | 过滤查询（在q查询符合结果中同时是fq查询符合的）                |

#### Solr的检索运算符

| 运算符  | 说明                                       |
| ---- | ---------------------------------------- |
| :    | 指定字段查指定值，如返回所有值\*:\*                     |
| ?    | 表示单个任意字符的通配                              |
| *    | 表示多个任意字符的通配                              |
| ~    | 表示模糊检索，如检索拼写类似于”roam”的项这样写：roam~将找到形如foam和roams的单词；roam~0.8，检索返回相似度在0.8以上的记录 |
|      |                                          |
|      |                                          |
|      |                                          |
|      |                                          |
|      |                                          |

#### CloudSolrServer

#### Spatial Search - 空间搜索

> 基于空间搜索技术，主要基于GeoHash和Cartesian Tiers 2个概念来实现，这两种原理分别对应GeohashPrefixTree类和QuadPrefixTree类

1. 对Point(经纬度)和其他的几何图形建索引
2. 根据距离排序
3. 根据矩形、圆形或者其他的几何形状过滤搜索结果