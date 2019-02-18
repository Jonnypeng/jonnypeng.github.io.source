---
title: pm2启动node监听80端口
date: 2019-02-18 11:21:26
tags:
- pm2
- node
- 80端口
categories:
- 命令行
---

 Linux下非root用户是无法监听1024以下的端口，所有监听80端口时需要root权限，而使用pm2无法启动node，绑定80端口号，直接输入以下命令。即可解除限制

``` bash
sudo setcap cap_net_bind_service=+ep /usr/local/bin/node
```
