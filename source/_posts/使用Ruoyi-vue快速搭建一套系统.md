---
title: Ruoyi-vue框架
author: yyl
top: false
cover: false
summary: 快速搭建一套具有权限认证、多终端认证的、直接进行业务开发的系统
categories: 项目框架
tags: 
  - Java 
  - Ruoyi-vue
date: 2024-03-15 17:15:29
img: https://zephyr0ne-1317564640.cos.ap-chengdu.myqcloud.com/2024/03/15/17104942576396.jpg
coverImg:
---

# <center>Ruoyi-vue框架快速搭建

> 目的:  快速搭建一套具有权限认证、多终端认证的、直接进行业务开发的系统

## 步骤
1. 从[Ruoyi官网](https://gitee.com/y_project/RuoYi-Vue/releases/tag/v3.8.7)下载zip包
2. 下载[懒人一键工具](https://gitee.com/lpf_project/RuoYi-MT/releases/tag/V4-20230425)(傻瓜式操作,如果打开软件出现闪退现象,建议使用后面的方法)或者[克隆代码](https://gitee.com/lpf_project/RuoYi-MT)到本地运行启动
3. 使用PyCharm打开第二步操作克隆下来的项目之后,配置右下角的python解释器(**默认你电脑环境已经有python环境了**,如果没有先去百度配置python安装配置环境变量),此时需要打开**requirements.txt**文件(代码拉取下来wxPython的版本是4.1.0),需要你去查看[python版本与wxPython版本的兼容性](https://www.wxpython.org/),python最低版本要求是3.7
4. 点击编辑器右上角的**install Package**或者在terminal中输入`pip install -r requirements.txt`命令,等待依赖下载完成之后,启动服务出现下面这个界面说明启动成功![](https://zephyr0ne-1317564640.cos.ap-chengdu.myqcloud.com/2024/03/15/17104930613810.jpg)
5. 然后选择Ruoyi-vue系列,根据自己项目名称编写目录名称、项目名等,最后一步点击**开始执行**,右侧操作记录显示任务结束,即可去相应目录查看文件(已解压),文件名称是时间戳,点击进去之后就是你想要的目录信息了![](https://zephyr0ne-1317564640.cos.ap-chengdu.myqcloud.com/2024/03/15/17104930721744.jpg)
6. 使用Idea打开此**Tencent**(你自己定义)文件,然后即可快速使用,[使用参考文档
   ](https://gitee.com/y_project/RuoYi-Vue/tree/master/doc)

-------
## 以下为可选择性操作
### 定时任务框架、代码生成框架移除
> ruoyi框架的sql表执行了之后你会发现一共有将近30张表,定时任务的表就占了一大部分,如果不想使用quartz定时任务框架的和代码生成框架的功能,其实可以删除这两个模块的内容
1. sql目录下的quartz.sql**不执行**,
2. sql目录下的ry_20231130.sql文件中的**15、16、18、19不执行**
3. 删除 Nacos-quartz、Nacos-generator两个模块
4. 在**根目录下**的pom.xml中删除
 ```xml 
    <module>ruoyi-quartz</module>
    <module>ruoyi-generator</module>
```
5. 在**Nacos-admin**模块下的pom.xml中删除
```xml
        <!-- 定时任务-->
        <dependency>
            <groupId>com.ruoyi</groupId>
            <artifactId>ruoyi-quartz</artifactId>
        </dependency>

        <!-- 代码生成-->
        <dependency>
            <groupId>com.ruoyi</groupId>
            <artifactId>ruoyi-generator</artifactId>
        </dependency>
```
**End**:  直接启动项目,测试是否能够正常启动


-------
### Mybatis-plus框架集成
> Ruoyi-vue框架没有集成Mybatis-plus框架,所以如果你想集成mp框架,需要根据[参考文档](https://doc.ruoyi.vip/ruoyi-vue/document/cjjc.html#%25E9%259B%2586%25E6%2588%2590mybatisplus%25E5%25AE%259E%25E7%258E%25B0mybatis%25E5%25A2%259E%25E5%25BC%25BA)进行操作,然后测试是否功能正常

-------
## 参考:
1. [Ruoyi-vue](https://gitee.com/y_project/RuoYi-Vue)
2. [RuoYi-MT](https://gitee.com/lpf_project/RuoYi-MT)
3. [Ruoyi集成文档](https://doc.ruoyi.vip/ruoyi-vue/document/cjjc.html)
