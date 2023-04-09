---
title: GitHub和Hexo搭建博客
date: 2021-06-28 22:44:10
categories: 码农笔记
toc: true
tags:
  - Hexo
  - GitHub
  - 博客
  - 服务器
---

## **0，前言**

Hexo是一款基于Node.js的静态博客框架，依赖少易于安装使用，可以方便的生成静态网页托管在GitHub上，是搭建博客的首选框架。

<!--more-->

搭建步骤：

- GitHub创建个人仓库
- 安装Git
- 安装Node.js
- 安装Hexo
- 推送网站

## **1，GitHub仓库创建 和 git安装**

这两个很简单一笔带过，唯一要注意的是仓库名字的固定写法：用户名.github.io，否则域名会变。

## **2，安装 Node 和 npm**

Node 是服务器端运行 Js 代码的引擎；npm 则是依赖包管理工具，可以轻松安装工具和代码类库。

Node 中文网 [http://nodejs.cn/download/](http://nodejs.cn/download/)

Windows上没什么好说的，Ubuntu上的步骤如下，Windows控制台安装也一样。

- ubuntu上安装

    ```powershell
    sudo apt install npm
    ```

- 查看版本检测是否安装成功

    ```powershell
    npm -v
    ```

- 修改npm的资源镜像链接

    ```powershell
    npm config set registry http://registry.npm.taobao.org
    ```

- 安装serve

    ```powershell
    npm i -g serve
    ```

在文件夹目录中启动serve即可搭建一个 web 服务器，以支持 http 协议访问。

值得注意的是，如果搭建服务器的话Ubuntu需要开放端口：

- 查看已经开启的端口

    ```powershell
    sudo ufw status
    ```

- 打开端口

    ```powershell
    sudo ufw allow 9123
    ```

- 开启防火墙

    ```powershell
    sudo ufw enable
    ```

- 重启防火墙

    ```powershell
    sudo ufw reload
    ```

## **3，Hexo安装和使用**

Hexo安装也很简单，这里记一些指令：

- 安装和初始化

    ```powershell
    npm install hexo-cli -g  # 全局安装Hexo
    npm update hexo -g  # 升级
    hexo init  # 初始化博客（在当前目录新建一个博客）
    ```

- 命令简写

    ```powershell
    hexo n "我的博客" == hexo new "我的博客"  # 新建文章
    hexo g == hexo generate  # 生成
    hexo s == hexo server  # 启动服务预览
    hexo d == hexo deploy  # 部署

    hexo server  # Hexo会监视文件变动并自动更新，无须重启服务器
    hexo server -s  # 静态模式
    hexo server -p 5000  # 更改端口
    hexo server -i 192.168.1.1  # 自定义 IP
    hexo clean  # 清除缓存，若是网页正常情况下可以忽略这条命令
    ```

## **4，推送网站**

在blog根目录里的_config.yml文件称为站点配置文件，另外还有主题配置文件，主题抄好之后就都靠自己魔改了。

- _config.yml文件的设置

    ```powershell
    deploy:
        type: git
        branch: master
        repo: https://github.com/kangxiatao/kangxiatao.github.io.git
    ```

其实就是给hexo d 这个部署命令做相应的配置，d是deploy的缩写，让hexo知道你要把blog部署在哪个位置，很显然，我们部署在我们GitHub的仓库里。

- 安装Git部署插件：

    ```powershell
    npm install hexo-deployer-git --save
    ```

- 生成和部署网站：

    ```powershell
    hexo g 
    hexo d
    ```

此时博客已经上线了，也可以先用```hexo s```在本地预览。

## **5，更新博客**

在```\blog\source\_posts```文件夹下添加md文件，编辑完之后，更新到GitHub就行，也就是生成和部署网站的步骤，over，我要魔改UI去了。

## **6，支持mathjax**

hexo默认的渲染器是marked，并不支持mathjax。kramed是在marked基础上修改的，支持了mathjax。

- 工程目录下安装kramed

    ```powershell
    npm uninstall hexo-renderer-marked --save
    npm install hexo-renderer-kramed --save
    ```

- 修改配置文件

    到主题配置文件```_config.yml```，找到```mathjax```，将其修改为```true```

    ```powershell
    mathjax:
      enable: true
    ```

- 文章渲染标签

    为加快渲染速度，渲染器只会在标签中有```mathjax: true```的文章中使用利用mathjax渲染。

    ```powershell
    ---
    title: 强化学习（Reinforcement Learning）
    date: 2021-01-27 00:00:58
    mathjax: true
    categories: 学习日志
    tags:
    - 强化学习
    - 神经网络
    ---
    ```

## **999，Error**

- server : 无法加载文件，因为在此系统上禁止运行脚本

    以管理员身份打开windows powershell 输入`set-ExecutionPolicy RemoteSigned`
