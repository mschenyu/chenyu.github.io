---
title: hexo踩坑
date: 2020-07-08 16:03:57
tags:
---

## hexo deploy默认使用的是全局的user.name和user.email
如果你全局的git config配的是公司的信息，就得注意了，就算改了当前项目的user信息，它在push的时候也带的是全局的。怎么改呢？  
可以在_config.yml里面设置，hexo d 的时候会自动push代码到相应的repo  
```
deploy:
  type: git
  repo: <repository url>
  branch: [branch]
  name: [git user]
  email: [git email]
```

## 在`.deploy_git`目录已经生成的情况下，如果不小心把它删了，原来所有的提交记录全都没有了  
再执行一次`hexo d` 会生产一个磨默认的"First commit"和你hexo d的那个

## 提交源码可以拉一个source分支，master用于放hexo生成的文件