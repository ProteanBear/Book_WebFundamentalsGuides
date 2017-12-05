# 异步函数 - 提高 Promise 的易用性

> 作者：Jake Archibald

Chrome 55 中默认情况下启用异步函数，坦率地讲，它们的作用相当不可思议。 可以利用它们像编写同步代码那样编写基于 Promise 的代码，而且还不会阻塞主线程。 它们可以让异步代码“智商”下降、可读性提高。

异步函数的工作方式是这样的：

```js
async function myFirstAsyncFunction() {
  try {
    const fulfilledValue = await promise;
  }
  catch (rejectedValue) {
    // …
  }
}
```

如果在函数定义之前使用了`async`关键字，就可以在函数内使用`await`。 当您`await`某个 Promise 时，函数暂停执行，直至该 Promise 产生结果，并且暂停并不会阻塞主线程。 如果 Promise 执行，则会返回值。 如果 Promise 拒绝，则会抛出拒绝的值。

注：如果不熟悉 Promise，可以看一看[我们的 Promise 指南](https://developers.google.com/web/fundamentals/getting-started/primers/promises?hl=zh-cn)。

## 示例：记录获取日志 {#_1}

假设我们想获取某个网址并以文本形式记录响应日志。以下是利用 Promise 编写的代码：

```js
function logFetch(url) {
  return fetch(url)
    .then(response => response.text())
    .then(text => {
      console.log(text);
    }).catch(err => {
      console.error('fetch failed', err);
    });
}
```

以下是利用异步函数具有相同作用的代码：

```js
async function logFetch(url) {
  try {
    const response = await fetch(url);
    console.log(await response.text());
  }
  catch (err) {
    console.log('fetch failed', err);
  }
}
```

代码行数虽然相同，但去掉了所有回调。这可以提高代码的可读性，对不太熟悉 Promise 的人而言，帮助就更大了。

注：您`await`的任何内容都通过`Promise.resolve()`传递，这样您就可以安全地`await`非原生 Promise。

## 异步函数返回值 {#_2}

无论是否使用`await`，异步函数_都会_返回 Promise。该 Promise 解析时返回异步函数返回的任何值，拒绝时返回异步函数抛出的任何值。

因此，对于：

```js
// wait ms milliseconds
function wait(ms) {
  return new Promise(r => setTimeout(r, ms));
}

async function hello() {
  await wait(500);
  return 'world';
}
```

…调用`hello()`返回的 Promise 会在_执行_时返回`"world"`。

```js
async function foo() {
  await wait(500);
  throw Error('bar');
}
```

…调用`foo()`返回的 Promise 会在_拒绝_时返回`Error('bar')`。

## 示例：流式传输响应 {#_3}

异步函数在更复杂示例中更有用武之地。假设我们想在流式传输响应的同时记录数据块日志，并返回数据块最终大小。

注：一看到“记录数据块日志”这几个字就让我感到不舒服。

以下是使用 Promise 编写的代码：

```js
function getResponseSize(url) {
  return fetch(url).then(response => {
    const reader = response.body.getReader();
    let total = 0;

    return reader.read().then(function processResult(result) {
      if (result.done) return total;

      const value = result.value;
      total += value.length;
      console.log('Received chunk', value);

      return reader.read().then(processResult);
    })
  });
}
```

请“Promise 大师”Jake Archibald 给我检查一下。看到我是如何在`processResult`内调用其自身来建立异步循环了吧？ 这样编写的代码让我觉得_很智能_。 但就像大多数“智能”代码那样，你得盯着它看上半天才能弄明白它的作用，要拿出揣摩上世纪 90 年代流行的魔眼图片的那种劲头才行。

我们再用异步函数来编写上面这段代码：

```js
async function getResponseSize(url) {
  const response = await fetch(url);
  const reader = response.body.getReader();
  let result = await reader.read();
  let total = 0;

  while (!result.done) {
    const value = result.value;
    total += value.length;
    console.log('Received chunk', value);
    // get the next result
    result = await reader.read();
  }

  return total;
}
```

所有“智能”都不见了。让我大有飘飘然之感的异步循环被替换成可靠却单调乏味的 while 循环。 但简明性得到大幅提高。未来，我们将获得[异步迭代器](https://github.com/tc39/proposal-async-iteration)，这些迭代器[会将`while`循环替换成 for-of 循环](https://gist.github.com/jakearchibald/0b37865637daf884943cf88c2cba1376)，从而进一步提高代码的简明性。

注：我有点偏爱卡片信息流。如果不熟悉流式传输，可以[看一看我的指南](https://jakearchibald.com/2016/streams-ftw/#streams-the-fetch-api)。

## 其他异步函数语法 {#_4}

我们已经见识了`async function() {}`，但`async`关键字还可用于其他函数语法：

### 箭头函数 {#_5}

```
// map some URLs to json-promises


const
 jsonPromises 
=
 urls
.
map
(
async url 
=
>
{


const
 response 
=
 await fetch
(
url
);


return
 response
.
json
();


});


```

注：`array.map(func)`不在乎我提供给它的是不是异步函数，只把它当作一个返回 Promise 的函数来看待。 它不会等到第一个函数执行完毕就会调用第二个函数。

### 对象方法 {#_6}

    const
     storage 
    =
    {

      async getAvatar
    (
    name
    )
    {

    const
     cache 
    =
     await caches
    .
    open
    (
    'avatars'
    );

    return
     cache
    .
    match
    (
    `/avatars/${name}.jpg`
    );

    }

    };


    storage
    .
    getAvatar
    (
    'jaffathecake'
    ).
    then
    (…);


### 类方法 {#_7}

    class
    Storage
    {

      constructor
    ()
    {

    this
    .
    cachePromise 
    =
     caches
    .
    open
    (
    'avatars'
    );

    }


      async getAvatar
    (
    name
    )
    {

    const
     cache 
    =
     await 
    this
    .
    cachePromise
    ;

    return
     cache
    .
    match
    (
    `/avatars/${name}.jpg`
    );

    }

    }


    const
     storage 
    =
    new
    Storage
    ();

    storage
    .
    getAvatar
    (
    'jaffathecake'
    ).
    then
    (…);


注：类构造函数以及 getter/settings 方法不能是异步的。

