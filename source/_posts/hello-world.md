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
git git branch -M main
git remote add origin git@github.com:XXXXX/docs.git
git push --set-upstream origin master //推送到master分支
git push -u origin main 将 main 分支 push 到 GitHub
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
安装官方文档操作步骤即可，[https://hexo.io/zh-cn/docs/](https://hexo.io/zh-cn/docs/)

## theme安装
>[官方文档](https://redefine-docs.ohevan.com/getting-started)
```
//安装
npm install hexo-theme-redefine@latest
//启用
_config.yml
theme: redefine
//创建主题配置文件
在 Hexo 根目录下创建 _config.redefine.yml 文件。
并将主题下的config文件复制进去的所有内容复制进去。
本文件会自动覆盖主题的配置项，创建本文件的目的是为了方便你在升级主题时，不会丢失你的配置。
```