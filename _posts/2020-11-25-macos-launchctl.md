---
layout: post
author: chenDoInG
title: "macOS launchctl"
description: ""
category: [Mac,MacOS,tools]
tags: [Mac,MacOS]
---

# Mac定时任务命令：launchctl

macOS通过launchctl命令加载plist配置文件来管理定时任务

> launchctl是一个统一的服务管理框架，可以启动、停止和管理守护进程、应用程序、进程和脚本等。
> launchctl是通过配置文件来指定执行周期和任务的。

## plist存放目录

- `/Library/LaunchDaemons` -->只要系统启动了，哪怕用户不登陆系统也会被执行

- `/Library/LaunchAgents` -->当用户登陆系统后才会被执行

- ~/Library/LaunchAgents 由用户自己定义的任务项

- /System/Library/LaunchAgents 由Mac OS X为用户定义的任务项

- /System/Library/LaunchDaemons 由Mac OS X定义的守护进程任务项

  

## plist标签说明

```
1、Label：对应的需要保证全局唯一性；
2、Program：要运行的程序；
3、ProgramArguments：命令语句
4、StartCalendarInterval：运行的时间，单个时间点使用dict，多个时间点使用 array <dict>
5、StartInterval：时间间隔，与StartCalendarInterval使用其一，单位为秒
6、StandardInPath、StandardOutPath、StandardErrorPath：标准的输入输出错误文件，这里建议不要使用 .log 作为后缀，会打不开里面的信息。
7、定时启动任务时，如果涉及到网络，但是电脑处于睡眠状态，是执行不了的，这个时候，可以定时的启动屏幕就好了
```

## launchctl命令

```
# 加载任务, -w选项会将plist文件中无效的key覆盖掉，建议加上
$ launchctl load -w com.xx.xx.plist

# 删除任务
$ launchctl unload -w com.xx.xx.plist

# 查看任务列表, 使用 grep '任务部分名字' 过滤
$ launchctl list | grep 'com.xx.xx'

# 开始任务
$ launchctl start  com.xx.xx.plist

# 结束任务
$ launchctl stop   com.xx.xx.plist
```

修改任务之后必须先unload，然后在load，才能生效。load之后，用start可以立即执行来测试任务是否正确

# 实战

## 一、编写一个启动脚本run.sh

```
cd ~/Library/LaunchAgents
vi run.sh
mkdir log
```

```
#!/bin/sh

# 进入launch.py程序所在目录
cd /Users/chenDoInG/Library/LaunchAgents &&
# 记录一下开始时间
echo `date` >> log/run.log &&
# 执行python脚本（注意前面要指定python运行环境/usr/bin/python，根据自己的情况改变）
/usr/local/bin/python3 test.py
# 运行完成
echo 'finish' >> log/run.log
```

！！！脚本很多会涉及相对路径问题，所以使用启动脚本，先进入程序所在目录可以解决大部分错误

## 二、编写python脚本test.py

```
#!/usr/local/bin/python3
# -*- coding:utf-8 -*-

import datetime

def add(path, content):
    with open(path,'a') as f:
        f.write(content + '\n')

path = '/Users/chenDoInG/Library/LaunchAgents/log/run.log'

now_time = "{}".format(datetime.datetime.now())
add(path, now_time)
```

跟run.sh一样 python环境需要自己的情况改变

## 三、测试下脚本是否可以运行

```
./run.sh
cat log/run.log
```

### 如果提示permission denied

执行

```
chmod 754 run.sh
```

### 如果授权完还提示operation not permitted

> 重启电脑，重启过程中按住command+R,进入保护模式
>
> 打开terminal终端，输入
>
> ```
> csrutil disable
> ```
>
> 退出终端，重启

## 四、编写plist

```
vi com.test.plist
```

填写

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <!-- 名称，要全局唯一 -->
    <key>Label</key>
    <string>com.test.unique</string>

    <!-- 脚本任务 -->
    <key>Program</key>
    <string>/Users/chenDoInG/Library/LaunchAgents/run.sh</string>

    <!-- 指定要运行的时间 -->
    <key>StartCalendarInterval</key>
    <array>
        <dict>
                <key>Minute</key>
                <integer>40</integer>
                <key>Hour</key>
                <integer>14</integer>
        </dict>
        <dict>
                <key>Minute</key>
                <integer>30</integer>
                <key>Hour</key>
                <integer>08</integer>
        </dict>
    </array>

    <!--开机启动-->

    <!-- 错误输出 -->
    <key>StandardErrorPath</key>
    <string>/Users/chenDoInG/Library/LaunchAgents/log/run-error</string>
</dict>
</plist>
```

这里用到是两组指定时间运行14.40和8.30，如果要用时间间隔，讲<array>整体替换成

```xml
<!-- 运行间隔，与StartCalenderInterval使用其一，单位为秒 -->
    <key>StartInterval</key>
    <integer>30</integer>
```

## 五、启动launctl

```
launchctl load -w com.test.plist
tail -f log/run*
```



