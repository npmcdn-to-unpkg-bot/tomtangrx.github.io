---
layout: post
title: meteor 分页优化
category: 技术
tags: meteor 分页
keywords: 分页 meteor
---

#  meteor 分页优化

## 问题
1. 采用客户端分页后如果数据量大的时候有卡顿的情况，测试了10万条数据
2. 如果一个页面有两个同样集合的订阅会窜掉。

## 解决思路
1. 针对客户端数据库大的情况采用 服务端分页的办法
2. 两个同样集合的订阅的解决办法是把订阅后的集合名字叫不同的名字
   参照[Meteor.publish](http://docs-zh-cn.meteor.com/#/full/meteor_publish)的例子 counts-by-room

## 代码

### 服务端分页帮助方法
```javascript
/**
 * 分页帮助
 * @param sub
 * @param pageCollectionName
 * @param query
 * @constructor
 */
Meteor.PagerPublishHelp = function(sub,pageCollectionName,query){
  var initializing  = true;
  var c = query();
  var handle1  = c.observeChanges({
    movedBefore:function(id, before) {
      return query().forEach(function(o, i) {
        return sub.changed(pageCollectionName, o._id,o);
      });
    },
    changed: function(id, fields) {
      var e, error;
      try {
        return sub.changed(pageCollectionName, id, fields);
      } catch (error) {
        e = error;
      }
    },
    removed: function(id) {
      var e, error;
      try {
        sub.removed(PageName, id);
        return query().forEach(function(o, i) {
          return sub.changed(pageCollectionName, o._id,o);
        });
      } catch (error) {
        e = error;
      }
    }
  });
  var handle2  = c.observe({
      addedAt: function (doc,at,bef) {
        if (initializing) return;
        query().forEach(function(d,i){
          if(at === i){
            return sub.added(pageCollectionName, d._id, doc);
          }else {
            return sub.changed(pageCollectionName, d._id, d);
          }
        });
      },
    }
  );
  initializing = false;
  query().forEach(function(d,i){
    return sub.added(pageCollectionName, d._id, d);
  });
  sub.ready();
  sub.onStop(function (){
    handle1.stop();
    handle2.stop();
  });
};

```

### 服务端订阅方法
```javascript
/**
 * @param typeId  文章类别
 * @param pageSize 每页条数
 * @param page 第几页
 * @param pageCollectionName  客户端集合名
 */
Meteor.publish('article', function (typeId,pageSize,page,pagerCollectionName) {
  var self = this;
  // 当前第几页
  if(page < 0){
    page = 0;
  }
  var skip = page * pageSize; // 计算跳过条数
  Meteor.PagerPublishHelp(self,pagerCollectionName,function(){
    // 分页查询
    return Article.find({articleTypeId:typeId}, {
      sort: {createAt: -1},
      skip: skip || 0
      , limit: pageSize
    });
    
  });
});
```

### 订阅写法

```javascript
Meteor.subscribe('article',typeId,50,pageIndex,ArticleListCollectionName);
```


### 条数的取得方式采用`easday:publish-counts`



### 客户端分页写法

**HTML 分页模板**

```html
<template name="_pagerList">
    {{ "{{" }}> Template.dynamic template=getTemplateName data=innerDataContext }}
</template>
<template name="_defaultPagerList">
    <div class="page">
        {{ "{{" }}#if showFirst}}
            <a  href="{{ "{{" }}pageUrl}}0" >首页</a>
        {{ "{{" }}/if}}
        {{ "{{" }}#if showFirst}}
            <a  href="{{ "{{" }}pageUrl}}{{ "{{" }}prePage}}" >上一页</a>
        {{ "{{" }}/if}}
        {{ "{{" }}#each pages}}
            <a  href="{{ "{{" }}pageUrl}}{{ "{{" }}this.num}}" class="{{ "{{" }}#if this.current}}cur{{ "{{" }}/if}}">{{ "{{" }}this.text}}</a>
        {{ "{{" }}/each}}
        {{ "{{" }}#if showLast}}
            <a  href="{{ "{{" }}pageUrl}}{{ "{{" }}nextPage}}" >下一页</a>
        {{ "{{" }}/if}}
        {{ "{{" }}#if showLast}}
            <a  href="{{ "{{" }}pageUrl}}{{ "{{" }}lastPage}}" >尾页</a>
        {{ "{{" }}/if}}
    </div>
</template>

```

**HTML 分页模板 js**

```javascript
var defaultOptions = {
  pageCount : 0,
  pageSize:20,
  visiblePage:7,
  totalPages:0,
};
Template._pagerList.helpers({
  getTemplateName:function() {
    return "_defaultPagerList";
  },
  innerDataContext :function(){
    var opt = _.extend({},defaultOptions,this);
    var totalPages = Math.ceil(opt.pageCount / opt.pageSize);
    if(opt.totalPages !== totalPages){
      opt.totalPages = totalPages;
    }
    return opt;
    //return this;
  }
});
Template._defaultPagerList.onCreated(function(){
  var pageUrl = Template.instance().data.pageUrl;
  this.urlTemplate = _.template(pageUrl);
});
Template._defaultPagerList.helpers({
  pages:function(){
    var visiblePage = this.visiblePage;
    if (visiblePage > this.totalPages) {
      visiblePage =  this.totalPages;
    }
    var half = Math.floor(visiblePage / 2);
    var start = this.currentPage - half - visiblePage % 2;
    var end = this.currentPage + half+1;
    if (start < 0) {
      start = 0;
      end = visiblePage;
    }
    if (end > this.totalPages-1) {
      end = this.totalPages-1;
      start = this.totalPages - visiblePage;
    }
    var page = [];
    var itPage = start;
    while (itPage <= end) {
      page.push({num:itPage,text:itPage+1, current: itPage==this.currentPage });
      itPage++;
    }
    return page;
  },
  showFirst:function(){
    return this.currentPage !=0;
  },
  prePage:function(){
    return this.currentPage - 1;
  },
  nextPage:function(){
    return this.currentPage + 1;
  },
  showLast:function(){
    return this.currentPage !=( this.totalPages -1);
  },
  lastPage:function(){
    return this.totalPages -1;
  },
  pageUrl:function(pageIndex){
    return Template.instance().urlTemplate({page:pageIndex});
  }

});

```

### 文章列表HTML模板

```html
<template name="NewsList">
    <div class="txt_box">
        <div class="title"><strong>文章列表</strong></div>
        <div class="maintxt">
            <ul class="news_list">
                {{ "{{" }}#each this.pageData}}
                    <li>
                         列表详细模板
                    </li>
                {{ "{{" }}/each}}
            </ul>
        </div>

        {{ "{{" }}> _pagerList this.page}}
    </div>
</template>
```

`this.pageData`是订阅回来的数据，`this.page`是分页的设置
this.page的格式： 
```json
page:{
    currentPage : this.params.pageIndex | 0,  // 当前页码
    pageCount: Counts.get('articleCount2KR7BKN68hKdYem28'), // 总条数
    pageSize:50,   // 每页条数
    pageUrl:'/news/newscenter/p<%=page%>', // 页的URL  page 表示页码
},
```
其他参数可参照 *HTML 分页模板 js* 的 `defaultOptions`