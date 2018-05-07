---
title: cross-env解决windows上NODE_ENV的问题
date: 2018-05-07 11:45:29
tags:
- 防坑指南
categories:
- 防坑指南
---

> 平时搭项目时本地起服务正常,但遇到用windows的同事down了项目后启动会报错,这里需要修改下`package.json`的`scripts`标签

windows不支持NODE_ENV=development的设置方式。
这个包能够提供一个设置环境变量的scripts，让你能够以unix方式设置环境变量，然后在windows上也能兼容运行。


需要在script中加入 cross-env,如下所示:
```
"scripts": {
    "dev": "npm run route&&cross-env NODE_ENV=dev node build/dev-server.js",
    "build-dev": "npm run route&&cross-env NODE_ENV=dev node build/build.js",
    "build-test": "npm run route&&cross-env NODE_ENV=test node build/build.js",
    "build-demo": "npm run route&&cross-env NODE_ENV=demo node build/build.js",
    "build-production": "npm run route&&cross-env NODE_ENV=production node build/build.js",
    "unit": "cross-env BABEL_ENV=test karma start test/unit/karma.conf.js --single-run",
    "e2e": "node test/e2e/runner.js",
    "test": "npm run route&&cross-env NODE_ENV=test npm run unit",
    "route": "node generate-routes.js",
    "easymock init": "node .easy-mock",
    "build-preview": "cd dist&&http-server"
  },
```

> 使用方法

- 安装cross-env:npm install cross-env --save-dev
- 在NODE_ENV=xxxxxxx前面添加cross-env就可以了。