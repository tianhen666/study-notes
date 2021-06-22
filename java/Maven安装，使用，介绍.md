#### 什么是Maven

> Maven是由早期为Jakarta Turbine定制的构建工具发展而来的，现在成为了一个独立的软件项目管理工具
>
> 我们构建一个项目需要很多第三方库，比如写一个使用Spring的web项目就需要引入大量的jar包。
>
> jar包之间的关系错综复杂，一个jar包往往又会依赖其他jar包，缺少一个jar包都会导致项目编译失败。
>
> Maven是一款帮助程序员构建项目的工具，只需要告诉maven需要哪些jar包，他会帮助我们下载所有jar包，极大提升开发效率。

[参考文档一](https://www.cnblogs.com/ziph/p/13157977.html)

#### 安装Maven环境变量配置

[maven的官网](https://maven.apache.org/)

##### 安装目录分析

bin文件夹：含有mvn运行的脚本

boot文件夹：含有plexus-classworlds是一个类加载器框架

conf文件夹：含有settings.xml配置文件

lib文件夹：含有maven运行时所需要的java类库

##### 设置maven环境变量

新建环境变量MAVEN_HOME 指向maven目录

编辑Path，增加“%MAVEN_HOME%\bin”

通常需要设置MAVEN_OPTS的值为-Xms128m -Xmx512m，因为Java默认的最大可用内存往往不能够满足Maven运行的需要，比如在项目较大时，使用Maven生成项目站点需要占用大量的内存，如果没有该配置，则很容易得到java.lang.OutOfMemeoryError。

#### 规定的开发时目录规范

├─maventest 项目目录
│  │  pom.xml 配置文件
│  │  
│  └─src
│      ├─main 
│      │  ├─java 存放项目源码
│      │  └─resources 存放项目资源文件
│      └─test
│          └─java 存放单元测试源码

#### Maven的基本命令

##### mvn -v

查询当前版本

##### compile

编译 将java源文件编译成class文件

##### test

执行test目录下的测试用例

##### package

将项目打成jar包或者war包

##### clean

删除target文件夹

##### install

将当前项目放到maven仓库中。供其他项目使用

#### Maven仓库

Maven仓库用来存放Maven管理的所有Jar包。分为：本地仓库 和 中央仓库。

- ==本地仓库==：Maven本地的Jar包仓库。

	默认在：C:\Users\Administrator.m2

	修改默认conf/settings.xml 下的 localRepository

- ==中央仓库==： Maven官方提供的远程仓库。

当项目编译时，Maven首先从本地仓库中寻找项目所需的Jar包，若本地仓库没有，再到Maven的中央仓库下载所需Jar包。

#### 寻找包时的“坐标”

在Maven中，坐标是Jar包的唯一标识，Maven通过坐标在仓库中找到项目所需的Jar包。

如下代码中，groupId和artifactId构成了一个Jar包的坐标。

```xml
<dependency>
   <groupId>cn.missbe.web.search</groupId>
   <artifactId>resource-search</artifactId>
   <packaging>jar</packaging>
   <version>1.0-SNAPSHOT</version>
</dependency>
```
**groupId**:所需Jar包的项目名

**artifactId**:所需Jar包的模块名

**version**:所需Jar包的版本号

#### Maven依赖范围的分类

maven项目中有3套classpath：编译classpath、测试classpath、运行classpath。 

##### compile

默认范围，当我们引入依赖时，如果<scope>标签没有指定，那么默认就是complie。

比如spring：    

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-core</artifactId>
  <version>5.2.4.RELEASE</version>
</dependency>
```

意义：3个classpath环境中都能使用，并且项目打包时，也会将这些依赖打包进去。

##### test

测试范围有效，比如Junit相关的测试包：

```xml
<dependency>
   <groupId>junit</groupId>
   <artifactId>junit</artifactId>
   <version>4.12</version>
   <scope>test</scope>
 </dependency>
```

意义：只在测试的classpath中才能够使用。在src/main/java里面的代码是无法使用这些api的，并且项目打包时，也不会将"test"标记的<scope>打入"jar"包或者"war"包

##### provide

编译范围和测试范围有效，比如servlet相关的jar包。

```xml
<dependency>
   <groupId>javax.servlet</groupId>
   <artifactId>javax.servlet-api</artifactId>
   <version>4.0.1</version>
   <scope>provide</scope>
 </dependency>
```

意义：只有在编译的classpath和测试的classpath有效。当项目运行时，是不会使用<scope>provide</scope>标记的依赖，项目打包时也不会打入。
因为servlet容器(如Tomcat)已经提供了servlet的jar，如果我们还将servlet打入jar或者war包中，那么在容器中运行时反而会出现异常

##### runtime

运行时范围有效。比如mysql驱动

```xml
<dependency>
   <groupId>mysql</groupId>
   <artifactId>mysql-connector-java</artifactId>
   <version>5.1.46</version>
   <scope>runtime</scope>
 </dependency>
```

##### system

系统范围依赖，作用范围与"provide"一致。需要配合<systemPath></systemPath>标签使用指定依赖文件的路径。

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
     <version>2.1.10.release</version>
   <scope>system</scope>
   <systemPath>${java.home}/lib/rt.jar</systemPath>
 </dependency>
```

##### 其他范围

systemPath: 仅用于范围为system。提供相应的路径

optional:  当项目自身被依赖时，标注依赖是否传递。用于连续依赖时使用

#### 传递依赖 与 排除依赖

##### 传递依赖

如果我们的项目引用了一个Jar包，而该Jar包又引用了其他Jar包，那么在默认情况下项目编译时，Maven会把直接引用和间接引用的Jar包都下载到本地。

##### 排除依赖

如果我们只想下载直接引用的Jar包，那么需要在pom.xml中做如下配置：(将需要排除的Jar包的坐标写在中)

```xml
<exclusions>
   <exclusion>
      <groupId>cn.missbe.web.search</groupId>
      <artifactId>resource-search</artifactId>
      <packaging>pom</packaging>
      <version>1.0-SNAPSHOT</version>
   </exclusion>
</exclusions>
```

##### 为什么要排除jar包中的依赖？

在写pom的时候，我们引用的依赖往往会依赖于其他的包，而这些包可能是过时的不安全的，因此需要排除并重新引用安全的版本，先在依赖这个项目的pom中去除想排除的依赖，再添加指定版本的依赖。或者==解决依赖冲突==

#### 依赖冲突

##### 什么是依赖冲突

依赖冲突是当直接引用或者间接引用出现了==相同的jar包拥有不同版本==的时候。

**举个例子：**

A依赖于B，B依赖于C，此时C的版本为V1.0；如果此时引入的C依赖还有一个V2.0，那么我们的A传递依赖于C，此时C的版本为V2.0。这时候就是一个冲突，直接或间接的都引用了C，而C版本有两个！

##### 短路优先

本项目——>A.jar——>B.jar——>X.jar

本项目——>C.jar——>X.jar

若本项目引用了A.jar，A.jar又引用了B.jar，B.jar又引用了X.jar，并且C.jar也引用了X.jar。

在此时，Maven只会引用引用路径最短的Jar。

##### 声明优先

若引用路径长度相同时，在pom.xml中谁先被声明，就使用谁。

#### 聚合

- 什么是聚合

	将多个项目同时运行就称为聚合。

- 如何实现聚合

	只需在pom中作如下配置即可实现聚合：

	```xml
	<modules>
	    <module>web-connection-pool</module>
	    <module>web-java-crawler</module>
	</modules>
	```

#### 继承

1. 什么是继承？

	在聚合多个项目时，如果这些被聚合的项目中需要引入相同的Jar，那么可以将这些Jar写入父pom中，各个子项目继承该pom即可。

2. 如何实现继承？

##### 父pom配置

将需要继承的Jar包的坐标放入标签即可。

```xml
<dependencyManagement>
    <dependencies>
          <dependency>
            <groupId>cn.missbe.web.search</groupId>
            <artifactId>resource-search</artifactId>
            <packaging>pom</packaging>
            <version>1.0-SNAPSHOT</version>
          </dependency> 
    </dependencies>
</dependencyManagement>
```

##### 子pom配置

```xml

<parent>
      <groupId>父pom所在项目的groupId</groupId>
      <artifactId>父pom所在项目的artifactId</artifactId>
      <version>父pom所在项目的版本号</version>
</parent>
 <parent>
      <artifactId>resource-search</artifactId>
      <groupId>cn.missbe.web.search</groupId>
      <version>1.0-SNAPSHOT</version>
</parent>
```





