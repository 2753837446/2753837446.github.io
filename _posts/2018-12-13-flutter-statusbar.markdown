---
layout:     post
title:      "flutter设置沉浸式状态栏"
subtitle:   "Android&Flutter"
date:       2018年12月13日13:01:32
author:     "Luhao"
header-img: "img/post-bg-2015.jpg"
tags:
    - 生活
---

> “Yeah It's on. ”


## Flutter下配置Android沉浸式状态栏

### 记录一下在Flutter开发中遇到的小问题和解决方法

## 正文

### 解决Android沉浸式的方法有两种
1. Native(Android)配置
2. Flutter启动文件中配置

### 咱们今天主要讲讲第二种

### 1. 先导包
```
import 'dart:io';
import 'package:flutter/services.dart';
```
### 2. 启动类修改
```
void main() {
  runApp(MyApp());
  if (Platform.isAndroid) {
    SystemUiOverlayStyle systemUiOverlayStyle =
        SystemUiOverlayStyle(statusBarColor: Colors.transparent);
    SystemChrome.setSystemUIOverlayStyle(systemUiOverlayStyle);
  }
}
```

> 至于说为什么要把修改部分放在启动后, 是因为如果放在启动前该配置会被MaterialApp的配置覆盖掉


