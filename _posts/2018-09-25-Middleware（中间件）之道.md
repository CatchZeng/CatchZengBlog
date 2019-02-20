---
layout: post
title: "Middleware（中间件）之道"
subtitle: "设计模式"
date: 2018-09-25 09:38:27
author: "CatchZeng"
header-img: "img/post-bg-ios.jpg"
tags:
  - 设计模式
---

<span id="busuanzi_container_page_pv"></span>

## 一、前言

在日常开发中，我们经常遇到逻辑复杂的业务，导致代码写得又长又乱。有些逻辑像一个流程，在不同的节点需要做不同的操作。
比如，我们经常会遇到上传文件的业务。该业务要求先验证文件正确性，然后上传，最后跳转到成功的页面。

```
if  checkFile {
    uploadFile  { result
            if result {
                  showSuccessView { result
                             if result {
                                  //handle success
                             } else {
                                  //handle error
                              }
                  }
            } else {
               //TODO handle error
            }
    }
} else {
  //TODO handle error
}
```

我们可以看到伪代码中呈现着**回调地狱**，上传前、上传、上传后的操作逻辑也零散地分布，很容易造成阅读和维护困难，而**中间件**的出现，让我们处理这类业务变得简单很多。

在讲中间件之前我们先来回顾下 AOP。

## 二、AOP

AOP 意为面向切面编程。 以页面统计为例，先来看下传统的流程。

