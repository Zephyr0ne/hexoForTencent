---
title: 创建第一个Docker服务
author: Zephyr0ne#文章作者
top: true
cover: false
summary: Docker镜像服务
categories: Docker
tags:
  - Docker
abbrlink: 7e49dd14
date: 2019-06-24 10:22:59
img: 'https://zephyr0ne-1317564640.cos.ap-chengdu.myqcloud.com/New_Docker_logo_Logo.jpg'
coverImg: 
---

## <center>创建第一个Docker服务

### **什么是Docker?**

> Docker是一种轻量级的虚拟化技术，同时是一个开源的应用容器运行环境搭建平台，可以让开发者以便捷方式打包应用到一个可移植的容器中，然后安装至任何运行Linux或Windows等系统的服务器上。相较于传统虚拟机，Docker容器提供轻量化的虚拟化方式、安装便捷、启停速度快。
>
> Docker容器具有以下三大特点：
>
> 轻量化：
>
> ​		一台主机上运行的多个Docker容器可以共享主机操作系统内核；启动迅速，只需占用很少的计算和内存资源。
>
> 标准开放：
>
> ​		Docker容器基于开放式标准，能够在所有主流Linux版本、Microsoft Windows以及包括VM、裸机服务器和云在内的任何基础设施上运行。
>
> 安全可靠：
>
> ​		Docker赋予应用的隔离性不仅限于彼此隔离，还独立于底层的基础设施。Docker默认提供最强的隔离，因此应用出现问题，也只是单个容器的问题，而不会波及到整台主机

### **什么是容器?**

> 镜像创建的运行实例，Docker利用容器来运行应用。每个容器都是相互隔离的、保证安全的平台。我们可以把容器看做是一个轻量级的Linux运行环境。

### **什么是镜像?**

> Docker镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的配置参数。镜像不包含任何动态数据，其内容在构建之后也不会被改变。

### **什么是镜像仓库?**

> 集中存放镜像文件的地方。用户创建完镜像后，可以将其上传到公共仓库或者私有仓库，需要在另一台主机上使用该镜像时，只需要从仓库上下载即可。

## 傻瓜式操作步骤:

### **Docker:**

1. 设置Docker yum源(yum是Linux系统的安装软件必备工具)

   ```c
   sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
   ```

2. 查看当前可以安装docker-ce版本

   ```c
   yum list docker-ce --showduplicates | sort -r
   ```

3. 安装指定版本的格式 ,注意3:xxx 请移除3:

   ```c
   yum -y install docker-ce-[VERSION]
   ```

4. 重启Docker

   ```c
   systemctl restart docker
   ```

5. 设置Docker开机启动

   ```c
   systemctl enable docker
   ```

6. 查看Docker版本信息

   ```c
   docker version
   ```

### **容器:**

1. 拉取Redis镜像

   ```c
   docker pull redis:latest
   ```

2. 直接执行启动命令

   ```c
   docker run -itd  -p 6379:6379  \
   --name myRedis   \
   redis   \
   ```

   就可以直接启动一个最基本的redis服务了,但是我们一般还会加入其他的一些启动命令,让我们的服务可操作性更大

3. 在服务器上创建对应的数据目录

   ```c
   mkdir -p /data/scms/redis/{conf,data} 
   ```

4. 在新建的conf目录下 新建一个redis.conf配置文件,具体配置信息如图所示

   ![](https://zephyr0ne-1317564640.cos.ap-chengdu.myqcloud.com/redis.png)



5. 执行启动命令

   ```c
   docker run -itd  -p 6379:6379  \
   -v /data/scms/redis/conf/redis.conf:/usr/local/etc/redis/redis.conf   \
   -v /data/scms/redis/data/:/data   \
   --name myRedis   \
   redis   \
   redis-server /usr/local/etc/redis/redis.conf
   ```

6. 命令详解

   ```html
   -p 代表端口映射（-p6379:6379 表示将容器6379 端口映射到主机6379 端口）
   -itd 代表容器在后台运行，并支持交互模式（启动时可以执行命令），并且打印容器id
   --name 标识容器名称
   -v 给容器挂载存储卷，挂载到容器的某个目录（目录映射）
   ```

7. 添加6379端口的访问策略
8. 查看容器运行状态  
   ```c
   docker ps
   ```
   
9. 使用RDM工具新建连接测试是否能够连接成功

   ![](https://zephyr0ne-1317564640.cos.ap-chengdu.myqcloud.com/QQ20230620-140448%402x.png)

## 完成以上步骤即可开始使用自己的redis服务了
