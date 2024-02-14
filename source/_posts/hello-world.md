---
title: PH BLOG 搭建日志
tags:
    - "Hexo教程"
    - "github"
    - "网站部署"
categories:
    - "随记"
---
记录本站建立的日志，供参考交流

## Git部分
>[官方文档](https://pages.github.com/)re

### 基础语句

``` bash
ssh -vT xx@github.com
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:XXXXX/docs.git
git push -u origin main
git push -f //强制推送
```
### 修改文件

`cd /文件所在目录`
`git add 文件名`
同新增操作

### 删除文件夹
```
git rm -r css
git commit -m "delete"
git push
```
## gihub ssh key
```
ssh-keygen -t rsa -C "xxx@xxx.com"
cat /root/.ssh/id_rsa.pub
//github后台添加key以后,提交前验证
ssh -vT git@github.com
```

## hexo安装

## theme安装