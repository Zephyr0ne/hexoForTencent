---
title:  hexo部署服务器                #文章标题，强烈建议填写此选项
date:   2022-11-6 9:45:22                 #发布时间，强烈建议填写此选项，且最好保证全局唯一
author: yyl             #文章作者
img:                    #文章特征图，推荐使用图床
top: false              #推荐文章（文章是否置顶）  默认false
cover: false            #表示该文章是否需要加入到首页轮播封面中  默认false
coverImg:               #表示该文章在首页轮播封面需要显示的图片路径，如果没有，则默认使用文章的特色图片
summary:   GitHub Page转移到服务器             #文章摘要，自定义的文章摘要内容
categories:  Hexo           #文章分类
tags:                   #文章标签，一篇文章可以多个标签
  - hexo
---

# <center>hexo部署到服务器

默认熟悉hexo、服务器相关知识,并且之前是操作部署过GitHub Page,现在想部署到自己服务器上面的bloger

## 服务器

1. 下载git、nginx

   ```
   yum install -y nginx git
   ```

2. 添加ssh密钥

   ```
   mkdir -p ~/.ssh
   touch ~/.ssh/authorized_keys
   chmod 600 ~/.ssh/authorzied_keys
   chmod 700 ~/.ssh
   vim ~/.ssh/authorized_keys
   #将自己本地.ssh文件下的id_rsa.pub公钥复制到authorized_keys中去
   ```

3. 创建git仓库并使用git-hooks实现自动部署

   ```
   sudo mkdir -p /data/rep    #新建目录，这是git仓库的位置
   sudo mkdir pp /data/hexo
   cd /data/rep  #转到git仓库的文件夹
   sudo git init --bare blog.git #创建一个名叫blog的仓库
   sudo vim /data/rep/blog.git/hooks/post-update
   #将下面内容复制进去
   git --work-tree=/data/hexo --git-dir=/data/rep/blog.git checkout -f
   ```

4. 配置nginx配置

   ```
   #监听443端口
   server {
           listen      443 ssl;
           server_name  域名;
           
           #如果不配置ssl证书,则忽略或注释ssl相关的配置
           #证书文件名称
           ssl_certificate /容器内地址;
           #私钥文件名称
           ssl_certificate_key /容器内地址;
           ssl_session_timeout 5m;
           #请按照以下协议配置
           ssl_protocols TLSv1.2 TLSv1.3;
           #请按照以下套件配置，配置加密套件，写法遵循 openssl 标准。
           ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
           ssl_prefer_server_ciphers on;
   
           charset utf-8;
   
           location / {
                   proxy_ssl_server_name on;
                   proxy_ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
                   proxy_ssl_session_reuse off;
                   #一定是容器内部地址,如果挂载了文件,需要去查看对应的路径
                   root  /容器内地址;
                   index  index.html index.htm;
                   
                   #try_files $uri $uri/ index.html;
           }
   
           #图片缓存时间设置
           location ~ .*.(gif|jpg|jpeg|png|bmp|swf)$
           {
               expires 10d;
           }
   
         
       }
   		
           #监听80端口
       server {
           listen    80 ;
           server_name    域名;
           #强制将http重定向为https请求
           return 301 https://$host$request_uri;
           }
   ```


5. 如果是用docker部署的nginx镜像服务则:

   ```
   docker restart nginx
   ```

   如果是直接部署nginx服务则:

   ```
   nginx -t
   nginx -s reload
   ```

6. 解析域名

## 本地

本地环境默认安装node、npm、git

1. 修改根目录下的config文件

   ```
   deploy:
     type: git
     repo: root@ip地址 :/data/rep/blog.git
     branch: master
   ```

2. 执行一键三连

   ```
   hexo cl && hexo g && hexo d
   ```

3. 到服务器查看是否推送最新代码到指定的git仓库

   ```
   cd /data/hexo 
   ll
   #查看最新更新时间是否是刚更新的
   ```

4. 同步此文件内容到nginx挂载的html文件中

   ```
   rsync -avz /data/hexo/ /data/nginx/page/hexo/
   ```

5. 查看nginx目录下的文件是否更新

6. 访问测试,打开浏览器,输入域名看是否正常
