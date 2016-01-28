---
layout: post
title:  警惕javascript 中的浮点数
category: 技术
tags: javascript, 浮点数
keywords: javascript, 浮点数, float
---

# 警惕javascript 中的浮点数

## Javascript中的所有数字都是双精度浮点数

精度从 $-2^53$ 到 $2^53$

*简单的查看数字类型*

```javascrpt
typeof 17; // 'number'
typeof 98.6; // 'number'
typeof -2.5; // 'number'
```

```
0.1+0.2; // 0.30000000000000004
```

实数满足结合律 在 javascript的浮点运算就不满足了

** (x+y)+z = x+(y+z) **

```javascript
(0.1+0.2)+0.3; //  0.6000000000000001
0.1+(0.2+0.3); //  0.6

```

整数运算没有上面的问题，但是需要担心整数的大小必须在 $ -2^53 2^52 $ 之间。
