---
layout: post
title:  react native 遇坑1
category: 技术
tags: react native
keywords: react native
---

# react native 遇坑1

## 创建项目失败

**错误内容**

```cmd
npm WARN optional dep failed, continuing fsevents@1.0.7
npm ERR! Windows_NT 10.0.10240
npm ERR! argv "C:\\nodejs\\node.exe" "C:\\nodejs\\node_modules\\npm\\bin\\npm-cl
i.js" "install" "--save" "react-native"
npm ERR! node v4.2.6
npm ERR! npm  v2.14.12
npm ERR! path C:\Users\Administrator\AppData\Roaming\npm-cache\left-pad\0.0.3\pa
ckage\package.json.bddd775306fb7291d680806f5fff792f
npm ERR! code EPERM
npm ERR! errno -4048
npm ERR! syscall rename

npm ERR! Error: EPERM: operation not permitted, rename 'C:\Users\Administrator\A
ppData\Roaming\npm-cache\left-pad\0.0.3\package\package.json.bddd775306fb7291d68
0806f5fff792f' -> 'C:\Users\Administrator\AppData\Roaming\npm-cache\left-pad\0.0
.3\package\package.json'
npm ERR!     at Error (native)
npm ERR!  { [Error: EPERM: operation not permitted, rename 'C:\Users\Administrat
or\AppData\Roaming\npm-cache\left-pad\0.0.3\package\package.json.bddd775306fb729
1d680806f5fff792f' -> 'C:\Users\Administrator\AppData\Roaming\npm-cache\left-pad
\0.0.3\package\package.json']
npm ERR!   errno: -4048,
npm ERR!   code: 'EPERM',
npm ERR!   syscall: 'rename',
npm ERR!   path: 'C:\\Users\\Administrator\\AppData\\Roaming\\npm-cache\\left-pa
d\\0.0.3\\package\\package.json.bddd775306fb7291d680806f5fff792f',
npm ERR!   dest: 'C:\\Users\\Administrator\\AppData\\Roaming\\npm-cache\\left-pa
d\\0.0.3\\package\\package.json',
npm ERR!   parent: 'line-numbers' }
npm ERR!
npm ERR! Please try running this command again as root/Administrator.

npm ERR! Please include the following file with any support request:
npm ERR!     D:\github\nodejs\react-native\AwesomeProject\npm-debug.log

`npm install --save react-native` failed

```

## 解决办法

1. 使用管理员权限cmd
2. 输入命令
```cmd
npm cache clean
```

查看 `%APPDATA%\npm-cache` 是否还存在。
