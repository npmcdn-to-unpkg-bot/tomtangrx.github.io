---
layout: post
title: Sublime Text3 常用插件
category: 资源
tags: Sublime Text3
keywords: Sublime Text3, 插件, Package
---


## 安装Sublime Text 3插件的方法

1. 输入 ``Ctrl+``` 调出控制器
2. 粘贴以下命令行并回车：
`import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())`

3. 重启Sublime Text 3
4. 如果在Perferences->package settings中看到package control这一项，则安装成功

## 常见插件

###1. Git
*在工作中，版本控制软件最常用的软件之一，而最流行的 VCS 是 Git。你是否厌倦了保存文本文件，并切换回终端运行一些 Git 命令。如果你能从文本编辑器本身执行 Git 命令，岂不是很好?*

### 2. GitGutter
*Sublime Text 有了 Git 插件之后，GitGutter 更好的帮助开发者查看文件之前的改动和差异，提升开发效率。*

### 3. Emmet
*Emmet 项目的前身是前端开发人员熟知的 Zen Coding（快速编写 HTML/CSS 代码的方案）。在 Sublime Text 编辑器中搭配 Emmet 插件真的是让你编码快上加快。*

### 4. AllAutocomplete
*Sublime Text 默认的 Autocomplete 功能只考虑当前的文件，而 AllAutocomplete 插件会搜索所有打开的文件来寻找匹配的提示词。*

### 5. Terminal
*当你想要打开在当前文件所在的目录的终端，这个插件可以帮助你。不过，在默认情况下，它设置按 Ctrl / Cmd + Shift + T 键的快捷方式打开终端。不过这也是打开上次关闭的文件的快捷方式，你需要修改一个快捷键来兼容两个功能。*

### 6. DocBlockr
*如果你遵循的编码的风格很严格，这款插件能够使你的任务更容易。DocBlokr 帮助你创造你的代码注释，通过解析功能，参数，变量，并且自动添加基本项目。*

### 7. 皮肤 `Brogrammer`