---
title: 解决angluar在Node服务器上的路由问题
date: 2019-01-22 22:13:57
tags:
- angluar
- node.js
- angluar 404
categories:
- 教程
---

官方教程中没有涉及到node服务器对angluar的编译文件解决路由的问题，其实原理与apahce是一样的，当路由返回404的时候，应该让路由再直接找index.html这个源文件，具体代码如下

``` javascript
var express = require("express");
var app = express();
var path = require("path");

/* 这里是设置中间件，让编译文件的静态文件可以被获取 */
app.use(express.static("dist/w-app"));

/* 这里是不管url地址是什么，路由都统一在index.html上进行查询，然后获取到真实的Angluar的路由 */
app.get('*', function (req, res, next) {
    res.sendFile(path.join(__dirname,'dist/w-app/index.html'));
});

app.listen(3000,function (){
    console.log("server is running");
});

```
