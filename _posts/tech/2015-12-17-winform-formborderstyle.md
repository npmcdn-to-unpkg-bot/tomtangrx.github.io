---
layout: post
title: Winform 文件大小固定设置
category: 技术
tags: Winform
keywords: 文件大小固定设置
---

#  Winform 文件大小固定设置

使用`Form`的属性`FormBorderStyle` 设置为`FixedSingle`

```csharp
this.FormBorderStyle = System.Windows.Forms.FormBorderStyle.FixedSingle;
```


## FormBorderStyle属性介绍

设置窗体边框可以通过设置窗体的FormBorderStyle属性设置。属性值可以通过枚举类型`FormBorderStyle`获取，它的取值和意义如下表所示。
默认值：`Sizable`。

可供设置的`FormBorderStyle` 如下：

1. None : 无边框
2. FixedSingle ： 固定的单行边框
3. Fixed3D： 固定的三维样式边框
4. FixedDialog：固定的对话框样式的粗边框
5. Sizable: 可调整大小的边框
6. FixedToolWindow : 不可调整大小的工具窗口边框
7. SizableToolWindow : 可调整大小的工具窗口边框