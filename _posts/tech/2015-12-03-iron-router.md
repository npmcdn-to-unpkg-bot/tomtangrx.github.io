---
layout: post
title: Iron.Router 指南
category: 技术
tags: [meteor , nodejs , Iron.Router]
keywords: meteor,nodejs,Iron.Router
---

# Iron.Router 指南

专门为[Meteor](https://github.com/meteor/meteor)设计的，一套可以在浏览器和服务器工作的路由器


## 快速开始
你可以通过Meteor的包管理系统来安装 iron:router:

```bash
> meteor add iron:router
```

可以使用`meteor update`命令更新iron:router到最新版本:

```bash
> meteor update iron:router
```

首先，在你的javascript文件中创建一个路由。默认，路由是为客户端创建的，会运行在浏览器中。

```javascript
Router.route('/', function () {
  this.render('Home');
});
```

当用户导航到url`/`， 上面的路由将会渲染名字叫`Home`的模板到页面上。

```javascript
Router.route('/items');
```

第二个路由会自动渲染名字叫`Items`或`items`的模板到页面上。像上面这样简单的例子，不需要使用路由函数。

到目前为止，我们只创建了直接运行在浏览器上的路由。但是我们也可以创建服务端路由。这些钩子直接进入HTTP请求，并且被使用到实现REST端点。

```javascript
Router.route('/item', function () {
  var req = this.request;
  var res = this.response;
  res.end('从服务器端返回的你好！！\n');
}, {where: 'server'});
```

`where: 'server'` 选项告诉Router，这是服务器端路由.

## 内容列表

- [概念](#concepts)
  - [仅限服务器](#server-only)
  - [仅限客户端](#client-only)
  - [客户端和服务器](#client-and-server)
  - [响应](#reactivity)
- [路由参数](#route-parameters)
- [渲染模板](#rendering-templates)
- [渲染带数据的模板](#rendering-templates-with-data)
- [布局](#layouts)
  - [渲染模板到（带Javascript）区域](#rendering-templates-into-regions-with-javascript)
  - [设置区域数据上下文](#setting-region-data-contexts)
  - [使用contentFor渲染模板到区域](#rendering-templates-into-regions-using-contentfor)
- [客户端导航](#client-navigation)
  - [使用链接](#using-links)
  - [使用JavaScript](#using-javascript)
  - [使用重定向](#using-redirects)
  - [使用链接到服务器段路由](#using-links-to-server-routes)
- [命名路由](#named-routes)
  - [取得当前路由](#getting-the-current-route)
- [模板查找](#template-lookup)
- [模板的路径、链接帮助器](#path-and-link-template-helpers)
  - [pathFor](#pathfor)
  - [urlFor](#urlfor)
  - [linkTo](#linkto)
- [路由设置项](#route-options)
  - [路由特别的设置项](#route-specific-options)
  - [全局默认设置项](#global-default-options)
- [订阅](#subscriptions)
  - [等待和就绪](#wait-and-ready)
  - [订阅设置项](#the-subscriptions-option)
  - [waitOn设置项](#the-waiton-option)
- [服务器端路由](#server-routing)
  - [创建路由](#creating-routes)
  - [资源路由](#restful-routes)
  - [404s 和 客户端 vs 服务器端路由](#404s-and-client-vs-server-routes)
- [插件](#plugins)
  - [创建插件](#creating-plugins)
- [钩子](#hooks)
  - [使用钩子](#using-hooks)
  - [应用钩子到特定路由](#applying-hooks-to-specific-routes)
  - [使用Iron.Router.hooks命名空间](#using-the-ironrouterhooks-namespace)
  - [使钩子方法有效](#available-hook-methods)
- [路由控制器](#route-controllers)
  - [创建路由控制器](#creating-route-controllers) 
  - [继承路由控制器](#inheriting-from-route-controllers) 
  - [访问当前路由控制器](#accessing-the-current-route-controller)
  - [设置响应状态变量](#setting-reactive-state-variables)
  - [取得响应状态变量](#getting-reactive-state-variables)
- [自定义路由渲染](#custom-router-rendering)
- [遗留的浏览器支持](#legacy-browser-support)

## 概念

### Server Only 

### 仅仅服务器

在典型的web应用中，你通过一个独特的Url（如“/items/5”），创建一个http请求到服务器。并且一个在服务器端的路由决定哪个函数被调用。这个函数会向浏览器发送一些html并关闭连接。

### Client Only

### 仅仅客户端

在一些流行的web应用中，你会使用“客户端”路由，如pagejs、Backbone路由。 这些路由运行在浏览器中，不访问服务器，通过使用高级的浏览器HTML5特性（pushState、url哈希片段）来导航应用程序。

### Client and Server

### 客户端和服务器端

Iron.Router运行在客户端*和*服务器端。你可以定义一个路由只运行在服务器端，或者一个路由只运行在客户端。大多数时候会创建运行于客户端的路由。这会让我们的应用在载入的时候非常快，因为当你围绕应用导航的时候，你不需要载入新的html页面。

路由器*知道*所有在浏览器和服务器端的路由。这意味着你可以点击一个链接到一个服务器路由，也可能到一个客户端路由。它也意味在服务器端，如果没有客户端路由定义，我们可以返回一个404响应到客户端，而不是加载Meteor应用。

### __Reactivity__

### 响应

路由函数和大多数钩子运行在响应计算中。这意味着如果响应数据代码改变的时候他们将会自动运行。举个例子，如果在路由函数中访问`Meteor.user()`，每次`Meteor.user()`改变路由函数会重新运行。

## 路由参数 <span id="route-parameters"></span>

路由和可以有参数变量。 举个例子, 可以创建一个带参数id的路由去显示任何文章。参数`id`依赖于你想显示的文章，如"/posts/1"或者"/posts/2"。在路由中，定义参数的方法是，在参数名称前使用符号`:`标记。当我们访问这个url，在对应的路由函数中，这个参数的实际值会存储在路由的属性`this.params`.

在这个例子中，我们有一个路由参数名字叫`_id`。如果在浏览器中，导航到url`/post/5`，在路由函数中我们可以通过`this.params._id`取得参数`_id`的实际值。在这里例子中 `this.params._id => 5`.

```javascript
// 如果url是 "/post/5"
Router.route('/post/:_id', function () {
  var params = this.params; // { _id: "5" }
  var id = params._id; // "5"
});
```

我们可以设置多个路由参数。在下面的例子中，我们有`_id`和`commentId`两个参数。如果你导航到url`/post/5/comments/100`，这个时候在路由函数中`this.params._id => 5`和`this.params.commentId => 100`.

```javascript
// 如果url是"/post/5/comments/100"
Router.route('/post/:_id/comments/:commentId', function () {
  var id = this.params._id; // "5"
  var commentId = this.params.commentId; // "100"
});
```

如果有查询字符串或者散列片段在url中，可以通过访问`this.params`对象的`query`和`hash`属性得到。

```javascript
// 如果url是: "/post/5?q=s#hashFrag"
Router.route('/post/:_id', function () {
  var id = this.params._id;
  var query = this.params.query;
  
  // query.q -> "s"
  var hash = this.params.hash; // "hashFrag"
});
```

**Note**: 如果想当散列改变的时候，重新运行一个函数，可以这样做:

```javascript
// 取得一个控制器的句柄
// 在 template helper 中用下面注释掉的句子
// var controller = Iron.controller();
var controller = this;

// params的任何部分改版将会是排版失效，并响应 getParams 方法
// 包括散列.
var params = controller.getParams();
```

默认情况下路由器将遵循浏览器的默认行为。如果点击一个有hash frag的链接，他会滚动到对应Id的元素那里。如果想使用`controller.getParams()`，可以在对应的autorun中或者在helper中执行。

## 渲染模板<span id="rendering-templates"></span>

通常我们希望在用户到一个特定的url的时候给他渲染特定的模板。举个例子，当用户访问url`/posts/1`的时候我们想要渲染名字是`Post`的模板给用户。

```handlebars
<template name="Post">
  <h1>Post: {{title}}</h1>
</template>
```

```javascript
Router.route('/post/:_id', function () {
  this.render('Post');
});
```

我们可以在路由函数中，通过调用`render`的方法渲染模板。方法`render`第一个参数是模板名称

## Rendering Templates with Data

## 渲染带数据的模板

在上面的例子中`title`值没有定义。你可以在Post模板中创建一个名字叫`title`的helper，或者我们可以直接在路由上下文中直接设置一个值到模板的data 上下文中。怎么做呢？我们在调用`render`的第二个参数中提供了一个叫`data`的选项。

```javascript
Router.route('/post/:_id', function () {
  this.render('Post', {
    data: function () {
      return Posts.findOne({_id: this.params._id});
    }
  });
});
```

## Layouts

## 布局

布局允许重用一个公共的钩子并且对多个页面产生影响，所以我们不需要重复定义html个逻辑在每个单独的页面模板。

布局也是模板。但是在布局中我们可以使用特殊的helper`yield`, 可以认为 `yield`是一个内容的占位符。 这个占位符定义了一个 *区域*。在实际运行路由的时候，内容会被`注入`到这个区域中。这让我们只需要改变 *yield regions* 的内容就可以在多个不同的页面重用布局。

```handlebars
<template name="ApplicationLayout">
  <header>
    <h1>{{title}}</h1>
  </header>

  <aside>
    {{> yield "aside"}}
  </aside>

  <article>
    {{> yield}}
  </article>

  <footer>
    {{> yield "footer"}}
  </footer>
</template>
```

可以在路由函数中通过调用`layout`方法设置布局模板。

```javascript
Router.route('/post/:_id', function () {
  this.layout('ApplicationLayout');
});
```

如果想要为所有的路由设置默认的布局模板，可以配置全局路由设置。

```javascript
Router.configure({
  layoutTemplate: 'ApplicationLayout'
});
```

### Rendering Templates into Regions with JavaScript

### 使用JavaScript渲染模板到区域

在我们的路由函数中我们可以设置哪个模板会渲染到哪个区域。

```handlebars
<template name="Post">
  <p>
    {{post_content}}
  </p>
</template>

<template name="PostFooter">
  Some post specific footer content.
</template>

<template name="PostAside">
  Some post specific aside content.
</template>
```
比如说，我们想使用`ApplicationLayout`，并且想在路由`/post/:_id`的各自区域中使用上面定义的模板。我们可以直接在路由函数中使用`render`方法的`to`选项。

```javascript
Router.route('/post/:_id', function () {
  // 使用名字叫ApplicationLayout的模板做layout
  this.layout('ApplicationLayout');

  // 渲染Post模板到"main"区域
  // {{> yield}}
  this.render('Post');

  // 渲染模板PostAside名字叫"aside"的区域 
  // {{> yield "aside"}}
  this.render('PostAside', {to: 'aside'});

  // 渲染模板PostFooter到"footer"区域 
  // {{> yield "footer"}}
  this.render('PostFooter', {to: 'footer'});
});
```

### Setting Region Data Contexts

### 设置区域数据上下文

通过`render`方法的`data`属性设置区域数据上下文。也可以设置布局的数据上下文.

```javascript
Router.route('/post/:_id', function () {
  this.layout('ApplicationLayout', {
    data: function () { return Posts.findOne({_id: this.params._id}) }
  });

  this.render('Post', {
    // we don't really need this since we set the data context for the
    // the entire layout above. But this demonstrates how you can set
    // a new data context for each specific region.
    data: function () { return Posts.findOne({_id: this.params._id})
  });

  this.render('PostAside', {
    to: 'aside',
    data: function () { return Posts.findOne({_id: this.params._id})
  });

  this.render('PostFooter', {
    to: 'footer',
    data: function () { return Posts.findOne({_id: this.params._id})
  });
});
```

### Rendering Templates into Regions using contentFor

### 使用contentFor渲染模板到区域

在路由函数中渲染模板到区域非常有用，尤其当我们需要运行一些自定义的逻辑或者模板名称是动态的时候。 但是常常一个更简单的方式是直接在主模板中通过`contentFor` helper 直接为区域提供内容。比如说同样是上面的例子，模板我们还是使用`ApplicationLayout`。但是这次不为每个区域定义新的模板，我们直接 *内嵌* 内容到`post` 模板中

```handlebars
<template name="Post">
  <p>
    {{post_content}}
  </p>

  {{#contentFor "aside"}}
    Some post specific aside content.
  {{/contentFor}}

  {{#contentFor "footer"}}
    Some post specific footer content.
  {{/contentFor}}
</template>
```

现在我们可以简单指定我们的布局并且渲染`Post`模板。

```javascript
Router.route('/post/:_id', function () {
  this.layout('ApplicationLayout', {
    data: function () { return Posts.findOne({_id: this.params._id}) }
  });

  // this time just render the template named "Post" into the main
  // region
  this.render('Post');
});
```

甚至不使用内嵌内容而提供一个`template`选项给`contentFor` helper。

```handlebars
<template name="Post">
  <p>
    {{post_content}}
  </p>

  {{> contentFor region="aside" template="PostAside"}}

  {{> contentFor region="footer" template="PostFooter"}}
</template>
```

## Client Navigation
*客户端导航*

大多数时候你的应用程序的用户在浏览器中浏览内容，而不是创建一个新的请求到服务器。到服务器请求数据的时候很少。

### Using Links
*使用链接*

用户可以通过点击链接导航程序。打个比方，我们在layout中有很多导航链接。

```handlebars
<template name="ApplicationLayout">
  <nav>
    <ul>
      <li>
        <a href="/">Home</a>
      </li>
      
      <li>
        <a href="/one">Page One</a>
      </li>

      <li>
        <a href="/two">Page Two</a>
      </li>
    </ul>
  </nav>

  <article>
    {{> yield}}
  </article>
</template>

<template name="Home">
  Home
</template>

<template name="PageOne">
  Page One
</template>

<template name="PageTwo">
  Page Two
</template>
```

下一步，为这些页面定义路由。

```javascript
Router.route('/', function () {
  this.render('Home');
});

Router.route('/one', function () {
  this.render('PageOne');
});

Router.route('/two', function () {
  this.render('PageTwo');
});
```

当应用第一次导入根url`/`，第一个路由会运行，并且`Home`模板会被渲染到页面上。

如果用户点击链接`Page One`，浏览器的url会变为 `/one` 并且第二个路由会运行，渲染模板 `PageOne`。

同样，如果用户点击链接`Page Two`，浏览器的url会变为`/two`，并且第三个路由运行，渲染模板`PageTwo`.

虽然浏览器的url是改变了，但是这个是浏览器端路由，浏览器不需要创建请求到服务器。

### Using JavaScript
*使用JavaScript*

可以通过JavaScript调用`Router.go`方法导航到一个给定的Url 或者路由名字。比如说我们定义一个按钮的点击事件句柄。

```handlebars
<template name="MyButton">
  <button id="clickme">Go to Page One</button>
</template>
```

在点击事件的句柄中，我们告诉路由到url `/one`。

```javascript
Template.MyButton.events({
  'click #clickme': function () {
    Router.go('/one');
  }
});
```

这会改变浏览器的url到`/one` 并且会运行相对于的路由。

### Using Redirects
### 使用重定向

在路由函数中，使用`redirect`方法可以从一个路由重定向到另一个路由。

```javascript
Router.route('/one', function () {
  this.redirect('/two');
});

Router.route('/two', function () {
  this.render('PageTwo');
});
```

### Using Links to Server Routes
*使用链接到服务器端路由*

比如说你想链接到一个服务器端路由。举个例子，一个文件下载路由（这个是不得不请求服务器的）。

```javascript
Router.route('/download/:filename', function () {
  this.response.end('some file content\n');
}, {where: 'server'});
```

现在，在我们的html我们有一个链接用来下载一个特别的文件。

```handlebars
<a href="/download/myfilename">下载文件</a>
```

当用户点击 `下载文件` 链接，这个路由会请求服务器，并且运行服务器端路由。

## Named Routes
*命名路由*

路由可以命名，这个名字可以用来指向这个路由。如果没有给它命名。路由会基于路径命名。但是我们可以使用`name`属性明确的给路由命名。

```javascript
Router.route('/posts/:_id', function () {
  this.render('Post');
}, {
  name: 'post.show'
});
```

现在已经给我们的路由命名了，如果需要可以访问路由对象，像这样:

```javascript
Router.routes['post.show']
```

但是我们也可以在方法`Router.go`中使用路由名字，像这样:

```javascript
Router.go('post.show');
```

现在可以在`Router.go`中使用命名的路由，我们也可以传入参数对象，查询和哈希散列的参数对象.

```javascript
Router.go('post.show', {_id: 1}, {query: 'q=s', hash: 'hashFrag'});
```

上面的JavaScript将会导航到这个url:

```handlebars
/post/1?q=s#hashFrag
```

### Getting the Current Route
*取得当前路由*

可用通过当前控制器得到当前路由的名字：

```javascript
Router.current().route.getName()
```

## Template Lookup
*模板钩子*

如果你没有在路由中明确的设置模板属性，并且你没有明确的渲染模板名字，这个如有会尝试通过路由的名字去渲染模板。默认，路由会查找模板的类案例名称。

举个例子，如果定义的路由如下：

```javascript
Router.route('/items/:_id', {name: 'items.show'});
```

这个路由会默认查找名字叫`ItemsShow`的模板，每个单词的首字母大写，并且标点符号被移除。如果你打算自定义这个行为，可以设置自己的转换函数。举个例子，我们不想要任何的转换，可以吧转换函数这样写：

```javascript
Router.setTemplateNameConverter(function (str) { return str; });
```

## Path and Link Template Helpers
*路径和链接模板`Helpers`*

### pathFor

这里有一些模板帮助，我们可以通过他们创建基于路由的链接。首先，我们使用`{{pathFor}}` helper去生成一个已经命名路由的路径。我们创建一个名字叫`post.show`的路由的链接:

```handlebars
{{#with post}}
  <a href="{{pathFor route='post.show'}}">Post Show</a>
{{/with}}
```

假设我们有一个id是1的post，上面的代码片段等价于:

```handlebars
<a href="/posts/1">Post Show</a>
```

我们可以传入 `data`, `query` 和 `hash` 选项到 `pathFor` helper。

```handlebars
<a href="{{pathFor route='post.show' data=getPost query='q=s' hash='frag'}}">Post Show</a>
```

这个data对象将会被内插到路由参数中。`query`和`hash`参数将会被作为query string 和hash散列添加到链接中。比如，data对象如下:

```javascript
data = { _id: 1 };
```

上面的`pathFor`表达式生成的链接结果如下:

```handlebars
<a href="/post/1?q=s#frag">Post Show</a>
```

使用`pathFor` helper的好处是在应用程序中我们不需要使用硬编码`href`属性。


### urlFor

`pathFor` helper生成路由改定的路径，`urlFor`会生成一个完整的链接。举个例子，`pathFor`如果生成的路径是`/posts/1`，但是`urlFor`将生成 `http://mysite.com/posts/1`。

### linkTo

`linkTo` helper可以根据改定的路由、参数、hash和query自动生成一个html锚标记。你也可以提供一个内容块嵌入到链接中。

```handlebars
{{#linkTo route="post.show" data=getData query="q=s" hash="hashFrag" class="my-cls"}}
  <span style="color: orange;">
    Post Show
  </span>
{{/linkTo}}
```

上面的表达式将转换为下面的html：

```handlebars
<a href="/posts/1?q=s#hashFrag" class="my-cls">
  <span style="color: orange;">
    Post Show
  </span>
</a>
```

## Route Options
*路由选项*
到目前为止，你见到多种方式可以设置路由的`name`选项。这里提供其他方式设置路由选项。

### Route Specific Options
*路由具体选项*
在这个例子我们将省略路由函数，仅仅提供一个选项对象。属性对象将解释每个可能的选项.

```javascript
Router.route('/post/:_id', {
  // 路由的名字.
  // Used to reference the route in path helpers and to find a default template
  // for the route if none is provided in the "template" option. If no name is
  // provided, the router guesses a name based on the path '/post/:_id'
  name: 'post.show',

  // To support legacy versions of Iron.Router you can provide an explicit path
  // as an option, in case the first parameter is actually a route name.
  // However, it is recommended to provide the path as the first parameter of the
  // route function.
  path: '/post/:_id',

  // 控制器
  // If we want to provide a specific RouteController instead of an anonymous
  // one we can do that here. See the Route Controller section for more info.
  controller: 'CustomController',

  // 模板名字
  // If the template name is different from the route name you can specify it
  // explicitly here.
  template: 'Post',

  // layout 模板名
  // A layout template to be used with this route.
  // If there is no layout provided, a default layout will
  // be used.
  layoutTemplate: 'ApplicationLayout',

  // yield 区域设置
  // A declarative way of providing templates for each yield region
  // in the layout
  yieldRegions: {
    'MyAside': {to: 'aside'},
    'MyFooter': {to: 'footer'}
  },

  // 设置你订阅的地方
  subscriptions: function() {
    this.subscribe('items');
    
    // 添加订阅到等待列表
    this.subscribe('item', this.params._id).wait();
  },

  // 订阅或者其他你想等待的项。它会自动调用loading钩子,
  // 这也是和上面`subscriptions`选项不同的地方
  waitOn: function () {
    return Meteor.subscribe('post', this.params._id);
  },

  // 数据函数，会被自动使用到设置layout的data上下文上。
  // 这个函数也可以被钩子和插件使用。
  // 比如，当这个函数返回`null`的时候，`dataNotFound`插件会被调用，
  // 如果这样的话，not found 模板将会被渲染。
  data: function () {
    return Posts.findOne({_id: this.params._id});
  },

  // 在`Using Hooks`这一节，下面的钩子项目会再被介绍
  onRun: function () {},
  onRerun: function () {},
  onBeforeAction: function () {},
  onAfterAction: function () {},
  onStop: function () {},

  // action操作，可以使用函数名
  // The same thing as providing a function as the second parameter. You can
  // also provide a string action name here which will be looked up on a Controller
  // when the route runs. More on Controllers later. Note, the action function
  // is optional. By default a route will render its template, layout and
  // regions automatically.
  // Example:
  //  action: 'myActionFunction'
  action: function () {
    // render all templates and regions for this route
    this.render();
  }
});
```

### Global Default Options
*全局默认选项*
上面所有的路由设置选项都可以设置。这也会成为所有路由的默认选项设置。
使用方法`configure`设置默认路由选项：

```javascript
Router.configure({
  layoutTemplate: 'ApplicationLayout',

  template: 'DefaultTemplate'

  // .
  // .
  // .
});
```

每个路由的选项设置会覆盖默认路由设置。

## Subscriptions
*订阅*

有些时候你想等待一个或者多个订阅完成，或者其他action的结果。例如，在等待订阅数据的时候，你可能想显示`loading`模板。

### Wait and Ready
*等待和完成*

你可以使用`wait`方法去添加一个订阅到等待列表。如果等待列表中所有的项都完成的时候，`this.ready()`会返回`true`。

```javascript
Router.route('/post/:_id', function () {
  // 添加订阅指针到等待列表
  this.wait(Meteor.subscribe('item', this.params._id));

  // 如果等待列表中所有的项目都完成，this.ready() 是 true
  if (this.ready()) {
    this.render();
  } else {
    this.render('Loading');
  }
});
```

上面的例子的替代方法是直接调用订阅的`wait`方法。在这个例子我们调用`this.subscribe` 而不是 `Meteor.subscribe`。

```javascript
Router.route('/post/:_id', function () {
  this.subscribe('item', this.params._id).wait();

  if (this.ready()) {
    this.render();
  } else {
    this.render('Loading');
  }
});
```

### The subscriptions Option
*subscriptions选项*
在路由中使用`subscriptions`选项我们可以自动得到这个功能的好处。

```javascript
Router.route('/post/:_id', {
  subscriptions: function() {
    // 返回一个订阅句柄或者一个定义句柄数组
    // 并且添加他们到等待列表
    return Meteor.subscribe('item', this.params._id);
  },

  action: function () {
    if (this.ready()) {
      this.render();
    } else {
      this.render('Loading');
    }
  }
});
```

`subscriptions`函数可以返回一个单独的订阅句柄（`Meteor.subscribe`的返回值）或者一个订阅句柄的数组。这些订阅将会并用来驱动`.ready()`的状态。 

你也可以从全局路由配置中继承订阅或者从控制器中继承(看下面).

### The waitOn Option
*waitOn选项*
另外的选择是使用`waitOn`替代`subscribe`。这个有同样的效果，但是会让你的路由`action`更小(如下)，完成之前，显示`loadingTemplate`替换。你可以指定这个路由的模板或者让路由自己决定：

```javascript
Router.route('/post/:_id', {
  // 这个模板将会被渲染，直到订阅完成
  loadingTemplate: 'loading',
  
  waitOn: function () {
    // 返回一个句柄，或者函数，或者数组
    return Meteor.subscribe('post', this.params._id);
  },

  action: function () {
    this.render('myTemplate');
  }
});
```

## Server Routing
*服务器端路由*

### Creating Routes
*创建路由*

到目前为止我们看到的功能主要用户浏览器。但是我们也可以创建服务器端路由，这里我们可以完全访问NodeJS的request和response对象。去创建服务器端路由，可以在路由上使用`where: 'server'` 选项。

```javascript
Router.route('/download/:file', function () {
  // NodeJS request 对象
  var request = this.request;

  // NodeJS  response 对象
  var response = this.response;

  this.response.end('下载文件内容\n');
}, {where: 'server'});
```

### Restful Routes
*Restful路由*
你也可以创建一个基于http动作的服务器端rest路由。如果为其他服务提交数据设置一个web钩子，这个就特别有用。

```javascript
Router.route('/webhooks/stripe', { where: 'server' })
  .get(function () {
    // GET /webhooks/stripe
  })
  .post(function () {
    // POST /webhooks/stripe
  })
  .put(function () {
    // PUT /webhooks/stripe
  })
```

### 404s and Client vs Server Routes
当你最初导航到Meteor应用的url的时候，服务器端路由将会检查是否有路由满足这个url，不管是服务器端还包括客户端路由。如果没有路由满足，服务器将会返回一个http 404状态code,表示给定的url没有任何资源找到。


## Plugins
*插件*
插件是路由中的一种重用功能的一种方式，要么已经构建自己的应用程序，要么使用别的包作者。这里有一个内置插件名字叫`dataNotFound`。

调用插件的方法是调用`Router`的方法`plugin`，传入插件名字和其他插件的参数。

```javascript
Router.plugin('dataNotFound', {notFoundTemplate: 'notFound'});
```

这个`out-of-box`插件将会自动的渲染模板`notFound` 如果这个路由的数据是falsey (i.e. `! this.data()`).

### Applying Plugins to Specific Routes
*将插件应用与特定的路由*
你可以使用`except` 或者 `only`选项给特定路由应用相应的插件功能。当你不想显示的应用插件用于客户端，这一点对于服务器端是非常有用的。

```javascript
Router.plugin('dataNotFound', {
  notFoundTemplate: 'NotFound', 
  except: ['server.route']
  // or only: ['routeOne', 'routeTwo']
});
```

在上面的例子中，`dataNotFound`将会应用到所有路由，除了名字叫的`server.route`路由。

### Creating Plugins
`创建插件`
创建插件只需要把函数放在`Iron.Router.plugins`对象上，如下:

```javascript
Iron.Router.plugins.loading = function (router, options) {
  // this loading plugin just creates an onBeforeAction hook
  router.onBeforeAction('loading', options);
};
```
这个插件函数会被调用，这个插件函数可以访问路由对象和任何用户传入的参数。

*鼓励大家创建新的插件!*

## Hooks
`钩子`

### Using Hooks
`使用钩子`
钩子就是一个函数。 钩子是处理路由的过程中的一个插头，代表性的使用是自定义渲染行为或者执行一些业务逻辑。

在这个例子，在路由中我们只渲染模板和区域。如果用户已经登录，我们会使用`onBeforeAction`添加一个钩子函数，告诉路由这个函数会在路由函数（或`action`函数）运行前运行

```javascript
Router.onBeforeAction(function () {
  // all properties available in the route function
  // are also available here such as this.params

  if (!Meteor.userId()) {
    // if the user is not logged in, render the Login template
    this.render('Login');
  } else {
    // otherwise don't hold up the rest of hooks or our route/action function
    // from running
    this.next();
  }
});
```

现在比方说我们有如果一个路由定义:

```javascript
Router.route('/admin', function () {
  this.render('AdminPage');
});
```

当我们访问`/admin`链接的时候，我们的 `onBeforeAction` 钩子函数会在路由函数运行前运行。如果这个用户没有登录，这个路由函数将不会被访问，并且`AdminPage`模板也不会渲染到页面。

Hook functions and all functions that get run when dispatching to a route are
run in a **reactive computation**: they will rerun if any reactive data sources
invalidate the computation. In the above example, if `Meteor.user()` changes the entire set of route functions will be run again.

### Applying Hooks to Specific Routes
`应用钩子到特定路由`
通过`except`或者`only`选项，我们可以应用一个钩子到特定路由。

```javascript
Router.onBeforeAction(myAdminHookFunction, {
  only: ['admin']
  // or except: ['routeOne', 'routeTwo']
});
```

在上面的例子, 这个`myAdminHookFunction`将只应用到`admin`路由。

### Using the Iron.Router.hooks Namespace
*使用`Iron.Router.hooks`命名空间*
Package authors can add hook functions to `Iron.Router.hooks` and users can
reference those hooks by string name.

```javascript
Iron.Router.hooks.customPackageHook = function () {
  console.log('hi');
  this.next();
};

Router.onBeforeAction('customPackageHook');
```
### Available Hook Methods
* **onRun**: Called when the route is first run. It is not called again if the
  route reruns because of a computation invalidation. This makes it a good 
  candidate for things like analytics where you want be sure the hook only runs once. Note that this hook *won't* run again if the route is reloaded via hot code push. You *must* call `this.next()` to continue calling the next function.

* **onRerun**: Called if the route reruns because its computation is
  invalidated. Similarly to `onBeforeAction`, if you want to continue calling the next function, you
  *must* call `this.next()`.

* **onBeforeAction**: Called before the route or "action" function is run. These
  hooks behave specially. If you want to continue calling the next function you
  *must* call `this.next()`. If you don't, downstream onBeforeAction hooks and
  your action function will *not* be called.

* **onAfterAction**: Called after your route/action function has run or had a
  chance to run. These hooks behave like normal hooks and you don't need to call
  `this.next()` to move from one to the next.

* **onStop**: Called when the route is stopped, typically right before a new
  route is run.


### Server Hooks and Connect

On the server, the API signature for a `onBeforeAction` hook is identical to that of a [connect](https://github.com/senchalabs/connect) middleware:

```javascript
Router.onBeforeAction(function(req, res, next) {
  // in here next() is equivalent to this.next();
}, {where: 'server'});
```

This means you can attach any connect middleware you like on the server side using `Router.onBeforeAction()`. For convience, IR makes express' [body-parser](https://github.com/expressjs/body-parser) available at `Iron.Router.bodyParser`.

The Router attaches the JSON body parser automatically.

## Route Controllers
An `Iron.RouteController` object is created when the Router handles a url
change. The `RouteController` gives us a place to store state as we run the
route, and persists until another route is run.

We've been calling a few methods inside of our route functions like
`this.render()` and `this.layout()`. The `this` object inside of these functions
is actually an instance of a `RouteController`. If you're building a simple
application you probably don't need to worry about `RouteController`. But if
your application gets larger, using `RouteControllers` directly offers two key
benefits:

* **Inheritance**: You can inherit from other RouteControllers to model your
  application's behavior.
* **Organization**: You can begin to separate your route logic into
  RouteController files instead of putting all of your complex business logic
  into one big route file.

### Creating Route Controllers
You can create a custom `RouteController` like this:

```javascript
PostController = RouteController.extend();
```

When you define a route, you can specify a controller to use, or the router will
try to find a controller automatically based on the name of the route.

```javascript
Router.route('/post/:_id', {
  name: 'post'
});
```

The route defined above will automatically use the `PostController` using the
name of the route. We can tell the route to use a different controller by
providing a controller option.

```javascript
Router.route('/post/:_id', {
  name: 'post.show',
  controller: 'CustomController'
});
```

We can use all of the same options from our routes on our `RouteControllers`.

```javascript
PostController = RouteController.extend({
  layoutTemplate: 'PostLayout',

  template: 'Post',

  waitOn: function () { return Meteor.subscribe('post', this.params._id); },

  data: function () { return Posts.findOne({_id: this.params._id}) },

  action: function () {
    this.render();
  }
});
```

We might have some options defined globally with `Router.configure`, some
options defined on the `Route` and some options defined on the
`RouteController`. Iron.Router looks up options in this order:

1. Route
2. RouteController
3. Router

### Inheriting from Route Controllers
RouteControllers can inherit from other RouteControllers. This enables some
interesting organization schemes for your application.

Let's say we have an `ApplicationController` which we want to use as the default
controller for all routes.

```javascript
ApplicationController = RouteController.extend({
  layoutTemplate: 'ApplicationLayout',

  onBeforeAction: function () {
    // do some login checks or other custom logic
    this.next();
  }
});

Router.configure({
  // this will be the default controller
  controller: 'ApplicationController'
});

// now we have a route for posts
Router.route('/posts/:_id', {
  name: 'post'
});

// inherit from `ApplicationController` and override any
// behavior you'd like.
PostController = ApplicationController.extend({
  layoutTemplate: 'PostLayout'
});
```



*NOTE: This is currently a bit tricky with Meteor since you can't precisely
control file load order. You need to make sure parent RouteControllers are
evaluated before child RouteControllers.*

### Accessing the Current Route Controller
There are two ways to access the current `RouteController`.

If you're on the client, you can use the `Router.current()` method. This will
reactively return the current instance of a `RouteController`. Keep in mind this
value could be `null` if no route has run yet.

You can also access the current `RouteController` from inside your template
helpers by using the `Iron.controller()` method.

```javascript
Router.route('/posts', function () {
  this.render('Posts');
});
```

This route will render the `Posts` template defined below.

```handlebars
<template name="Posts">
  Posts
</template>
```

Now let's say we want to access the current controller from a template helper
defined on the `Posts` template.

```javascript
Template.Posts.helpers({
  myHelper: function () {
    var controller = Iron.controller();

    // now we can get properties and call methods on the controller
  }
});
```

### Setting Reactive State Variables
You can set reactive state variables on controllers using the `set` method on
the controller's [ReactiveDict](https://atmospherejs.com/meteor/reactive-dict) `state`.

Let's say we want to store the post `_id` in a reactive variable:

```javascript
Router.route('/posts/:_id', {name: 'post'});

PostController = RouteController.extend({
  action: function () {
    // set the reactive state variable "postId" with a value
    // of the id from our url
    this.state.set('postId', this.params._id);
    this.render();
  }
});
```

### Getting Reactive State Variables
You can get a reactive variable value by calling `this.state.get("key")` on the
`RouteController`. Using the example above, let's grab the value of `postId`
from a template helper.

```javascript
Template.Post.helpers({
  postId: function () {
    var controller = Iron.controller();

    // reactively return the value of postId
    return controller.state.get('postId');
  }
});
```

## Custom Router Rendering
So far we've been letting the Router render itself to the page automatically.
But you can also control precisely where the Router renders itself by using a
global helper method.

```handlebars
<body>
  <h1>Some App Html</h1>
  <div class="container">
    {{! Render the router into this div instead of the body}}
    {{> Router}}
  </div>
</body>
```

## Legacy Browser Support
Legacy browsers do not support the HTML5 `pushState` and `history` features
required for normal client side browsing with the `Router`. To solve this
problem, the `Router` can fall back to using hash fragments in the url.
Actually, under the hood, `iron-router` uses a package called `iron-location`
which handles all of this. It works similarly to the `History.js` project but
works seamlessly.

This functionality is automatically enabled for **IE8** and **IE9**. If you want
to enable it manually to play around you can configure `Iron.Location` like
this:

```javascript
Iron.Location.configure({useHashPaths: true});
```

Even though the url will appear differently in the browser when using this mode,
the url, query, hash and parameters will look like their regular values inside
of `RouteController` functions. Here are a few examples of how urls will be
translated.

```bash
http://localhost:3000/items/5?q=s#hashFrag
```

The url above would be transformed to the url below in your browser.

```bash
http://localhost:3000/#/items/5?q=s&__hash__=hashFrag
```

But in your `RouteController` functions you can access the url, query and hash
values just like you have before.

```javascript
Router.route('/items/:_id', function () {
  var id = this.params._id; // "5"
  var query = this.params.query; // {q: "s"}
  var hash = this.params.hash; // "hashFrag"
});
```

**NOTE: Please let us know if you can help test support on other browsers!**

