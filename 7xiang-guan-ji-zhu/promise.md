# JavaScript Promise：简介

> 作者：Jake Archibald

女士们，先生们，请做好准备，迎接网页开发史上的关键时刻。

_\[鼓点响起\]_

Promise 已获得 JavaScript 的原生支持！

_\[烟花绽放、彩纸飘飘、人群沸腾\]_

此刻，您可能属于以下其中某一类：

* 人群在您身边欢呼雀跃，但是您感到莫名其妙。可能您甚至连“promise”是什么都不知道。因此您耸耸肩，但是从天而降的彩纸虽轻如鸿毛却让您无法释怀。如果真是这样，您也无需担心，我可花了很长的时间才弄明白为什么我应该关注它。您可能想从[开始](#whats-all-the-fuss-about)处开始。
* 您非常抓狂！觉得晚了一步，对吗？您可能之前使用过这些 Promise，但让您困扰的是，不同版本的 API 各有差异。JavaScript 官方版本的 API 是什么？您可能想要从[术语](#promise-terminology)开始。
* 您已知道这些，您会觉得那些上窜下跳的人很好笑，居然把它当作新闻。您可以先自豪一把，然后直接查看[API 参考](#promise-api-reference)

## 人们究竟为何欢呼雀跃？ {#whats-all-the-fuss-about}

JavaScript 是单线程工作，这意味着两段脚本不能同时运行，而是必须一个接一个地运行。在浏览器中，JavaScript 与因浏览器而异的其他 N 种任务共享一个线程。但是通常情况下 JavaScript 与绘制、更新样式和处理用户操作（例如，高亮显示文本以及与格式控件交互）处于同一队列。操作其中一项任务会延迟其他任务。

我们人类是多线程工作。您可以使用多个手指打字，可以一边开车一边与人交谈。唯一一个会妨碍我们的是打喷嚏，因为当我们打喷嚏的时候，所有当前进行的活动都必须暂停。这真是非常讨厌，尤其是当您在开车并想与人交谈时。您可不想编写像打喷嚏似的代码。

您可能已使用事件和回调来解决该问题。以下是一些事件：

```js
img1.callThisIfLoadedOrWhenLoaded(function() {
  // loaded
}).orIfFailedCallThis(function() {
  // failed
});

// and…
whenAllTheseHaveLoaded([img1, img2]).callThis(function() {
  // all loaded
}).orIfSomeFailedCallThis(function() {
  // one or more failed
});
```

这可不会像打喷嚏那样打断您。我们获得图片、添加几个侦听器，之后 JavaScript 可停止执行，直至其中一个侦听器被调用。

遗憾的是，在上例中，事件有可能在我们开始侦听之前就发生了，因此我们需要使用图像的“complete”属性来解决该问题：

```js
img1.ready().then(function() {
  // loaded
}, function() {
  // failed
});

// and…
Promise.all([img1.ready(), img2.ready()]).then(function() {
  // all loaded
}, function() {
  // one or more failed
});
```

这不会捕获出错的图像，因为在此之前我们没有机会侦听到错误。遗憾的是，DOM 也没有给出解决之道。而且，这还只是加载一个图像，如果加载一组图像，情况会更复杂。

## 事件并不总是最佳方法 {#_1}

事件对于同一对象上发生多次的事情（如 keyup、touchstart 等）非常有用。对于这些事件，实际您并不关注在添加侦听器之前所发生的事情。但是，如果关系到异步成功/失败，理想的情况是您希望：

```js
img1.callThisIfLoadedOrWhenLoaded(function() {
  // loaded
}).orIfFailedCallThis(function() {
  // failed
});

// and…
whenAllTheseHaveLoaded([img1, img2]).callThis(function() {
  // all loaded
}).orIfSomeFailedCallThis(function() {
  // one or more failed
});
```

这是 promise 所执行的任务，但以更好的方式命名。如果 HTML 图像元素有一个返回 promise 的“ready”方法，我们可以执行：

```js
img1.ready().then(function() {
  // loaded
}, function() {
  // failed
});

// and…
Promise.all([img1.ready(), img2.ready()]).then(function() {
  // all loaded
}, function() {
  // one or more failed
});
```

最基本的情况是，promise 有点类似于事件侦听器，但有以下两点区别：

* promise 只能成功或失败一次，而不能成功或失败两次，也不能从成功转为失败或从失败转为成功。
* 如果 promise 已成功或失败，且您之后添加了成功/失败回调，则将会调用正确的回调，即使事件发生在先。

这对于异步成功/失败尤为有用，因为您可能对某些功能可用的准确时间不是那么关注，更多地是关注对结果作出的反应。

## Promise 术语 {#promise-terminology}

[Domenic Denicola](https://twitter.com/domenic)校对了本篇文章的初稿，并在术语方面给我打分为“F”。他把我留下来，强迫我抄写[状态和结果](https://github.com/domenic/promises-unwrapping/blob/master/docs/states-and-fates.md)100 遍，并给我的父母写了封告状信。尽管如此，我还是对很多术语混淆不清，以下是几个基本的概念：

promise 可以是：

* **已执行 **- 与 promise 有关的操作成功
* **已拒绝 **- 与 promise 有关的操作失败
* **待定 **- 尚未执行或拒绝
* **已解决 **- 已执行或拒绝

[本规范](https://people.mozilla.org/~jorendorff/es6-draft.html#sec-promise-objects)还使用术语**thenable**来描述类似于 promise 的对象，并使用`then`方法。该术语让我想起前英格兰国家队教练[Terry Venables](https://en.wikipedia.org/wiki/Terry_Venables)，因此我将尽可能不用这个术语。

## Promise 在 JavaScript 中受支持！ {#promise_javascript}

Promise 有一段时间以库的形式出现，例如：

* [Q](https://github.com/kriskowal/q)
* [when](https://github.com/cujojs/when)
* [WinJS](https://msdn.microsoft.com/en-us/library/windows/apps/br211867.aspx)
* [RSVP.js](https://github.com/tildeio/rsvp.js)

以上这些与 JavaScript promise 都有一个名为[Promise/A+](https://github.com/promises-aplus/promises-spec)的常见标准化行为。如果您是 jQuery 用户，他们还有一个类似于名为[Deferred](https://api.jquery.com/category/deferred-object/)的行为。但是，Deferred 与 Promise/A+ 不兼容，这就使得它们[存在细微差异且没那么有用](https://thewayofcode.wordpress.com/tag/jquery-deferred-broken/)，因此需注意。此外，jQuery 还有[Promise 类型](https://api.jquery.com/Types/#Promise)，但它只是 Deferred 的子集，因此仍存在相同的问题。

尽管 promise 实现遵照标准化行为，但其整体 API 有所不同。JavaScript promise 在 API 中类似于 RSVP.js。下面是创建 promise 的步骤：

```js
var promise = new Promise(function(resolve, reject) {
  // do a thing, possibly async, then…

  if (/* everything turned out fine */) {
    resolve("Stuff worked!");
  }
  else {
    reject(Error("It broke"));
  }
});
```

Promise 构造函数包含一个参数和一个带有 resolve（解析）和 reject（拒绝）两个参数的回调。在回调中执行一些操作（例如异步），如果一切都正常，则调用 resolve，否则调用 reject。

与普通旧版 JavaScript 中的`throw`一样，通常拒绝时会给出 Error 对象，但这不是必须的。Error 对象的优点在于它们能够捕捉堆叠追踪，因而使得调试工具非常有用。

以下是有关 promise 的使用示例：

```js
promise.then(function(result) {
  console.log(result); // "Stuff worked!"
}, function(err) {
  console.log(err); // Error: "It broke"
});
```

`then()`包含两个参数：一个用于成功情形的回调和一个用于失败情形的回调。这两个都是可选的，因此您可以只添加一个用于成功情形或失败情形的回调。

JavaScript promise 最初是在 DOM 中出现并称为“Futures”，之后重命名为“Promises”，最后又移入 JavaScript。在 JavaScript 中使用比在 DOM 中更好，因为它们将在如 Node.js 等非浏览器 JS 环境中可用（而它们是否会在核心 API 中使用 Promise 则是另外一个问题）。

尽管它们是 JavaScript 的一项功能，但 DOM 也能使用。实际上，采用异步成功/失败方法的所有新 DOM API 均使用 promise。[Quota Management](https://dvcs.w3.org/hg/quota/raw-file/tip/Overview.html#idl-def-StorageQuota)、[Font Load Events](http://dev.w3.org/csswg/css-font-loading/#font-face-set-ready)、[ServiceWorker](https://github.com/slightlyoff/ServiceWorker/blob/cf459d473ae09f6994e8539113d277cbd2bce939/service_worker.ts#L17)、[Web MIDI](https://webaudio.github.io/web-midi-api/#widl-Navigator-requestMIDIAccess-Promise-MIDIOptions-options)[Streams](https://github.com/whatwg/streams#basereadablestream)等等都已经在使用 promise。

## 浏览器支持和 polyfill {#polyfill}

现在，promise 已在各浏览器中实现。

在 Chrome 32、Opera 19、Firefox 29、Safari 8 和 Microsoft Edge 中，promise 默认启用。

如要使没有完全实现 promise 的浏览器符合规范，或向其他浏览器和 Node.js 中添加 promise，请查看[polyfill](https://github.com/jakearchibald/ES6-Promises#readme)（gzip 压缩大小为 2k）。

## 与其他库的兼容性 {#_2}

JavaScript promise API 将任何使用`then()`方法的结构都当作 promise 一样（或按 promise 的说法为`thenable`）来处理，因此，如果您使用返回 Q promise 的库也没问题，因为它能与新 JavaScript promise 很好地兼容。

如我之前所提到的，jQuery 的 Deferred 不那么有用。幸运的是，您可以将其转为标准 promise，这值得尽快去做：

```js
var jsPromise = Promise.resolve($.ajax('/whatever.json'))
```

这里，jQuery 的`$.ajax`返回了一个 Deferred。由于它使用`then()`方法，因此`Promise.resolve()`可将其转为 JavaScript promise。但是，有时 deferred 会将多个参数传递给其回调，例如：

```js
var jqDeferred = $.ajax('/whatever.json');

jqDeferred.then(function(response, statusText, xhrObj) {
  // ...
}, function(xhrObj, textStatus, err) {
  // ...
})
```

而 JS promise 会忽略除第一个之外的所有参数：

```js
jsPromise.then(function(response) {
  // ...
}, function(xhrObj) {
  // ...
})
```

幸好，通常这就是您想要的，或者至少为您提供了方法让您获得所想要的。另请注意，jQuery 不遵循将 Error 对象传递到 reject 这一惯例。

## 复杂异步代码让一切变得更简单 {#_3}

对了，让我们写一些代码。比如说，我们想要：

1. 启动一个转环来提示加载
2. 获取一个故事的 JSON，确定每个章节的标题和网址
3. 向页面中添加标题
4. 获取每个章节
5. 向页面中添加故事
6. 停止转环

…但如果此过程发生错误，也要向用户显示。我们也想在那一点停止转环，否则，它将不停地旋转、眩晕并撞上其他 UI 控件。

当然，您不会使用 JavaScript 来提供故事，[以 HTML 形式提供会更快](https://jakearchibald.com/2013/progressive-enhancement-is-faster/)，但是这种方式在处理 API 时很常见：多次提取数据，然后在全部完成后执行其他操作。

首先，让我们从网络中获取数据：

## 对 XMLHttpRequest 执行 promise {#xmlhttprequest_promise}

旧 API 将更新为使用 promise，如有可能，采用后向兼容的方式。`XMLHttpRequest`是主要候选对象，不过，我们可编写一个作出 GET 请求的简单函数：

```js
function get(url) {
  // Return a new promise.
  return new Promise(function(resolve, reject) {
    // Do the usual XHR stuff
    var req = new XMLHttpRequest();
    req.open('GET', url);

    req.onload = function() {
      // This is called even on 404 etc
      // so check the status
      if (req.status == 200) {
        // Resolve the promise with the response text
        resolve(req.response);
      }
      else {
        // Otherwise reject with the status text
        // which will hopefully be a meaningful error
        reject(Error(req.statusText));
      }
    };

    // Handle network errors
    req.onerror = function() {
      reject(Error("Network Error"));
    };

    // Make the request
    req.send();
  });
}
```

现在让我们来使用这一功能：

```js
get('story.json').then(function(response) {
  console.log("Success!", response);
}, function(error) {
  console.error("Failed!", error);
})
```

[点击此处了解实际操作](https://github.com/googlesamples/web-fundamentals/blob/gh-pages/fundamentals/getting-started/primers/story.json)，检查 DevTools 中的控制台以查看结果。现在我们无需手动键入`XMLHttpRequest`即可作出 HTTP 请求，这真是太赞了，因为越少看到令人讨厌的书写得参差不齐的`XMLHttpRequest`，我就越开心。

## 链接 {#_4}

`then()`不是最终部分，您可以将各个`then`链接在一起来改变值，或依次运行额外的异步操作。

### 改变值 {#_5}

只需返回新值即可改变值：

```js
var promise = new Promise(function(resolve, reject) {
  resolve(1);
});

promise.then(function(val) {
  console.log(val); // 1
  return val + 2;
}).then(function(val) {
  console.log(val); // 3
})
```

举一个实际的例子，让我们回到：

```js
get('story.json').then(function(response) {
  console.log("Success!", response);
})
```

这里的 response 是 JSON，但是我们当前收到的是其纯文本。我们可以将 get 函数修改为使用 JSON[`responseType`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest#responseType)，不过我们也可以使用 promise 来解决这个问题：

```js
get('story.json').then(function(response) {
  return JSON.parse(response);
}).then(function(response) {
  console.log("Yey JSON!", response);
})
```

由于`JSON.parse()`采用单一参数并返回改变的值，因此我们可以将其简化为：

```js
get('story.json').then(JSON.parse).then(function(response) {
  console.log("Yey JSON!", response);
})
```

[了解实际操作](https://github.com/googlesamples/web-fundamentals/blob/gh-pages/fundamentals/getting-started/primers/story.json)，检查 DevTools 中的控制台以查看结果。实际上，我们可以让`getJSON()`函数更简单：

```js
function getJSON(url) {
  return get(url).then(JSON.parse);
}
```

`getJSON()`仍返回一个 promise，该 promise 获取 URL 后将 response 解析为 JSON。

### 异步操作队列 {#_6}

您还可以链接多个`then`，以便按顺序运行异步操作。

当您从`then()`回调中返回某些内容时，这有点儿神奇。如果返回一个值，则会以该值调用下一个`then()`。但是，如果您返回类似于 promise 的内容，下一个`then()`则会等待，并仅在 promise 产生结果（成功/失败）时调用。例如：

```js
getJSON('story.json').then(function(story) {
  return getJSON(story.chapterUrls[0]);
}).then(function(chapter1) {
  console.log("Got chapter 1!", chapter1);
})
```

这里我们向`story.json`发出异步请求，这可让我们请求一组网址，随后我们请求其中的第一个。这是 promise 从简单回调模式中脱颖而出的真正原因所在。

您甚至可以采用更简短的方法来获得章节内容：

```js
var storyPromise;

function getChapter(i) {
  storyPromise = storyPromise || getJSON('story.json');

  return storyPromise.then(function(story) {
    return getJSON(story.chapterUrls[i]);
  })
}

// and using it is simple:
getChapter(0).then(function(chapter) {
  console.log(chapter);
  return getChapter(1);
}).then(function(chapter) {
  console.log(chapter);
})
```

直到`getChapter`被调用，我们才下载`story.json`，但是下次`getChapter`被调用时，我们重复使用 story romise，因此`story.json`仅获取一次。耶，Promise！

## 错误处理 {#_7}

正如我们之前所看到的，`then()`包含两个参数：一个用于成功，一个用于失败（按照 promise 中的说法，即执行和拒绝）：

```js
get('story.json').then(function(response) {
  console.log("Success!", response);
}, function(error) {
  console.log("Failed!", error);
})
```

您还可以使用`catch()`：

```js
get('story.json').then(function(response) {
  console.log("Success!", response);
}).catch(function(error) {
  console.log("Failed!", error);
})
```

`catch()`没有任何特殊之处，它只是`then(undefined, func)`的锦上添花，但可读性更强。注意，以上两个代码示例行为并不相同，后者相当于：

```js
get('story.json').then(function(response) {
  console.log("Success!", response);
}).then(undefined, function(error) {
  console.log("Failed!", error);
})
```

两者之间的差异虽然很微小，但非常有用。Promise 拒绝后，将跳至带有拒绝回调的下一个`then()`（或具有相同功能的`catch()`）。如果是`then(func1, func2)`，则`func1`或`func2`中的一个将被调用，而不会二者均被调用。但如果是`then(func1).catch(func2)`，则在`func1`拒绝时两者均被调用，因为它们在该链中是单独的步骤。看看下面的代码：

```js
asyncThing1().then(function() {
  return asyncThing2();
}).then(function() {
  return asyncThing3();
}).catch(function(err) {
  return asyncRecovery1();
}).then(function() {
  return asyncThing4();
}, function(err) {
  return asyncRecovery2();
}).catch(function(err) {
  console.log("Don't worry about it");
}).then(function() {
  console.log("All done!");
})
```

以上流程与常规的 JavaScript try/catch 非常类似，在“try”中发生的错误直接进入`catch()`块。以下是上述代码的流程图形式（因为我喜欢流程图）：

蓝线表示执行的 promise 路径，红路表示拒绝的 promise 路径。

### JavaScript 异常和 promise {#javascript_promise}

当 promise 被明确拒绝时，会发生拒绝；但是如果是在构造函数回调中引发的错误，则会隐式拒绝。

```js
var jsonPromise = new Promise(function(resolve, reject) {
  // JSON.parse throws an error if you feed it some
  // invalid JSON, so this implicitly rejects:
  resolve(JSON.parse("This ain't JSON"));
});

jsonPromise.then(function(data) {
  // This never happens:
  console.log("It worked!", data);
}).catch(function(err) {
  // Instead, this happens:
  console.log("It failed!", err);
})
```

这意味着，在 promise 构造函数回调内部执行所有与 promise 相关的任务很有用，因为错误会自动捕获并进而拒绝。

对于在`then()`回调中引发的错误也是如此。

```js
get('/').then(JSON.parse).then(function() {
  // This never happens, '/' is an HTML page, not JSON
  // so JSON.parse throws
  console.log("It worked!", data);
}).catch(function(err) {
  // Instead, this happens:
  console.log("It failed!", err);
})
```

### 错误处理实践 {#_8}

在我们的故事和章节中，我们可使用 catch 来向用户显示错误：

```js
getJSON('story.json').then(function(story) {
  return getJSON(story.chapterUrls[0]);
}).then(function(chapter1) {
  addHtmlToPage(chapter1.html);
}).catch(function() {
  addTextToPage("Failed to show chapter");
}).then(function() {
  document.querySelector('.spinner').style.display = 'none';
})
```

如果获取`story.chapterUrls[0]`失败（例如，http 500 或用户离线），它将跳过所有后续成功回调，包括`getJSON()`中尝试将响应解析为 JSON 的回调，而且跳过将 chapter1.html 添加到页面的回调。然后，它将移至 catch 回调。因此，如果任一前述操作失败，“Failed to show chapter”将会添加到页面。

与 JavaScript 的 try/catch 一样，错误被捕获而后续代码继续执行，因此，转环总是被隐藏，这正是我们想要的。以上是下面一组代码的拦截异步版本：

```js
try {
  var story = getJSONSync('story.json');
  var chapter1 = getJSONSync(story.chapterUrls[0]);
  addHtmlToPage(chapter1.html);
}
catch (e) {
  addTextToPage("Failed to show chapter");
}
document.querySelector('.spinner').style.display = 'none'
```

您可能想出于记录目的而`catch()`，而无需从错误中恢复。为此，只需再次抛出错误。我们可以使用`getJSON()`方法执行此操作：

```js
function getJSON(url) {
  return get(url).then(JSON.parse).catch(function(err) {
    console.log("getJSON failed for", url, err);
    throw err;
  });
}
```

至此，我们已获取其中一个章节，但我们想要所有的章节。让我们尝试来实现。

## 并行式和顺序式：两者兼得 {#_9}

异步并不容易。如果您觉得难以着手，可尝试按照同步的方式编写代码。在本例中：

```
try
{


var
 story 
=
 getJSONSync
(
'story.json'
);


  addHtmlToPage
(
story
.
heading
);




  story
.
chapterUrls
.
forEach
(
function
(
chapterUrl
)
{


var
 chapter 
=
 getJSONSync
(
chapterUrl
);


    addHtmlToPage
(
chapter
.
html
);


});




  addTextToPage
(
"All done"
);


}


catch
(
err
)
{


  addTextToPage
(
"Argh, broken: "
+
 err
.
message
);


}




document
.
querySelector
(
'.spinner'
).
style
.
display 
=
'none'


```

[试一下](https://googlesamples.github.io/web-fundamentals/fundamentals/getting-started/primers/sync-example.html)

这样可行（查看[代码](https://github.com/googlesamples/web-fundamentals/blob/gh-pages/fundamentals/getting-started/primers/sync-example.html)）！ 但这是同步的情况，而且在内容下载时浏览器会被锁定。要使其异步，我们使用`then()`来依次执行任务。

```
getJSON
(
'story.json'
).
then
(
function
(
story
)
{


  addHtmlToPage
(
story
.
heading
);




// TODO: for each url in story.chapterUrls, fetch 
&
amp; display


}).
then
(
function
()
{


// And we're all done!


  addTextToPage
(
"All done"
);


}).
catch
(
function
(
err
)
{


// Catch any error that happened along the way


  addTextToPage
(
"Argh, broken: "
+
 err
.
message
);


}).
then
(
function
()
{


// Always hide the spinner


  document
.
querySelector
(
'.spinner'
).
style
.
display 
=
'none'
;


})


```

但是我们如何遍历章节的 URL 并按顺序获取呢？以下方法**行不通**：

```

```

`forEach`不是异步的，因此我们的章节内容将按照下载的顺序显示，这就乱套了。我们这里不是非线性叙事小说，因此得解决该问题。

