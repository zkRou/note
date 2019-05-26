---
title: 流 - Stream
author: Kairou Zeng
date: 2019/05/15
tags: [Java8, 流]
---

## 如何生成流

在Java8中，集合接口有两个方法来生成流：

- stream()：为集合创建串行流

- parallelStream()：为集合创建并行流

## Stream提供的方法

- forEach - 用于迭代每个数据

```java
List<String> list = Arrays.asList("a","b","c");
list.forEach(System.out::println);
```

- map - 用于映射每个元素到对应的结果

```java
List<Integer> numbers = Arrays.asList(3,2,2,3);
List<Integer> squaresList = numbers.stream().map(i -> i*i).distinct().collect(Collectors.toList());
```

- filter - 用于通过设置的条件过滤出元素

```java
List<String> strings = Arrays.asList("a","b","c","");
int count = strings.stream().filter(s -> s.isEmpty()).count();
```

- limit - 用于获取指定数量的流

```java
List<String> list = Arrays.asList("a","b","c");
list.stream().limit(1).forEach(System.out::println);
```

- sorted - 对流进行排序

```java
List<Integer> numbers = Arrays.asList(3,2,2,3);
numbers.stream().sorted().forEach(System.out::println);
```