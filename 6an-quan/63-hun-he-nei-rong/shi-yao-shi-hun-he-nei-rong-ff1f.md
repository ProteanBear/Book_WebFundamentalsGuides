# 什么是混合内容？

> 作者：Jo-elvan Bergen

**混合内容**在以下情况下出现：初始 HTML 内容通过安全的 HTTPS 连接加载，但其他资源（例如，图像、视频、样式表、脚本）则通过不安全的 HTTP 连接加载。之所以称为混合内容，是因为同时加载了 HTTP 和 HTTPS 内容以显示同一个页面，且通过 HTTPS 加载的初始请求是安全的。现代浏览器会针对此类型的内容显示警告，以向用户表明此页面包含不安全的资源。

### TL;DR {#tldr_hide-from-toc}

* HTTPS 对于保护您的网站和用户免受攻击非常重要。
* 混合内容会降低您的 HTTPS 网站的安全性和用户体验。

## 资源请求和网络浏览器 {#_1}

当浏览器访问网站的页面时，它将请求 HTML 资源。然后，网络服务器返回 HTML 内容，浏览器进行解析并显示给用户。通常，一个 HTML 文件不足以显示一个完整页面，因此，HTML 文件包含浏览器需要请求的其他资源的引用。这些子资源可以是图像、视频、额外 HTML、CSS 或 JavaScript 之类的资源；每个资源均使用单独的请求获取。

## HTTPS 的优势 {#https}

当浏览器通过 HTTPS（HTTP Secure 的缩写形式）请求资源时，它使用一个已加密连接与网络服务器进行通信。

使用 HTTPS 有三个主要优势：

* 身份验证
* 数据完整性
* 保密性

### 身份验证 {#_2}

我正在访问的网站是正确的吗？

HTTPS 让浏览器检查并确保其已打开正确的网站，并且没有被重定向到恶意的网站。 当导航到您的银行网站时，您的浏览器对该网站进行身份验证，从而防止攻击者冒充您的银行窃取您的登录凭据。

### 数据完整性 {#_3}

是否有人篡改我正在发送或接收的内容？

HTTPS 让浏览器检测是否有攻击者更改了浏览器接收的任何数据。 使用您的银行网站转账时，这样做可防止当您的请求在传输中时攻击者更改目标帐号。

### 保密性 {#_4}

是否有人能看到我正在发送或接收的内容？

HTTPS 可防止攻击者窃取浏览器的请求，跟踪访问的网站或窃取已发送或接收的信息。

### HTTPS、传输层安全协议 \(TLS\) 和 SSL {#https_tls_ssl}

HTTPS 是 HTTP Secure 的缩写，即超文本传输安全协议。此处的**secure**部分来自于添加到浏览器发送和接收的请求的加密。目前大多数浏览器都使用传输层安全协议 \(TLS\) 提供加密；**TLS**有时称为 SSL。

本文不会详细介绍 HTTPS、传输层安全协议 \(TLS\) 和 SSL，但是，如果您想了解更多信息，可以先从以下资源入手：

