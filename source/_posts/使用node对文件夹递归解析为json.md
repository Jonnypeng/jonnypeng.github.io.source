---
title: 使用node对文件夹递归解析为json
date: 2019-01-30 14:07:34
tags:
- node
- fs
- path
- json

categories:
- 代码片段
---

使用场景：工作中经常制作H5广告，需要做预加载机制，而文件数据需要通过外部进行加载，使用node的fs模块，可以快速地生成json文件，下面是代码解读;

<!-- more -->

``` javascript
var path = require("path");
var fs = require("fs");

var customUrl = {
    relative: "../../src/assets/airWar/images",     //以本文件作为一个入口，定义读取目录的相对地址
    stitching: "assets/airWar/images",              //定义工程项目所对应的读取目录的地址，用来拼接字符串
    output:path.join(__dirname,"../output")         //定义一个文件输出目录，用来输出json文件
}

var url = path.join(__dirname, customUrl.relative);    //将读取目录从相对地址转换为绝对地址
var results = {
    data:[]
}

var fileLength = 0;       // 设置一个初始化的文件数量长度

readDir(url);           //初始化载入数据，下面的代码被封装为函数，目的是为了递归循环，再传入新的参数
function readDir(_url) {
    fs.readdir(_url, function (err, files) {
        fileLength+=files.length;       // 只要载入函数，文件数量的长度就会自增读取的文件长度
        if (err) {
            console.log(err);
            return false;
        }
        files.forEach(function (filename) {             // 循环文件列表
            let isHide = (/^\./).test(filename);        // 判断是否是隐藏文件
            if (!isHide) {
                let fsStats = fs.statSync(`${_url}/${filename}`);       // 用来判断是文件夹或者文件
                if (fsStats.isDirectory()) {     // 如果是文件夹
                    fileLength-=1;         // 如果发现是目录，就会自减1     
                    customUrl.stitching += `/${filename}`;      //重新定义需要拼接的字符串
                    readDir(`${_url}/${filename}`);         // 重新返回函数头部，再重新解析数据
                } else if (fsStats.isFile()) {
                    console.log("this is file");
                    results.data.push(`${customUrl.stitching}/${filename}`);    // 直接推送到需要写入的json文件内容中
                    console.log(results.data.length,fileLength);
                    if(results.data.length===fileLength){    // 如果输入数据长度与文件数据长度相等，则执行写入
                       writeFile();
                    }
                }
            }else{
                fileLength-=1;   // 如果发现是隐藏文件，就会自减1   
            }
        });
    });
}

function writeFile(){
    fs.writeFile(`${customUrl.output}/manifest.json`,JSON.stringify(results),function (err){
        if(err){
            return console.log(err);
        }
        console.log(`文件创建成功`);
    });
}


```
