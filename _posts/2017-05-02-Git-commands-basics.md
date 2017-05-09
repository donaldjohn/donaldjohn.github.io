---
layout: post
title: Git 基础命令
categories: Git
description: Git 基础命令
keywords: Git, Git入门，Git基础
---
Git常用指令速查目录，根据廖海峰教程整理


**目录**

* TOC
{:toc}


#### 1. 初始化####

```
$ git init
```
#### 2. 添加文件####

```
$ git add readme.txt
```
#### 3. 提交变更 ####

```
git commit -m "wrote a readme file"
```
#### 4. 查看当前状态 ####

```
$ git status
```

#### 5. 查看修改内容 ####

```
$ git diff
```

#### 6. 查看历史内容####

```
$ git log
```

#### 7. 版本回溯 ####

在Git中，用HEAD表示当前版本，也就是最新的提交3628164...882e1e0（注意我的提交ID和你的肯定不一样），上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。

回溯到上个版本
```
$ git reset --hard HEAD^
```
回溯到指定版本
```
$ git reset --hard 3628164
```
版本号没必要写全，前几位就可以了，Git会自动去找。当然也不能只写前一两位，因为Git可能会找到多个版本号，就无法确定是哪一个了。
#### 8. 查看命令历史 ####

```
$ git reflog
```
#### 9. 恢复文件状态到最近一次的commit或add ####

```
$ git checkout -- readme.txt
```

#### 10. 重置已Add内容 ####

```
$ git reset HEAD readme.txt
```

#### 11. 删除文件 ####

```
$ git rm test.txt
```
恢复误删文件
```
$ git checkout -- test.txt
```

#### 12. 添加远程库 ####

```
$ git remote add origin git@github.com:michaelliao/learngit.git
```

#### 13. 提交 本地内容到远程库 ####
第一次提交， -u参数设置upstream， 以后可使用简化的git push指令
```
git push -u origin master
```
#### 14. 克隆远程库 ####

```
$ git clone git@github.com:donaldjohn/gitskills.git
```

#### 15. 创建并切换到分支 ####

```
$ git checkout -b dev
```

#### 16. 切换分支 ####

```
$ git checkout master
```

#### 17. 合并分支 ####
将制定分支合并到当前分支
```
$ git merge dev
```

#### 18. 删除分支 ####

```
$ git branch -d dev
```

#### 19. 查看分支的合并情况 ####

```
$ git log --graph --pretty=oneline --abbrev-commit
```

#### 20. 暂存当前工作区状态 ####

```
$ git stash
```

#### 21. 查看缓存记录 ####

```
$ git stash lis
```

#### 22. 恢复暂存内容 ####
第一种方式：
```
$ git stash apply
$ git stash drop
```
第二种方式：
```
$ git stash pop
```

#### 23. 查看远程库信息  ####

```
git remote
```

#### 24. 推送分支 ####

```
#推送到远程主分支
$ git push origin master
#推送到远程dev分支
$ push origin dev
```


#### 25. 创建远程分支到本地 ####

```
$ git checkout -b dev origin/dev
```

#### 26. 抓取分支内容 ####

```
$ git pull
```

#### 27. 创建标签 ####

```
$ git tag v1.0
#指定commit id
$ git tag v0.9 6224937
```
#### 28. 查看标签列表 ####

```
$ git tag
```

#### 29. 查看标签详情 ####

```
$ git show v0.9
```


#### 30. 删除标签 ####

```
$ git tag -d v0.1
```

#### 31. 推送标签 ####

```
#推送制定标签
$ git push origin v1.0
#推送全部标签
$ git push origin --tags
```

#### 32. 删除远程标签 ####

```
#删除本地标签
$ git tag -d v0.9
#删除远程标签$ git push origin :refs/tags/v0.9
```
