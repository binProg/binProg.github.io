---
title: Docker笔记之基础篇
date: 2021-12-15 20:44:43
tags: Docker
mathjax: true
---



<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fwww.orphadata.org%2Fcgi-bin%2Fimg%2Fbody%2Fontologies%2Fdocker.png&refer=http%3A%2F%2Fwww.orphadata.org&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1641898195&t=63f4be05817a4cd9795000d4bb060979" style="zoom:100%">

 # Docker基础

## 一、基本组成

<img src="https://s4.ax1x.com/2021/12/15/Tp0WPU.png">

**镜像：**可以通过镜像来创建容器， app镜像===>run===>app容器（提供服务），一个镜像可以启动多个容器（分布式集群的搭建）

**容器：**Docker通过容器技术，独立运行一个或者一组应用。一个容器相当于一个简易的linux系统

**仓库：**存放镜像的地方，分为公有仓库和私有仓库

配置阿里云官方镜像加速器：

1. `sudo mkdir -p /etc/docker`

2. `sudo tee /etc/docker/daemon.json <<-'EOF'`

   `{`

   `"registry-mirrors": ["http://hd1esep4.mirror.aliyuncs.com"]`

   `}`

   `EOF`

3. `sudo systemctl daemon-reload`

4. `sudo systemctl restart docker`

## 二、docker的镜像run的流程图

<img src="https://s4.ax1x.com/2021/12/15/TpBERg.png">

## 三、Docker底层原理

Docker是一个CS结构的系统，Docker的守护进程运行在主机上，通过Socket从客户端访问。DockerServer接收到DockerClient的指令就会执行这个指令

<img src="https://s4.ax1x.com/2021/12/15/TpBAJS.png">

## 四、Docker常用命令

### 链接

https://docs.docker.com/engine/reference/commandline

---

### 镜像命令

------

**docker images**：查看本地主机上的镜像

**docker search**：搜索命令

**docker pull**：下载镜像

**docker rmi**： 删除镜像

---

### 容器命令

------

**docker run [可选参数] image** ：新建容器并启动

> 参数说明
>
> ​	--name="Name" 容器名字 xx01  xx02 用来区分容器
>
> ​	--d			     后台方式运行
>
> ​	--it			      使用交互方式运行，进入容器查看内容
>
> ​	-p                          指定容器的端口   -p 8080：8080
>
> - -p ip：主机端口：容器端口
> - -p 主机端口：容器端口**（常用）**
> - -p 容器端口

**docker ps**：查看运行中的容器

> -a 查看所有容器（正在运行和历史运行）
>
> -n=x 显示最近创建的 x个容器
>
> -q 只显示容器的编号

**exit**：停止并退出正在运行的容器

**Ctrl+P+Q**：不停止但退出正在运行的容器

**docker rm 容器id**：删除容器，正在运行的容器必须强制删除-f

**docker start 容器id**：启动容器

**docker restart 容器id**：重启容器

**docker stop 容器id**: 停止当前正在运行的容器

**docker kill 容器id**：强制停止当前容器

**docker top 容器id**：查看容器内部的进程信息

**docker exec -it 容器id /bin/bash**：进入容器后开启一个新的终端，可以在里面操作

**docker attach 容器id**：进入容器正在执行的终端，不回启动新的进程！

**docker cp 1e3d0b0653e8:/home/test.java C:/Users/BINJ/Desktop**：从容器内复制文件到本地主机

> 拷贝（cp）是一个手动过程，使用 -v 卷的技术，可以实现自动同步， /home /home



## 五、Docker镜像基本原理

## Docker镜像详解

## 镜像概述

1. 镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，包含运行某个软件所需的所有内容，包括：代码、运行时、库、环境变量和配置文件
2. 所有应用，直接打包docker镜像，就可以直接跑起来
3. 获取方式：
   1. 从远程仓库下载
   2. 别人拷贝给你
   3. 自己制作一个镜像DockerFile

## Docker镜像加载原理

#### UnionFS（联合文件系统）

> UnionFS是一种分层、轻量级并且高性能的文件系统，支持对文件系统的修改作为一次提交来一层层叠加，同时可以将不同目录挂载到同一个虚拟文件系统下。
>
> UnionFS是Docker镜像的基础，镜像可以通过分层来进行继承，基于基础镜像（父镜像）可以制作各种具体的应用镜像
>
> 特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录

#### 加载原理

> docker的镜像实际上就是由一层层的文件系统构成的，这种层级的文件系统就是UnionFS
>
> **bootfs**(boot file system)主要包含bootloader和kernel，bootloader主要引导加载kernel，Linux刚启动时会加载bootfs文件系统。Docker镜像的最底层就是bootfs，当boot加载完后，整个内核就在内存中了，此时内存的使用权已经由bootfs转交给内核，此时系统也会卸载bootfs
>
> **rootfs**，在bootfs之上，包含的就是典型Linux系统中的/dev,/proc,/bin,/etc等标准目录和文件。rootfs就是各种不同的操作系统的发行版本，如Ubuntu，Centos等

#### 分层理解

所有Docker镜像都起始于一个基础镜像层，当进行修改或增加心的内容时，就会在当前镜像层之上，创建新的镜像层。

<img src="https://gitee.com/binProg/my-pic-cdn/raw/master/hexo_imgs/A%7B208OI1%7D7_1D)3N3FNN%5BCJ.png">

特点：

> Docker**镜像**默认都是**只读**的，**当容器启动时**，一个新的**可读写层**被加载到镜像的顶部！这个新的可读写层就是**容器层**，容器层之下的都叫镜像
>
> 即，user的所有操作都是基于**容器层**的。原来的镜像层+user自己的容器层 = user发布的新镜像

<img src="https://gitee.com/binProg/my-pic-cdn/raw/master/hexo_imgs/3A8618A8021E1F09C26DF1ED8994F223.png">



#### Commit镜像

```
docker commit 提交容器成为一个新的副本
docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名:[TAG] 
```

<img src="https://gitee.com/binProg/my-pic-cdn/raw/master/hexo_imgs/2%7BID%7DBTJA(MR8Y25ALI2L_3.png">

## 六、小结

<img src="https://s4.ax1x.com/2021/12/15/TpBnLn.png">

## 六、练习

### 部署一个nginx

> - docker search nginx
>
> - docker pull nginx
>
> - docker run -d --name nginx01 -p 3344:80 nginx
>
> - 测试：
>
>   > curl localhost:3344 //向localhost:3344发出GET请求

### 重要：端口暴露

<img src="https://s4.ax1x.com/2021/12/15/TpBki8.jpg">

### 思考

我们每次改动nginx配置文件，都需要进入容器内部？十分麻烦，要是可以在容器外部提供一个映射路径，达到在容器外修改文件名，容器内部就可以自动修改？ -v **容器卷**技术

