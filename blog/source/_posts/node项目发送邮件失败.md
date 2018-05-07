---
title: node项目发送邮件失败
date: 2018-05-07 10:46:43
tags:
- 防坑指南
categories:
- 防坑指南
---


> 描述问题

搭建一个node项目时，需要在注册时发送邮件，邮箱配置如下：

```
// 邮箱配置
  mail_opts: {
    host: 'smtp.163.com',
    port: 465,
    secure: true,
    auth: {
      user: '*****@163.com',
      pass: '客户端授权码'
    }

  },
```

**出现的问题：**
无法发送邮件，出现报错信息535，Log如下：
` send mail finally error { Error: Invalid login: 535 Error: authentication failed
`

**经排查：**
原因应该是会授权码跟pop3未开启的问题

> 解决办法

以163邮箱为例：
开启pop3/smtp ，imap/smtp，再设置客户端授权码，完美解决~~~

![image](http://118.24.153.41/public/imgs/1525662631127.jpg)


![image](http://118.24.153.41/public/imgs/1525662725881.jpg)


