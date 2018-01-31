---
layout:     post
title:      "搭建一个轻量级的Java Web框架--使用IntelliJ IDEA 搭建Java Web 项目"
subtitle:   "Java Web"
date:       2016-05-05 09:38:27 
author:     "CatchZeng"
header-img: "img/post-bg-ios.jpg"
tags:
    - Java Web
---
<span id="busuanzi_container_page_pv"></span>

## 搭建Java Web项目

1.新建maven项目，create from archetype，选择maven-archetype-webapp
![Idea](http://upload-images.jianshu.io/upload_images/943491-06088fc954b13f8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.填写GroupId，ArtifactId和Version
![GroupId，ArtifactId，Version](http://upload-images.jianshu.io/upload_images/943491-dbcd1c3fce24fde0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.Maven
![Maven](http://upload-images.jianshu.io/upload_images/943491-d4949b78d0dbb6ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在这里若遇到maven生成骨架的时候将会非常慢，可以在Properties中添加一个参数archetypeCatalog=internal（我的工程生成很快故没有加该参数）。
```
archetypeCatalog表示插件使用的archetype元数据，默认为remote即中央仓库，由于中央仓库的archetype太多了，所以导致很慢，指定internal来表示仅使用内部元数据。
```

4.Project name & Location 
![Project name & Location](http://upload-images.jianshu.io/upload_images/943491-079b382f96a1c7e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5.工程结构
![工程结构](http://upload-images.jianshu.io/upload_images/943491-a1b055dbbcfe7ac1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

6.在main文件夹下添加 Java 目录,并把它设为源代码文件夹。
![屏幕快照 2016-05-05 下午9.27.20.png](http://upload-images.jianshu.io/upload_images/943491-8bbc53efee53c0fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

7.配置pom.xml文件 
![配置pom.xml文件 ](http://upload-images.jianshu.io/upload_images/943491-edccd475572ddf78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
具体如下:

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">    <modelVersion>4.0.0</modelVersion>    <groupId>com.catch</groupId>    <artifactId>catchjava</artifactId>    <packaging>war</packaging>    <version>1.0</version>    <name>catchjava Maven Webapp</name>    <url>[http://maven.apache.org](http://maven.apache.org)</url>    <!--编码格式UTF-8-->   <properties>        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>    </properties>    <!--依赖-->   <dependencies>        <!-- Servlet -->       <dependency>            <groupId>javax.servlet</groupId>            <artifactId>javax.servlet-api</artifactId>            <version>3.1.0</version>            <scope>provided</scope>        </dependency>        <!-- JSP -->       <dependency>            <groupId>javax.servlet.jsp</groupId>            <artifactId>jsp-api</artifactId>            <version>2.2</version>            <scope>provided</scope>        </dependency>        <!-- JSTL -->       <dependency>            <groupId>javax.servlet</groupId>            <artifactId>jstl</artifactId>            <version>1.2</version>            <scope>runtime</scope>        </dependency>        <!--Junit-->       <dependency>            <groupId>junit</groupId>            <artifactId>junit</artifactId>            <version>3.8.1</version>            <scope>test</scope>        </dependency>    </dependencies>    <!--编译-->   <build>        <plugins>            <!-- Compile -->           <plugin>                <groupId>org.apache.maven.plugins</groupId>                <artifactId>maven-compiler-plugin</artifactId>                <version>3.3</version>                <configuration>                    <source>1.6</source>                    <target>1.6</target>                </configuration>            </plugin>            <!-- Test -->           <plugin>                <groupId>org.apache.maven.plugins</groupId>                <artifactId>maven-surefire-plugin</artifactId>                <version>2.18.1</version>                <configuration>                    <skipTests>true</skipTests>                </configuration>            </plugin>            <!-- Tomcat -->           <plugin>                <groupId>org.apache.tomcat.maven</groupId>                <artifactId>tomcat7-maven-plugin</artifactId>                <version>2.2</version>                <configuration>                    <path>/${project.artifactId}</path>                </configuration>            </plugin>        </plugins>        <finalName>catchjava</finalName>    </build></project>
```

8.新建HelloServlet,并重写doGet方法。
![新建HelloServlet](http://upload-images.jianshu.io/upload_images/943491-0ad32550da3f316a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![doGet](http://upload-images.jianshu.io/upload_images/943491-e70a2aca7711869d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

9.新建hello.jsp
![hello.jsp](http://upload-images.jianshu.io/upload_images/943491-934b70fbacfa76bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

10.加入@WebServlet标记
![@WebServlet](http://upload-images.jianshu.io/upload_images/943491-1d5acbd4795be0d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

11.配置Tomcat
![Tomcat](http://upload-images.jianshu.io/upload_images/943491-2f72aac5963da27b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Tomcat](http://upload-images.jianshu.io/upload_images/943491-2b5673c0cc9a3f51.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Tomcat](http://upload-images.jianshu.io/upload_images/943491-17326d620b383ed9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Tomcat](http://upload-images.jianshu.io/upload_images/943491-25ce46d229e6baa9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Tomcat](http://upload-images.jianshu.io/upload_images/943491-5b97dd21579a76b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Tomcat](http://upload-images.jianshu.io/upload_images/943491-7bc4b35cdd692f00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

12.Run
![Run](http://upload-images.jianshu.io/upload_images/943491-f1e211658682aca2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

13.加入<%@ page isELIgnored ="false"%>去除j sp显示错误。
![isELIgnored](http://upload-images.jianshu.io/upload_images/943491-37efd5527e8ba3f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

14.再次Run，输入http://localhost:8080/catchjava/hello 得到正确结果。[HelloServlet中 @WebServlet("/hello")的作用就在于此]
![Run](http://upload-images.jianshu.io/upload_images/943491-bac9d43f3c714275.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 后续
本期介绍了如果使用IntelliJ IDEA搭建一个Java Web项目。后续的文章将在这一篇的基础上一步一步搭建一个轻量级的Java Web框架，有兴趣的朋友咱们下期见。
