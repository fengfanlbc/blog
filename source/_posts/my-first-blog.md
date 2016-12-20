---
title: 利用gitpage和hexo搭建一个个人博客
date: 2016-09-15 09:46:34
tags: blog hexo
description: "Welcome to my blog! This is my first post."

---

<!-- more -->

*"we only live once"*

<br/>

# Mac上基于gitpage和hexo搭建个人博客

之前一直就想搭一个个人博客，总觉得博客园，cnblog之类的太丑，简书挺漂亮的但是还是感觉不是自己的东西。本来想尝试自己买个服务器然后基于wordpress搭建一个博客，但是觉得太麻烦······于是综合考虑选择了gitpage+hexo搭建，以下是我的一个搭建流程：
> * 配置环境：安装Node，安装Git
> * 安装hexo
> * 注册一个github账户
> * 创建一个名字为：yourAccount.github.io的仓库
> * 将hexo部署到gitpage上
> * 创建第一篇博客
> * 选择一个自己喜欢的主题

## 配置环境

Hexo是一个静态博客生成器，用node.js开发，作者是一名台湾大学生（感觉好牛），node.js可以到对应官网上下载最新版本。git的话安装了xcode会自带。

## 安装hexo

然后新建一个名为blog的文件夹，进入该文件夹后执行如下命令安装Hexo：

`npm install hexo -g hexo`

执行如下命令进行初始化：

`hexo init`

安装依赖包：

`sudo npm install`

这样所有的安装工作就完成了，进入blog文件夹这行如下命令生成静态页面：

`hexo g`

然后执行本地启动命令：

`hexo s`

执行完后在浏览器输入：

`http://localhost:4000`

这样你就可以看到hexo默认的静态页面效果了

## 部署至github

部署之前得去注册一个github账户，这个很简单，然后新建一个repository，如图所示：

![my repository](http://odbihfqll.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-09-15%20%E4%B8%8B%E5%8D%884.11.40.png)

然后进入blog文件夹内使用如下指令部署至github：

`hexo d`

这样在浏览器输入：

`http://yourAcount.github.io`

便可以直接访问到hexo默认主页

## 写第一篇博客

这里推荐大家下一个markdown编辑器：**Mou**，感觉挺好用，当然还有很多其他的markdown编辑器。在自己博客里写博文主要就是用markdown了，所以还推荐大家到如下页面学习下markdown语法：

[Markdown 教程](https://www.zybuluo.com/mdeditor)

我觉得可以一遍写一遍学，这个语法应该不难，接下来在blog文件夹里用如下指令创建自己的第一篇博文：

`hexo new "your blog name"`

这条指令执行后会在source文件夹中的post文件夹里生成一个新的md文件，用Mou打开开始编辑，比如改成如下：

```---
title: 利用gitpage和hexo搭建一个个人博客

date: 2016-09-15 09:46:34

tags: blog hexo

description: "Welcome to my blog! This is my first post."
---
your content```

改好后在blog下执行`hexo g && hexo s`指令生成静态文件并本地启动，然后在浏览器输入`localhost:4000`查看效果，我们会发现多了一篇你刚刚写的博文

之后博文的润色，就要参照markdown语法自己慢慢修改了。

## 选择自己喜欢的hexo主题

在github上有许多为hexo量身定做的主题，基本可以满足不同人的口味。可以上github自己搜索，这里是知乎给出的推荐：

[hexo theme](http://www.zhihu.com/question/24422335/answer/46357100)

我的推荐是这几款：

> * [yelee](https://github.com/fengfanlbc/hexo-theme-yelee)
> * [cover](https://github.com/fengfanlbc/hexo-themes-cover)
> * [apollo](https://github.com/pinggod/hexo-theme-apollo)
> * [huxpro](https://github.com/Huxpro/huxpro.github.io)

选好主题后，按照主题作者给出的说明应该就可以完成了，也就几条指令而已。当然之后要完善我觉得还是要废些功夫。所以我最推荐的还是第一个主题**yelee**作者给出了很详细的说明，使用起来很方便，也是我现在博客所用的主题。对于博文里的照片自然是不能用本地的，肯定是要放到网上，这里我推荐使用**七牛云**提供的个人空间和外链服务，将你的照片外链，然后直接引入链接就好。





