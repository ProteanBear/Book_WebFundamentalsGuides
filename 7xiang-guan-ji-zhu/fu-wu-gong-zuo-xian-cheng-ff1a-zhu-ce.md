# 服务工作线程注册

> 作者：Jeff Posnick

[服务工作线程](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers?hl=zh-cn)可有效加快重复访问网络应用的速度，但您应采取措施确保服务工作线程的初始安装不会恶化用户的首次访问体验。

一般情况下，延迟服务工作线程[注册](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerContainer/register)直至初始页面完成加载可为用户（特别是网络连接较慢的移动设备用户）提供最佳体验。

## 通用注册样板文件 {#_1}

如果您曾阅读有关服务工作线程的内容，您可能会看到与以下内容实质相似的样板文件：

```js
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/service-worker.js');
}
```

上述内容有时会附带几个`console.log()`语句，或包含[代码](https://github.com/GoogleChrome/sw-precache/blob/master/demo/app/js/service-worker-registration.js#L20)（其检测是否对以前的服务工作线程注册进行了更新），以此通知用户刷新页面。但对于标准的几行代码而言，这些只是细微变化。

那么，`navigator.serviceWorker.register`是否有任何细微差别？是否有任何可以遵循的最佳做法？ 毫无意外（考虑到本文还未完结），对这两个问题的回答都是“有”。

## 用户的首次访问 {#_2}

我们考虑一下用户首次访问网络应用。此时还没有服务工作线程，浏览器无法提前知道最终是否会安装一个服务工作线程。

作为开发者，您的首要任务应该是确保浏览器快速获取显示交互页面所需的最低限度的关键资源集。拖慢检索这些响应速度的任何资源都不利于实现快速的交互体验。

现在，想象一下，在下载页面需要渲染的 JavaScript 或图像的进程中，您的浏览器决定启动一个后台线程或进程（为简单起见，我们假设启动一个线程）。假定您使用的不是较大的台式机，而是性能不足的手机，世界上很多人都将手机作为主要设备。启动这个额外的线程将加剧对 CPU 时间和内存的争用，从而影响浏览器渲染交互网页。

空闲的后台线程不太可能有重大意义。但是，如果该线程不处于空闲状态，而是决定也将开始从网络下载资源，那该怎么办？比起对 CPU 或内存的任何顾虑，我们更应该担心许多移动设备可用的有限带宽。带宽非常宝贵，因此，不要同时下载次要资源，这样会影响关键资源的下载。

所有这些都说明，在后台启动一个新的服务工作线程下载和缓存资源，违背了为用户首次访问网站提供最快交互体验的目标。

## 改进样板文件 {#_3}

解决方案是通过选择调用`navigator.serviceWorker.register()`的时间来控制服务工作线程的启动。 一个简单的经验法则是延迟注册，直到[`load event`](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onload)在`window`上触发，如下所示：

```
if
(
'serviceWorker'
in
 navigator
)
{


  window
.
addEventListener
(
'load'
,
function
()
{


    navigator
.
serviceWorker
.
register
(
'/service-worker.js'
);


});


}


```

但是启动服务工作线程注册的适当时间还取决于您的网络应用在加载后将做什么。 例如，[Google I/O 2016 网络应用](https://events.google.com/io2016/?hl=zh-cn)在过渡到主屏幕前先显示一个简短的动画。我们的团队[发现](https://developers.google.com/web/showcase/2016/iowa2016?hl=zh-cn)，在显示动画期间启动服务工作线程注册会导致低端移动设备出现卡顿。当浏览器很有可能出现几秒的空闲状态时，我们可以[延迟](https://github.com/GoogleChrome/ioweb2016/blob/8cfa27261f9d07fe8a5bb7d228bd3f35dfc9a91e/app/scripts/helper/elements.js#L42)服务工作线程注册直到动画显示完毕，而不是为用户提供糟糕的体验。

同样，如果您的网络应用使用在页面加载后执行额外设置的框架，则查看一个框架特定的事件，其表明该工作何时完成。

