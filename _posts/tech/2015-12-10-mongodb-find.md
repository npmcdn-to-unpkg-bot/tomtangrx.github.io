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

<table class="table table-bordered table-striped table-condensed">
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

### 查询条件query
**查询一条**
db.collection.findOne() 
**查询所有**
db.collection.find() 
**多条件查询**
db.collection.find({createAt:{$gt: new Date() },name:'aaa'}) 
-> SQL:
where  createAt > now and name ='aaa'

### projection解析

```javascript
{
    // 排序 类型 document
    // [["a", "asc"], ["b", "desc"]]
    // ["a", ["b", "desc"]]
    //{a: 1, b: -1}
    sort: {a:1,b:-1},
    // 跳过的数据条数
    // 类型 数字
    skip: 10,
    // 最大数据获取的条数
    limit: 10,
    // 筛选获取的字段
    // 1 代表要只获取 0 代表不获取。
    fields: {firstname: 1, lastname: 1}
}

```

## 嵌入文档查询

--为后面的示例构造测试数据。

    > db.test.find()
    { "_id" : ObjectId("4fd5ada3b9ac507e96276f22"), "name" : { "first" : "Joe", "last" : "He" }, "age" : 45 }

    --当嵌入式文档为数组时，需要$elemMatch操作符来帮助定位某一个元素匹配的情况，否则嵌入式文件将进行全部的匹配。

    --即检索时需要将所有元素都列出来作为查询条件方可。

    > db.test.findOne()
    
    {
         "_id" : ObjectId("4fd5af76b9ac507e96276f23"),
         "comments" : [
                 {
                         "author" : "joe",
                         "score" : 3
                 },
                 {
                         "author" : "mary",
                         "score" : 6
                 }
         ]
    }

    > db.test.find({"comments": {"$elemMatch": 
        {"author":"joe","score":{"$gte":3}}}}

    { "_id" : ObjectId("4fd5af76b9ac507e96276f23"), "comments" : [ { "author" : "joe", "score" : 3 }, { "author" : "mary", "score" : 6 } ] }
    


## 复杂条件查询
MongoDB提供了一组比较操作符：$lt/$lte/$gt/$gte/$ne，依次等价于< / <= /> />= /!=。
```javascript
db.test.find({"age":{"$gte":18, "$lte":40}})
```
相当于：
```SQL
where age >= 18 and age <= 40
```

*$in == in*
*$nin*等同于SQL中的 *not in*，同时也是 *$in*的取反
*$or*等同于SQL中的 *or*，*$or*所针对的条件被放到一个数组中，每个数组元素表示or的一个条件
下面的示例等同于name = "stephen1" or age = 35
``` javascript
 db.test.find({"$or": [{"name":"stephen1"}, {"age":35}]})
```

*$not表示取反，等同于SQL中的not。*
db.test.find({"name": {"$not": {"$in":["stephen2","stephen1"]}}})

### null数据类型的查询
在进行值为null数据的查询时，所有值为null，以及不包含指定键的文档均会被检索出来。
```javascript
db.test.find({"x":null})
```
需要将null作为数组中的一个元素进行相等性判断，即便这个数组中只有一个元素。
再有就是通过$exists判断指定键是否存在。
```javascript
db.test.find({"x": {"$in": [null], "$exists":true}})
```
{ "_id" : ObjectId("4fd59d30b9ac507e96276f1b"), "x" : null }
### 正则查询
MongoDB中使用了Perl规则的正则语法。

i表示忽略大小写

db.test.find({"name":/stephen?/i})
{ "_id" : ObjectId("4fd59ed7b9ac507e96276f1d"), "name" : "stephen" }
{ "_id" : ObjectId("4fd59edbb9ac507e96276f1e"), "name" : "stephen1" } 

### 基于数组的查询
    数组中所有包含banana的文档都会被检索出来。

    > db.test.find({"fruit":"banana"})
    
    { "_id" : ObjectId("4fd5a177b9ac507e96276f1f"), "fruit" : [ "apple", "banana", "peach" ] }

    { "_id" : ObjectId("4fd5a1f0b9ac507e96276f21"), "fruit" : [ "cherry", "banana","apple" ] }

检索数组中需要包含多个元素的情况，这里使用$all。下面的示例中，数组中必须同时包含apple和banana，但是他们的顺序无关紧要。

   > db.test.find({"fruit": {"$all": ["banana","apple"]}})
 
    { "_id" : ObjectId("4fd5a177b9ac507e96276f1f"), "fruit" : [ "apple", "banana", "peach" ] }

    { "_id" : ObjectId("4fd5a1f0b9ac507e96276f21"), "fruit" : [ "cherry", "banana", "apple" ] } 

下面的示例表示精确匹配，即被检索出来的文档，fruit值中的数组数据必须和查询条件完全匹配，即不能多，也不能少，顺序也必须保持一致。

    db.test.find({"fruit":["apple","banana","peach"]})

    { "_id" : ObjectId("4fd5a177b9ac507e96276f1f"), "fruit" : [ "apple", "banana", peach" ] } 

下面的示例将匹配数组中指定下标元素的值。数组的起始下标是0。

 > db.test.find({"fruit.2":"peach"})
    
    { "_id" : ObjectId("4fd5a177b9ac507e96276f1f"), "fruit" : [ "apple", "banana", peach" ] } 

可以通过$size获取数组的长度，但是$size不能和比较操作符联合使用。

db.test.find({"fruit": {$size : 3}})

如果需要检索size > n的结果，不能直接使用$size，只能是添加一个额外的键表示数据中的元素数据，在操作数据中的元素时，需要同时更新size键的值。

> db.test.update({}, {"$set": {"size":3}},false,true)
> 

通过$slice返回数组中的部分数据。"$slice":2表示数组中的前两个元素。

 > db.test.find({},{"fruit": {"$slice":2}, "size":0})
    
    { "_id" : ObjectId("4fd5a18cb9ac507e96276f20"), "fruit" : [ "apple", "kumquat" ]}
    
    { "_id" : ObjectId("4fd5a1f0b9ac507e96276f21"), "fruit" : [ "cherry", "banana" ]} 

通过$slice返回数组中的部分数据。"$slice":-2表示数组中的后两个元素。
> db.test.find({},{"fruit": {"$slice":-2}, "size":0})
    
    { "_id" : ObjectId("4fd5a18cb9ac507e96276f20"), "fruit" : [ "orange", "strawberry" ] }
    
    { "_id" : ObjectId("4fd5a1f0b9ac507e96276f21"), "fruit" : [ "apple", "strawberry" ] }

$slice : [2,1]，表示从第二个2元素开始取1个，如果获取数量大于2后面的元素数量，则取后面的全部数据。

    db.test.find({},{"fruit": {"$slice":[2,1]}, "size":0})


## 排序

## 分组

## 分页