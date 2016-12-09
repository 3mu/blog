---
title: hexo 命令备忘
date: 2016-12-09 22:51:54
tags: hexo
---

# 安装

+ nodejs[下载](https://nodejs.org/en/) 

```js
$ npm install hexo-cli -g
$ hexo init blog
$ cd blog
$ npm install
$ hexo server
```
# 简写
```hexo
hexo n "我的博客" == hexo new "我的博客" #新建文章
hexo p == hexo publish
hexo g == hexo generate#生成
hexo s == hexo server #启动服务预览
hexo d == hexo deploy#部署
```


