---
layout: post
title: Tomcat在mac下的环境配置
excerpt: Tomcat
categories: WebServer
tags: [WebServer]
comments: false
---

##下载tomcat

地址: http://tomcat.apache.org/

选择左侧Download，根据本机JDK版本选择对应的tomcat版本，下载ZIP包

## 路径配置

1、解压缩zip包，将文件夹名字改为Tomcat，将Tomcat文件夹放入到/Library/路径下

2、打开终端，cd到/Library/Tomcat/bin路径下，输入：

```
sudo sh startup.sh
```

如果出现以下提示则说明安装并运行成功

```
Using CATALINA_BASE: /Library/Tomcat 
Using CATALINA_HOME: /Library/Tomcat 
Using CATALINA_TMPDIR: /Library/Tomcat/temp 
Using JRE_HOME: /System/Library/Frameworks/JavaVM.framework/Versions/CurrentJDK/Home
```

在浏览器输入localhost:8080并回车，页面跳转到Apache Tomcat后说明tomcat已成功运行

## 脚本配置
使用文本编辑器输入以下脚本

```
#!/bin/bash

case $1 in
start)
sh /Library/Tomcat/bin/startup.sh
;;
stop)
sh /Library/Tomcat/bin/shutdown.sh
;;
restart)
sh /Library/Tomcat/bin/shutdown.sh
sh /Library/Tomcat/bin/startup.sh
;;
*)
echo “Usage: start|stop|restart”
;;
esac

exit 0
```

保存文件名为tomcat，不带后缀。打开终端，cd到tomcat文件所在路径，输入以下命令：

```
sudo chmod 777 tomcat
```

赋予文件可执行权限后，将文件拖放到/usr/local/bin目录下，然后在终端中输入tomcat start，tomcat stop和tomcat restart来使用tomcat了。如果tomcat start等命令执行后出现Permission Denied的情况，需要加上sudo，回车后输入本机登录密码就行了。
