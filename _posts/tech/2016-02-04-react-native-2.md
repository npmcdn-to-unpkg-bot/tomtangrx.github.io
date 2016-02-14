---
layout: post
title:  react native 遇坑2
category: 技术
tags: react native
keywords: react native
---

# react native 遇坑2

## windows 环境下创建命令


*++ 这篇文章是用于记录自己按照官方文档[官方文档](https://facebook.github.io/react-native/docs/signed-apk-android.html)操作的时候遇到的坑 ++*

### 问题1
	*Setting up gradle variables* 这节的路径：` ~/.gradle/gradle.properties ` 路径应该是程序目录下 `android\gradle.properties`
    
### 问题2 
    *Generating the release APK*  下命令：
    ```cmd
    cd android && ./gradlew assembleRelease 
	```
    windows 下
    ```cmd
    cd android && gradlew assembleRelease 
	```
### 问题3
    *Generating the release APK* -> *If you don't have a react.gradle file*
    ```cmd
     mkdir -p android/app/src/main/assets
     react-native bundle --platform android --dev false --entry-file index.android.js \
      --bundle-output android/app/src/main/assets/index.android.bundle \
      --assets-dest android/app/src/main/res/
     cd android && ./gradlew assembleRelease
	```
    windows 下：
    ```cmd
    mkdir  android\app\src\main\assets
    react-native bundle --platform android --dev false --entry-file index.android.js --bundle-output android/app/src/main/assets/index.android.bundle   --assets-dest android/app/src/main/res/
	```

### 问题 红屏

错误内容 大体如下:
```cmd
react native can't find variable   __fbbatchedbridge
```
解决办法是 参照 *Generating the release APK* -> *If you don't have a react.gradle file* [链接](https://facebook.github.io/react-native/docs/signed-apk-android.html#adding-signing-config-to-your-app-s-gradle-config)
    
    
    
    

