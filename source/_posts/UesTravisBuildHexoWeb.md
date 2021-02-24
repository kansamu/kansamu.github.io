---
title: 使用TravisCI自动生成Hexo静态网页
date: 2020-08-26 21:02:26
tags: 技术
thumbnail: https://cdn.jsdelivr.net/gh/kansamu/kansamu@v1.1/pic/weatheringwithyou/dom01.jpg
---

 本篇文章内容是让Hexo实现跨平台跨设备和浏览器环境下修改你的网站。<!-- more -->

## 前言

### Hexo存在的问题

使用Hexo这个node_js程序来生成静态网站是无疑十分方便的，仅仅在本地执行一行`Hexo g`便可生成一个完整的静态网站。

你可以把静态网站上传到个个服务商的对象存储又或者是Github，Gitee这样的提供静态网站托管的平台来部署一个静态的网站。

和wordpress之类的动态web程序把程序运行在服务端相比，Hexo的程序在本地。

这就造成了一个问题。**我们想要发布一篇新文章亦或是对Blog进行修改只能在有程序的设备上**。

我们需要Node_js环境以及单独属于这个网站的Hexo程序。如果你的主题还需要额外的node_js依赖，需要的环境便更加复杂。

可是动态博客可以在任意浏览器环境进行发布或者对博客的更改。如果Hexo部署在本地是不能这样做的。

本篇文章**内容便是让Hexo实现跨平台和跨设备，甚至是浏览器环境下修改你的网站。**

## 正文

### 环境准备

**<u>请注意！阅读本文需要接触过命令行以及有一定的计算机基础。遇到问题请善用搜索引擎</u>**

- 一个搭建完毕的Hexo框架
- 一个Github账号
- 一个Git程序

> 关于Hexo的搭建：Hexo官网有详细的文章，在互联网上也有很多相关的资料。本文不再赘述。

------

### 将Hexo放在Github上

**本文使用Windows环境进行讲解**

#### 新建Github仓库

打开[Github](Github.com),新建一个repositories，将仓库名命名为**username.github.io**

