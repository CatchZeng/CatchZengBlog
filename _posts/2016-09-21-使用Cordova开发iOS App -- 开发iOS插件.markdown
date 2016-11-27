---
layout:     post
title:      "使用Cordova开发iOS App -- 开发iOS插件"
subtitle:   "iOS开发"
date:       2016-09-21 09:38:27 
author:     "CatchZeng"
header-img: "img/post-bg-2015.jpg"
tags:
    - iOS
---
<span id="busuanzi_container_page_pv"></span>


本篇在[上一篇--Cordova的环境搭建](http://catchzeng.com/2016/09/20/使用Cordova开发iOS-App-环境搭建/)基础上以一个例子讲解如何为Cordova开发iOS插件。

### 一、创建插件文件

在Plugins文件夹下创建插件文件夹命名为com.catchzeng.testplugin
![Plugins](/img/in-post/post-cordova2/1.png)

创建类TestPlugin继承于CDVPlugin
![Plugins](/img/in-post/post-cordova2/2.png)

注意：创建类后会报头文件``` #import <Cordova/Cordova.h>```找不到的问题，替换成```  #import <Cordova/CDVPlugin.h> ```即可。

### 二、实现插件代码

该插件实现的功能是JS调用原生的代码弹出一个自定义视图控制器（附带参数），并在弹起后回传参数给JS。

![Plugins](/img/in-post/post-cordova2/3.gif)

```

@implementation TestPlugin

-(void)testWithTitle:(CDVInvokedUrlCommand *)command{
    if (command.arguments.count>0) {
        //customize argument
        NSString* title = command.arguments[0];
        
        TestViewController* testViewCtrl = [[TestViewController alloc]init];
        [self.viewController presentViewController:testViewCtrl animated:YES completion:^{
            CDVPluginResult* pluginResult = [CDVPluginResult resultWithStatus:CDVCommandStatus_OK messageAsString:@"我是OC回传的参数!"];
            testViewCtrl.labTitle.text = title;
            [self.commandDelegate sendPluginResult:pluginResult callbackId:command.callbackId];
        }];
    }else{
        //callback
        CDVPluginResult* pluginResult = [CDVPluginResult resultWithStatus:CDVCommandStatus_ERROR messageAsString:@"没有参数"];
        [self.commandDelegate sendPluginResult:pluginResult callbackId:command.callbackId];
    }
}

@end
```
说明：

command.arguments：JS传递过来的参数列表，可自行协定参数的格式

CDVPluginResult：插件结果回调类，用于描述结果的状态和回传数据给JS

command.callbackId：对应回调JS时，指定发送的函数id


### 三、测试插件
在config.xml文件中加入以下代码让JS能够调用我们的OC类,需要注意的是需要配置Staging下的config.xml,而不是外部的

```
<feature name="ocTestPlugin">
  <param name="ios-package" value="TestPlugin" />
</feature>
```
修改index.html

```
<!DOCTYPE html>
<html>
    <head>
        <title>testPlugin</title>
        <meta http-equiv="Content-type" content="text/html; charset=utf-8">
            <script type="text/javascript" charset="utf-8" src="cordova.js"></script>
            <script type="text/javascript" charset="utf-8">
                
            function testPlugin() {
                cordova.exec(testSuccess,testFailed,"ocTestPlugin","testWithTitle",["我是JS传的参数！"]);
            }
            function testSuccess(msg) {
                alert(msg);
            }
            function testFailed(msg) {
                alert('failed: ' + msg);
            }
            </script>
            </head>
    
    <body style="padding-top:100px">
        <button style="font-size:17px;" onclick="testPlugin();">测试iOS插件</button>
        <br>
    </body>
</html>
```
最后附上工程的结构图

![Plugins](/img/in-post/post-cordova2/4.png)

开发iOS插件部分就先讲到这里，[下一篇](http://www.catchzeng.com/2016/09/22/使用Cordova开发iOS-App-打包插件及JS脚本/)将讲解如何将本篇的插件打包供前端人员使用。