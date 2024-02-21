---
title: docker基础及各种镜像使用
date: 2024-02-01 9:41:05
tags:
    - "docker"
categories:
    - "工具教程"
---
docker相关的常用命令及各种镜像使用

# docker 
>建议使用linux系统安装docker，如果使用windows则需要bios开启虚拟化，cpu为64位，系统安装hyper-v的组件  
- 网上资料解释：  
Docker 底层的核心技术包括 Linux 上的名字空间（ Namespaces） 、 控制组（ Control groups） 、 Union 文
件系统（ Union file systems） 和容器格式（ Container format） 。  
我们知道， 传统的虚拟机通过在宿主主机中运行 hypervisor 来模拟一整套完整的硬件环境提供给虚拟机的
操作系统。 虚拟机系统看到的环境是可限制的， 也是彼此隔离的。 这种直接的做法实现了对资源最完整的
封装， 但很多时候往往意味着系统资源的浪费。   
例如， 以宿主机和虚拟机系统都为 Linux 系统为例， 虚拟机中运行的应用其实可以利用宿主机系统中的运行环境。  
我们知道， 在操作系统中， 包括内核、 文件系统、 网络、 PID、 UID、 IPC、 内存、 硬盘、 CPU 等等， 所有
的资源都是应用进程直接共享的。 要想实现虚拟化， 除了要实现对内存、 CPU、 网络IO、 硬盘IO、 存储空
间等的限制外， 还要实现文件系统、 网络、 PID、 UID、 IPC等等的相互隔离。 前者相对容易实现一些， 后
者则需要宿主机系统的深入支持。  
随着 Linux 系统对于名字空间功能的完善实现， 程序员已经可以实现上面的所有需求， 让某些进程在彼此
隔离的名字空间中运行。 大家虽然都共用一个内核和某些运行时环境（ 例如一些系统命令和系统库） ， 但
是彼此却看不到， 都以为系统中只有自己的存在。 这种机制就是容器（ Container） ， 利用名字空间来做权
限的隔离控制， 利用 cgroups 来做资源分配。
## 安装
### linux
- 安装组件：`sudo apt install curl`
- 阿里云镜像安装
  - 下载路径：`sudo curl -fsSL https://get.docker.com | bash -s` `docker --mirror Aliyun`
- 官方安装
  - `$ curl -fsSL https://get.docker.com -o get-docker.sh`
  - `$ sudo sh get-docker.sh`  
如果提示`无法定位软件包 docker-compose-plugin`，则update或者更换源
- 下载完成后查看一下版本信息
`docker -v`
- Debian下可直接通过apt安装  
`apt install docker.io`  

- Ubuntu下离线安装  

```
1. 在联网的环境中拉取Docker的安装包和镜像
bash
curl -fsSL get.docker.com -o get-docker.sh
sudo sh get-docker.sh 
docker pull ubuntu:16.04  # 拉取一个示例镜像用于测试
2. 导出刚拉取的Docker安装包、镜像和其他依赖
bash
\\导出Docker安装包
sudo tar -cvf docker.tar /usr/bin/docker /usr/share/doc/docker*
\\导出Docker镜像
docker save -o ubuntu.tar ubuntu:16.04  
\\导出依赖
sudo tar -cvf docker-deps.tar /var/lib/docker  /var/run/docker.sock /etc/docker
3. 将导出的 docker.tar、ubuntu.tar和docker-deps.tar复制到离线环境
4. 在离线环境中安装Docker
bash
\\解压依赖
sudo tar -xvf docker-deps.tar
\\安装Docker
sudo tar -xvf docker.tar -C /
\\导入镜像 
docker load -i ubuntu.tar
5. 验证安装是否成功
bash
docker version   # 验证Docker版本
docker run -it ubuntu:16.04 /bin/bash  # 运行导出的ubuntu镜像
如果能成功运行容器并进到bash,则证明Docker已成功安装。
需要注意的是:
1) 导出和导入的镜像是架构相关的,比如amd64架构的镜像无法在arm64架构的机器上运行。
2) 如果Docker有更新版本,则需要重新进行离线部署的步骤拉取最新版本的安装包和依赖。
3) 可以导出更多的依赖组件,比如 containerd、runc 等,以确保离线环境有更完整的环境。
```

