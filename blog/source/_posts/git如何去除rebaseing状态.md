---
title: git如何去除rebaseing状态
date: 2018-05-30 10:45:00
tags:
- 防坑指南
categories:
- 防坑指南
---

> 如果想要放弃当前rebase操作，用
 `git rebase --abort`

> 如果冲突已经解决，先add冲突文件，之后
 `git rebase --continue`
