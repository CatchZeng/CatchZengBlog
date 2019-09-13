---
layout: post
title: "Android Maven 私有库"
subtitle: "Android"
date: 2018-12-12 09:38:27
author: "CatchZeng"
header-img: "img/post-android.jpeg"
tags:
  - Android
---
<span id="busuanzi_container_page_pv"></span>

## 一、前言

### Maven

![](http://maven.apache.org/images/maven-logo-black-on-white.png)

[Maven](http://maven.apache.org) 是一个项目管理和自动构建工具。Maven 包是由 POM（Project Object Model）所定义的文件包格式。Maven 包集中存放的地方，就是 Maven 仓库。这些仓库，可以是放在本地，也可以放在某个远程服务器上。 可以是私有仓库，也可以是公开的。使用方式如下

```shell
allprojects {
    repositories {
        mavenCentral();
        jcenter()
        maven {
            url 'file:///Users/name/Documents/Android/repo/'
        }
        maven {
            url 'http://x.x.x.x:8081/nexus/repositories/android/'
        }
    }
}
```

### Gradle

![](https://ss2.baidu.com/6ONYsjip0QIZ8tyhnq/it/u=1950150083,1970746056&fm=58&bpow=500&bpoh=526)

[Gradle](https://gradle.org) 是一个基于 JVM 的构建工具，是一款通用灵活的构建工具，支持 Maven， Ivy 仓库，支持传递性依赖管理，而不需要远程仓库或者是 pom.xml 和 ivy.xml 配置文件，基于 Groovy，build 脚本使用 Groovy 编写。

Android 支持的 Maven 仓库：

- mavenCentral：最早的 maven 中央仓库
- jcenter：Android Studio 0.8 版本起的默认 maven 中央仓库
- 本机仓库
- 内网服务器的私有仓库

## 二、搭建 Maven 私有库原因

每一种技术,每一个框架都不是凭空产生,都是为了**解决特定需求**。因此使用什么技术取决于我们的需求,作为一个研发人员,我们不仅仅要懂得编码,更要有**洞察真实需求的能力**。

做开发时间长的同学一般都会开发多个 App。而这些 APP 有**很多共同的部分**，比如一些工具类，自定义 UI，视频播放功能等甚至是框架。为了**提高整体团队的开发效率，不重复造车轮子**，我们便会设想把这些可以复用的代码通过一种特殊的方式进行管理。而 Maven 正符合我们的要求。这样做有几点好处：

- 实现模块化，不同功能模块可以单独引入，优化实现方式，符合类似**单一原则**

- 对各个模块进行版本迭代更新，执行持续集成。某个模块修改了，跑单元测试，通过后才放上仓库，保证模块质量。

- 带版本管理，有问题还可以回滚到上一个版本
- 组件化，按功能拆分出各种组件，数据存储、网络层、日志 等。提高代码复用率
- 私有库带缓存，可提高引用公开模块的速度

## 三、搭建 Maven 私有库

### Nexus

![](https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=2933299757,2202685093&fm=26&gp=0.jpg)

[Nexus](https://www.sonatype.com/download-oss-sonatype) 是一个基于 Maven 的仓库管理的社区项目。主要的使用场景就是可以在局域网搭建一个 Maven 私服，用来部署**第三方公共构件**或者**作为远程仓库在该局域网的一个代理**。简单举个例子就是：第三方 Jar 包可以放在 Nexus 上，项目可以直接通过 Url 和路径配置直接引用。方便进行统一管理。同时有多个项目在开发的时候，一些共用基础模块可以单独抽取到 Nexus 上，需要用的项目直接从 Nexus 上拉取就行。封闭开发的过程中开发机是不能上公网的，所以连接 central repository 和下载 jar 就比较麻烦，这时就可以用 nexus 搭建起来一个介于公网和局域网之间的桥梁。

### 搭建

#### 1、安装

前往 [Nexus 官网](https://www.sonatype.com/download-oss-sonatype) 下载安装包，并解压存放到自己希望保存的目录。

#### 2、启动

![](/img/in-post/post-maven/nexus.jpg)

前往 nexus 存放位置下的 bin 文件夹，执行 `./nexus start`。稍等 nexus 启动完毕，即可使用浏览器访问 http://127.0.0.1:8081 即可看到 nexus 欢迎页。

![](/img/in-post/post-maven/home.jpg)

#### 3、登录

使用用户名 admin 和默认密码 admin123 登录，即可创建和管理用户。

![](/img/in-post/post-maven/user.jpg)

#### 4、创建仓库

![](/img/in-post/post-maven/repo1.jpg)

仓库分了三种类型：

- hosted(宿主仓库)：用来部署自己，第三方或者公共仓库的构件
- proxy(代理仓库)：代理远程仓库
- group(仓库组)：统一管理多个仓库

这里我们选择 hosted 作为私有库类型，创建 test 仓库。

![](/img/in-post/post-maven/repo2.jpg)

## 四、上传私有库

新建 testutils module，并新建 Utils 测试类。

![](/img/in-post/post-maven/testutils.jpg)

在项目的 build.gradle 中加入 mavenLocal

```shell
allprojects {
    repositories {
        google()
        jcenter()
        //需要添加的
        mavenLocal()
    }
}
```

到 testutils 的 build.gradle 中添加 maven 信息

```shell
apply plugin: 'com.android.library'

//需要添加的
apply plugin: 'maven'

//需要添加的
uploadArchives {
    repositories.mavenDeployer {
        repository(url:"http://127.0.0.1:8081/repository/test/") {
            authentication(userName:"test", password:"123456")
        }
        pom.version="1.0"
        pom.artifactId="testutils"
        pom.groupId="com.test"
    }
}

android {
    compileSdkVersion 28



    defaultConfig {
        minSdkVersion 21
        targetSdkVersion 28
        versionCode 1
        versionName "1.0"

        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"

    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }

}

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])

    implementation 'com.android.support:appcompat-v7:28.0.0'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
}

```

注：

repository 的 url 可访问 nexus  进行复制

![](/img/in-post/post-maven/repourl.jpg)

sync 完 gradle 后，打开 gradle 选项，找到 upload，进行上传。

![](/img/in-post/post-maven/upload.jpg)

上传成功后，即可前往 nexus 查看。

![](/img/in-post/post-maven/uploaded.jpg)

## 五、使用私有库

在项目的 build.gradle 中加入私有 Maven 库

```shell
allprojects {
    repositories {
        google()
        jcenter()
        //需要添加的
        maven { url "http://127.0.0.1:8081/repository/test/" }
    }
}
```

在需要引用私有库的 module 的 build.gradle 中引用私有库

```
implementation 'com.test:testutils:1.0'
```

注：不是 com.test.testutils 而是 **com.test:testutils**

sync 完 gradle 后，即可在 module 中引用

![](/img/in-post/post-maven/import.jpg)

至此，我们完成了私有 Maven 库的搭建和使用，有问题的同学给我留言。

<span id="busuanzi_container_page_pv">
本文总阅读量<span id="busuanzi_value_page_pv"></span>次
</span>
