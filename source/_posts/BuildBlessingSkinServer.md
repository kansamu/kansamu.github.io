---
title: 零基础搭建专属皮肤站实现皮肤托管和外置登录
date: 2020-09-01 13:50:23
tags: 技术,Minecraft
thumbnail: https://cdn.jsdelivr.net/gh/kansamu/kansamu@1.3/pic/weatheringwithyou/dom04.jpg
---

通过BlessingSkin实现皮肤加载和可管理外置登录，便利的管理玩家账号<!-- more -->

## 前言

**非在线模式服务器的问题** 

玩家皮肤和披风的加载一直是非在线模式Minecraft服务器的问题。

使用LitterSkin等大型皮肤站来加载皮肤难免会出现一些因为ID重复而造成的问题

同样，登录插件不便于玩家和管理员对账号进行管理，还会出现忘记密码难以找回，程序批量注册小号等

现在，我们可以解决这些问题了，通过BlessingSkinServer+authlib-injector的组合



**实现的功能**

在Web端通过邮箱注册账号，可使用谷歌验证码拦截机器注册

玩家可自己通过邮箱修改密码，全程在Web端可视化进行

提供上传，托管，游戏内加载皮肤和披风的功能

可选择插件提供邀请码功能限制注册

------



## 环境及程序准备

### 主机环境

**<u>本教程使用Centos8.0.1905，强烈建议使用linux系统</u>**

**Linux或windows环境**

> 强烈建议使用Linux环境，本教程使用宝塔面板，全程可视化界面操作
>
> 不支持虚拟主机

拥有独立ip或共享ip的很多个端口

> 国内的主机需要备案才能使用80/443端口

可用内存至少为1GB，上行带宽1M即可

### 程序准备

**BlessingSkinServer**

