---
title: node.js的循环下载
date: 2019-01-31 11:00:25
tags:
- node
- shell
- 循环下载
categories:
- 代码片段
---

场景：某些网站图片太多，使用node对其命名规则进行for循环计算，然后下载下载

<!-- more -->

### 配置文件
``` javascript
class Imgs {
    constructor(url,start,length){
        this.url = url
        this.start = start;
        this.length = length;
    }
}

let config = new Imgs(
    'http://www.abc.com/1234',
    1848,
    165
);

module.exports = config;
```
### 执行文件
``` javascript
const spawn = require('child_process').spawn;   //载入子进程模块

var config = require("./assets");      // 载入自定义的资源

var wget;

var num = 0;
while(num < config.length){
    wget = spawn('wget',["-c","-p","-U","Mozial",`${config.url}${config.start + num}.jpg`]);
    num+=1;
}

wget.stdout.on('data',function (data){
    console.log(`out: ${data}\n`);
})

wget.stderr.on("data",function (data){
    console.log(`${data}`);
});

wget.on('exit',function (code,signal){
    console.log(`code is ${code},is ${signal}`);
});
```
