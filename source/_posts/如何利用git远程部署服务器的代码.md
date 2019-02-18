---
title: 如何利用git远程部署服务器的代码
date: 2018-12-24 15:04:43
tags:
- git
- git部署服务器
categories:
- 教程
---


### 使用场景
ftp部署代码很麻烦，因为需要隔离出已经修改与为修改的部分，并进行上传，但是git很方便，如果在远程服务器已经绑定了ssh免密码登陆就更加方便，只需要推送，就可以将代码部署到服务器,而且不用去区分哪一些是修改过的，哪一些是没有修改过的。

### 远程服务目录结构

``` 
├── html
  └── myApp // 项目
   └── site // 这是工作区,手工创建空目录
       ├── index.php
       ├── test2.php
       ├── test3.php
       └── test.php
   └── gitLab // 这是版本区
        ├── branches
        ├── config
        ├── description
        ├── HEAD
        ├── hooks // post-receive钩子代码写在这里面
        ├── index
        ├── info
        ├── objects
        └── refs
```
<!-- more -->
在远程gitLab目录中的hooks，创建文件post-receive，写如下代码

``` bash
# 指定我的代码检出目录,就是工作区
DIR=/var/www/html/myApp/site
git --work-tree=${DIR} clean -fd
# 直接强制检出
git --work-tree=${DIR} checkout --force
```

这个文件请授权
``` bash
sudo chmod -R u+x post-receive
```

将工作区授权
``` bash
sudo chmod -R a=wxr site
```

然后返回本地计算机，添加远程库
``` bash
git remote add origin root@abc.com:/var/www/html/myApp/gitLab
```

最后就是各种推送，我着这里就不再赘述