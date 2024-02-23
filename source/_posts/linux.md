---
title: Linux系统基础及软件使用
date: 2024-02-01 9:41:05
tags:
    - "Linux"
    - "Ubuntu"
categories:
    - "工具教程"
excerpt: "Linux系统基础及软件使用 知识干货的整理"
---

## Linux
>除了知识干货的整理，会穿擦很多操作的感悟和总结  
## 常用命令
- 更新
  ```
  apt-get update                  # 更新源  
  apt-get upgrade                 # 更新所有已安装的包  
  apt-get dist-upgrade            # 发行版升级
  ```
- 清空历史记录 \
`vi ~/.bash_history`  
`history -c`

- 系统版本升级  
`do-release-upgrade` 
>升级系统版本比如20.0升级到22.0   
>neofetch或类似工具来获取操作系统详细信息

- 查看监听端口  
`lsof -i -P -n | grep LISTEN `

## 安装
我自己在笔记本装了win和ubuntu的双系统，win里也有linux的虚拟机，从微软商店直接下载就行了，但是内核更新受限于wsl，其实它就是wsl的一个发布版本。  

另外，如果想用ARM系统的话，可以装在树莓派上，早以前我用树莓派3b装的kali学习网络安全，后来出了4b，性能好了很多，直接装ubuntu22带桌面的系统也可以跑得很流畅。

这里注意了，流畅的前提，选择一个速度更快的sd卡，或者把系统装在高速的U盘或者ssd当中，用sd卡做一个引导就可以了。ubuntu还是比树莓派本身的系统要耗性能。

具体安装过程，还没时间整理，先列一下，学习linux系统的几种安装方式
- 真机安装  
  这个是最麻烦的，但是装好以后比较好用。
- 树莓派
- vm虚拟机  
  可以直接在对应系统的官方下载vm的文件，导入即可使用
- wsl  
  微软商店搜索系统名称直接下载打开就行了
- 云主机  
  这里可以在服务器商直接购买便宜一些的云服，选择想使用的linux系统就可以了

