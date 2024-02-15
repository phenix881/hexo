---
title: PH BLOG 搭建日志
tags:
    - "github"
    - "网站搭建"
    - "Hexo"
    - "CDN"
categories:
    - "随记"
---
记录本站建立的日志，供参考交流

## Git部分
>[github官方文档](https://pages.github.com/)

### ssh key
```
ssh-keygen -t rsa -C "xxx@xxx.com"
cat /root/.ssh/id_rsa.pub
//github后台添加key以后,提交前验证
ssh -vT git@github.com
```

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

## Hexo安装
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

## 自定义域名
>一共两条记录
- 在域名提供商，添加A记录，对应项目ip地址
- 添加CNAME记录，对应repository的二级域名（如果使用二级域名，该域名和项目的name是一致的即可）

## CDN
- 选择合适的cdn进行加速，代理，这里需要根据cdn服务商提示，修改域名本身的dns（这里的修改在域名提供商的网站修改）
- 由于github是强制https，所以在cdn中要勾选https，也就是ssl这一项，并选择"Full (strict)"模式，否则可能出现无限重定向导致无法访问


## 搭建过程一些小问题
- github上传以后，项目的编译解析过程需要等待，不会立即生效
- 更换域名后，仍然会跳转到之前的域名，等待一天也换不过来，所以就删掉repository重建了一个
- 一定要核对github对应项目的repository的setting中，pages显示的访问地址，所对应的ip地址，这里会变换地址，比如108，109这种，如果ip地址变换了，我们在自己的域名解析A记录未能正确对应的话，那么域名会无法访问到你的项目