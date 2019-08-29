# React-router怎么实现侦听浏览器路由变化的？

**React Router是React官方提供的标准路由库。一直以来前端缺乏一个统一的路由解决方案，我们之前通常都是自己封装统一的路由，由于使用的框架不同，实现的方式也是五花八门。React Router的出现是伴随着React一起普及的，让我们开始吧，看看React Router完成的哪些工作，又做出了哪些创新。**

这个教程主要给你介绍React Router 的v4版本，以及你使用它可以做的大部分事情。我们在使用的inferno-router,基本也是沿用React-router的文档和API。

> Inferno Router is a routing library for Inferno. It is a port of react-router 4.

![React Set](https://ask.qcloudimg.com/http-save/yehe-1120945/pmlkf7y2i3.jpeg?imageView2/2/w/1620)

## 开始

熟悉`React`或者`Webpack`的应该都知道客户端渲染创建的单页应用（SPAs）。一个SPA会包含很多视图，对应`webpack`中配置的entry（也可以称为入口页面），和传统的多页应用不同的是，视图之间的跳转不应该导致整个页面被重新加载，而是应该就在当前页面里渲染。具象化来说是一个`portal`(入口)，里面包含多个页面view。习惯于多页应用的最终用户，期望在一个SPA中应该满足以下功能：

- 每个视图应该有唯一URL来标识。这样用户可以通过书签收藏的URL指向引用的资源。 例如： www.example.com/pageA

- 浏览器的前进后退功能

- 动态URL的适配。动态生成的URL使用嵌套视图来对应。 - 例如：`example.com/product/shoes/101`，101是动态的产品id。

**路由定义**是指在初始化React Router时，声明的路由项配置。
```
<Route path="/about" component={About}/>
```
这是一个最常见的Route配置，配置项非常语义化，`path`对应路由路径，`component`对应[React Component](https://react.docschina.org/docs/react-component.html)。你可以把`<Route>`组件放在任何你想要路由渲染的地方。当然，`<Route>`,`<Link>`以及其他React Router都已经封装成了组件，所以可以非常方便的在React里使用。

## 概述

React Router的基础
1. 基本路由跳转
2. 嵌套路由
3. 带参数路径
4. 保护式路由

主要围绕构建这些路由所涉及的概念进行讨论。

React Router库包含三个包： `react-router`, `react-router-dom`, 和 `react-router-native`。 `react-router`是路由的核心包，而其他两个是基于特定环境的。如果你在开发一个网站，你应该使用 `react-router-dom`，如果你在移动应用的开发环境使用React Native，你应该使用 `react-router-native`。

React Router 是建立在[history](https://github.com/ReactTraining/history)之上的。简而言之，一个 `history` 知道如何去监听浏览器地址栏的变化， 并解析这个 URL 转化为 location 对象， 然后 router 使用它匹配到路由，最后正确地渲染对应的组件。

## React Route 基础
下面是普遍的路由的例子：
```
<Router>
  <Route exact path="/" component={Home}/>
  <Route path="/category" component={Category}/>
  <Route path="/login" component={Login}/>
  <Route path="/products" component={Products}/>
</Router>
```
上面的例子中，我们使用了`<Router>`组件和一些`<Route>`组件来创建一个基本的路由结构。

通过阅读源码，我们知道了所有的路由操作都是通过操作`history`来进行的，接下来正式介绍下`history`。

## History

> `history`是一个统一了所有DOM和非DOM环境会话记录的Javascript库。history提供了简洁的API,让你可以管理history堆栈，跳转，跳转前确认，以及保持会话之间的状态。

history是react router所必需的两个依赖之一。
每个router组件创建了一个history对象，用来记录当前路径( `history.location` )，上一步路径也存储在堆栈中。当前路径改变时，视图会重新渲染，给你一种跳转的感觉。当前路径又是如何改变的呢？history对象有 `history.push()`和 `history.replace()`这些方法来实现。当你点击 `<Link>`组件会触发 `history.push()`，使用 `<Redirect>`则会调用 `history.replace()`。其他方法 - 例如 `history.goBack()`和 `history.goForward()` - 用来根据页面的后退和前进来跳转history堆栈。

那么history内部又是怎么样实现路由监听和响应的呢？

#### History的实现

在认识history之前，我们首先知道浏览器给我们提供的原生路由功能，比如[`location`](https://developer.mozilla.org/zh-CN/docs/Web/API/Location)，接下来的很多方法中将出现它们的影子。

`history`提供了三种路由实现的方式。
1. `browserHistory`，支持HTML5 history API的路由实现（[兼容性](https://caniuse.com/#feat=history)）
2. `hashHistory`，向下兼容传统浏览器的hash路由实现
3. `createMemoryHistory`，React实现的定制化路由，不依赖任何环境，可用于非DOM环境，比如`React Native` 或 `SSR`

#### browserHistory
通过`history.createBrowserHistory`创建

原生方法 ---> 封装方法
1. `pushState` ---> `push`
2. `popState`  ---> `goBack`
3. `replaceState` ---> `replace`

这些方法都只会操作浏览器的历史记录，而不会引起页面的刷新。`listen`方法是封装的监听的方法，

#### hashHistory
通过`history.createBrowserHistory`创建



location: pathname,search,hash,key






那么我们经常使用的 <BrowserRouter> 和 <HashRouter>是什么呢，相信你已经猜到了，这只是React Router对 browserHistory 和 hashHistory 实现的组件封装。



这两个有什么区别呢，我们可以在它们所创建的URL明显看出：
```
// <BrowserRouter>
不带hash值
http://example.com/about

// <HashRouter>
带hash值
http://example.com/#/about
```


## 

## Tips

#### 我经常在链接上看到`?_k=ckuvup`这样的标识符有用吗？
有用！当一个`history`通过应用程序的push或replace跳转时，它可以在新的location中存储"location state",而不显示在URL中，这就像是在POST一个表单数据。

在DOM API中，这些hash history通过`window.location.hash = newHash `，很简单的利用hash来实现跳转。但是这种行为是不具备回溯性的，我们想要全部的history都能够使用location state,这就要求我们为每一个location创建一个唯一的key，并把它们的状态存储在 **session storage** 中。当访客点击"前进"和"后退"时，我们就可以使用key:value的机制找到这个location 去恢复它的state。


#### 参考资料
[React-router wiki](http://react-guide.github.io/react-router-cn/docs/guides/basics/Histories.html)

[React History](https://github.com/ReactTraining/history)

[React Router v4 完全指北](https://cloud.tencent.com/developer/article/1407614)




