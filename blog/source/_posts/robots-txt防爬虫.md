---
title: robots.txt防爬虫
date: 2018-07-12 18:20:00
tags:
- 防坑指南
categories:
- 防坑指南
---

> 事因： 公司内部项目在百度搜索上输入名字会出现在搜索结果中，大佬们肯定不愿意啊，就需要禁止爬虫爬取。在项目根目录加入这个文件就行了。

## WHAT?
::::::

robots.txt是一个纯文本文件，是爬虫抓取网站的时候要查看的第一个文件，一般位于网站的根目录下。robots.txt文件定义了爬虫在爬取该网站时存在的限制，哪些部分爬虫可以爬取，哪些不可以爬取（防君子不防小人）。

**robots.txt中的参数含义：**
1. User-agent：描述搜索引擎spider的名字。在“robots.txt“文件中，如果有多条 User-agent记录，说明有多个robot会受到该协议的约束。所以，“robots.txt”文件中至少要有一条User- agent记录。如果该项的值设为*（通配符），则该协议对任何搜索引擎机器人均有效。在“robots.txt”文件 中，“User-agent：*”这样的记录只能有一条。
2. Disallow: / 禁止访问的路径 <br/>

```
例如:

Disallow: /home/news/data/，代表爬虫不能访问/home/news/data/后的所有URL，但能访问/home/news/data123

Disallow: /home/news/data，代表爬虫不能访问/home/news/data123、/home/news/datadasf等一系列以data开头的URL。

前者是精确屏蔽，后者是相对屏蔽
```




3.Allow：/允许访问的路径

```
例如:

Disallow：/home/后面有news、video、image等多个路径

接着使用Allow：/home/news，代表禁止访问/home/后的一切路径，但可以访问/home/news路径
```


介绍感兴趣就看看，不感兴趣的话直接复制下面的放在根目录就可以了。

robots.txt：
```
User-agent: Baiduspider
Disallow: /
User-agent: Googlebot
Disallow: /
User-agent: Googlebot-Mobile
Disallow: /
User-agent: Googlebot-Image
Disallow:/
User-agent: Mediapartners-Google
Disallow: /
User-agent: Adsbot-Google
Disallow: /
User-agent:Feedfetcher-Google
Disallow: /
User-agent: Yahoo! Slurp
Disallow: /
User-agent: Yahoo! Slurp China
Disallow: /
User-agent: Yahoo!-AdCrawler
Disallow: /
User-agent: YoudaoBot
Disallow: /
User-agent: Sosospider
Disallow: /
User-agent: Sogou spider
Disallow: /
User-agent: Sogou web spider
Disallow: /
User-agent: MSNBot
Disallow: /
User-agent: ia_archiver
Disallow: /
User-agent: Tomato Bot
Disallow: /
User-agent: *
Disallow: /
```