![页面统计](https://upload-images.jianshu.io/upload_images/943491-632c86a8323e9556.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们可以把方框里的流程合为一个，另外系统还会有其他页面统计流程，我们先把这些流程放到一起：

![页面统计](https://upload-images.jianshu.io/upload_images/943491-407dcc957c986b17.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不难发现每个页面都有一个相同的页面统计流程。这样的处理有如下几个问题：

- **重复代码**：每个界面都需要加入页面统计的代码
- **影响主流程的清晰度**：页面统计跟**主流程**无关，但又需要加入到各个界面中
- **扩展性差**：新增一个界面，就得为其加入页面统计的代码

有没有想过把这个页面统计的代码是提取出来，不放到主流程里去呢？这就是 AOP 的思想了，传统的流程讲究**从上而下**的处理流程 ，而 AOP 讲究**“面”**,从**横向切面**将相同的流程提取出去，所以也叫**“横切面”**，如下图所示。

![Group 5.png](https://upload-images.jianshu.io/upload_images/943491-bc91873e5f664616.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

AOP 提倡从横向切面思路向管道某个位置插入一段代码逻辑，这样就实现在任何业务**逻辑前后**都有相同代码逻辑段，开发者只需专注写业务逻辑，既不影响主流程，而且隔离了业务逻辑，达到**高内聚低耦合**。

附上 iOS 使用 AOP 实现页面统计的代码，帮助大家理解 AOP。

```
@implementation UIViewController (Analytics)

+ (void)load{
    [self swizzleInstanceMethod:@selector(viewWillAppear:) with:@selector(swizzled_viewWillAppear:)];
    [self swizzleInstanceMethod:@selector(viewWillDisappear:) with:@selector(swizzled_viewWillDisappear:)];
}

- (void)swizzled_viewWillAppear:(BOOL)animated{
    [self swizzled_viewWillAppear:animated];
    [Analytics beginTimingEvent: self.className];
}

- (void)swizzled_viewWillDisappear:(BOOL)animated{
    [self swizzled_viewWillDisappear:animated];
    [Analytics endTimingEvent: self.className];
}
```

### 思考

- AOP 和 OOP 是什么关系？有什么区别？
  写多了 OOP 的代码，会发现 AOP 跟 OOP 的思路不同。OOP 是将做**同一件事情的业务逻辑**封装成一个对象。但是，在做一件事情过程（主流程）中又想做别的事情（比如页面统计）对 OOP 来说难以解决。而 AOP 的出现让 OOP 代码能专注于主流程，更好地遵循[单一职责原则](http://catchzeng.com/2017/02/19/聊聊设计模式原则-一-单一职责原则/)，提高内聚性。所以，我认为 AOP 对 OOP 做了一个补充。

* 怎么判断代码是否达到了高内聚？
  这个问题吊一下大家胃口，这里先不解答，大家可以在留言处评论，后续会贴出个人的理解。

* AOP 思想如何解决上传文件的业务的问题？
  由于 JavaScript 的动态性较好，下面以 JavaScript 代码为例，看下如何利用 AOP 优化上传文件业务。

### 利用 AOP 优化上传文件业务

AOP 从横向切面思路向管道**某个位置**插入一段代码逻辑。而这个位置通常就是**调用前**（before）和**调用后**（after）。

```
Function.prototype.before = function(fn){
  var self = this;
   return function(){
     var res = fn.call(this);
     if(res) {
        self.apply(this, arguments);
     }
   }
}

Function.prototype.after = function(fn){
  var self = this;
   return function(){
     self.apply(this, arguments);
     fn.call(this);
   }
}
```

有了这两个扩展，可以按下面的方式实现上传业务。

```
function checkFile(){
   console.log('checking file');
   if fileIsVaild() {
      return true
   } else {
      console.log('file is invalid');
      return false
    }
}

function uploadFile() {
  console.log('uploading file');
  if fileUploadSuccess() {
      return true
   } else {
      console.log('file upload failed');
      return false
   }
}

function showSuccessView() {
  console.log('show success view');
}

uploadFile.before(checkFile).after(showSuccessView)();
```

### 思考

- 使用 AOP 后解决了什么问题？还有什么问题？
  从上面的例子，可以看出 AOP 已经实现了业务隔离。但却带来了一串长长的**链式调用**，如果处理不当很容易**掉链子**。另外，这种结构实现**异步操作**较为麻烦。

- 有什么办法，既能隔离业务，又能清爽地使用？
  这时候，我们的主角就该上场了。

## 三、中间件

为了理解中间件，我们来看下[Koa](https://koajs.com/#introduction)的中间件使用。

```
const logger = (ctx, next) => {
  console.log(`${Date.now()} ${ctx.request.method} ${ctx.request.url}`);
  next();
}
app.use(logger);
```

像上面代码中的 logger 函数就叫做"中间件"（middleware），因为它处在 HTTP Request 和 HTTP Response 中间，用来实现某种中间功能。app.use()用来加载中间件。

### 中间件栈

多个中间件会形成一个栈结构（middle stack），以"先进后出"（first-in-last-out）的顺序执行，被称为**洋葱结构**。

![洋葱结构](https://upload-images.jianshu.io/upload_images/943491-4030de75a169e4c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

举个例子

```
const one = (ctx, next) => {
  console.log('>> one');
  next();
  console.log('<< one');
}

const two = (ctx, next) => {
  console.log('>> two');
  next();
  console.log('<< two');
}

const three = (ctx, next) => {
  console.log('>> three');
  next();
  console.log('<< three');
}

app.use(one);
app.use(two);
app.use(three);

/*result
>> one
>> two
>> three
<< three
<< two
<< one
*/
```

中间件一个奇妙的点在于**next 函数**。如果中间件内部没有调用 next 函数，那么执行权就不会传递下去。

### 中间件的实现

```
export default class MiddlewareCenter {
    constructor() {
        this._middlewares = []
        this._context = null
    }

    use(middleware) {
        if (typeof middleware != 'function') {
            console.warn('middleware must be a function.')
            return null
        }
        this._middlewares.push(middleware)
        return this
    }

    handleRequest(context) {
        const fn = compose(this._middlewares)
        this._context = context
        fn(this._context)
    }
}

function compose(middleware) {
    if (!Array.isArray(middleware)) throw new TypeError('Middleware stack must be an array!')

    for (const fn of middleware) {
        if (typeof fn !== 'function') throw new TypeError('Middleware must be composed of functions!')
    }

    /**
     * @param {Object} context
     * @return {Promise}
     * @api public
     */
    return function (context, next) {
        // last called middleware #
        let index = -1

        return dispatch(0)

        function dispatch(i) {
            if (i <= index) return Promise.reject(new Error('next() called multiple times'))
            index = i

            let fn = middleware[i]
            if (i === middleware.length) fn = next
            if (!fn) return Promise.resolve()
            try {
                return Promise.resolve(fn(context, dispatch.bind(null, i + 1)))
            } catch (err) {
                return Promise.reject(err)
            }
        }
    }
}
```

详细的代码可以看我的开源项目[middleware-center](https://github.com/CatchZeng/middleware-center)。

### 利用中间件优化上传业务

```
const middlewareCenter = new UploadFileCenter()
middlewareCenter.handle('uploader')

/* result
beforeUpload
startUplaod
finishUpload
after finishUpload
after startUplaod
after beforeUpload
*/
```

```
class UploadFileCenter extends MiddlewareCenter {

    constructor() {
        super()
        this._middlewareMap = { 'uploader': [this.beforeUpload, this.startUplaod, this.finishUpload] }
        this.content = ""
    }

    handle(name) {
        let middlewares = this._middlewareMap[name]
        for (let middleware of middlewares) {
            this.use(middleware)
        }
        this.handleRequest(this)
    }

    // Middlewares

    async beforeUpload (ctx, next) {
        console.log('beforeUpload')
        ctx.content = await ctx.genContent()
        await next()
        console.log('after beforeUpload')
    }

    async startUplaod (ctx, next) {
        console.log('startUplaod')
        let result = await ctx.upload(ctx.content)
        await next()
        console.log('after startUplaod')
    }

    finishUpload (ctx, next) {
        console.log('finishUpload')
        //do something like notify listeners
        console.log('after finishUpload')
    }

    // Helpers
    genContent() {
        return new Promise((resolve, reject) => {
            setTimeout(function () {
                resolve('upload content')
            }, 3)
        })
    }

    upload(content) {
        return new Promise((resolve, reject) => {
            setTimeout(function () {
                resolve(true)
            }, 5)
        })
    }
}
```

大家可以看到，利用中间件不但可以实现业务隔离，调用也很清晰，只要**根据业务调整 use 的顺序**即可。各个中间件还可以**随意组合，各组件间也没有依赖关系，自身内聚性高**。细心的朋友可以发现，[middleware-center](https://github.com/CatchZeng/middleware-center) 还支持**异步方法**。得益于中间件的洋葱结构，使得使用者可以处理业务的任何“位置”（如：beforeUpload、startUplaod、finishUpload、after finishUpload、after startUplaod、after beforeUpload）

### 遍地开花

好思想应该遍地开花，业余时间我用 swift 简单实现了中间件模型[MiddlewareCenter](https://github.com/CatchZeng/MiddlewareCenter)，但还没优化，和处理引用问题，感兴趣的朋友可以一起维护。

```
class ViewController: UIViewController {

    let center = MiddlewareCenter()

    override func viewDidLoad() {
        super.viewDidLoad()

        center.use(BeforeUpload())
        center.use(StartUpload())
        center.use(EndUpload())

        center.handle(ctx: nil)
    }
}

public class StartUpload: Middleware {
    public override func execute() {
        print("before StartUpload.")
        next?.execute()
        print("after StartUpload.")
    }
}

public class BeforeUpload: Middleware {
    public override func execute() {
        print("before upload.")
        next?.execute()
        print("after upload.")
    }
}

public class EndUpload: Middleware {
    public override func execute() {
        print("before EndUpload.")
        next?.execute()
        print("after EndUpload.")
    }
}
```

## 四、总结

中间件实现了业务隔离，满足每个业务所需的数据，又能很好控制业务**下发执行**的权利，所以“中间件”模式算是一种不错的设计。
理解中间件，主要理解三个概念，包括：context、next、洋葱结构。context 为业务所需的上下文，比如 koa 是处理网络请求的，所以它的 context 包含 request、response；next 是业务流的下发控制，使用好 next，可以灵活地处理各种业务，包括错误处理、中间件重用等；洋葱结构可以让使用者轻松地处理各个流程的“位置”。

<span id="busuanzi_container_page_pv">
本文总阅读量<span id="busuanzi_value_page_pv"></span>次
</span>
