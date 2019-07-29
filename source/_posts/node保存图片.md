---
title: node保存图片
date: 2019-05-29 11:00:38
tags:
- NODE
- binaryData
categories:
- 代码片段
---

环境是前端发出一张BASE64编码的图片，后端将base64建立缓冲区，并转为字符串，通过fs进行写入

``` javascript
let headImgSave = function (image){
    let binaryData = Buffer.from(image,"base64").toString("binary");
    let openid = Math.random().toString(26).substr(2,8);
    let imgPath = path.join(__dirname, `upload`);
    let headImg = `${imgPath}/${openid}.jpg`;
        fs.writeFile(headImg,binaryData,'binary',function (err){
            if(err) throw err;
        })
}
```