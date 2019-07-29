---
title: 前端模拟后端数据的神器json-Server
date: 2018-11-05 14:20:33
tags:
- json server
categories:
- 教程
---

### install Json Server 
``` bash
npm install -g json-server
```

### 在指定目录下创建db.json
``` json
{
  "posts": [
    { "id": 1, "title": "json-server", "author": "typicode" }
  ],
  "comments": [
    { "id": 1, "body": "some comment", "postId": 1 }
  ],
  "profile": { "name": "typicode" }
}
```

### 启动服务并监听文件
``` bash
json-server --watch db.json
```

[项目文档](https://github.com/typicode/json-server)

