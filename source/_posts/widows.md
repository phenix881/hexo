---
title: windows系统基础及软件使用
date: 2024-02-01 9:41:05
tags:
    - "windows"
    - "windows11"
categories:
    - "工具教程"
---
windows系统基础及软件使用 知识干货的整理

# windows
## 常用命令
### 端口查询  
`netstat -ano |findstr "端口号"`
### 查询关闭进程
- `tasklist /fi "imagename eq nginx.exe"`
- `taskkill /F /PID 31904`
- `taskkill /F /IM nginx.exe`

## 工具
### powershell
>遇到执行策略的问题,自带程序终端的时候会报出"系统禁止脚本运行的错误"
```
管理员身份运行 window.powershell
get-ExecutionPolicy，显示Restricted
et-ExecutionPolicy -Scope CurrentUser
输入参数：RemoteSigned
```
### Ubuntu子系统
>手动安装
建好文件夹，在目录打开cmd
- `Invoke-WebRequest -Uri https://wsldownload.azureedge.net/Ubuntu_2004.2020.424.0_x64.appx -OutFile Ubuntu20.04.appx -UseBasicParsing`
- 然后执行
```
Rename-Item .\Ubuntu20.04.appx Ubuntu.zip
Expand-Archive .\Ubuntu.zip -Verbose
cd .\Ubuntu\
.\ubuntu2004.exe
```
>指定wsl默认系统为ubuntu
```
wsl --set-default Ubuntu-20.04
bash //运行
```

### NGINX
- `start nginx`
- `nginx.exe -s reload`

### HEIC图片浏览
>win10不能直接预览苹果手机的图片格式，所以...  
- [HEIF Image Extensions](https://apps.microsoft.com/store/detail/heif-image-extensions/9PMMSR1CGPWG?hl=en-us&gl=us)  
  这个扩展不太好用，安装以后对图片还是解析不出来
- Real HEIC File Viewer  
  不错，直接微软商店安装，免费
还试过一些别的软件，各种问题，感觉还是微软商店的软件靠谱些
### v2部署
服务端windows版本的文件拷贝到服务器，从客户端导出服务器的config配置，覆盖到服务端，然后通过cmd执行v2--.exe run

## 常见问题
### 时间无法同步
>windows11
设置》时间和日期》相关链接》附加时钟》选择标签Internet时间》点击按钮“更改时间设置”》服务器地址修改为：  
```
//填写后，点击立即更新，链接成功的即可用
// 阿里云
ntp1.aliyun.com
ntp2.aliyun.com
ntp3.aliyun.com
ntp4.aliyun.com
ntp5.aliyun.com
ntp6.aliyun.com
ntp7.aliyun.com

// 腾讯云
time1.cloud.tencent.com 
time2.cloud.tencent.com 
time3.cloud.tencent.com
time4.cloud.tencent.com
time5.cloud.tencent.com
```
