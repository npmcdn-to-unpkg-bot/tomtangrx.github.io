---
layout: post
title: mongodb  查询
category: 技术
tags: Mongodb
keywords: mongodb
---

#  mongodb  查询

## 集合的查询

db.collection.find()  查询集合中文档并返回结果为游标的文档集合。
语法：
``` javascript
> db.collection.find(query, projection)
```

|参数           |类型           |描述                            |
|---------------|:-------------:| ------------------------------:|
|query          |文档           |可选. 使用查询操作符指定查询条件|
|projection     |文档           |可选.使用投影操作符指定返回的键。查询时返回文档中所有键值， 只需省略该参数即可（默认省略）.|

返回值: 匹配查询条件的文档集合的游标. 如果指定投影参数，查询出的文档返回指定的键 ,"_id"键也可以从集合中移除掉。


## 嵌入文档查询

## 复杂条件查询

## 排序

## 分组

## 分页