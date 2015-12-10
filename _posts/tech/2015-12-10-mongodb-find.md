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

<table>
    <thead><tr><th>参数</th><th>类型</th><th>描述</th></tr></thead>
    <tbody>
        <tr>
            <td>query</td>
            <td>文档</td>
            <td>可选. 使用查询操作符指定查询条件</td>
        </tr>
        <tr>
            <td>projection</td>
            <td>文档</td>
            <td>可选.<br/>使用投影操作符指定返回的键。<br/>查询时返回文档中所有键值，<br/> 只需省略该参数即可（默认省略）.</td>
        </tr>
    </tbody>
</table>

返回值: 匹配查询条件的文档集合的游标. 如果指定投影参数，查询出的文档返回指定的键 ,"_id"键也可以从集合中移除掉。


## 嵌入文档查询

## 复杂条件查询

## 排序

## 分组

## 分页