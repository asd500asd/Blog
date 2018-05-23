---
title: openresty配置node服务端口转发到80端口
date: 2018-05-23 11:38:14
tags:
- 服务端
categories:
- 服务端
---

## 场景

当搭建Node项目时，Node起的服务在进行域名指向时需要进行端口转发，转发到80端口，因为在进行域名指向需要指到80端口。具体配置如下：

![image](http://118.24.153.41/public/imgs/open.png)

> 在openresty/nginx 的 nginx.conf 中将需要进行端口转发的进行配置，server_name中为需要指向的域名，proxy_pass 中为 Node起的服务。

就酱。。。