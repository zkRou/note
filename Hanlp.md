---
title: NLP
author: Kairou Zeng
date: 2017/11/29
---

#### NLP工具 

> 汉语言处理包HanLP是由一系列模型与算法组成的Java工具包，目标是普及自然语言处理在生产环境中的应用。功能：主要有中文分词，词性标注，命名实体识别，关键词提取，自动摘要，短语提取，拼音转换，简繁转换，文本推荐，依存句法分析，文本分类：情感分析，word2vec，语料库工具

- 调用方法

  HanLP几乎所有的功能都可以通过工具类HanLP快捷调用。

  ```java
  HanLP.segment("你好，欢迎使用HanLP汉语处理包！")
  ```

  HanLP中有一系列"开箱即用"的静态分词器。

  分词结果包含词性。

- 标准分词

  ```java
  StandardTokenizer.segment("商品和服务")
  ```

  `HanLP.segment`其实是对`StandardTokenizer.segment`的包装。

- NLP分词

  ```java
  NLPTokenizer.segment("中国科学院计算技术研究所的宗成庆教授正在教授自然语言处理课程")
  ```

  NLP分词`NLPTokenizer`会执行全部命名实体识别和词性标注。

  索引分词`IndexTokenizer`是面向搜索引擎的分词器，能够对长词全切分，另外通过`term.offset`可以获取单词在文本中的偏移量。

- 索引分词

  ```java
  List<Term> termList = IndexTokenizer.segment("主副食品");
  for(Term term : termList){
    System.out.println(term.toString() + "[" + term.offset + ":" + (term.offset + term.word.length()) + "]");
  }
  ```

- N-最短路径分词

  ```java
  Segment nShortSegment = new NShortSegment().enableCustomDictionary(false).enablePlaceRecognize(true).enableOrganizationRecognize(true);
          Segment shortestSegment = new DijkstraSegment().enableCustomDictionary(false).enablePlaceRecognize(true).enableOrganizationRecognize(true);
          String sentence = "中国科学院计算技术研究所的宗成庆教授正在教授自然语言处理课程";
          System.out.println("N-最短分词：" + nShortSegment.seg(sentence) + "\n最短路分词：" + shortestSegment.seg(sentence));
  ```

  ​



`相关链接`：

[词性标注](http://www.hankcs.com/nlp/part-of-speech-tagging.html#h2-8)