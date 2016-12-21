---
title: 多电脑维护hexo
date: 2016-12-20 21:28:54
tags: hexo
description: "用github备份实现多台电脑共同维护hexo"
---

<!-- more -->

# 使用多台电脑维护Hexo博客

*"we only live once"*

<br/>



在用Hexo写博客的时候可能需要在多台机器上进行，如果只能在一台笔记本上写的话就太局限了。所以下就是实现多台电脑共同维护hexo博客。

在搭建好hexo博客后，我们使用hexo deploy命令把最新版本的博客部署到自己的github上，实际上部署的是hexo生成的静态页面，不是整个的hexo博客工程文件，所以我们不能在另一台机器上直接弄下来再上传。我这里使用的方案是用git进行备份。我们在临时比较急的情况下也可以考虑使用云盘备份。

以下是详细流程：
<br/>
## 1. 创建新的项目

在github上创建一个新的repository,我将其命名为blog（也可以在原来博客的项目下创建一个分支，将整个工程文件备份到分之当中）
<br/>
## 2. 新建仓库

使用已经建立hexo博客的电脑，进入博客根目录运行个git bash，然后输入：
```command
$ git init
$ git remote add origin <server address>
```
注意这里的**server address**指的是刚刚建的仓库的地址，在这里我的就是 *https://github.com/fengfanlbc/blog.git* 
**remote add**指令会将本地仓库映射到github仓库
<br/>
## 3. 把本地的文件同步到你的github仓库

分别输入以下指令：
```command
$ git add .  #添加所有目录，注意add后面有个点`.`
$ git commit -m "add to Github" #添加提交说明，每次提交都需要
$ git push -u origin master  #把更新推送到云端
```
这时候再去看看自己的blog仓库是否有我们刚刚上传的项目
<br/>
## 4. 项目下载到新电脑

分别输入以下指令：
```command
$ git clone https://github.com/yourname/blog
$ cd blog
$ npm install hexo #hexo初始化
$ npm install
$ npm install hexo-deployer-git –-save
```
但是我们要注意，我们用第一台机器上传的hexo项目文件中并不含主题文件，我们可以进入themes文件夹里，会发现主题文件夹都是空的，所以我们需要在第二台机器上重新下载下自己的主题：
```commnad
$ git clone https://github.com/fengfanlbc/hexo-theme-yelee.git themes/yelee
```
<br/>
## 5. 同步并部署

现在我们就可以尝试在第二台电脑写一篇博客，然后部署并同步部署：
部署：
```command
$ hexo new post "my_new_post"
$ hexo generate
$ hexo deploy
```
同步：
```command
$ git add .
$ git commit -m "commit from PC_B"
$ git push -u origin master
```
在第一台电脑上获取最新版本博客：
```command
$ git pull
```
<br/>
## 总结

以上就是所有的搭建流程了，搭建好后，每次写完博客就是执行如下命令：
```command
$ git pull
$ hexo g
$ hexo d
$ git add.
$ git commit -m "new post"
$ git push origin master
```





