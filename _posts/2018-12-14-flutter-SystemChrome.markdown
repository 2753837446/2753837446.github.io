---
layout:     post
title:      "Flutter SystemChrome详解"
subtitle:   "Flutter & 沉浸式"
date:       2018-12-14 16:24:32
author:     "Luhao"
header-img: "img/post-bg-2015.jpg"
tags:
    - Flutter
---

> “Here, We are!”


## Flutter SystemChrome 常用使用场景

* [setPreferredOrientations    指定屏幕展示方向](#Leo_Oriention)
* [setEnabledSystemUIOverlays  状态栏基底部虚拟按键展示与取消展示](#Leo_Enable)
* [setSystemUIOverlayStyle     设置展示主题](#Leo_Theme)
* restoreSystemUIOverlays     恢复主题

## 正文

### <span id="Leo_Oriention" />1. 指定屏幕展示方向
> setPreferredOrientations(List<DeviceOrientation> orientations)
> 
> 该方法需要我们传入一个  DeviceOrientation 的数组
> 
> 默认情况下Flutter项目创建后是可以四个方向旋转的, 对于有特殊需求的开发者来说该方法就是用来配置想要的展示方向的

```
/// Used by [SystemChrome.setPreferredOrientations].
enum DeviceOrientation {
  /// 默认展示: 竖向展示, Home键在正下方
  portraitUp,

  /// 相比默认展示手机向右旋转90度为展示方向 Home键在正右边
  landscapeLeft,

  /// 相比默认展示手机旋转180度为展示方向  Home键在正下方
  portraitDown,

  /// 相比默认展示手机向左旋转90度为正方向  Home键在正左边
  landscapeRight,
}
```

#### 这时候就要想了既然是设置方向, 那如果我想支持根据传感器旋转方向怎么办呢
**Talk is cheap, Show me the code**

```
///支持横屏状态下根据传感器旋转屏幕  list的第一个参数为默认展示方向
SystemChrome.setPreferredOrientations(
        [DeviceOrientation.landscapeRight, DeviceOrientation.landscapeLeft]);
        
        
同理....... 这里我就不细说了
..........
如果想恢复四个方向都支持  那就把四个参数都塞上吧
```

### <span id="Leo_Enable" />2.状态栏基底部虚拟按键展示与取消展示

> setEnabledSystemUIOverlays(List<SystemUiOverlay> overlays)
> 
> 该方法主要是针对状态栏和android虚拟按键操作
> 
> 如果是需要隐藏, 就将该方法的参数配置为 [],
> 相反, 如果需要展示的话就将下方提供的对应部分添加到请求参数中

```
/// Specifies a system overlay at a particular location.
///
/// Used by [SystemChrome.setEnabledSystemUIOverlays].
enum SystemUiOverlay {
  /// 状态栏
  top,

  /// 底部虚拟按键
  bottom,
}
```

### <span id="Leo_Theme" />3.设置展示主题 *(沉浸式)*
> setSystemUIOverlayStyle
> 
> 该方法主要用于在app开发过程中涉及到的状态栏颜色, 底部虚拟按键颜色, iOS设备的主题等的配置


```
const SystemUiOverlayStyle({
	
	 /// 底部状态栏和app内主题颜色 (Android O 8.0 及以上)
    this.systemNavigationBarColor,
    /// 底部虚拟按键和内容中间展示的Divider颜色  (Android P 9.0 及以上)
    this.systemNavigationBarDividerColor,
    /// 底部虚拟按键展示的主题  (light/dark)  (Android O 8.0 及以上)
    this.systemNavigationBarIconBrightness,
    /// 状态栏颜色  (Android M 6.0 及以上)
    this.statusBarColor,
    /// 只针对iOS 顶部状态栏颜色配置
    this.statusBarBrightness,
    /// 状态栏主题   (dark/light)  (Android M 6.0 及以上)
    this.statusBarIconBrightness,
  });
```


