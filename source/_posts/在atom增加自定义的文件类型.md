---
title: 在atom增加自定义的文件类型
date: 2018-10-23 10:29:34
tags:
---
找到atom的config.cson，写入

```
customFileTypes:
  "source.css": [
    "wxss"
  ]
  "source.js": [
    "wxs"
    "ts"
  ]
  "text.html.basic": [
    "wxml"
  ]
#以上代码显示，我添加了4个了类型，分别是小程序的3个类型，还有一个TypeScript类型，分别延用传统html／css／js格式
```
然后保存，即可在着4中文件类型中实现高亮
