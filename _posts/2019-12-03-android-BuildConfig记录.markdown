---
layout:     post
title:      "Gradle 打包 BuildConfig 记录"
subtitle:   "Android & Gradle"
date:       2019-12-02 17:41:11
author:     "Luhao"
header-img: "img/post-bg-2015.jpg"
tags:
    - Android
---

> “Here, We are!”


## Android 打包aar 不打入BuildConfig

```
afterEvaluate {
    generateReleaseBuildConfig.enabled = false
    generateDebugBuildConfig.enabled = false
}
```



