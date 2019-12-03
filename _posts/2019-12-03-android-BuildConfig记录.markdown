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

## Android 打包aar 修改release地址并且修改aar名称  兼容多渠道打包
```
    libraryVariants.all { variant ->
        if (variant.buildType.name == "release") {
            variant.getPackageLibrary().destinationDir = new File(project.rootDir.absolutePath + "/release")
        }

        if (variant.buildType.name == 'release') {
            variant.outputs.all { output ->
                def outputFile = output.outputFile
                if (outputFile != null && outputFile.name.endsWith('release.aar')) {
                    outputFileName = "${project.name}-${variant.flavorName}-${defaultConfig.versionName}.aar"
                }
            }
        }
    }

```

## Android 打包aar 不打入BuildConfig  兼容多渠道打包

```
    tasks.whenTaskAdded { task ->
        if (task.name.contains('ReleaseBuildConfig')) {
            task.enabled = false
        }
        if (task.name.contains('DebugBuildConfig')) {
            task.enabled = false
        }
    }
```



