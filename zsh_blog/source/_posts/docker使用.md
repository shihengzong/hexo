---
title: docker 使用
date: 2018-11-02 11:45:14
tags:
---

### 下载安装
[下载](https://store.docker.com/editions/community/docker-ce-desktop-windows)

    启动Microsoft Hyper-V
    在电脑上打开“控制面板”->“程序”-> “启动或关闭Windows功能”-> 勾选 Microsoft Hyper-V

    重启电脑
    打开.exe 一直安装完成



### docker 使用

    -- windows+r                                          输入cmd  打开终端
    -- docker -version                                    检查docker版本 
    -- docker login                                       输入dockerhub的昵称和密码
    -- docker search tutorial                             搜索名字有 "tutorial"的镜像
    -- docker pull learn/tutorial                         下载learn/tutorial 镜像
    -- docker -i -t learn/tutorial /bin/bash              使用命令docker run -i -t 镜像名字 /bin/bash创建一个容器
    -- docker run learn/tutorial echo "hello word"        运行镜像输出helloworld
    -- docker run learn/tutorial apt-get install -y ping  在容器中安装ping程序
    -- docker cp /zsh/demo c46fc7b6c818:/zsh/             将主机/zsh/demo目录拷贝到容器96f7f14e99ab的/zsh目录下(/zsh/ 两个"/"不能少)
    -- docker ps -l                                       查看修改后的容器id
    -- docker commit 698 learn/ping                       把这个镜像保存为learn/ping
    -- docker run lean/ping ping www.google.com           运行新镜像
    -- docker ps                                          查看所有正在运行中的容器
    -- docker inspect efe                                 查看某一个容器的具体信息
    -- docker tag c46fc7b6c818 zongsh/learn/ping          给新镜像带上账号信息
    -- docker push zongsh/learn/ping                      发布到docker账号