![](https://cdn.jsdelivr.net/gh/zaoyuanmoe/zaoyuanmoe@v1.3/img/QQ%E6%88%AA%E5%9B%BE20200826215210.png)

![](https://cdn.jsdelivr.net/gh/zaoyuanmoe/zaoyuanmoe@v1.2/img/QQ%E6%88%AA%E5%9B%BE20200826215337.png)

> username即为你的Github用户名，注意是可以登录使用的用户名而非昵称。

将仓库Clone到本地

右键一个合适的目录，选择Git bash here

输入Git clone  仓库地址

![](https://cdn.jsdelivr.net/gh/zaoyuanmoe/zaoyuanmoe@v1.3/img/20200826222222.png)

> **请注意！** 你可以选择http和ssh两种方式进行连接。http方式需要输入github账号和密码，ssh方式则是生成密钥。关于设置ssh密钥方式请自行学习。参考链接[百度百科](![](https://raw.githubusercontent.com/ZaoyuanMoe/zaoyuanmoe/master/img/20200826222222.png))。
>
> 当然你也可以选择http方式进行clone，看清复制是那种链接即可。

------



#### 部署Hexo到github仓库

完整的复制你的hexo所有的文件到clone下的文件夹内

通常情况下，一个完整的Hexo应该是长这样的，请复制所有内容到clone下的仓库根目录

![](https://cdn.jsdelivr.net/gh/zaoyuanmoe/zaoyuanmoe@v1.3/img/QQ20200826223230.png)

接下来请在仓库目录内运行git bash here

![](https://cdn.jsdelivr.net/gh/zaoyuanmoe/zaoyuanmoe@v1.3/img/QQ20200826223604.png)

正常情况下应该会有个（master），这代表你正在操作master分支

请执行以下命令

```
git add .
git commit -m "一段自定义的信息"
git push
```

> **请注意**，如果你是第一次使用git，请配置git的用户名和密码
>
> 请执行
>
> ```
> git config --global user.name  "username"  
> ```
>
> ```
> git config --global user.email  "email"
> ```
>
> username即为你在github的用户名
>
> email请使用你在github使用的邮箱之一

这时你访问你的github仓库，就会发现你的hexo程序已经上传到了github

### 创建用于存放静态网站的分支

#### 在本地创建分支

请执行以下命令

```
git checkout -b hexo
```

这段命令的意思是创建了一个名为hexo的分支，当然，这个名字你可以用其他的

这时你输入`git branch`可以查看本地的分支

![](https://cdn.jsdelivr.net/gh/zaoyuanmoe/zaoyuanmoe@v1.3/img//QQ20200826225134.png)

可以看到，你现在已经在hexo分支了，并且本地有两个分支，一个master，一个hexo

通过蓝色字体可以知道现在我们在hexo分支

####  push新分支到github

**请删除除了.git以外所有的文件和文件夹**

然后执行

```
git add .
git commit -m "初始化hexo分支"
```

现在我们已经完成了新建分支hexo

请切换回master分支

```
git checkout master
```

现在把新的hexo分支push到github

```
git push origin hexo
```

现在，你已经完成把和hexo分支上传到github，查看你的github会发现hexo分支已经出现

### 配置Travis CI与GithubPages

#### 向TravisCI添加你的github仓库

打开Travis [https://travis-ci.com/](https://travis-ci.com/)

点击右上角的sign选择sign with github，即使用github登录

在红圈的地方去github授权可以访问的仓库

![](https://cdn.jsdelivr.net/gh/zaoyuanmoe/zaoyuanmoe@v1.3/img/QQ%E6%88%AA%E5%9B%BE20200826230609.png)

#### 向TravisCI授予token

选择你的Github右上角头像，找到setting，在sitting的左栏最下面找到Developer setting

找到Personal access tokens

或者直接访问[https://github.com/settings/tokens](https://github.com/settings/tokens)

新建一个token，note可以任意填写，但是这个名字一会要用，我填写为HexoCI

**勾选repo选项**

![](https://cdn.jsdelivr.net/gh/zaoyuanmoe/zaoyuanmoe@v1.3/img/20200826231759.png)

复制生成的token，前往TravisCI的[Setting](https://travis-ci.com/account/repositories)

点击右边所在仓库的setting，找到Environment Variables

在name项填入你刚才生成token的名称，我应该填写HexoCI

在Value填入你复制的token

![](https://cdn.jsdelivr.net/gh/zaoyuanmoe/zaoyuanmoe@v1.3/img/20200826232606.png)

#### 配置.travis.yml

在你的本地仓库下创建一个名为.travis.yml的文件

填入以下代码

```
language: node_js

node_js: stable 

cache:
    directorise:
    - node_modules
 
install:
    - npm install
   
script:
    - hexo clean
    - hexo g
  
after_script:
    - cd ./public
    - git init
    - git config user.name "username" # 在此填入你的github用户名
    - git config user.email "email" # 在此填入你的github所用邮箱
    - git add .
    - git commit -m "自动构建Hexo博客文章"
    - git push --force --quiet "https://${HexoCI}@${GH_REF}" master:hexo
    # 请注意最后一行花括号内的HexoCI应填入你刚才生成的token名，我的是HexoCI
branches:
    only:
    - master
    # 触发自动构建的分支名称
env:
    global:
    - GH_REF: github.com/Kansamu/Kansamu.github.io.git #在次填入你的仓库地址
```

如何配置已在代码中进行注释，现再重复一遍

> - git config user.name "username" # 在此填入你的github用户名
> - git config user.email "email" # 在次填入你的github所用邮箱

对于此行配置

`git push --force --quiet "https://${HexoCI}@${GH_REF}" master:hexo`

HexoCI为你创建的token名

后面的master:hexo指从master分支push到hexo分支

`GH_REF: github.com/Kansamu/Kansamu.github.io.git #在次填入你的仓库地址`

请把kansamu/kansamu.github.io替换成你的用户名和你的仓库名称

**注意不要忘了结尾的.git**

------

如果你是在本地仓库创建的，请push到github上

老生常谈的操作

```
git add .travis.yml
git commit -m "create .travis.yml"
git push
```

请注意，请在本地仓库的master分支进行操作，.travis.yml应该存在于master分支。

#### 配置Github pages

请在仓库的Setting中的github pages中切换分支为hexo

![](https://cdn.jsdelivr.net/gh/zaoyuanmoe/zaoyuanmoe@v1.3/img/20200826234842.png)



------

## 进阶内容

### 关于主题suka的特殊配置

我的博客使用的是Sukkaw开发的suka主题[https://github.com/SukkaW/hexo-theme-suka](https://github.com/SukkaW/hexo-theme-suka)

在进行部署自动构建的时候遇到了一些问题

![](https://cdn.jsdelivr.net/gh/zaoyuanmoe/zaoyuanmoe@v1.3/img/20200827001025.png)

<u>**可以看到，suka需要额外的依赖**</u>

通过查看travis的build日志，我发现了缺少的依赖

```
$ hexo generate
INFO  Validating config
INFO  --------------------------------------------------------
 ____        _           _____ _
/ ___| _   _| | ____ _  |_   _| |__   ___ _ __ ___   ___
\___ \| | | | |/ / _` |   | | | '_ \ / _ \ '_ ` _ \ / _ \
 ___) | |_| |   < (_| |   | | | | | |  __/ | | | | |  __/
|____/ \__,_|_|\_\__,_|   |_| |_| |_|\___|_| |_| |_|\___|
hexo-theme-suka ( https://theme-suka.skk.moe )
--------------------------------------------------------------
INFO  Checking dependencies
ERROR Package node-prismjs is not installed.
ERROR Package qr-image is not installed.
ERROR Please install the missing dependencies.
ERROR You can enter suka-theme directory and run following commands:
ERROR $ npm i --production
ERROR $ yarn --production # If you prefer yarn.
```

也就是suka这个主题需要这两个额外的依赖

- node-prismjs
- qr-image

在本地安装这个主题时，我只需要执行`npm install --production`来补全运行环境

所以我们要在.travis.yml里添加几行配置来安装这个依赖

使用npm包管理安装依赖十分简单

```
npm install 包名
```

```
	#在.travis.yml的script:项目额外添加的配置
    - cd themes/suka
    - npm install node-prismjs
    - npm install qr-image
    - cd ../..
```

同时，为了方便更新，suka这个主题本身就是一个仓库

> 为了避免冲突，「Suka」中只有 `_config.example.yml`，`主题配置文件` 需要将 `_config.example.yml` 复制一份并重命名为 `_config.yml`。
>
> 为了防止开发中 `_config.yml` 被推送进 Repo 中，我们将 `_config.yml` 写入了 `.gitignore`。这意味着在使用 Git 管理 Hexo 站点文件时，`主题配置文件` 并不会被 Push 到 Repo 中。此时你需要将 `_config.yml` 从 `.gitignore` 中删除，注意这有可能会造成使用 `git pull` 更新「Suka」时造成冲突。

我们需要手动这两个操作执行

在主题文件夹内的.gitignore内删除_config.yml

把你本地的主题_config.yml手动push到github

**思维发散**

我并不知道你用的是什么主题，但是我的这段经历可以给你很多思维启发

主题有额外的依赖，需要在.travis.yml中手动添加配置来使TravisCI可以构建

如果你的主题也是一个仓库，留意.gitignore

可能还会有其他地方，所有你的主题文档特殊提醒的地方，都值得你去注意
