# 优化关键渲染路径

> 作者：Ilya Grigorik

为尽快完成首次渲染，我们需要最大限度减小以下三种可变因素：

* 关键资源的数量。
* 关键路径长度。
* 关键字节的数量。

关键资源是可能阻止网页首次渲染的资源。这些资源越少，浏览器的工作量就越小，对 CPU 以及其他资源的占用也就越少。

同样，关键路径长度受所有关键资源与其字节大小之间依赖关系图的影响：某些资源只能在上一资源处理完毕之后才能开始下载，并且资源越大，下载所需的往返次数就越多。

最后，浏览器需要下载的关键字节越少，处理内容并让其出现在屏幕上的速度就越快。要减少字节数，我们可以减少资源数（将它们删除或设为非关键资源），此外还要压缩和优化各项资源，确保最大限度减小传送大小。

**优化关键渲染路径的常规步骤如下：**

1. 对关键路径进行分析和特性描述：资源数、字节数、长度。
2. 最大限度减少关键资源的数量：删除它们，延迟它们的下载，将它们标记为异步等。
3. 优化关键字节数以缩短下载时间（往返次数）。
4. 优化其余关键资源的加载顺序：您需要尽早下载所有关键资产，以缩短关键路径长度。

# PageSpeed 规则和建议

本指南审视 PageSpeed Insights 规则背景：优化关键渲染路径时的注意事项以及原因。

## 消除阻塞渲染的 JavaScript 和 CSS {#javascript_css}

要以最快速度完成首次渲染，需要最大限度减少网页上关键资源的数量并（尽可能）消除这些资源，最大限度减少下载的关键字节数，以及优化关键路径长度。

## 优化 JavaScript 的使用 {#javascript}

默认情况下，JavaScript 资源会阻塞解析器，除非将其标记为`async`或通过专门的 JavaScript 代码段进行添加。阻塞解析器的 JavaScript 会强制浏览器等待 CSSOM 并暂停 DOM 的构建，继而大大延迟首次渲染的时间。

### 首选使用异步 JavaScript 资源 {#javascript_1}

异步资源不会阻塞文档解析器，让浏览器能够避免在执行脚本之前受阻于 CSSOM。通常，如果脚本可以使用`async`属性，也就意味着它并非首次渲染所必需。可以考虑在首次渲染后异步加载脚本。

### 避免同步服务器调用 {#_1}

使用`navigator.sendBeacon()`方法来限制 XMLHttpRequests 在`unload`处理程序中发送的数据。 因为许多浏览器都对此类请求有同步要求，所以可能减慢网页转换速度，有时还很明显。 以下代码展示了如何利用`navigator.sendBeacon()`向`pagehide`处理程序而不是`unload`处理程序中的服务器发送数据。

```js
<script>
  function() {
    window.addEventListener('pagehide', logData, false);
    function logData() {
      navigator.sendBeacon(
        'https://putsreq.herokuapp.com/Dt7t2QzUkG18aDTMMcop',
        'Sent by a beacon!');
    }
  }();
</script>
```

新增的`fetch()`方法提供了一种方便的数据异步请求方式。由于它尚未做到随处可用，因此您应该利用功能检测来测试其是否存在，然后再使用。该方法通过 Promise 而非多个事件处理程序来处理响应。不同于对 XMLHttpRequest 的响应，从 Chrome 43 开始，fetch 响应将是 stream 对象。这意味着调用`json()`也会返回 Promise。

```js
<script>
fetch('./api/some.json')  
  .then(  
    function(response) {  
      if (response.status !== 200) {  
        console.log('Looks like there was a problem. Status Code: ' +  response.status);  
        return;  
      }
      // Examine the text in the response  
      response.json().then(function(data) {  
        console.log(data);  
      });  
    }  
  )  
  .catch(function(err) {  
    console.log('Fetch Error :-S', err);  
  });
</script>
```

`fetch()`方法也可处理 POST 请求。

```js
<script>
fetch(url, {
  method: 'post',
  headers: {  
    "Content-type": "application/x-www-form-urlencoded; charset=UTF-8"  
  },  
  body: 'foo=bar&lorem=ipsum'  
}).then(function() { // Aditional code });
</script>
```

### 延迟解析 JavaScript {#javascript_2}

为了最大限度减少浏览器渲染网页的工作量，应延迟任何非必需的脚本（即对构建首次渲染的可见内容无关紧要的脚本）。

### 避免运行时间长的 JavaScript {#javascript_3}

运行时间长的 JavaScript 会阻止浏览器构建 DOM、CSSOM 以及渲染网页，所以任何对首次渲染无关紧要的初始化逻辑和功能都应延后执行。如果需要运行较长的初始化序列，请考虑将其拆分为若干阶段，以便浏览器可以间隔处理其他事件。

## 优化 CSS 的使用 {#css}

CSS 是构建渲染树的必备元素，首次构建网页时，JavaScript 常常受阻于 CSS。确保将任何非必需的 CSS 都标记为非关键资源（例如打印和其他媒体查询），并应确保尽可能减少关键 CSS 的数量，以及尽可能缩短传送时间。

### 将 CSS 置于文档 head 标签内 {#css_head}

尽早在 HTML 文档内指定所有 CSS 资源，以便浏览器尽早发现`<link>`标记并尽早发出 CSS 请求。

### 避免使用 CSS import {#css_import}

一个样式表可以使用 CSS import \(`@import`\) 指令从另一样式表文件导入规则。不过，应避免使用这些指令，因为它们会在关键路径中增加往返次数：只有在收到并解析完带有`@import`规则的 CSS 样式表之后，才会发现导入的 CSS 资源。

### 内联阻塞渲染的 CSS {#css_1}

为获得最佳性能，您可能会考虑将关键 CSS 直接内联到 HTML 文档内。这样做不会增加关键路径中的往返次数，并且如果实现得当，在只有 HTML 是阻塞渲染的资源时，可实现“一次往返”关键路径长度。

