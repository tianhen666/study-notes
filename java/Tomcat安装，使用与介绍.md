#### 一，Tomcat简介

> Tomcat 服务器是一个免费的开放源代码的Web应用服务器
> 最新的Servlet 和JSP 规范总是能在Tomcat 中得到体现

#### 二，配置环境变量与如何启动

##### windows

- 先配置JDK环境变
	- 添加 变量 JAVA_HOME、变量值为JDK所在路径，
	- path中添加 %JAVA_HOME%\bin
	- ==低于JDK11==需要配置JRE_HOME
- 然后配置TOMCAT环境变量
	- [下载地址](https://tomcat.apache.org/)
	- 添加 变量 CATALINA_HOME、变量值为TOMCAT所在路径，
	- path中添加 %CATALINA_HOME%\bin
- 如何启动
	- windows下解压后，双击bin目录下 startup.bat 文件
	- 配置环境变量后 cmd下执行命令 startup

- ==注意事项==

	- 首先安装如上方式配置JDK环境变量才可以启动成功

	- 解决控制台乱码问题

	- 修改conf/logging.properties文件（注释掉原来的，然后新增即可）第51行

```properties
java.util.logging.ConsoleHandler.encoding = GBK
```

##### linux

- 切换到tomcat主目录下的bin目录
	- 直接启动 ./startup.sh
	- 直接关闭./shutdown.sh

#### 三，tomcat配置

+ 配置文件/conf/server.xml
+ 端口配置

```xml
<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443" />
```

#### 四，tomcat结构体系和源码解析

[参考一]: https://zhuanlan.zhihu.com/p/141947605
[参考二]: https://www.cnblogs.com/Soy-technology/p/11223604.html
[参考三]: https://zhuanlan.zhihu.com/p/361139792

