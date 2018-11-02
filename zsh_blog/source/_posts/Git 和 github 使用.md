---
title: Git 和 github 使用
date: 2018-11-01 11:45:14
tags:
---
### 1. 下载Git
[下载](https://gitforwindows.org/)  默认安装

### 2. 注册github账号
[注册](https://github.com/join?source=login) 按步骤注册github账号

### 3. 创建私钥
桌面右键,打开gitbash
```bash
$ ssh-keygen -t rsa -C "your_email@youremail.com"
```
后面的your_email@youremail.com改为你在github上注册的邮箱，之后会要求确认路径和输入密码，我们这使用默认的一路回车就行。成功的话会在~/下生成.ssh文件夹，进去，打开id_rsa.pub，复制里面的key。

回到github上，进入 Account Settings（账户配置），左边选择SSH Keys，Add SSH Key,title随便填，粘贴在你电脑上生成的key

### 4. 设置默认用户
```bash
$ git config --global user.name "your name"
$ git config --global user.email "your_email@youremail.com"
```

### 5. 新建仓库
到github上 ,右上角 new repository 新建一个仓库
进到src 目录下执行  git clone htttp://github.com/xxxxx  (仓库的地址)
进到克隆的项目目录下, bee new "项目名"

### 6. 上传到github
```bash
$ git add .
$ git commit -m "first"
$ git push -u origin master
```

### 7. git 常用命令
```bash
$ git checkout -b dev-zsh-seeu   //创建一个 dev-zsh-seeu 的开发分支
$ git stash                      //把当前修改暂存
$ git stash  pop                 //把暂存的修改取回来  
$ git pull origin master         //把master分支的代码拉下来
$ git checkout                   //放弃所有修改
$ git branch -a                  //查看所有分支
$ git config --global credential.helper store  //不用每次输密码
```