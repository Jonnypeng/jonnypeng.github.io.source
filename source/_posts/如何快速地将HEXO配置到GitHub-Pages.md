---
title: 如何快速地将HEXO配置到GitHub Pages
date: 2018-10-15 16:25:40
tags:
- HEXO
- Github
- Hexo Github Page
categories:
- 教程
---
* https://github.com/hexojs/hexo
* https://hexo.io/docs/
### 教程导语
*当您在阅读这个教程的时候，是假设您已经具备安装及其使用node.js,npm,git,github的基础之上的，如果您对这些还不了解，请对其进行了解并能够简单使用，才能正确理解本教程的内容，本教程的主体部分来源[这位大神](https://gist.github.com/btfak/18938572f5df000ebe06fbd1872e4e39),本人也只是第一次使用HEXO，完全属于菜鸟级别，也在不断的学习之中，这篇文章就作为博客的第二篇文章吧，只是作部分修改与添加，感谢原作者简易且有用的教程*


### 1. Install Hexo *安装HEXO*
```
$ sudo npm install -g hexo-cli

$ hexo -v
hexo-cli: 0.1.9
os: Darwin 14.3.0 darwin x64
http_parser: 2.3
node: 0.12.7
v8: 3.28.71.19
uv: 1.6.1
zlib: 1.2.8
modules: 14
openssl: 1.0.1p
```

<!-- more -->

### 2. Create a project for your GitHub Pages *运用hexo创建本地项目*
首先登陆您的github帐号，假设您的用户名为yt，那么新创建一个项目，这个项目的名称则必须为yt8yt.github.io，在这github pages的机制中，是唯一能够识别且证明是你个人博客的项目名称，切记不要使用其他项目名称
```
$ hexo init yt8yt.github.io
INFO  Copying data to ~/***/yt8yt.github.io
INFO  You are almost done! Don't forget to run 'npm install' before you start blogging with Hexo!

$ cd yt8yt.github.io

$ npm install
```

### 3. Run a test server for your page on Mac *运行本地服务器*
如果你需要端口号为5000上运行服务器，需要如下
```
$ hexo server -p 5000
```
以下是默认端口号为4000的服务器，按住Ctrl+C能正确终止服务
```
$ hexo server
INFO  Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.
```

### 4. Set information for your new blog
https://hexo.io/docs/configuration.html
以下配置非常重要，请您认真阅读，特别是url的配置地址，必须是你的github pages的地址
```
$ vi _config.yml

~~~~~~~~~~~~~~~~~~ _config.yml ~~~~~~~~~~~~~~~~~~
# Site
title: yt8yt's note
subtitle:
description: yt8yt's personal blog
author: yt8yt
language:
timezone: Japan

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://yt8yt.github.io/
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:
```

### 5. Set information to use Git *在你的本地项目中配置git发布的设置*
https://github.com/hexojs/hexo-deployer-git
```
$ npm install hexo-deployer-git --save
$ vi _config.yml

~~~~~~~~~~~~~~~~~~ _config.yml ~~~~~~~~~~~~~~~~~~
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: git@github.com:yt8yt/yt8yt.github.io.git
  branch: master
```

### 6. Set "watch" before starting your work *预览你生成的静态文件*
"watch" command can monitor your files.  
https://hexo.io/docs/generating.html
```
$ hexo generate --watch
```

### 7. Create a new post file *发布一篇新文章*
```
$ hexo new first-post
INFO  Created: ~/***/yt8yt.github.io/source/_posts/first-post.md
```

### 8. Edit the above file with Markdown or Hexo's Helper *你可以使用Markdown语法或者Hexo的辅助函数进行你的文章编写*
Hexo's Helper  
https://hexo.io/docs/helpers.html  
I use Atom with "shift + control + m" when I use Markdown :-)  
https://atom.io/

### 9. Delete "source/_posts/hello-world.md" *删除一篇md源码文件*
It's not necessary to deploy.

### 10. Deploy your new blog!! *重新发布你的博客*   
https://hexo.io/docs/deployment.html
```
$ hexo clean
$ hexo deploy
```
After writting the above command, you can see your new blog on GitHub Pages.  *现在访问你的github pages地址，已经可以看到你的博客啦！*
http://******.github.io/

### 11. Change your blog theme *改变博客主题*
https://github.com/hexojs/hexo/wiki/Themes
```
For instance, How to use the following theme.
https://hexo.io/hexo-theme-light/

## Install it 切换到项目文件，克隆远程主题
$ cd yt8yt.github.io
$ git clone git://github.com/tommy351/hexo-theme-light.git themes/light

## Update the above files  切换到新安装的主题文件夹，并通过git拉取最新的版本
$ cd themes/light
$ git pull

## Set information to use the theme  切换到项目文件，使用编辑器对_config.yml编辑,以下命令是使用vi编辑器的例子
$ cd yt8yt.github.io
$ vi _config.yml

~~~~~~~~~~~~~~~~~~ _config.yml ~~~~~~~~~~~~~~~~~~
## 将主题修改为 light
# Extensions
## Plugins: http://hexo.io/plugins/
## Themes: http://hexo.io/themes/
theme: light
```

### 12. Create a new page file 创建一个格式为源码文件
https://hexo.io/docs/writing.html
```
$ hexo new page aboutme
INFO  Created: ~/***/yt8yt.github.io/source/aboutme/index.md

$ cd source/aboutme/

$ vi index.md
```

### 13. Use "Read More"  如果您使用过Wordpress，那么您对以下代码就在熟悉不过来，在你需要断行截取首页描述的地方，使用以下代码，自动进行描述截取
Write `<!-- more -->` in your articles.  

### 14. Use Plugins 在这里了解插件
https://github.com/hexojs/hexo/wiki/Plugins
