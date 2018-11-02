---
title: golang安装与使用
date: 2018-11-01 11:18:55
tags:
---

### 1. 下载golang安装包
[下载](https://www.golangtc.com/download) msi 安装后自动配置环境变量

    继续设置   GoPath: D:\go       //工作目录
               GoRoot: C:\Go\      // go的安装目录

### 2. 安装beego框架
```bash
$ go get -u github.com/astaxie/beego  
$ go get -u github.com/beego/bee
```
    src目录下执行 --->  $ go install github.com/beego/bee  (安装bee工具)  
    ---> gopath目录下会出现bin目录,里面包含 bee.exe      
    环境变量配置:   path: src/bin
```bash
$ bee new hello   --->  生成src文件  ---> 存放源码
$ bee run hello   --->  生成bin/pkg 文件
$ bee version     --->  验证是否安装成功
```

### 3. 安装vscode工具
[下载](https://code.visualstudio.com/)后一直继续,安装成功
点击最左边工具栏"拓展"图标(ctrl+shift+x),搜索Go,安装后重启
