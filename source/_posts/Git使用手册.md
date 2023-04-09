---
title: Git使用手册
date: 2021-09-10 18:44:32
categories: 码农笔记
tags:
  - Git
toc: true
---

## 前言

总是记不得git的一些指令，查一次用一次，做个笔记吧。

<!--more-->

## Git 基本操作

- 创建仓库命令
  - ```git init``` 初始化仓库
  - ```git clone``` 拷贝一份远程仓库，也就是下载一个项目。

- 提交与修改
  - ```git add``` 添加文件到仓库
  - ```git status``` 查看仓库当前的状态，显示有变更的文件。
  - ```git diff``` 比较文件的不同，即暂存区和工作区的差异。
  - ```git commit``` 提交暂存区到本地仓库。
  - ```git reset``` 回退版本。
  - ```git rm``` 删除工作区文件。
  - ```git mv``` 移动或重命名工作区文件。

- 提交日志
  - ```git log``` 查看历史提交记录
  - ```git blame <file>``` 以列表形式查看指定文件的历史修改记录

- 远程操作
  - ```git remote``` 远程仓库操作
  - ```git fetch``` 从远程获取代码库
  - ```git pull``` 下载远程代码并合并
  - ```git push``` 上传远程代码并合并

## Git Github

- 添加SSH公钥：基于SSH协议的Git服务，好比是仓库的钥匙
  - ```ssh-keygen -t rsa -C "账号（如：github邮箱）"```
  - ```cat ~/.ssh/id_rsa.pub``` 得到key值，给到相应平台
  - 验证是否成功: ```ssh -T git@github.com```

- 初始化本地仓库（要上传的文件根目录）：```git init```
- 本地连接github远程仓库：```git remote add origin [github上仓库的地址]```
  - 移除连接：```git remote rm origin```
  - 如是https，可解除https的ssl的凭证```git config --global http.sslVerify false```
- 将github上的文件克隆到本地，保持本地和远程的github的文件保持一致：```git pull --rebase origin main```
- ```git add 文件名``` or ```git add .``` 添加所有的文件
- 将代码提交到本地仓库：```git commit -m "提交说明"```
- 将本地仓库所有上传到github上管理：```git push -u origin main```
  - 第一次推送，加上了```-u```参数，Git本地和远程关联起来，在以后的推送或者拉取时就可以简化命令。
  - 加```-f```是强制上传（危险操作）

## 使用Git上传到多个Github仓库

- 一个Github对应一个SSH公钥
- 在```~/.ssh/```下创建config文件用于映射

  ```
  # 799802172@qq.com
  Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa

  # 2072483140@qq.com
  Host ano.github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_anoo
  ```

- 将SSH key的密钥加入ssh agent中
- 添加对应的GitHub的邮箱和名称（上传仓库的作者）
