---
title: 正则表达式获取img的src
date: 2018-10-16 16:43:57
tags:
- 正则表达式
categories:
- 代码片段
---
首先从后端获取到一堆没有过滤的html标签，通过正则表达式获取每个img标签的src地址
### JS
```
var str = gameData.content;
  var imgReg = /<img />]*src=['"]([^'"]+)[^&gt;]*&gt;/gi;
  var srcReg = /src=['"]?([^'"]*)['"]?/i;
  var arr = str.match(imgReg);
  var imgsrc = [];
  for (var i = 0; i &lt; arr.length; i++) {
    var src = arr[i].match(srcReg);
    if(src[1]){
      imgsrc.push(src[1]);
    }
  };
  console.log(imgsrc);
```
