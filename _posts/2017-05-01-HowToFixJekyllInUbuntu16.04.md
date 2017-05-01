---
layout: post
title: Ubuntu16.04 安装Jekyll时报错及处理方法
categories: [cate1, cate2]
description: some word here
keywords: keyword1, keyword2
---

# 安装Jekyll时报错及处理方法

1.  执行```gem install bundler```提示权限不够
```
ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /var/lib/gems/2.3.0 directory.
```
解决办法: 使用sudo执行该命令
```
sudo gem install bundler
```

2. 
