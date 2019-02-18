---
title: MAC写入USB启动盘
date: 2018-10-22 14:46:05
tags:
- MAC
- USB
categories:
- 教程
---

首先，先抹掉USB设备的内容，最好格式化为MS－DOS格式，然后卸载USB设备；

其次，假设ISO文件路径为/Path/A.iso ，而我需要输出的USB路径为/Path/USB

最后，打开终端，运行以下命令

```
sudo dd if=/Path/A.iso of=/Path/USB bs=10m

#参数if- 输入文件名，就是iso的文件名
#参数of- 输出文件名
#参数bs- 同时设置读写块的大小，单位是bytes
```
等待结束，就可以安装啦
