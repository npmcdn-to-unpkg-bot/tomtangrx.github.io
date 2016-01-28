---
layout: post
title:  Windows  Node js 完全删除
category: 技术
tags: Windows, NodeJs, 完全删除
keywords: Windows, NodeJs, 完全删除
---

# Windows  Node js 完全删除

1. 先通过 *程序和功能* 删除掉Nodejs
2. 删除以下目录
```
C:\Program Files (x86)\Nodejs
C:\Program Files\Nodejs
C:\Users\{User}\AppData\Roaming\npm (or %appdata%\npm)
C:\Users\{User}\AppData\Roaming\npm-cache (or %appdata%\npm-cache)
```

3. 检查环境变量path中是否存在Nodejs 或者npm 
4. 重启
