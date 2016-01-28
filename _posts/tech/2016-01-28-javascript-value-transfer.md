---
layout: post
title:  javascript 中的类型转换
category: 技术
tags: javascript, 类型转换
keywords: javascript, 浮点数, float
---

# javascript 中的类型转换

## 隐式强制类型转换

### 1. 运算符会自动调用类型转换

普通运算符会自动把值转换为double

**+** 号 在字符串和加法都有用到，如果有字符串， 最后结果则是字符串。

对象会自动调用 toString() 转换为字符串 或者自动调用 valueOf 方法转换为数字：

```javascript
console.log("J" + {toString:function(){return "s";}}); // 'js'
 
console.log(2 * {valueOf:function(){return 3;}});      // 6
```

加号优先调用 _valueOf_

```javascript
var obj = {
  toString: function toString() {
    return "s";
  }, 
  valueOf: function valueOf() {
    return 3;
  } 
};
console.log('a' + obj); // a3
```

### 真值运算 truthiness

if、|| 、&& 等逻辑运算符需要布尔值作为操作参数， 但实际上可以接受任何值。 javascript按照简单的规则将值转换。 大多数的Javascript对象都是真值， 对于字符串和数字以外的其他对象，真值运算不会隐式调用任何强制类型转换方法。 javascript 中有7个假值：false, 0 , -0 , "" , NaN , null 和 undefined。其他所有都是真值。数字和字符串可能为假值， 所以使用真值运算判断是否定义不是绝对安全的。
判断一个值是否定义， 可以使用typeof 或者与undefined 进行比较 而不是真值运算

```javascript

if(typeof x === "undefined"){

}

if( x === undefined ){

}

```