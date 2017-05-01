---
layout: post
title: Ubuntu16.04 安装Jekyll时报错及处理方法
categories: [Q&A]
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

2. 安装 json 错误
```
An error occurred while installing json (1.8.6), and Bundler cannot
continue.
Make sure that `gem install json -v '1.8.6'` succeeds before bundling.
```
解决办法 安装 ruby-dev
```
sudo apt-get install ruby-dev
```
3. 提示nokogiri安装错误
```
An error occurred while installing nokogiri (1.7.1), and Bundler cannot
continue.
Make sure that `gem install nokogiri -v '1.7.1'` succeeds before bundling.
```
解决办法: 安装lib-xml2, lib-xml2-dev库
```
sudo apt-get install lib-xml2 lib-xml2-dev
```
或者 安装 zlib1g-dev
```
sudo apt-get install zlib1g-dev
```

4. 提醒缺少JavaScript运行时
```
jekyll 3.4.3 | Error:  Could not find a JavaScript runtime. See https://github.com/rails/execjs for a list of available runtimes.
```
解决办法: 安装JavaScript运行时, 此处安装NodeJs

```

```