## 内核升级
[linux官网](https://www.kernel.org/)  
[ubuntu官网](https://kernel.ubuntu.com/~kernel-ppa/mainline/)

#### 方案1
```
点击你所选择的 Linux 内核版本链接，找到你对应的架构（“Build for XXX”）的那部分。然后下载符合以下格式的两个文件（其中 X.Y.Z 是最高版本号）：
1. linux-image-X.Y.Z-generic-*.deb
2. linux-modules-X.Y.Z-generic-.deb
在终端中改变到文件所在的目录，然后执行此命令手动安装内核：
$ sudo dpkg --install *.deb
重启系统，使用新内核：
$ sudo reboot
检查是否如你所愿：
$ uname -r
```
 
#### 方案2
https://github.com/pimlie/ubuntu-mainline-kernel.sh

```
apt install wget
wget https://raw.githubusercontent.com/pimlie/ubuntu-mainline-kernel.sh/master/ubuntu-mainline-kernel.sh
chmod +x ubuntu-mainline-kernel.sh
sudo mv ubuntu-mainline-kernel.sh /usr/local/bin/
Install latest version:
 ~ $ sudo ubuntu-mainline-kernel.sh -i
#指定版本，注意是三个数字，如下
 ubuntu-mainline-kernel.sh -i v5.15.0
```
#### 方案3
#查看当前内核版本
`uname -r` \
#查看软件库中可下载的内核
`sudo apt list | grep linux-generic*`

#### 操作日志
*2022.09.03*  
升级了笔记ubuntu系统的内核，从5.15到5.19.使用方案三。启动以后遇到了sign的签名错误，根据提示，关闭了bios里的
boot secure选项。如果遇到lib版本的错误，要升级系统版本来解决依赖问题。同时，因为带桌面端，可借助软件更新器
清理不使用的内核包或者是软件。
至此，系统和内核均升级到了最新版本。

## 工具
### vim full版本
ubuntu默认安装的是vim tiny版本：  
$sudo apt-get remove vim-common  
$sudo apt-get install vim   

### 全局缩放
>登陆以后设置的缩放比例，重启后不生效，需要修改全局
```
cd cd /usr/share/glib-2.0/schemas
cd /usr/share/glib-2.0/schemas
vi  org.gnome.desktop.interface.gschema.xml
<key name="scaling-factor" type="u">
      <default>2</default>
      <summary>Window scaling factor</summary>
sudo glib-compile-schemas /usr/share/glib-2.0/schemas/
```

### scrapy
- 安装
  - 官网：https://scrapy.org/
  - `pip install scrapy`
- 创建项目和虫
```
scrapy startproject 项目名
cd 项目名 
scrapy genspider 虫名称 "域名" 
scrapy.cfg 文件：是虫项目的配置文件
//创建好spider以后脚本如下
import scrapy
class xxxSpider(scrapy.Spider):
    name = "xxx"
    allowed_domains = ["www.xxx.xx"]
    start_urls = ['http://xxxx.cn/']

    def parse(self, response):
        for selector in response.xpath("//div[@class='article']"):
            title=selector.xpath('./h2/a/text()').extract_first()
            url=selector.xpath('./h2/a/@href').extract_first()
            #yield {'title': title,'url':url}
            yield scrapy.Request(
                    url,
                    callback=self.parse_detail,
                    meta={"title": title}
                )            
    def parse_detail(self, response):
        title = response.meta["title"]
        # 获取详情页的内容、图片
        content = response.xpath("//div[@id='link-report-intra']/span/text()").extract_first()
        yield {'title': title,'content':content}  # 对返回的数据进行处理
#运行
scrapy runspider spiders/xxx.py
#排除常规日志
scrapy runspider --loglevel=ERROR spiders/xx.py 
#输出运行结果到excel
scrapy crawl xxspider-o data.csv
scrapy crawl xxspider-o data.json
```

### pip

```
#Requires the latest pip
pip install --upgrade pip
#Current stable release for CPU and GPU
pip install tensorflow
# r try the preview build (unstable)
pip install tf-nightly
# 罗列软件包的版本
pip list --outdated
#升级特定软件包
pip install package_name -U
```

### pnpm

```
#如果出现验证失败则临时去掉ssl验证
npm config set strict-ssl false
npm install -g pnpm
pnpm config set registry https://registry.npm.taobao.org/
#检查
pnpm config get registry
#查看ts-node的所有版本
pnpm view ts-node versions
#等价与npm i nodemon -g
pnpm add nodemon -g
#npm i
pnpm i
#查看依赖(全局)
pnpm list [-g]
#较好用的地方是: npm run dev/test/build
pnpm dev
#当然习惯npm的也可以用 pnpm run dev
#win下配置全局变量
pnpm config set store-dir "D:\Program Files\pnpm\storeDir" 
pnpm config set global-dir "D:\Program Files\pnpm\globalDir"
pnpm config set global-bin-dir "D:\Program Files\pnpm\globalBinDir"
pnpm config set state-dir "D:\Program Files\pnpm\state"
pnpm config set cache-dir "D:\Program Files\pnpm\cache"
```

### TensorFlow2
- Python 3.6–3.9
- Ubuntu 16.04 或更高版本
- docker
```
 docker pull tensorflow/tensorflow:latest  # Download latest stable image
 docker run -it -p 8888:8888 tensorflow/tensorflow:latest-jupyter  # Start Jupyter server 
```
- 教程学习 
  - [tensor官方,for js](https://www.tensorflow.org/js/tutorials)
  - [tensor官方中文站](https://tensorflow.google.cn/)

### nodejs
>NPM的全称是Node Package Manager，是一个NodeJS包管理和分发工具，已经成为了非官方的发布Node模块（包）的标准
Node.js发布于2009年5月，由Ryan Dahl开发，是一个基于 Chrome V8 引擎的 JavaScript 运行环境，是一个让 JavaScript 运行在服务端的开发平台

```
sudo apt install nodejs-legacy
sudo apt install npm
#菜鸟
sudo apt-get install nodejs
sudo apt-get install npm
node -v
#设置为淘宝镜像
npm config set registry https://registry.npm.taobao.org/
yarn config set registry https://registry.npm.taobao.org/
#查看当前镜像
npm get registry 
yarn config get registry
#淘宝镜像到期报错
npm config set strict-ssl false
```
>更换完以后清除缓存yarn cache clean,并删除项目下的yarn.lock文件

### SMB
### SSHD
敲入sshd，系统会提示安装的软件，也可输入以下命令直接安装  
`apt install openssh-server`

### nginx
>带https，多站点反向代理

### net7 net8
(https://learn.microsoft.com/zh-cn/dotnet/core/install/linux-ubuntu-2004#add-the-microsoft-package-repository)
```
mkdir net7
cd net7/
sudo apt-get update && sudo apt-get install -y dotnet-sdk-7.0
sudo apt-get update && sudo apt-get install -y aspnetcore-runtime-7.0
//如果提示找不到库，则先添加 Microsoft 包存储库再重新运行
wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
rm packages-microsoft-prod.deb
dotnet --info
```

### v2
> linux系统下服务端
- 可以直接安装工具（推荐，更省心）  
` bash <(curl -s -L https://git.io/v2[Delete]ray.sh)`
- 或者使用docker，自己配置
- 客户端一般在win系统，用git开源的core，带了一个N字头的UI
#### 常见问题
  - 经常出现无法连接网站，这个首先要在服务端查看日志，看看客户端是否连接上，如果连接上，可能是服务端的配置问题，如果直接用的工具，更新工具版本就行了，如果是自己的配置，查一下最近官方的消息推荐的配置更改。
  - 如果服务端没有客户端连接的消息，那么检查客户端的输出框报错提示进行解决  
  - 记一次问题解决  
      - 报错  
      `failed to listen on address: 127.0.0.1:10808 > listen tcp 127.0.0.1:10808: bind: An attempt was made to access a socket in a way forbidden by its access permissions.`
      - 解决  
         - 运行`netsh winsock reset`后，重启系统。（这些一般就是百度一下报错信息，就能找到解决方案了，win系统就容易出现这种奇奇怪怪的报错）
         - 接着如下`connection refused by remote machine`的报错，进入服务器查询监听端口，发现端口不在了，重新配置以后，重启服务，再回看客户端，已成功连接

## 系统相关
### dpkg
```
dpkg -i  microsoft-edge-stable 
dpkg -P --purge  microsoft-edge-stable 

```

### 查看cpu型号
`cat /proc/cpuinfo | grep 'model name' |uniq`

### 查看cpu温度
```
apt install install lm_sensors
sensors
```

### 磁盘测试
```
hdparm -Tt /dev/sda
/dev/sda:
Timing cached reads: 6676 MB in 2.00 seconds = 3340.18 MB/sec
Timing buffered disk reads: 218 MB in 3.11 seconds = 70.11 MB/sec
可以看到,2秒钟读取了6676MB的缓存,约合3340.18 MB/sec;
```
### 换源
>务必要更换对应的codename  
windows wsl ubuntu的codename为jammy（22），facal为20.0  
可用`lsb_release -a`查询  
https://mirrors.ustc.edu.cn/repogen/

```
vim /etc/apt/sources.list
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse

#kali官方源
deb http://http.kali.org/kali kali-rolling main non-free contrib

#中科大的源
deb http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
deb-src http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
deb http://mirrors.ustc.edu.cn/kali kali-rolling main contrib non-free
deb-src http://mirrors.ustc.edu.cn/kali kali-rolling main contrib non-free
deb http://mirrors.ustc.edu.cn/kali-security kali-current/updates main contrib non-free
deb-src http://mirrors.ustc.edu.cn/kali-security kali-current/updates main contrib non-free
```
#### 常见问题
- respi执行update报错`The repository 'http://http.re4son-kernel.com/re4son kali-pi InRelease' is not signed`  
更新key签名`apt-key adv --keyserver keys.gnupg.net --recv-keys 11764EE8AC24832F`可解决
