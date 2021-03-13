---
title: 如何使用使用Git
date: 2021-03-13 17:17:57
tags: 技术
---

本篇文章将简单的介绍git的用法

## 安装Git

**Git for windows**

https://npm.taobao.org/mirrors/git-for-windows/v2.31.0-rc2.windows.1/Git-2.31.0-rc2-64-bit.exe

双击，然后一直下一步即可

## 使用Git

在某目录内右键选择

> Git Bash Here

然后出来一个黑框框，还有路径，现在就可以使用git了

## 配置Git用户名和密码

输入

`git config --global user.name "用户名"`

`git config --global user.name "邮箱"`

用户名指可以登录的那个"账号"而非"昵称"

## 生成ssh秘钥

git依赖于ssh进行通讯，所以要生成ssh秘钥

`ssh-keygen -t rsa -C "邮箱"`

出来选项按回车键就可以，出来一串图案就代表已经生成完成

------

生成的秘钥存于

C:\Users\Administrator\.ssh

Administrator指当前的用户，请选择你当前的用户

目录下的id_rsa.pub文件即为公钥