-windows下，则下载官方桌面端，双击安装。记得先打开系统的虚拟化组件。
    >vbox虚拟机和docker，或者wsl只能用一个

    管理员身份启动cmd  
    关闭  
    `bcdedit /set hypervisorlaunchtype off`  
    开启  
    `bcdedit /set hypervisorlaunchtype auto`  
    关闭虚拟机平台，vbox可启动  
    打开的化，vbox无法使用，可使用windos本身的虚拟机
### windows
- 在docker官网直接下载dockers desktop
- 如果没有hyper支持，可以用旧版本的docker toolbox，在[阿里云镜像站docker页面](https://mirrors.aliyun.com/docker-toolbox/windows/docker-toolbox/?spm=a2c6h.25603864.0.0.298a2767ua94JZ),这个是一组套件，相当于在vbox虚拟机里运行docker。
### 配置加速器
```
针对安装了Docker Toolbox的用户，您可以参考以下配置步骤：
创建一台安装有Docker环境的Linux虚拟机，指定机器名称为default，同时配置Docker加速器地址。
docker-machine create --engine-registry-mirror=https://esmp74oa.mirror.aliyuncs.com -d virtualbox default
查看机器的环境配置，并配置到本地，并通过Docker客户端访问Docker服务。
docker-machine env default
eval "$(docker-machine env default)"
docker info
针对安装了Docker for Windows的用户，您可以参考以下配置步骤：
在系统右下角托盘图标内右键菜单选择 Settings，打开配置窗口后左侧导航菜单选择 Docker Daemon。编辑窗口内的JSON串，填写下方加速器地址：
{
  "registry-mirrors": ["https://esmp74oa.mirror.aliyuncs.com"]
}
编辑完成后点击 Apply 保存按钮，等待Docker重启并应用配置的镜像加速器。  
linux环境下，编辑配置文件/etc/docker/daemon.json
```
>start working→
## 使用
### jenkins
```
docker pull jenkins/jenkins
sudo mkdir /var/jenkins_home/
sudo chmod 777  /usr/jenkins_home/
docker run -u root -it --name jenkins -p 8080:8080 -p 50000:50000 -v /usr/jenkins_home:/jenkins_home -d jenkins/jenkins
```

### net6
- 镜像创建
  - Dockfile
```
#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 1888
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src
COPY . .
ENTRYPOINT ["dotnet", "XXXX.dll"]
```
  - publish
发布选项对应平台记得匹配docker的系统，linux或者win
  - 进入发布好的目录
命令行，执行`docker build -t myimagename .`
- 启动
这里主要，最好把静态文件和配置文件映射出来方便修改，还有端口
`docker run -itd --name myname-p myport:1802 -v myfile/appsettings.Production.json:/src/appsettings.Production.json --network bridge diyname /bin/bash`
- 网络
面的命令网络选择为网桥，所以会有一个虚拟网络地址转换。如何要实现和宿主机或者其他容器的通讯，首先在命令界面`ipconfig /all`查出本机虚拟网卡的实际地址，如果win系统开启了虚拟化，则虚拟网卡名称为`Hyper-V Virtual Ethernet Adapter`,这个就是宿主机的虚拟网络中的地址。因为我的mysql是装在宿主机，所以容器连接需要使用该地址。
- mysql授权
```
mysql> use mysql;
Database changed
mysql> update user set host = '%' where user = 'root';
Query OK, 1 row affected (0.02 sec)
Rows matched: 1  Changed: 1  Warnings: 0
mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.01 sec)
```
- 遇到问题  
部分页面打开报错，提示找不到Views下的Index，这里主要的原因时因为，页面名称为小写的i，index的都打不开，但在win系统下是可以的。

### ghost
[ghost官网](https://hub.docker.com/_/ghost/)
>nodejs轻量级博客系统  
```
docker pull ghost
$ docker run -d --name some-ghost -e NODE_ENV=development -e url=http://localhost:3001 -p 3001:2368 ghost
$ docker exec <container-id> node --version
```

### wikiJS
[Docker | Wiki.js](http://requarks.io)  
[requarks/wiki - Docker Image | Docker Hub](https://hub.docker.com/r/requarks/wiki)
```
#这里之前错误的拉成了arm的版本，导致无法启动，可以不填具体版本，默认就行
docker pull requarks/wiki
#wiki
#8080为映射的本地端口
docker run -d -p 8080:3000 --name wiki --restart unless-stopped -e "DB_TYPE=mysql" -e "DB_HOST=192.168.8.103" -e "DB_PORT=3308" -e "DB_USER=root" -e "DB_PASS=root" -e "DB_NAME=wiki" 3065654519c8
```

### aria2

```
oldiy/aria2-ui-ng
#Aria2+WebUI+AriaNG+FilesWeb-Docker
docker run -d --name aria2 -p 80:80 -p 6800:6800 -p 443:443 
-p 81:81 -p 8080:8080 -e SECRET=oldiy 
-v /your/data:/data -v /your/conf:/conf oldiy/aria2-ui-ng:latest
//实测
docker run -d --name aria2 -p 14153:80 -p 6800:6800 
-p 14152:443 -p 14151:81 -p 14150:8080 
-e SECRET=oldiy 
-v /volume2/3t-Data/aria2-data:/data oldiy/aria2-ui-ng:latest
```

### tomcat
```
$ sudo docker run -itd ubuntu:14.04 /bin/bash  
docker run -d -p 58080:8080 -v /root/webapps/:/opt/tomcat/webapps/ --name mytomcat_1 mytomcat:1.0 
docker run -d -p 8080:8080 tomcat（镜像名）
docker run <相关参数> <镜像 ID> <初始命令>
docker run -i -t -v /root/software/:/mnt/software/ e934 /bin/bash　
```
-i：表示以“交互模式”运行容器
-t：表示容器启动后会进入其命令行
-v：表示需要将本地哪个目录挂载到容器中，格式：-v <宿主机目录>:<容器目录>
### mysql
- arm版本：`docker pull arm64v8/mysql`
- 普通版：`docker pull mysql:8.0.31`  

```
# docker run -itd --name mysql
 -restart=always -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
#mysql8
#官方启动代码
docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag
#自定义配置文件启动
docker run --name mysql8 -p 3308:3306 --restart=always  -v /localdisk/mysql8/my.cnf:/etc/mysql/my.cnf -e MYSQL_ROOT_PASSWORD=111111 -d mysql:8 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
#映射目录出现未授权建立文件的情况
docker run -d --name mysql8 -p 3306:3306 --restart=always -e MYSQL_ROOT_PASSWORD=11111  --privilege
d=true 70325c69f1fe
#远程连接授权
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%';   #修改权限
#Mysql 8.0的加密规则变了,在Mysql 8.0以前，加密规则为mysql_native_password，Mysql8.0以后，加密规则为caching_sha2_password caching_sha2_password是一种新的加密方式，提供了比mysql_native_password插件更安全的加密方式
#查看默认加密方式 
show variables like '%authen%'
#为root修改加密方式
alter user 'root'@'%' identified with mysql_native_password by 'root';
```
### kali
```
docker run -itd --name kali 01eef62a8b9b  --restart=always /bin/bash
--privileged=true  //获得最高权限，可以挂载smb、
//支持中文，支持smb，支持自动启动 it是进入交互模式，d则是后台运行，attach可进
 docker run -itd --name kali 
 --privileged=true 
 --restart=always 616f66e03c6b env LANG=C.UTF-8  /bin/bash
```
### awtrix2
>(指定环境变量，时区)  
```
docker run -d --name awtrix2  --restart always  \
 -p 8000:8000   -p 8001:8001  -v /volume2/3t-Data/awtrix2:/data \
  -e TZ=Asia/Shanghai whyet/awtrix2
#202311更新,关闭自动更新，避免出现因为更新需要下载jar包无法连接导致启动不了的情况
docker run --name awtrix2 -p 7000:7000 -p 7001:7001 -p 5568:5568/udp --restart always -e TZ=Asia/Shanghai -e AUTOUPDATE=false  -v /myfiles:/data  -d whyet/awtrix2:latest
```
### cloudnas/clouddrive
```
docker run -d  --name clouddrive  --restart unless-stopped  -p 9798:9798  cloudnas/clouddrive
//挂载点
/CloudNAS/CloudDrive
Before run
Clouddrive uses fuse to mount cloud storages, to enable fuse in docker container and share fuse mounts to host, one of the following options should be set in host:
Option 1: enable MountFlags in docker service
#mkdir -p /etc/systemd/system/docker.service.d/
#cat <<EOF > /etc/systemd/system/docker.service.d/clear_mount_propagation_flags.conf
[Service]
MountFlags=shared
EOF
Option 2: enable shared mount option for mapped volume in host
#mount --make-shared <volume contains the path to accept cloud mounts>
docker run -d \
      --name clouddrive \
      --restart unless-stopped \
      -v <path to accept cloud mounts>:/CloudNAS:shared \
      -v <path to app data>:/Config \
      -v <other local shared path>:/media:shared \
      -p 9798:9798 \
     --privileged \
     --device /dev/fuse:/dev/fuse \
     cloudnas/clouddrive
```
### phpzender
`docker run -d -p 803:10081 --name phpzender2 php-zendserver:9.0-php7`
### xampp
```
docker run -d -p 805:80 --name xampp 
-v /root/www/:/opt/lampp/htdocs xampp:7
```
### openauth
- 根据自制的镜像启动
- 在项目目录下新建Dockerfile，然后运行docker build，生成image
```
FROM mcr.microsoft.com/dotnet/core/sdk:3.1-alpine
COPY . /publish
WORKDIR /publish
EXPOSE 6000
CMD ["dotnet", "OpenAuth.App.dll", "--server.urls", "http://*:60000"]:q
docker run -d  -v /root/publish/:/publish/ 
--name openauth -p 6000:6000 openauth:v1
```
>要重新打tag的话，语法是：
`docker tag imageId repository:newTag`

## 常用命令
### 清除所有运行的容器包括镜像  
`docker system prune -a -f`

### 启动容器支持中文  
`docker run -i -t mysql env LANG=C.UTF-8 /bin/bash`

- 创建一个守护态的Docker容器
`$ sudo docker run -itd ubuntu:14.04 /bin/bash`

- 进入一个已经在运行的容器
```
$ sudo docker ps
$ sudo docker exec -it 775c7c9ee1e1 /bin/bash
$ sudo docker exec -it 775c7c9ee1e1 /bin/sh
```
>如果容器本身以bash建立，直接用attach进入，不是则exec

### 创建一个容器，退出时删除  
`$ docker run --rm <container_id>`

### 暂停和恢复
```
docker pause
docker unpause
```
### 修改参数
```
//docker container update --restart=always
docker update kali --restart always
docker container update --restart=always kali 
```
### 保存新的镜像
将容器a404c6c174a2 保存为新的镜像,并添加提交人信息和说明信息。

```
  docker commit -a "runoob.com" -m "my apache" a404c6c174a2 mymysql:v1
  -a :提交的镜像作者；  
  -c :使用Dockerfile指令来创建镜像；  
  -m :提交时的说明文字；  
  -p :在commit时，将容器暂停。
```

- 导出  
  `docker save ae513a47849c > nginx-save.tar`

### 提交到docker hub
```
//登录后，需要在docker仓库先creat
docker login
docker push XXXX/kali
docker pull XXXX/kali:latest
```

### 查询docker日志的存储目录
```
docker inspect container_ID | grep json.log`
docker inspect container_name | grep json.log
```
### 清docker日志
`cat /dev/null > /var/log/…log-json.log`

### 查询并取消容器挂载位置
```
cat /proc/mounts |grep "docker" |grep "caf8ef20f3c1"
#umount /data/sys/var/docker/containers/caf8ef20f3c1c78f03a5844ee23abc1d7e44246f242292040f1ef288899d0cb8/secrets
# umount /data/sys/var/docker/containers/caf8ef20f3c1c78f03a5844ee23abc1d7e44246f242292040f1ef288899d0cb8/shm
```

- 进行安装htop工具
`$:sudo apt-get install htop`

  - 安装后使用$:htop命令可以更直观查看CPU使用信息
- 防火墙
  - 查看防火墙状态  
sudo ufw status
  - 开启防火墙  
sudo ufw enable
  - 关闭防火墙  
sudo ufw disable
  - 防火墙没问题可能就是80端口没开,执行:  
iptables -I INPUT -p tcp --dport 36572 -j ACCEPT

    - 1、清空iptables所有配置（新系统操作）
sudo iptables -F  
sudo iptables -X  
sudo iptables -Z  

    - 2、放行SSH远程22端口及lookback规则:  
sudo iptables -A INPUT -i lo -j ACCEPT  
sudo iptables -A OUTPUT -o lo -j ACCEPT  
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT  

    - 3、配置默认动作为拒绝,只允许流出数据：  
sudo iptables --policy OUTPUT ACCEPT  
sudo iptables --policy FORWARD DROP  
sudo iptables -P INPUT DROP  
    >操作这部之前，请确保放行了ssh或者其他远程端口.

    - 4、配置放行的关联规则包
  ```
    sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
    sudo iptables -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
   ```
    - 5、Ubuntu保存iptables配置与开机启动(root账号下)
    >如果是普通用户，请切换到root
  ```
  super@super:~$ su - root
  Password: 
  root@super:~# iptables-save > /etc/iptables-rules  # 保存配置
  ```

  - 5.1、ubuntu配置规则开机自启动(可以普通账号下)
  
    ```
    super@super:~$ more /etc/rc.local   
    #请先配置rc.local服务自启动
    #!/bin/bash
    #rc.local启动
    ```

- iptables配置恢复,默认重启系统iptables配置丢失.
```
iptables-restore < /etc/iptables-rules
exit 0
```

### Docker 镜像和容器的导入导出
- 镜像的导出和导入
- 镜像的保存

```
[root@k8s-master ~]# docker images
REPOSITORY                                        TAG                 IMAGE ID            CREATED             SIZE
nginx                                             latest              ae513a47849c        2 months ago        109MB
debian                                            jessie              4eb8376dc2a3        2 months ago        127MB
rabbitmq                                          3.6.8               8cdcbee37f62        15 months ago       179MB
[root@k8s-master tmp]# docker save ae513a47849c > nginx-save.tar
[root@k8s-master tmp]# ls -lh
total 108M
-rw-r--r-- 1 root root 108M Jul  4 09:32 nginx-save.tar
```

- 第二种写法：
`docker save -o nginx-save.tar ae513a47849c`
- 2.镜像的导入
  - 可以将导出的nginx-save.tar包传到需要的docker主机上面，然后执行导入命令.  

```
[root@k8s-master tmp]# ls -lh
total 108M
-rw-r--r-- 1 root root 108M Jul  4 09:32 nginx-save.tar
[root@k8s-master tmp]# docker load < nginx-save.tar 
82b81d779f83: Loading layer [==================================================>]  54.21MB/54.21MB
7ab428981537: Loading layer [==================================================>]  3.584kB/3.584kB
Loaded image ID: sha256:ae513a47849c895a155ddfb868d6ba247f60240ec8495482eca74c4a2c13a881
```

- 第三种写法：
`docker load -i nginx-save.tar`

### 容器的导出和导入
- 1.容器的导出
```
[root@k8s-master tmp]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
220aee82cfea        tomcat:7            "catalina.sh run"   9 seconds ago       Up 7 seconds        8080/tcp            tomcat7
docker export -o mysql-`date +%Y%m%d`.tar 220aee82cfea
```
- 2.容器的导入
`docker import  my_ubuntu_v3.tar runoob/ubuntu:v4  `

- 镜像和容器 导出和导入的区别:  
  - 1.镜像导入 是复制的过程
  - 2.容器导入 是将当前容器 变成一个新的镜像
  - save 和 export区别：
    - 1）save 保存镜像所有的信息-包含历史
    - 2）export 只导出当前的信息

  - docker save images_name：  
    - 将一个镜像导出为文件，再使用docker load命令将文件导入为一个镜像，会保存该镜像的的所有历史记录。比docker export命令导出的文件大，很好理解，因为会保存镜像的所有历史记录。  
    - docker export container_id：  
      - 将一个容器导出为文件，再使用docker import命令将容器导入成为一个新的镜像，但是相比docker save命令，容器文件会丢失所有元数据和历史记录，仅保存容器当时的状态，相当于虚拟机快照。

- 普通启动的容器
```
runoob@runoob:~$ docker run ubuntu:15.10 /bin/echo "Hello world"
Hello world
```

>各个参数解析：

- docker: Docker 的二进制执行文件。

- run:与前面的 docker 组合来运行一个容器。

- ubuntu:15.10指定要运行的镜像，Docker首先从本地主机上查找镜像是否存在，如果不存在，Docker 就会从镜像仓库 Docker Hub 下载公共镜像。

- /bin/echo "Hello world": 在启动的容器里执行的命令

- 运行交互式的容器  
我们通过docker的两个参数 -i -t，让docker运行的容器实现"对话"的能力
```
runoob@runoob:~$ docker run -i -t ubuntu:15.10 /bin/bash
root@dc0050c79503:/#
```
>各个参数解析：

-t:在新容器内指定一个伪终端或终端。

-i:允许你对容器内的标准输入 (STDIN) 进行交互。

启动容器（后台模式）
使用以下命令创建一个以进程方式运行的容器
```
runoob@runoob:~$ docker run -d ubuntu:15.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"
2b1b7a428627c51ab8810d541d759f072b4fc75487eed05812646b8534a2fe63
```
- 首先，我们需要确认容器有在运行，可以通过 docker ps 来查看

`runoob@runoob:~$ docker ps`

CONTAINER ID:容器ID

NAMES:自动分配的容器名称

在容器内使用docker logs命令，查看容器内的标准输出

`runoob@runoob:~$ docker logs 2b1b7a428627`  


### 构建镜像
我们使用命令 docker build ， 从零开始来创建一个新的镜像。为此，我们需要创建一个 Dockerfile 文件，其中包含一组指令来告诉 Docker 如何构建我们的镜像。
```
runoob@runoob:~$ cat Dockerfile 
FROM    centos:6.7
MAINTAINER      Fisher "fisher@sudops.com"
RUN     /bin/echo 'root:123456' |chpasswd
RUN     useradd runoob
RUN     /bin/echo 'runoob:123456' |chpasswd
RUN     /bin/echo -e "LANG=\"en_US.UTF-8\"" >/etc/default/local
EXPOSE  22
EXPOSE  80
CMD     /usr/sbin/sshd -D
```
每一个指令都会在镜像上创建一个新的层，每一个指令的前缀都必须是大写的。
第一条FROM，指定使用哪个镜像源
RUN 指令告诉docker 在镜像内执行命令，安装了什么。。。
然后，我们使用 Dockerfile 文件，通过 docker build 命令来构建一个镜像。
```
runoob@runoob:~$ docker build -t runoob/centos:6.7 .
Sending build context to Docker daemon 17.92 kB
Step 1 : FROM centos:6.7
 ---&gt; d95b5ca17cc3
Step 2 : MAINTAINER Fisher "fisher@sudops.com"
 ---&gt; Using cache
 ---&gt; 0c92299c6f03
Step 3 : RUN /bin/echo 'root:123456' |chpasswd
 ---&gt; Using cache
 ---&gt; 0397ce2fbd0a
Step 4 : RUN useradd runoob
```
>参数说明：

-t ：指定要创建的目标镜像名

. ：Dockerfile 文件所在目录，可以指定Dockerfile 的绝对路径

使用docker images 查看创建的镜像已经在列表中存在,镜像ID为860c279d2fec

runoob@runoob:~$ docker images 
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE

我们可以使用新的镜像来创建容器

runoob@runoob:~$ docker run -t -i runoob/centos:6.7  /bin/bash
[root@41c28d18b5fb /]# id runoob
uid=500(runoob) gid=500(runoob) groups=500(runoob)
从上面看到新镜像已经包含我们创建的用户runoob

网络端口映射
我们创建了一个 python 应用的容器。
```
runoob@runoob:~$ docker run -d -P training/webapp python app.py
fce072cc88cee71b1cdceb57c2821d054a4a59f67da6b416fceb5593f059fc6d
```
另外，我们可以指定容器绑定的网络地址，比如绑定 127.0.0.1。

我们使用 -P 参数创建一个容器，使用 docker ps 来看到端口5000绑定主机端口32768。

另外，我们可以指定容器绑定的网络地址，比如绑定127.0.0.1。
```
runoob@runoob:~$ docker run -d -p 127.0.0.1:5001:5000 training/webapp python app.py
95c6ceef88ca3e71eaf303c2833fd6701d8d1b2572b5613b5a932dfdfe8a857c
```
这样我们就可以通过访问127.0.0.1:5001来访问容器的5000端口。

上面的例子中，默认都是绑定 tcp 端口，如果要绑定 UDP 端口，可以在端口后面加上 /udp。
```
runoob@runoob:~$ docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py
6779686f06f6204579c1d655dd8b2b31e8e809b245a97b2d3a8e35abe9dcd22a
```
docker port 命令可以让我们快捷地查看端口的绑定情况。
```
runoob@runoob:~$ docker port adoring_stonebraker 5000
127.0.0.1:5001
```
### 容器连接
端口映射并不是唯一把 docker 连接到另一个容器的方法。

docker有一个连接系统允许将多个容器连接在一起，共享连接信息。

docker连接会创建一个父子关系，其中父容器可以看到子容器的信息。

容器命名
当我们创建一个容器的时候，docker会自动对它进行命名。另外，我们也可以使用--name标识来命名容器，例如：

runoob@runoob:~$  docker run -d -P --name runoob training/webapp python app.py
43780a6eabaaf14e590b6e849235c75f3012995403f97749775e38436db9a441
我们可以使用 docker ps 命令来查看容器名称。
```
runoob@runoob:~$ docker ps -l
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                     NAMES
43780a6eabaa        training/webapp     "python app.py"     3 minutes ago       Up 3 minutes        0.0.0.0:32769->5000/tcp   runoob
```

- Docker文件目录和容器内部操作
- Docker默认的文件目录位于Linux server的/var/lib/docker 下面。目录结构如下

|-----containers：用于存储容器信息
|-----image：用来存储镜像中间件及本身信息，大小，依赖信息
|-----network
|-----swarm
|-----tmp：docker临时目录
|-----trust：docker信任目录
|-----volumes：docker卷目录

>还可以通过docker指令确认文件位置：
`docker info`
 
### 查看某个容器的文件目录
 docker exec 容器name ls

### 文件互相拷贝

- 将本地文件拷贝到docker 镜像内
```
 docker cp 本地路径 容器Id或name:容器目录
 docker cp /Users/howey/Documents/apache-maven-3.5.2/ containername:/opt
```
 
- 将docker内文件拷贝到本地文件夹内
```
  docker cp 容器Id或name:本地路径 镜像路径
  docker cp containername:/Users/howey/Documents/apache-maven-3.5.2 /opt/
```

>如何进入容器内部呢：使用docker exec可以进入Docker容器并运行指令  
```
#docker exec 在运行的容器中执行命令
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```
OPTIONS说明：  
-d :分离模式: 在后台运行  
-i :即使没有附加也保持STDIN 打开  
-t :分配一个伪终端  
通过 exec 命令对指定的容器执行 bash:    
`docker exec -it 容器ID或name  bash`  
>-it : 目前的理解浅薄,就是要等在容器内的命令执行完毕才会出来到当前操作; 没有-it的加 就相当于在容器内执行一下命令,不等容器内部是否执行完毕直接出来.
案例：在容器内创建sqlserver目录，如下所示，安装sqlserver后实际上已经自动创建了data目录，映射的时候无需再次创建直接使用即可.

```
# docker exec -it  sqlserver bash
mssql@649b4b891718:/$ mkdir /var/opt/mssql/data
mkdir: cannot create directory '/var/opt/mssql/data': File exists
mssql@649b4b891718:/$  ls  #查看目录列表
bin  boot  dev    etc  home  lib    lib64  media  mnt  opt    proc  root  run  sbin  srv  sys  tmp  usr  var
mssql@649b4b891718:/$ cd /var/opt #转到opt目录
mssql@649b4b891718:/var/opt$ ls #查看opt目录
mssql
mssql@649b4b891718:/var/opt$ cd mssql 
mssql@649b4b891718:/var/opt/mssql$ ls
data  log  secrets
mssql@649b4b891718:/var/opt/mssql$ 
```