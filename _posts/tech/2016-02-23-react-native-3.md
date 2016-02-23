---

layout: post

title:  react native 遇坑3

category: 技术

tags: react native

keywords: react native

---



# react native 遇坑3



## Android 端口问题

```language
adb reverse tcp:8081 tcp:8081 
adb reverse tcp:3000 tcp:3000 
```

## 红频

### websocketmodule.connect got 4 arguments expected 2
删除 node_modules 可能会好 （mac）
windows 10 上没有解决， 有网友说是npm 版本升级问题

### 编译问题1
删除 *node_modules\react-deep-force-update\.babelrc* 文件

### 红频问题2 - undefined is not an object (evaluating 'screenPhysicalPixels.width')
在mac 下 0.19 版本没有遇到， 0.20 版本有这个问题
在windows 10 下 0.19 和 0.20 都有这个问题

解决办法：
修改文件： *\node_modules\react-native\Libraries\Utilities\Dimensions.js*
35行代码

```javascript
if (Platform.OS === 'android') {
    // Screen and window dimensions are different on android
    var screenPhysicalPixels = dimensions.screenPhysicalPixels;
    dimensions.screen = {
      width: screenPhysicalPixels.width / screenPhysicalPixels.scale,
      height: screenPhysicalPixels.height / screenPhysicalPixels.scale,
      scale: screenPhysicalPixels.scale,
      fontScale: screenPhysicalPixels.fontScale,
    };

    // delete so no callers rely on this existing
    delete dimensions.screenPhysicalPixels;
  } else {
	dimensions.screen = dimensions.window;
  }
```

改为

```javascript
/*if (Platform.OS === 'android') {
    // Screen and window dimensions are different on android
    var screenPhysicalPixels = dimensions.screenPhysicalPixels;
    dimensions.screen = {
      width: screenPhysicalPixels.width / screenPhysicalPixels.scale,
      height: screenPhysicalPixels.height / screenPhysicalPixels.scale,
      scale: screenPhysicalPixels.scale,
      fontScale: screenPhysicalPixels.fontScale,
    };

    // delete so no callers rely on this existing
    delete dimensions.screenPhysicalPixels;
  } else {
    */
	dimensions.screen = dimensions.window;
  //}
```