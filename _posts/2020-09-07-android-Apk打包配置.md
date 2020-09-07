---
layout:     post
title:      "Android Gradle 打包后Apk处理"
subtitle:   "Android & Gradle"
date:       2020-09-07 13:43:47
author:     "Luhao"
header-img: "img/post-bg-2015.jpg"
tags:
    - Android
---

> Let's go!

## Android Gradle多渠道打包 改名 并且复制到指定目录下


```
    applicationVariants.all { variant ->
        if (variant.buildType.name == "release") {
            variant.outputs.all { output ->
                def outputFile = output.outputFile
                def fileName
                if (outputFile != null && outputFile.name.endsWith('.apk')) {
                    fileName = "${project.name}_${variant.productFlavors[0].name}_v${android.defaultConfig.versionName}.apk"
                    outputFileName = fileName
                }
            }
            variant.assemble.doLast {
                variant.outputs.all { output ->
                    def outputPath = [project.rootDir.absolutePath, "apk", android.defaultConfig.versionCode].join(File.separator)
                    copy {
                        from output.outputFile
                        into outputPath
                    }
                    println("打包成功: ${output.outputFile.name}")
                }
            }
        }
    }

```

