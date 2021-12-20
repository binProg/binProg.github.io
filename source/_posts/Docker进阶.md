---
title: Docker进阶
date: 2021-12-20 09:41:13
tags: Docker
mathjax: true
---

# Docker进阶

## 一、Docker容器卷

容器运行时，数据都在容器中，如果容器被删除，那么数据就会丢失。由此，产生出需求：**数据持久化**。

为了满足这个需求，容器之间需要有一个**数据共享**技术。即，Docker容器中产生的数据，可以同步到本地。

卷技术：将容器内的目录，挂载到Linux上。

<img src="https://gitee.com/binProg/my-pic-cdn/raw/master/hexo_imgs/%E5%AE%B9%E5%99%A8%E6%8A%80%E6%9C%AF%E4%BA%A4%E4%BA%922.png" width="500px">

> 容器卷技术：容器的持久化和同步操作！容器间也是可以数据共享的！

### 1. 使用数据卷

> 方式一：直接使用命令来挂载 -v

```
docker run -it -v 主机目录:容器内的目录
# 启动起来后可以使用 docker inspect 容器id 来查看挂载详情
```

<img src="https://gitee.com/binProg/my-pic-cdn/raw/master/hexo_imgs/UO_ZG0%60K9$WOG0V_HP%7BPF4U.png">

### 2. 实战：MYSQL

```cmd
docker run -d -p 3310:3306 -v C:/Users/BINJ/Desktop/电网/ceshi/mysql/conf:/etc/mysql/conf.d -v C:/Users/BINJ/Desktop/电网/ceshi/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7
```

### 3. 匿名和具名挂载

所有docker容器内的卷，没有指定目录的情况下都在 `/var/lib/docker/volumes/xxxxx(具名)/_data` 

具体的路径可以使用命令`docker volumn inspect xxxxx`查看

```
# 区分具名和匿名
-v 容器内路径    #匿名挂载
-v 卷名：容器内路径  #具名挂载
-v 宿主机路径：容器内路径   #具体路径挂载
```



## 二、Dockerfile

Dockerfile就是用来构建docker镜像的构建文件！命令脚本！

```
# 创建一个dockerfile文件，名字建议 Dockerfile
# 文件中的内容： 指定（大写） 参数
FROM centos

VOLUME ["volume01","volume02"]

CMD echo "------end------"

CMD /bin/bash

# 这里的每个命令就是镜像的一层
```













