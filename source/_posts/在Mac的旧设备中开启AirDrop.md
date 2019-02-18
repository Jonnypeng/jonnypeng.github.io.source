---
title: 在Mac的旧设备中开启AirDrop
date: 2018-10-16 17:04:13
tags:
- Airdrop
categories:
- 教程
---
运行 Terminal,并执行以下命令：
```
defaults write com.apple.NetworkBrowser BrowseAllInterfaces 1
```
如果你以后希望关闭AirDrop，则执行
```
defaults write com.apple.NetworkBrowser BrowseAllInterfaces 0
```
如果你在Mac上开启了AirDrop，则直接在Finder中可以找到AirDrop