* [Wikipedia HTTPS](https://en.wikipedia.org/wiki/HTTPS)
* [Wikipedia TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security)
* [可汗学院 \(Khan Academy\) 的加密课程](https://www.khanacademy.org/computing/computer-science/cryptography)
* [高性能浏览器网络](http://chimera.labs.oreilly.com/books/1230000000545)（作者：Ilya Grigorik）中的[传输层安全协议 \(TLS\) 章节](http://chimera.labs.oreilly.com/books/1230000000545/ch04.html)

## 混合内容会降低 HTTPS 的安全性 {#https_1}

使用不安全的 HTTP 协议请求子资源会降低整个页面的安全性，因为这些请求容易受到**中间人攻击**，攻击者窃听网络连接，查看或修改双方的通信。通过使用这些资源，攻击者通常可以完全控制页面，而不只是泄露的资源。

尽管许多浏览器向用户报告混合内容警告，但出现警告时为时已晚：不安全的请求已被执行，且页面的安全性被破坏。遗憾的是，这种情况在网络中很普遍，正因如此，浏览器不能简单地阻止所有混合请求，否则将会限制许多网站的功能。

![](https://developers.google.com/web/fundamentals/security/prevent-mixed-content/imgs/image-gallery-warning.png?hl=zh-cn "混合内容：页面已通过 HTTPS 加载，但请求了不安全的图像。此内容也应通过 HTTPS 提供。")

修正应用中的混合内容问题是开发者的责任。

### 一个简单的示例 {#_5}

从 HTTPS 页面加载不安全的脚本。

查看通过**HTTPS**—[**https**://googlesamples.github.io/web-fundamentals/.../simple-example.html](https://googlesamples.github.io/web-fundamentals/fundamentals/security/prevent-mixed-content/simple-example.html)加载的此示例页面 — 添加一个**HTTP**脚本标记，其尝试加载混合内容。

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
    <link rel="stylesheet" href="https://code.getmdl.io/1.2.1/material.indigo-pink.min.css">
    <script defer src="https://code.getmdl.io/1.2.1/material.min.js"></script>
    <style>
      body {
        margin: 2em;
      }
    </style>
    <title>Simple mixed content example</title>
  </head>
  <body>
    <div role="main">
      <h1>
        Simple mixed content example!
      </h1>
      <p>
        View page over: <a href="https://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/prevent-mixed-content/simple-example.html">HTTP</a> - <a href="https://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/prevent-mixed-content/simple-example.html">HTTPS</a>
      </p>
      <p>
        This page loads the script simple-example.js using HTTP. This is the simplest case of mixed content. When the simple-example.js file is requested by the browser, an attacker can inject code into the returned content and take control of the entire page. Thankfully, most modern browsers block this type of dangerous content by default and display an error in the JavaScript console. This can be seen when the page is viewed over HTTPS.
      </p>
      <div id="output">Waiting for insecure script to run...</div>
      <script src="https://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/prevent-mixed-content/simple-example.js"></script>
    </div>
    <script>
  (function(b,o,i,l,e,r){b.GoogleAnalyticsObject=l;b[l]||(b[l]=
  function(){(b[l].q=b[l].q||[]).push(arguments)});b[l].l=+new Date;
  e=o.createElement(i);r=o.getElementsByTagName(i)[0];
  e.src='https://www.google-analytics.com/analytics.js';
  r.parentNode.insertBefore(e,r)}(window,document,'script','ga'));
  ga('create','UA-52746336-1');ga('send','pageview');
  var isCompleted = {};
  function sampleCompleted(sampleName){
    if (ga && !isCompleted.hasOwnProperty(sampleName)) {
      ga('send', 'event', 'WebCentralSample', sampleName, 'completed'); 
      isCompleted[sampleName] = true;
    }
  }
</script>
  </body>
</html>
```

在此示例中，使用一个**HTTP**网址加载脚本`simple-example.js`。这是最简单的混合内容案例。浏览器请求`simple-example.js`文件时，攻击者可以将代码注入返回的内容，并控制整个页面。

幸运的是，大多数现代浏览器均默认阻止此类危险的内容。 请参阅[具有混合内容的浏览器行为](#browser-behavior-with-mixed-content)。

![](https://developers.google.com/web/fundamentals/security/prevent-mixed-content/imgs/simple-mixed-content-error.png?hl=zh-cn "混合内容：页面已通过 HTTPS 加载，但请求了不安全的脚本。此请求已被阻止，内容必须通过 HTTPS 提供。")

Chrome 可阻止不安全的脚本。

### 一个 XMLHttpRequest 示例 {#xmlhttprequest}

通过 XMLHttpRequest 加载不安全的数据。

查看通过**HTTPS**—[**https**://googlesamples.github.io/web-fundamentals/.../xmlhttprequest-example.html](https://googlesamples.github.io/web-fundamentals/fundamentals/security/prevent-mixed-content/xmlhttprequest-example.html)加载的此示例页面 — 添加一个通过**HTTP**加载的`XMLHttpRequest`，以获取混合内容`JSON`数据。

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
    <link rel="stylesheet" href="https://code.getmdl.io/1.2.1/material.indigo-pink.min.css">
    <script defer src="https://code.getmdl.io/1.2.1/material.min.js"></script>
    <style>
      body {
        margin: 2em;
      }
    </style>
    <title>XMLHttpRequest mixed content example</title>
  </head>
  <body>
    <div role="main">
      <h1>
        XMLHttpRequest mixed content example!
      </h1>
      <p>
        View page over: <a href="http://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/xmlhttprequest-example.html">HTTP</a> - <a href="https://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/xmlhttprequest-example.html">HTTPS</a>
      </p>
      <p>
        This page constructs an HTTP URL dynamically in JavaScript, the URL is eventually used to load an insecure resource by XMLHttpRequest. When the xmlhttprequest-data.js file is requested by the browser, an attacker can inject code into the returned content and take control of the entire page. Thankfully, most modern browsers block this type of dangerous content by default and display an error in the JavaScript console. This can be seen when the page is viewed over HTTPS.
      </p>
      <div id="output">Waiting for data...</div>
      <script>
        var rootUrl = 'http://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content';
        var resources = {
          jsonData: '/xmlhttprequest-data.js'
        };
        var request = new XMLHttpRequest();
        request.addEventListener('load', function() {
          var jsonData = JSON.parse(request.responseText);
          document.getElementById('output').innerHTML += '<br>' + jsonData.data;
        });
        request.open('GET', rootUrl + resources.jsonData, true);
        request.send();
      </script>
    </div>
    <script>
  (function(b,o,i,l,e,r){b.GoogleAnalyticsObject=l;b[l]||(b[l]=
  function(){(b[l].q=b[l].q||[]).push(arguments)});b[l].l=+new Date;
  e=o.createElement(i);r=o.getElementsByTagName(i)[0];
  e.src='//www.google-analytics.com/analytics.js';
  r.parentNode.insertBefore(e,r)}(window,document,'script','ga'));
  ga('create','UA-52746336-1');ga('send','pageview');
  var isCompleted = {};
  function sampleCompleted(sampleName){
    if (ga && !isCompleted.hasOwnProperty(sampleName)) {
      ga('send', 'event', 'WebCentralSample', sampleName, 'completed'); 
      isCompleted[sampleName] = true;
    }
  }
</script>
  </body>
</html>
```

下面的**HTTP**网址是在 JavaScript 中动态构建的，并且最终被`XMLHttpRequest`用于加载不安全的资源。 与上面简单的示例相似，当浏览器请求`xmlhttprequest-data.js`文件时，攻击者可以将代码注入返回的内容中，并控制整个页面。

大多数现代浏览器也会阻止这些危险的请求。

![](https://developers.google.com/web/fundamentals/security/prevent-mixed-content/imgs/xmlhttprequest-mixed-content-error.png?hl=zh-cn "混合内容：页面已通过 HTTPS 加载，但请求了不安全的 XMLHttpRequest 端点。此请求已被阻止，内容必须通过 HTTPS 提供。")

Chrome 可阻止不安全的 XMLHttpRequest。

### 图像库示例 {#_6}

使用 jQuery 灯箱加载不安全的图像。

查看通过**HTTPS**—[**https**://googlesamples.github.io/web-fundamentals/.../image-gallery-example.html](https://googlesamples.github.io/web-fundamentals/fundamentals/security/prevent-mixed-content/image-gallery-example.html)加载的此示例页面时 — 最初没有任何混合内容问题；但是当点击缩略图时，将通过**HTTP**加载完整尺寸的混合内容图像。

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
    <link rel="stylesheet" href="https://code.getmdl.io/1.2.1/material.indigo-pink.min.css">
    <script defer src="https://code.getmdl.io/1.2.1/material.min.js"></script>
    <style>
      body {
        margin: 2em;
      }
    </style>
    <title>Image gallery mixed content example</title>
    <script src="//ajax.googleapis.com/ajax/libs/jquery/2.1.4/jquery.min.js"></script>
    <script>
      $(document).ready(function() {
        $('.gallery').click(function(e) {
          e.preventDefault();
          $('.overlay-foreground').css('background-image', 'url(' + $(this).attr('href') + ')');
          $('.overlay').fadeIn('slow');
        })
        $('.overlay').click(function() {
          $('.overlay').fadeOut('slow');
        })
      });
    </script>
    <style>
      .overlay {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
      }
      .overlay-background {
        background-color: #000;
        filter:alpha(opacity=80);
        -moz-opacity: 0.8;
        -khtml-opacity: 0.8;
        opacity: 0.8;
        z-index: 10000;
      }
      .overlay-foreground {
        background-position: center center;
        background-repeat: no-repeat;
        z-index: 10001;
      }
    </style>
  </head>
  <body>
    <div role="main">
      <h1>
        Image gallery mixed content!
      </h1>
      <p>
        View page over: <a href="http://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/image-gallery-example.html">HTTP</a> - <a href="https://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/image-gallery-example.html">HTTPS</a>
      </p>
      <p> 
        Image galleries often rely on the &lt;img&gt; tag src attribute to display thumbnail images on the page, the anchor ( &lt;a&gt; ) tag href attribute is then used to load the full sized image for the gallery overlay. Normally &lt;a&gt; tags do not cause mixed content, but in this case the jQuery code overrides the default link behavior &mdash; to navigate to a new page &mdash; and instead loads the HTTP image on this page. While this content isn't blocked, modern browsers display a warning in the JavaScript console. This can be seen when the page is viewed over HTTPS and the thumbnail is clicked.
      </p>
      CLICK ME! -->
      <a class="gallery" href="http://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/puppy.jpg">
        <img src="https://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/puppy-thumb.jpg">
      </a>
      <div class="overlay overlay-background" style="display: none;"></div>
      <div class="overlay overlay-foreground" style="display: none;"></div>
    </div>
    <script>
  (function(b,o,i,l,e,r){b.GoogleAnalyticsObject=l;b[l]||(b[l]=
  function(){(b[l].q=b[l].q||[]).push(arguments)});b[l].l=+new Date;
  e=o.createElement(i);r=o.getElementsByTagName(i)[0];
  e.src='//www.google-analytics.com/analytics.js';
  r.parentNode.insertBefore(e,r)}(window,document,'script','ga'));
  ga('create','UA-52746336-1');ga('send','pageview');
  var isCompleted = {};
  function sampleCompleted(sampleName){
    if (ga && !isCompleted.hasOwnProperty(sampleName)) {
      ga('send', 'event', 'WebCentralSample', sampleName, 'completed'); 
      isCompleted[sampleName] = true;
    }
  }
</script>
  </body>
</html>
```

图像库通常依靠`<img>`标记`src`属性在页面上显示缩略图，然后，使用定位 \(`<a>`\) 标记`href`属性为图像库叠加层加载完整尺寸的图像。正常情况下，`<a>`标记不会产生混合内容，但在此例中，jQuery 代码替换默认链接行为（导航到新页面），改为在此页面上加载**HTTP**图像。

![](https://developers.google.com/web/fundamentals/security/prevent-mixed-content/imgs/image-gallery-warning.png?hl=zh-cn "混合内容：页面已通过 HTTPS 加载，但请求了不安全的图像。此内容也应通过 HTTPS 提供。")

不安全的图像会降低网站的安全性，但是它们的危险性与其他类型的混合内容不一样。 现代浏览器仍会加载混合内容图像，但也会向用户显示警告。

## 混合内容类型与相关安全威胁 {#_7}

混合内容有两种：主动混合内容和被动混合内容

**被动混合内容**指的是不与页面其余部分进行交互的内容，从而使中间人攻击在拦截或更改该内容时能够执行的操作受限。被动混合内容包括图像、视频和音频内容，以及无法与页面其余部分进行交互的其他资源。

**主动混合内容**作为整体与页面进行交互，并且几乎允许攻击者对页面进行任何操作。 主动混合内容包括浏览器可下载和执行的脚本、样式表、iframe、flash 资源及其他代码。

### 被动混合内容 {#_8}

被动混合内容仍会给您的网站和用户带来安全威胁。 例如，攻击者可以拦截针对网站上的图像的 HTTP 请求，调换或更换这些图像；此攻击者可以调换“save and delete”按钮图像，导致您的用户无意间删除内容；将您的产品图表更换为下流或淫秽内容，从而损害您的网站；或将您的产品图像更换为不同网站或产品的广告。

即使攻击者不改变您的网站内容，您仍面临严重的隐私问题，攻击者可以使用混合内容请求跟踪用户。攻击者可以基于浏览器加载的图像或其他资源了解用户访问哪些页面，以及查看了哪些产品。

以下是被动混合内容的示例：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
    <link rel="stylesheet" href="https://code.getmdl.io/1.2.1/material.indigo-pink.min.css">
    <script defer src="https://code.getmdl.io/1.2.1/material.min.js"></script>
    <style>
      body {
        margin: 2em;
      }
    </style>
    <title>Passive mixed content example</title>
    <style>
      audio, img, video {
        display: block;
        margin: 10px;
      }
    </style>
  </head>
  <body>
    <div role="main">
      <h1>
        Passive mixed content!
      </h1>
      <p>
        View page over: <a href="http://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/passive-mixed-content.html">HTTP</a> - <a href="https://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/passive-mixed-content.html">HTTPS</a>
      </p>
      <p>
        Several examples of passive mixed content. When viewed over HTTPS most browsers do <b>not</b> block this content but instead display warnings in the JavaScript console.
      </p>

      <!-- An insecure audio file loaded on a secure page -->
      <audio src="http://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/sleep.mp3" type="audio/mp3" controls></audio>

      <!-- An insecure image loaded on a secure page -->
      <img src="http://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/puppy.jpg">

      <!-- An insecure video file loaded on a secure page -->
      <video src="https://storage.googleapis.com/webfundamentals-assets/videos/chrome.webm" type="video/webm" controls></video>
    </div>
    <script>
  (function(b,o,i,l,e,r){b.GoogleAnalyticsObject=l;b[l]||(b[l]=
  function(){(b[l].q=b[l].q||[]).push(arguments)});b[l].l=+new Date;
  e=o.createElement(i);r=o.getElementsByTagName(i)[0];
  e.src='//www.google-analytics.com/analytics.js';
  r.parentNode.insertBefore(e,r)}(window,document,'script','ga'));
  ga('create','UA-52746336-1');ga('send','pageview');
  var isCompleted = {};
  function sampleCompleted(sampleName){
    if (ga && !isCompleted.hasOwnProperty(sampleName)) {
      ga('send', 'event', 'WebCentralSample', sampleName, 'completed'); 
      isCompleted[sampleName] = true;
    }
  }
</script>
  </body>
</html>
```

大多数浏览器仍向用户渲染此类型的混合内容，但是也会显示警告，因为这些内容会给您的网站和用户带来安全风险和隐私风险。

![](https://developers.google.com/web/fundamentals/security/prevent-mixed-content/imgs/passive-mixed-content-warnings.png?hl=zh-cn "混合内容：页面已通过 HTTPS 加载，但请求了不安全的视频。此内容也应通过 HTTPS 提供。")

来自 Chrome JavaScript 控制台的混合内容警告。

### 主动混合内容 {#_9}

与被动混合内容相比，主动混合内容造成的威胁更大。攻击者可以拦截和重写主动内容，从而完全控制页面，甚至整个网站。这让攻击者可以更改有关页面的任何内容，包括显示完全不同的内容、窃取用户密码或其他登录凭据、窃取用户会话 Cookie，或将用户重定向到一个完全不同的网站。

鉴于这种威胁的严重性，许多浏览器都会默认阻止此类型的内容以保护用户，但是其作用因浏览器供应商和版本而有所差异。

以下包含主动混合内容的示例：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
    <link rel="stylesheet" href="https://code.getmdl.io/1.2.1/material.indigo-pink.min.css">
    <script defer src="https://code.getmdl.io/1.2.1/material.min.js"></script>
    <style>
      body {
        margin: 2em;
      }
    </style>
    <title>Active mixed content example</title>
    <!-- An insecure script file loaded on a secure page -->
    <script src="http://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/simple-example.js"></script>

    <!-- An insecure stylesheet loaded on a secure page -->
    <link href="http://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/style.css" rel="stylesheet">

    <style>
      .insecure-background {
        /* An insecure resources loaded from a style property on a secure page, this can   
           happen in many places including, @font-face, cursor, background-image, and so on. */
        background: url('http://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/puppy-thumb.jpg') no-repeat;
      }
    </style>

    <style>
      .insecure-style-holder span {
        color: #fff;
      }
      .insecure-background {
        color: #000;
        font-weight: bold;
        background-position: left center;
        background-repeat: no-repeat;
        width: 300px;
        height: 140px;
      }
      iframe {
        width: 400px;
        height: 300px;
      }
    </style>

  </head>
  <body>
    <div role="main">
      <h1>
        Active mixed content!
      </h1>
      <p>
        View page over: <a href="http://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/active-mixed-content.html">HTTP</a> - <a href="https://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/active-mixed-content.html">HTTPS</a>
      </p>
      <p>
        Several examples of active mixed content. When viewed over HTTPS most browsers block this content and display errors in the JavaScript console.
      </p>
      <div class="insecure-style-holder">
        <span style="ba">Insecure style loaded</span>
      </div>
      <div class="insecure-background">
        Loading insecure background here...
      </div>

      <p>Loading insecure iframe...</p>
      <!-- An insecure iframed page loaded on a secure page -->
      <iframe src="http://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/image-gallery-example.html"></iframe>

      <!-- Flash resources also qualify as active mixed content and pose a
      serious security risk. Be sure to look for <object> tags with type set
      to "application/x-shockwave-flash", and an http:// data attribute. -->
      <!-- <object type="application/x-shockwave-flash" data="http://..."></object> -->

      <script>
        // An insecure resource loaded using XMLHttpRequest
        var request = new XMLHttpRequest();
        request.addEventListener('load', function() {
          var jsonData = JSON.parse(request.responseText);
          document.getElementById('output').innerHTML += '<br>' + jsonData.data;
        });
        request.open("GET", "http://googlesamples.github.io/web-fundamentals/samples/discovery-and-distribution/avoid-mixed-content/xmlhttprequest-data.js", true);
        request.send();
      </script>
      <div id="output">Waiting for insecure script to run...</div>
    </div>
    <script>
  (function(b,o,i,l,e,r){b.GoogleAnalyticsObject=l;b[l]||(b[l]=
  function(){(b[l].q=b[l].q||[]).push(arguments)});b[l].l=+new Date;
  e=o.createElement(i);r=o.getElementsByTagName(i)[0];
  e.src='//www.google-analytics.com/analytics.js';
  r.parentNode.insertBefore(e,r)}(window,document,'script','ga'));
  ga('create','UA-52746336-1');ga('send','pageview');
  var isCompleted = {};
  function sampleCompleted(sampleName){
    if (ga && !isCompleted.hasOwnProperty(sampleName)) {
      ga('send', 'event', 'WebCentralSample', sampleName, 'completed'); 
      isCompleted[sampleName] = true;
    }
  }
</script>
  </body>
</html>
```

![](https://developers.google.com/web/fundamentals/security/prevent-mixed-content/imgs/active-mixed-content-errors.png?hl=zh-cn "混合内容：页面已通过 HTTPS 加载，但请求了不安全的资源。此请求已被阻止，内容必须通过 HTTPS 提供。")来自 Chrome JavaScript 控制台的混合内容错误。

## 具有混合内容的浏览器行为 {#_10}

鉴于上述威胁，浏览器最好是阻止所有混合内容。 但是，这将破坏大量网站，而数百万用户每天都要访问这些网站。 当前的折衷做法是阻止最危险的混合内容类型，同时仍允许请求不太危险的混合内容类型。

现代浏览器遵循[混合内容规范](https://w3c.github.io/webappsec/specs/mixedcontent/)，其定义了[**可选择性地阻止的内容**](https://w3c.github.io/webappsec/specs/mixedcontent/#category-optionally-blockable)和[**可阻止的内容**](https://w3c.github.io/webappsec/specs/mixedcontent/#category-blockable)类别。

根据此规范，“当破坏网络重要部分的风险超过允许此资源作为混合内容使用的风险时”，该资源有资格成为可选择性阻止的内容；这是上述[被动混合内容](#passive-mixed-content)类别的子集。在撰写本文时，可选择性阻止的内容中仅包括图像、视频和音频资源以及预获取的链接这些资源类型。随着时间的推移，此类别可能会缩小。

**可选择性阻止的内容**以外的所有内容被视为**可阻止的内容**，将被浏览器阻止。

### 浏览器版本 {#_11}

切记，并不是网站的每个访问者都使用最新的浏览器。 不同浏览器供应商的不同版本的浏览器处理混合内容的方式不尽相同。 最糟糕的情况是，有些浏览器和版本根本不会阻止任何混合内容，这对于用户而言非常不安全。

每个浏览器的确切行为不断变化，因此，我们在这里不做具体介绍。 如果您对特定浏览器的行为方式感兴趣，请直接查看供应商发布的信息。

注：您的用户在访问您的网站时指望您保护他们。修复混合内容问题以保护**所有**访问者（包括使用较旧浏览器的访问者）很重要。

