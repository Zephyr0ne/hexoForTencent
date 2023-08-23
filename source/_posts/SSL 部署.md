---
title: SSL 部署方式
author: yyl
top: false
cover: false
summary: Nginx 和 jar 包部署 SSL 证书
categories: SSL
tags:
  - SSL
  - Nginx
  - SpringBoot
abbrlink: b7d83cf
date: 2022-05-17 17:38:25
img: 'https://zephyr0ne-1317564640.cos.ap-chengdu.myqcloud.com/ssl.jpg'
coverImg: 
---

## <center>SSL 证书部署

首先需要获得 SSL 证书，阿里云免费 SSL [证书申请地址](https://yundun.console.aliyun.com/?spm=5176.7968328.J_8413632810.1.22fb65c3OOastc&p=cas&showBuy=1#/certExtend/free/cn-hangzhou)、腾讯云免费 SSL [证书申请地址](https://console.cloud.tencent.com/ssl)



### 1.  nginx 部署 SSL

1. 获得 SSL 证书之后，选择下载对应版本的 SSL 证书，当前选择 Nginx下载

2. 将下载好之后的文件解压到服务器的 nginx 挂载的配置文件目录下，如下图
   ![](https://llds-1311848057.cos.ap-chengdu.myqcloud.com/2023-07-07%2016.49.55.png)
   3. 在 nginx.conf配置文件中配置

        server 模块中添加

       ```c
          listen       443 ssl;
                  server_name  域名地址;
                  #证书文件名称
                  ssl_certificate /etc/nginx/pem 文件名称;
                  #私钥文件名称
                  ssl_certificate_key /etc/nginx/key 文件名称;
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
                       index  index.html index.htm;
                       root  /usr/local/ui	;
                       try_files $uri $uri/ /index.html;
                  }
       ```

   证书文件地址和私钥文件地址是填写容器内的地址 并不是 宿主机的地址（踩坑注意），因为在启动容器的时候就挂载了nginx 配置的地址，容器的地址和宿主机的地址是相互映射的。

4. 到此 Nginx 的 SSL 部署就完成了

5. 访问 443 端口需要去服务器控制台开启 443 端口的访问策略，并开启服务器防火墙的443白名单

6. 浏览器直接访问域名，在域名左侧发现一个带锁🔐的标志就表示 SSL 部署成功！

### 2. SpringBoot jar 包部署 SSL

   此方法使用 Tomcat 环境下的部署

   服务器仅部署了 Java 环境，并且部署 jar 包到服务器，使用 Java 命令启动 jar 包方式运行服务

1. 在 SSL 控制台下载 Tomcat 版本到本地

2. 将下载好的fpx 文件拷贝到项目的 resources 目录下

3. 修改application.yml，添加 ssl 配置

   ```yaml
     ssl:
       key-store: classpath:第二点拷贝的fpx 文件名称
       key-store-password: 第一点中下载的pass文件中的秘钥
       enabled: true
       key-store-type: PKCS12（下载的 SSL 证书格式）
   ```

4. 设置自动重定向**http**到**https**（**80**端口**->443**端口）

   ```java
   @Configuration
   public class SecurityConfig{
       
       /**
        * 设置自动重定向http到https（80端口->443端口）
        *
        * @param
        * @return ServletWebServerFactory
        * @author youyulong
        * @since 2022/5/18 11:18
        */
       @Bean
       public ServletWebServerFactory servletContainer(){
           TomcatServletWebServerFactory tomcat = new TomcatServletWebServerFactory(){
               @Override
               protected void postProcessContext(Context context){
                   SecurityConstraint securityConstraint = new SecurityConstraint();
                   securityConstraint.setUserConstraint("CONFIDENTIAL");
                   SecurityCollection collection = new SecurityCollection();
                   collection.addPattern("/*");
                   securityConstraint.addCollection(collection);
                   context.addConstraint(securityConstraint);
               }
           };
           tomcat.addAdditionalTomcatConnectors(createHTTPConnector());
           return tomcat;
       }
       
       private Connector createHTTPConnector(){
           Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
           //同时启用http（8080）、https（8443）两个端口
           connector.setScheme("http");
           connector.setSecure(false);
           //80 端口是浏览器访问的 http 端口
           connector.setPort(80);
           //443 端口是项目运行的服务端口 443，也是https 端口
           connector.setRedirectPort(443);
           return connector;
       }
       
   }
   ```

5. 运行mvn clean compile  install -DSkipTests=true得到 jar 包
6. 通过 idea 的docker 插件添加端口绑定  80:80  443:443（踩坑注意，一定到绑定这两个端口）运行 Dockerfile文件
7. 前往服务器控制台打开 80 和 443 端口的访问策略，并开启服务器防火墙的白名单
8. 直接访问域名的 80端口也会重定向到 443 端口，同样在域名左侧看到带锁🔐的标志即表明 SSL 部署成功！

      

