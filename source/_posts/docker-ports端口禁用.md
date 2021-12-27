---
title: docker ports端口禁用
date: 2021-12-25 13:58:10
tags: docker 工具问题集

---

# Docker 暴露的端口被禁用

## 报错：

在使用Docker部署Mosquitto服务器时，报错如下：

```docker: Error response from daemon: Ports are not available: listen tcp 0.0.0.0:10001: bind: An attempt was made to access a socket in a way forbidden by its access permissions.```

即，10001端口不让用

## 解决：

使用如下命令查看哪些端口不让用

```
netsh interface ipv4 show excludedportrange protocol=tcp
```

<img src="https://gitee.com/binProg/my-pic-cdn/raw/master/hexo_imgs/FC6%5DBBQ~M72JH3(Q$KU%5D$F5.png">

换个不在上述列表中的端口即可

## 参考

https://www.cnblogs.com/jasongrass/p/13726009.html

