---
title: MongoDB Aggregation
author: Kairou Zeng
date: 2018/11/12
tags: [Mongodb, 聚合]
---

## 为什么写

新项目开始对外开展运营活动，因此需要将记录到的运营数据进行整理传递给平台，做KPI数据记录和统计。许久未用到Mongo的聚合操作，因此做一个笔记。

## 聚合操作

MongoDB提供了三种聚合方法:

 - Aggregation pipeline：
 
    管道提供了MongoDB原生的操作，是数据聚合的最佳操作。

    聚合管道可以在分片集合上操作

- Map-reduce function

    没有管道聚合这么高效，并且更为复杂。

    使用MapReduce要实现两个函数`Map函数`和`Reduce函数`。`Map函数`调用`emit(key,value)`，遍历collection中所有的记录，将key与value传递给`Reduce函数`进行处理。

    MapReduce可以在分片集合上操作，还可以输出到分片集合。

- Single purpose aggregation methods

    MongoDB也提供`db.collection.estimatedDocumentCount()`、`db.collection.count()`、`db.collection.distinct()`

