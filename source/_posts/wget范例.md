---
title: wget范例
date: 2018-11-05 00:08:54
tags:
- BASH
- Linux
- Shell
categories:
- 代码片段
---
如何通过规则性计算，下载符合特定规则的图片，就譬如，下载大量图片
``` bash
num=600
while(($num<900))
do
		wget -r -p -U Mozila "http://www.abc.com/d/7541228${num}.jpg"
		let "num++"
done
```
使用wget下载整个网站

``` bash
wget -c -r -np -k -L -p -U Mozila "http://abc.com/a/"

```

参数说明：
-c 断点续传
-r 递归下载，下载指定网页某一目录下（包括子目录）的所有文件
-nd 递归下载时不创建一层一层的目录，把所有的文件下载到当前目录
-np 递归下载时不搜索上层目录，如wget -c -r www.xxx.org/pub/path/
没有加参数-np，就会同时下载path的上一级目录pub下的其它文件
-k 将绝对链接转为相对链接，下载整个站点后脱机浏览网页，最好加上这个参数
-L 递归时不进入其它主机，如wget -c -r www.xxx.org/ 
如果网站内有一个这样的链接： 
www.yyy.org，不加参数-L，就会像大火烧山一样，会递归下载www.yyy.org网站
-p 下载网页所需的所有文件，如图片等
-A 指定要下载的文件样式列表，多个样式用逗号分隔
-i 后面跟一个文件，文件内指明要下载的URL


