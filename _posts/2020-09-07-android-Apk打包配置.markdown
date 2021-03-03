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



## 1. 配置多渠道数据
	    // 1.配置渠道
	    flavorDimensions 'versionCode'
	    productFlavors{
	        taptap {
	            // 动态替换applicationId
	            // AndroidManifest.xml文件中用到applicationId的地方替换为${applicationId}
	            // 不要把用到包名的地方也替换了，如per.goweii.one.MainActivity
	            applicationId "per.goweii.one"
	
	            // 动态生成常量
	            // 引用方法为BuildConfig.HTTP_DOMAIN
	            buildConfigField "String", "HTTP_DOMAIN", '"http://api.app.one/"'
	            buildConfigField "String", "WECHAT_APP_ID", '"WECHAT_APP_ID"'
	            buildConfigField "String", "WECHAT_APP_SECRET", '"WECHAT_APP_SECRET"'
	
	            // 动态生成资源文件，需要确保资源文件中没有同名资源
	            // 引用方式为R.string.app_name/@string/app_name
	            resValue "string", "app_name", "ONE"
	            resValue "bool", "is_show_wechat_pay", 'true'
	
	            // 动态生成AndroidManifest.xml文件引用
	            // 引用方法为${UMENG_CHANNEL_VALUE}
	            manifestPlaceholders = [
	                    UMENG_CHANNEL_VALUE: "one",
	                    UMENG_APP_KEY      : "UMENG_APP_KEY",
	            ]
	        }
	//        hy {
	//        }
	//        a4399 {
	//        }
	    }

## 2. Android Gradle多渠道打包 改名 并且复制到指定目录下


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


