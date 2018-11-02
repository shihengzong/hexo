---
title: nginx 基本使用
date: 2018-10-30 11:19:38
tags:
---

#### 1. 启动ngix

##### windows下启动
    cd nginx14-04    //进入目录
    start nginx

#####linux下启动
    cd nginx14-04    //进入目录
    nginx

#### 2. 反向代理
nginx接收到客户端的请求,通过正则表达式,转发到不同的服务器

##### 配置
```
 server {
        listen       81;                #监听端口(localhost:81)
        server_name  localhost;
        location / {                    #/表示匹配所有  转发到 http://127.0.0.1:9999
            proxy_pass   http://127.0.0.1:9999;
            proxy_redirect off;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            client_max_body_size 200m;
            access_log off;
        }
        location /v1 {                  #localhost:81/v1 转发到 http://127.0.0.1:9982
            proxy_pass   http://127.0.0.1:9982;
            proxy_redirect off;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            client_max_body_size 200m;
            access_log off;
        }
```

#### 2. 负载均衡
将同一任务的多次请求分摊到不同的服务器上,达到资源有效合理分配

    upstream mysvr { 
        server 127.0.0.1:7878 weight=1;
        server 192.168.10.121:3333 weight=2;
    }

weight: 加权轮询   按照abb abb abb 的循环去分配请求

#### 3. 关闭nginx
    cd nginx14-04
    nginx -s stop




