---
layout: post
title: "Android手机修改hosts"
description: ""
category: [Android]
tags: [Android]
---
{% include JB/setup %}
#Android手机修改host

## 1.下载adb工具

## 2.配置adb环境变量

###Mac系统下
		
		修改~/.bash_profile文件
		export ANDROID_HOME=adb存放路径
		export PATH=${PATH}:${ANT_HOME}/bin:${ANDROID_HOME}/tools:${ANDROID_HOME}/platform-tools
		修改完成后执行source ~/.bash_profile

## 3.获取root权限
		
		这个需要自己去百度/谷歌

## 4.修改hosts
		
在Android下，/etc是link到/system/etc的，需要先修改/system/etc/hosts的权限，该文件是只读的。

		adb root
		adb remount
		adb pull /system/etc/hosts
		vim hosts
		adb push hosts /system/etc/hosts
		修改完成
		adb shell
		cat /system/etc/hosts
		如果显示的hosts和你想要的hosts一致说明修改成功
