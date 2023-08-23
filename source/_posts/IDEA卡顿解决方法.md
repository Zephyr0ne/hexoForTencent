---
title: 你的IDEA很卡吗?
date: 2020-1-20
author: zephyr0ne
top: false
cover: false
summary: IDEA很卡
categories: IDEA
tags:
  - IDEA
abbrlink: db3e318a
img: 'https://zephyr0ne-1317564640.cos.ap-chengdu.myqcloud.com/IntelliJ-IDEA.png'
coverImg: 
---

## <center>关于idea占用cpu/内存过高导致电脑巨卡的解决方法

自从升级了idea最新版本之后,卡了可能有一个月了，cpu随时拉满，内存爆百，鼠标卡顿。。。

百度了很多解决方法，但是都没啥用，直到看到一篇推文。 直接上代码

```java

-Xms2048m
-Xmx4096m
-Xverify:none
-XX:+DisableExplicitGC
-XX:ReservedCodeCacheSize=720m
-XX:SoftRefLRUPolicyMSPerMB=50
-ea
-Dsun.io.useCanonCaches=false
-Djava.net.preferIPv4Stack=true
-Djdk.http.auth.tunneling.disabledSchemes=""
-XX:+HeapDumpOnOutOfMemoryError
-XX:-OmitStackTraceInFastThrow
# JIT 参数
# 设置用于编译的编译器线程
-XX:CICompilerCount=2
# 开启分层编码
-XX:TieredStopAtLevel=1
# 控制最大数量嵌套调用内存
-XX:MaxInlineLevel=3
# 即时编译的东西
-XX:Tier4MinInvocationThreshold=100000
-XX:Tier4InvocationThreshold=110000
-XX:Tier4CompileThreshold=120000

```

最有用的地方就是设置即时编译的地方，以前只是修改了分配的堆栈内存，效果并不明显
现在idea再也不会卡的鼠标一顿一顿 影响心情了！
