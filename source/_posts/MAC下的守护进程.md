---
title: MAC下的守护进程
date: 2019-09-10 15:50:21
tags: linux
categories: 原创
---
 进程守护是定时监测软件是否在运行，如果没运行就启动这个程序，运行的话就不理会。
 <!--more-->
 
 ### shell脚本文件 -- BetterTouchTool.sh
 
 ```shell script
#!/bin/bash
ps_out=`ps -ef | grep -w BetterTouchTool | grep -v 'grep' | grep -v $0`
result=$(echo $ps_out | grep BetterTouchTool)
if [[ "$result" != "" ]];then
   cat /dev/null > /Users/lql/Documents/Mine/sh/output/com.BetterTouchTool.plist.err
   cat /dev/null > /Users/lql/Documents/Mine/sh/output/com.BetterTouchTool.plist.stdout
else
    /Applications/BetterTouchTool.app/Contents/MacOS/BetterTouchTool
fi
```

### 定时脚本 -- com.BetterTouchTool.plist

```shell script
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>Label</key>
	<string>com.BetterTouchTool.plist</string>
	<key>ProgramArguments</key>
	<array>
		<string>/Users/lql/Documents/Mine/sh/BetterTouchTool.sh</string>
	</array>
	<key>StandardErrorPath</key>
	<string>/Users/lql/Documents/Mine/sh/output/com.BetterTouchTool.plist.err</string>
	<key>StandardOutPath</key>
	<string>/Users/lql/Documents/Mine/sh/output/com.BetterTouchTool.plist.stdout</string>
	<key>StartInterval</key>
	<integer>10</integer>
</dict>
</plist>
```

### 说明
1.  脚本文件放在`/Users/lql/Library/LaunchAgents`目录下；
2.  每次更改脚本文件时都要先停止，重新加载，再启动，具体命令如下：
```shell script
launchctl stop ~/Library/LaunchAgents/com.test.plist
launchctl unload -w ~/Library/LaunchAgents/com.test.plist
launchctl load -w ~/Library/LaunchAgents/com.test.plist
launchctl start ~/Library/LaunchAgents/com.test.plist

# 检测配置文件是否有错误，无报错的话，会输出OK
plutil -lint ~/Library/LaunchAgents/com.test.plist
```
3. 可能需要在 设置 - 安全与隐私 - 辅助功能里面将脚本文件添加进去。