---
title: "Hugo"
date: 2019-08-14T14:56:10+08:00
description: ""
draft: false
tags: ["hugo"]
categories: ["website"]
---

使用hugo创建博客
<!--more-->
# 使用hugo

## 下载hugo

参考文章：
https://www.gohugo.org/doc/tutorials/installing-on-windows/
https://www.gohugo.org/doc/overview/usage/
[使用hugo搭建博客](https://www.gohugo.org/post/coderzh-hugo/)

>https://github.com/gohugoio/hugo/releases

当前最新：[hugo_0.56.3_Windows-64bit.zip](https://github-production-release-asset-2e65be.s3.amazonaws.com/11180687/55152100-b3a3-11e9-93b3-7757a4550f5c?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20190814%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20190814T021625Z&X-Amz-Expires=300&X-Amz-Signature=c2cb84a6c1041e3e918adcf5b522033536b95ac142f4983785687aab2c64dfa2&X-Amz-SignedHeaders=host&actor_id=0&response-content-disposition=attachment%3B%20filename%3Dhugo_0.56.3_Windows-64bit.zip&response-content-type=application%2Foctet-stream)

下载之后解压得到“hugo.exe”文件。  


## 设置你的文件夹
你将需要一个存储 Hugo 可执行文件、博客内容（你创建的的那些文件），以及生成文件（Hugo 为你创建的 HTML）的地方。

打开 Windows Explorer。  
* 创建一个新的文件夹，D:\Hugo。      
* 创建一个新的文件夹，D:\Hugo\bin。  
* 创建一个新的文件夹，D:\Hugo\Sites。
* 确保 hugo.exe 文件在 D:\Hugo\bin 文件夹。

将hugo.exe所在的目录添加到**系统环境变量PATH**下面，打开cmd，输入“hugo version”。  


# 建立hugo项目，创建自己的站点
 hugo new site [site-name],[site-name]目前使用“blog”
```bash
hugo new site blog
```
然后hugo会自动生成这样一个目录结构：  

|||
|---------------|-----------------------------|
| ▸ archetypes  |default.md为模板             |
| ▸ content     |放的是你写的markdown文章     |
| ▸ layouts     |网站的模板文件               |
| ▸ static      |图片、css、js等资源          |
| ▸ config.toml |网站的配置文件               |

# 进入生成的site目录，创建一个页面
```bash
$ cd blog
$ hugo new about.md
```
**如果是博客日志最好在post目录里**
```bash
$ hugo new post/[blog-article-name].md
```
执行命令后即可看到对应的目录下创建的md文件，修改md文件内容即可。



# 安装主题
这里暂时不使用git命令clone下载主题，我们通过手动下载方式进行下载。  
Hugo官方主题：https://themes.gohugo.io/  
Hugo官方主题GitHub：https://github.com/spf13/hugoThemes/  
这里选取Hyde主题为例，从gitHub下载下来，**将解压出的文件放到hugo\\bin\\themes文件夹下**。  
[主题名]=Hyde  
将\blog\themes\\[主题名]\exampleSite\config.toml 替换 \blog\config.toml  
主题存放的路径为：Sites\blog\themes\\[主题名]，包含以下文件：  

|||
|---------------|--------------------------|
|- archetypes   |存放default.md，头文件格式|
|- layouts      |主题模板文件     |
|- static       |静态资源         |
|- theme.toml   |主题配置文件     |



# hugo运行server
在你的站点根目录执行 hugo server命令进行调试
```bash：  
$ hugo server --theme=hyde --buildDrafts  
```
/# --theme=hyde 为指定主题；  
/# --buildDrafts 为build手稿

(新版本中已经不再需要手动添加--watch、-w来实现自动更新文件到预览网页，随时修改md文件会即使更新到预览网页中)

预览浏览器里打开： http://localhost:1313

# 生成、部署你的站点  
最常见的用法是在你的当前站点目录里执行 hugo ：
```bash
$ hugo
0 draft content
0 future content
99 pages created
0 paginator pages created
16 tags created
0 groups created
in 120 ms
```
你的站点将会生成到 public 目录，用来部署到你的 Web 服务器。
一般本地开发并执行完 hugo server 调试之后，你只需要最后执行一次 hugo 命令 不带 server 参数 ，
然后你就可以通过拷贝 public 目录（通过 FTP 、 SFTP 、 WebDAV 、 Rsync 、 git push 等等）到你的生产环境的 Web 服务器来 部署你的站点。

由于 Hugo 生成的是一个静态站点，你的站点可以托管到任何地方，包括 Heroku 、 GoDaddy 、 DreamHost 、 GitHub Pages 、 Amazon S3 和 CloudFront ， 或者任何便宜甚至免费的静态网站托管服务。

## 关于部署的说明

运行 hugo 命令 并不会 删除之前生成的文件。   
这意味着你必须在运行 hugo 命令之前删除你的 public/ 目录（或者你通过 -d/--destination 指定的目录）。 不然的话，你可能有运行遗留在生成目录的错误文件的风险（比如草稿或者未来的文章）。

一个简单的处理方法是使用不同的目录用于开发和部署环境。

启动一个会生成草稿内容（有助于编辑）的 Server ，指定一个不同的目标目录： dev/ 目录
```bash
$ hugo server -wDs ~/Code/hugo/docs -d dev
```
当内容准备好发布时，使用默认的 public/ 目录：
```bash
$ hugo -s ~/Code/hugo/docs
```
这将防止你不小心发布了还未准备好的内容。

# 直接用 Hugo 作为你的 Web 服务器！
```bash
$ hugo --theme=hyde --baseUrl="https://YOURNAME.github.io/"
```
Hugo 配置文件config.toml的baseURL修改如下
baseURL = "http://pejoicen.gitee.io/blog"

# 配置文件config.toml


# 使用github

建立仓库名[用户名].github.io  
Pejoicen.github.io  

域名解析设置：  
blog.pejoicen.com  
主机记录：blog  
记录类型：** CNAME**  
记录值：Pejoicen.github.io  


![域名解析](\hugo\Snipaste_2019-08-19_23-20-45.png)



https://blog.csdn.net/qq_30682027/article/details/83536577
1.4.1. Hugo中Markdown插入图片路径问题
解决这个问题，主要是两个方面：

第一、在config.toml中配置baseurl。作用是无论在本地还是发布出去，图片的路径都是相对于baseurl而言的。

第二、在Markdown图片路径中，以static为根目录写全路径。

示例：假设测试baseurl为http://localhost:1313/，图片位置：/static/img/hugo/123.png，那么在Markdown中写作![图片说明](/img/hugo/123.png)。这时，在浏览器就可以看到图片了
 ———————————————— 
版权声明：本文为CSDN博主「欢欢2776479680」的原创文章，遵循CC 4.0 by-sa版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_30682027/article/details/83536577