---
layout:     post
title:      "使用Cordova开发iOS App -- 打包插件及JS脚本"
subtitle:   "iOS开发"
date:       2016-09-22 09:38:27 
author:     "CatchZeng"
header-img: "img/post-bg-2015.jpg"
tags:
    - iOS
---
<span id="busuanzi_container_page_pv"></span>

在[上一篇--开发iOS插件](http://catchzeng.com/2016/09/21/使用Cordova开发iOS-App-开发iOS插件/)中我们开发了一个插件，本篇将讲解如何将插件打包。

### 一、创建插件包
首先在桌面上创建一个文件夹com.catchzeng.testplugin(遵守命名规范),并创建子文件夹及子文件如下图,将插件的代码复制到ios文件夹下。

![Plugins](/img/in-post/post-cordova3/1.png)

![Plugins](/img/in-post/post-cordova3/2.png)

### 二、编写JS代码
编写testPlugin.js，向外部暴露testModel的testPlugin方法，这便是前端人员需要调用的接口。

```
var exec = require("cordova/exec");

function TestModel() {};

TestModel.prototype.testPlugin = function (success,fail,option) {
     exec(success, fail, 'testPlugin', 'testWithTitle', option);
};

var testModel = new TestModel();
module.exports = testModel;

```

### 三、配置plugin.xml文件
配置plugin.xml 就是为了告诉cordova我们的文件路径在哪,我们的oc类名是什么,oc对象名是什么,js类名及js对象名是什么等信息。这样cordova在安装插件时,才能找到正确的插件文件。

```
<?xml version="1.0" encoding="UTF-8" ?>
<plugin xmlns="http://phonegap.com/ns/plugins/1.0"
    id="com.catchzeng.testplugin"
    version="1.0.0">
    <engines>
        <engine name="cordova" version=">=3.3.0" />
    </engines>
    
    <name>testPlugin</name>
    <description>测试插件</description>
    
    <js-module src="www/testPlugin.js" name="testModel">
        <clobbers target="testModel" />
    </js-module>
    
    <platform name="ios">
        <source-file src="src/ios/TestPlugin.m" />
        <header-file src="src/ios/TestPlugin.h" />
        <source-file src="src/ios/TestViewController.m" />
        <header-file src="src/ios/TestViewController.h" />
        <resource-file src="src/ios/TestViewController.xib" />
        <resource-file src="src/ios/test.png" />
        
        <config-file target="config.xml" parent="/widget">
            
            <feature name="testPlugin">
                <param name="ios-package" value="TestPlugin" />
            </feature>
        </config-file>
    </platform>
</plugin>
```
参数说明：

``` id="com.catchzeng.testplugin"``` ：插件id必须与文件夹名称相同

```<js-module src="www/testPlugin.js" name="testModel">```：此处配置js所在目录和调用的类（testModel）

```<source-file>```：标示插件所需的oc .m文件

```<header-file>```：标示插件所需的oc .h文件

```<resource-file>```：标示插件所需资源文件（图片、xib资源等）

```<feature name="testPlugin"><param name="ios-package" value="TestPlugin" />```：指明插件映射至ios的类名，此处的testPlugin便是对应到testPlugin.js ```  exec(success, fail, 'testPlugin', 'testWithTitle', option);``` 中的testPlugin。

### 四、测试插件

新建一个Cordova项目，并添加iOS平台作为测试项目。

![Plugins](/img/in-post/post-cordova3/3.png)

添加插件到测试项目
```
cordova plugin add  xxxxxx
```

![Plugins](/img/in-post/post-cordova3/4.png)

修改工程项目的index.html

![Plugins](/img/in-post/post-cordova3/5.png)

```
<!DOCTYPE html>
<html>
    <head>
        <title>TestPlugin</title>
        <meta http-equiv="Content-type" content="text/html; charset=utf-8">
            <script type="text/javascript" charset="utf-8" src="cordova.js"></script>
            <script type="text/javascript" charset="utf-8">

            function testPlugin() {
                testModel.testPlugin(alertSuccess,alertFail,["我是JS传来的参数！"]);
            }

            function alertSuccess(msg) {
                alert(msg);
            }

            function alertFail(msg) {
                alert('调用OC失败: ' + msg);
            }
            </script>
    </head>

    <body style="padding-top:50px">
        <button style="font-size:17px;" onclick="testPlugin();">调用iOS插件</button> <br>
    </body>
</html>
```
重新build iOS项目
``` cordova build ios ```

![Plugins](/img/in-post/post-cordova3/6.png)

此时打开iOS工程后，运行程序便能得到看到插件的效果。

![Plugins](/img/in-post/post-cordova3/7.gif)


将插件文件上传到git后，前端人员也可以使用git的方式安装我们编写的插件

```  cordova plugin add  https://github.com/CatchZeng/com.catchzeng.testplugin ```

最后附上Demo地址：[https://github.com/CatchZeng/com.catchzeng.testplugin](https://github.com/CatchZeng/com.catchzeng.testplugin)