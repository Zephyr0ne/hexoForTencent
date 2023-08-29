---
title: Linux的文件同步         #文章标题，强烈建议填写此选项
date:  2022-11-23 14:23:44                 #发布时间，强烈建议填写此选项，且最好保证全局唯一
author: yyl             #文章作者
img:                    #文章特征图，推荐使用图床
top: false              #推荐文章（文章是否置顶）  默认false
cover: false            #表示该文章是否需要加入到首页轮播封面中  默认false
coverImg:               #表示该文章在首页轮播封面需要显示的图片路径，如果没有，则默认使用文章的特色图片
summary:   监听文件变动来实时同步内容             #文章摘要，自定义的文章摘要内容
categories:  Linux           #文章分类
tags:                   #文章标签，一篇文章可以多个标签
  - Linux
  - rsync
---

# <center>Linux服务器中的文件同步

没有学习inotify之前,一直只用了Linux自带的rsync来手动同步文件内容,现在因为hexo没有用GitHub Page来代理了之后,每次推送更新内容到服务器就需要手动输入命令来同步内容,相较于GitHub Page的部署反而还麻烦了一点,所以我去查询了一下Linux是否可以根据监听文件变动来进行实时同步.

![](https://zephyr0ne-1317564640.cos.ap-chengdu.myqcloud.com/2023-08-28%2010.06.38.png)

![](https://zephyr0ne-1317564640.cos.ap-chengdu.myqcloud.com/2023-08-28%2010.06.15.png)

## **inotify安装**

   在服务器中安装inotify,用于监听文件的变动

   ```c
   yum install -y epel-release
    
   yum --enablerepo=epel install inotify-tools
   ```

## **rsync Linux服务器一般是自带的**

   ```
   rsync -h
   ```



## **同步配置**

1. 随便在一个目录下新建一个脚本文件,并且赋予权限(777为超级权限,懒人必用,一般按照自己的需求去添加权限)

   ```
   touch /home/filesync.sh
    
   chmod +777 /home/filesync.sh
   ```

2. 编辑脚本内容

   ```c
   vim /home/filesync.sh
   #复制下面的内容到文件中去
   src=/home/filesync
   inotifywait -mrq --timefmt '%d/%m/%y %H:%M' --format '%T %w%f%e' -e modify,move,close_write,delete,create,attrib $src |  while read file
   do
               # rsync -avz  /source   /target
               # rsync的相关命令 自行百度
         rsync -avz /var/www/hexo/ /data/nginx/page/hexo/
                # 输出日志文件
         echo "  ${file} was rsynced" >>/home/filesync.log 2>&1
   done
   ```

3. 执行脚本

   ```
   nohup /home/filesync.sh > /home/filesync.out 2>&1 &
   ```



##  **完成同步配置**

## **测试**

   修改原文件中的内容,然后去到目标文件查看是否修改成功