[https://github.com/bs-community/blessing-skin-server](https://github.com/bs-community/blessing-skin-server)

**authlib-injector**

[https://github.com/yushijinhun/authlib-injector](https://github.com/yushijinhun/authlib-injector)

>  下载地址在旁边的release里面，你应该下载的是zip或者jar文件



**任意的ssh终端来使用linux主机**

------



## 开始搭建

### 安装集成环境

集成环境可以省去我们学习配置环境的麻烦

对于新手以及小白较为友好，本文使用宝塔集成环境进行教学

宝塔面板官网 [https://www.bt.cn/](https://www.bt.cn/)

**Centos安装宝塔**

```
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.sh
```

**Ubuntu/Deepin安装宝塔**

```
wget -O install.sh http://download.bt.cn/install/install-ubuntu_6.0.sh && sudo bash install.sh
```

> 其他linux发行版请前往宝塔官网自行查看。windows环境前往官网下载.exe文件安装即可



<u>安装完成即在ssh终端输出面板地址和初始用户名和密码</u>

<u>建议修改端口和面板入口避免被暴力破解</u>

------

### 安装php和Web程序

前往宝塔面板的软件商店安装以下运行环境

- PHP7.4
- Nginx1.19
- MySQL5.7
- phpMyAdmin5.0

> 强烈建议使用编译安装获取更新版本以及更好的安全性

![](https://s1.ax1x.com/2020/09/04/wAN7VS.png)

------

### 新建站点和数据库

<u>添加站点</u>

前往网站选择添加站点并添加域名

**在域名处填写你的网站域名**

**在此之前请前往域名服务商处将域名解析a记录到你的公网ip**

![](https://s1.ax1x.com/2020/09/04/wANb5Q.png)

------

<u>新建数据库</u>

前往数据库选择添加数据库

填入数据库名，用户名和密码

**数据库编码我们选择utf8mb4**

> 这是mysql的一个历史问题，utf8mb4才能兼容四字节的unicode

**如果你没有特殊需求，访问权限请选择本地服务器**

![](https://s1.ax1x.com/2020/09/04/wAaFw8.png)

------

<u>上传皮肤站程序</u>

前往网站的根目录，一般情况是以你域名为名的文件夹

删除原来的index.html和404文件

上传你下载的BlessingSkinserver压缩包

然后点击解压来解压它

![](https://s1.ax1x.com/2020/09/04/wANob8.png)

------



### 配置BS所需环境

#### 解除函数禁用

宝塔面板默认禁用了一些我们需要的php函数，我们需要解除禁用

入口在软件商店，已安装，php7.4，点击设置，找到禁用函数

- `symlink`
- `readlink`
- `putenv`
- `realpath`

> 请确保以上函数解除禁用，通常情况下可以解除所有的函数禁用

![](https://s1.ax1x.com/2020/09/04/wANHUg.png)

------

#### 安装php拓展

BlessingSkin运行需要以下拓展

- OpenSSL
- PDO
- Mbstring
- Tokenizer
- GD
- XML
- Ctype
- JSON
- fileinfo
- zip

**使用宝塔面板，我们只需要额外安装fileinfo拓展即可**

![](https://s1.ax1x.com/2020/09/04/wAN5KP.png)

------

#### 修改Nginx配置

请打开网站一栏，点击设置

找到配置文件，如图所示，删除其中的png规则

删除完的配置文件应该为这样

```
location ~ .*\.(gif|jpg|jpeg|bmp|swf)$
    {
        expires      30d;
        error_log off;
        access_log /dev/null;
    }
```

![](https://s1.ax1x.com/2020/09/04/wANO8s.png)

------

#### 配置伪静态规则

请在伪静态一栏填入以下伪静态规则

```php
location / {
    try_files $uri $uri/ /index.php?$query_string;
}
```

![](https://s1.ax1x.com/2020/09/04/wANLCj.png)

------

#### 修改运行目录

**请务必取消防跨站攻击选项**

**请务必取消防跨站攻击选项**

**请务必取消防跨站攻击选项**



**然后在运行目录填入/public**

![](https://s1.ax1x.com/2020/09/04/wADmqI.png)

------

#### 复制配置文件

请打开ssh终端，切换到网站根目录

```
cd /www/wwwroot/你的域名
```

然后执行

```
cp .env.example .env
```

![](https://s1.ax1x.com/2020/09/04/wANIDf.png)

windoows用户复制一份`.env.example`并重命名为`.env`即可

一般情况下你是在root用户下执行的cp命令，所以`.env`的所有者会是root

这会导致无法安装，请确保所有皮肤站的文件所有者为www，权限为755

![](https://s1.ax1x.com/2020/09/04/wANzrV.png)

------

#### 生成AppKey

`APP_KEY` 被用于加密 Session 等数据，对 Blessing Skin 来说是不可缺少的。

请执行以下命令生成 app key：

```text
php artisan key:generate
```

如果没有生成 app key，访问页面时会出现「No application encryption key has been specified.」的错误提示

![](https://s1.ax1x.com/2020/09/04/wANX2n.png)

------



### 完成安装

如果你完成了上述所有操作，现在访问你的网站应该是这样

![](https://s1.ax1x.com/2020/09/04/wANxK0.png)

**请按照指示填写信息即可完成安装**

------

## 实现外置登录

### BS皮肤站部分

BlessingSkinServer提供了一个插件市场

我们可以通过安装插件实现很多功能，比如外置登录

我们前往bs的插件市场

安装**Yggdrasil API**，并在插件管理中启用

![](https://s1.ax1x.com/2020/09/05/wEkNTS.png)

启用后，在用户中心的首页便会出现**认证服务器地址**

![](https://s1.ax1x.com/2020/09/05/wEEkb6.png)

------

bs作为一个皮肤站程序，提供了单账号多角色的功能

但是作为一个服务器的专属皮肤站，我们可以通过安装插件来关闭这个功能

我们在插件市场，安装**单角色限制**插件，这样一个邮箱只能拥有一个角色了

![](https://s1.ax1x.com/2020/09/05/wEk7m6.png)

------

### MC服务端部分

我们需要下载**authlib-injector**

地址[https://authlib-injector.yushi.moe/](https://authlib-injector.yushi.moe/)

下载到的**authlib-injector**应放置在服务端的根目录内，并添加以下服务端启动启动参数

```java
-javaagent:{authlib-injector.jar 的路径}={验证服务器 URL (API 地址)}
```

示例

```
java -javaagent:{authlib-injector-1.1.32.jar}={https://skin.xiamengwuyu.cn/api/yggdrasil} -jar CatServer-20c1883-universal.jar
```

> 第一个大括号内填写authlib-injector的路径
>
> 如果你的authlib-injector.jar和启动脚本位于同一目录，直接填写文件名即可
>
> 第二个大括号内填写认证服务器地址

![](https://s1.ax1x.com/2020/09/05/wEKoX4.png)

------

### MC客户端部分

请使用支持外置登录的Minecraft启动器

HMCL是对外置登录支持比较好的启动器

这里以HMCL启动器演示 **HMCL** [https://github.com/huanghongxun/HMCL](https://github.com/huanghongxun/HMCL)

1. 直接在皮肤站上拖动按钮到hmcl启动器
2. 手动输入地址登录