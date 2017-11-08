# 打造全屏体验

我们有能力轻松打造出沉浸式全屏网站和应用，而就像网络上的所有事物一样，可以通过几种方式来实现。这在时下显得尤为重要，因为已有更多浏览器支持一种全屏启动的“安装型网络应用”体验。

## 让应用或网站进入全屏模式 {#_1}

用户或开发者可通过几种方法让网络应用进入全屏模式。

* 作为对用户手势的响应请求浏览器进入全屏模式。
* 将应用安装到主屏幕。
* 弄虚作假：自动隐藏地址栏。

### 作为对用户手势的响应请求浏览器进入全屏模式 {#_2}

[并非所有平台都相同](http://caniuse.com/#feat=fullscreen)。iOS Safari 没有全屏 API，但 Chrome（Android 版）、Firefox 和 IE 11+ 上则有相应的 API。您构建的大多数应用都是组合使用全屏规范提供的 JS API 和 CSS 选择器。 您在打造全屏体验时需要在意的主要 JS API 如下：

* `element.requestFullscreen()`（目前在 Chrome、Firefox 和 IE 中添加前缀）：以全屏模式显示元素。

* `document.exitFullscreen()`（目前在 Chrome、Firefox 和 IE 中添加前缀。 Firefox 改用`cancelFullScreen()`）：取消全屏模式。

* `document.fullscreenElement`
  （目前在 Chrome、Firefox 和 IE 中添加前缀）：如有任何元素处于全屏模式，返回 true。

注：您会注意到，在添加前缀的版本中，屏幕中“S”的大小写处理存在大量不一致的情况。 这很不雅观，但也正是实行中规范存在的问题。

应用进入全屏模式时，无法再使用浏览器的 UI 控件。 这会改变用户与所提供体验的交互方式。 全屏模式下的浏览器没有 Forwards 和 Backwards 这样的标准导航控件，也没有 Refresh 按钮这样的出路。 必须迎合这种情境。 当浏览器进入全屏模式时，可以利用某些 CSS 选择器来帮助您改变网站的样式和呈现方式。

```
<
button
id
=
"goFS"
>
Go fullscreen
<
/button
>


<
script
>


var
 goFS 
=
 document
.
getElementById
(
"goFS"
);


  goFS
.
addEventListener
(
"click"
,
function
()
{


      document
.
body
.
requestFullscreen
();


},
false
);


<
/script
>


```

上例有点人为的痕迹；我将供应商前缀使用方面的复杂性全都隐藏了起来。

注：该死的供应商前缀！

实际代码要复杂得多。[Mozilla 创建了](https://developer.mozilla.org/en-US/docs/Web/Guide/API/DOM/Using_full_screen_mode)一个非常有用的脚本，您可以用它来切换全屏。 如您所见，与指定 API 相比，供应商前缀的情况更为复杂和繁琐。即便是以下略加简化的代码，看上去仍显复杂。

```
function
 toggleFullScreen
()
{


var
 doc 
=
 window
.
document
;


var
 docEl 
=
 doc
.
documentElement
;




var
 requestFullScreen 
=
 docEl
.
requestFullscreen 
||
 docEl
.
mozRequestFullScreen 
||
 docEl
.
webkitRequestFullScreen 
||
 docEl
.
msRequestFullscreen
;


var
 cancelFullScreen 
=
 doc
.
exitFullscreen 
||
 doc
.
mozCancelFullScreen 
||
 doc
.
webkitExitFullscreen 
||
 doc
.
msExitFullscreen
;




if
(!
doc
.
fullscreenElement 
&
&
!
doc
.
mozFullScreenElement 
&
&
!
doc
.
webkitFullscreenElement 
&
&
!
doc
.
msFullscreenElement
)
{


    requestFullScreen
.
call
(
docEl
);


}


else
{


    cancelFullScreen
.
call
(
doc
);


}


}


```

我们这些网络开发者痛恨复杂性。您可以使用的一个不错的高级抽象 API 是[Sindre Sorhus 的](http://sindresorhus.com/screenfull.js)Screenfull.js 模块，该模块将两个略有不同的 JS API 和供应商前缀统一成一个一致的 API。

#### Fullscreen API 温馨提示 {#fullscreen_api}

##### 让文档进入全屏模式 {#_3}

![](https://developers.google.com/web/fundamentals/native-hardware/fullscreen/images/body.png?hl=zh-cn)

图 1：让 body 元素进入全屏模式。

让 body 元素进入全屏模式是很自然的想法，但如果使用的是基于 WebKit 或 Blink 的渲染引擎，就会发现这会产生一种怪异的效果：将正文宽度缩减到能够容纳所有内容的最小尺寸。（Mozilla Gecko 不存在这个问题。）

![](https://developers.google.com/web/fundamentals/native-hardware/fullscreen/images/document.png?hl=zh-cn)

图 2：让 document 元素进入全屏模式。

要修复此问题，请使用 document 元素替代 body 元素：

```
document
.
documentElement
.
requestFullscreen
();


```

##### 让 video 元素进入全屏模式 {#video}

让 video 元素进入全屏模式与让任何其他元素进入全屏模式的方法完全相同。 只需调用 video 元素上的`requestFullscreen`方法。

```
<
video
id
=
videoElement
>
<
/video
>


<
button
id
=
"goFS"
>
Go Fullscreen
<
/button
>


<
script
>


var
 goFS 
=
 document
.
getElementById
(
"goFS"
);


  goFS
.
addEventListener
(
"click"
,
function
()
{


var
 videoElement 
=
 document
.
getElementById
(
"videoElement"
);


      videoElement
.
requestFullscreen
();


},
false
);


<
/script
>


```

如果`<video>`元素未定义控件属性，视频进入全屏模式后用户将无法对其进行控制。 建议的对策是使用一个初级容器，将视频和您希望用户看到的控件包装在这个容器内。

```
<
div
id
=
"container"
>


<
video
>
<
/video
>


<
div
>


<
button
>
Play
<
/button
>


<
button
>
Stop
<
/button
>


<
button
id
=
"goFS"
>
Go fullscreen
<
/button
>


<
/div
>


<
/div
>


<
script
>


var
 goFS 
=
 document
.
getElementById
(
"goFS"
);


  goFS
.
addEventListener
(
"click"
,
function
()
{


var
 container 
=
 document
.
getElementById
(
"container"
);


      container
.
requestFullscreen
();


},
false
);


<
/script
>


```

这可以大幅提高灵活性，因为您可以将 container 对象与 CSS 伪选择器合并（例如，达到隐藏“goFS”按钮的目的）。

```
<
style
>


#
goFS
:
-webkit-full-screen 
#
goFS 
{


display
:
 none
;


}


#
goFS
:
-moz-full-screen 
#
goFS 
{


display
:
 none
;


}


#
goFS
:
-ms-fullscreen 
#
goFS 
{


display
:
 none
;


}


#
goFS
:
fullscreen 
#
goFS 
{


display
:
 none
;


}


<
/style
>


```

按照这些模式，可以在检测到全屏模式处于运行状态时对用户界面作出相应调整，例如：

* 提供一个返回开始页面的链接
* 提供一种关闭对话框或回退的机制

### 从主屏幕以全屏模式启动页面 {#_4}

无法实现在用户导航到网页时启动全屏模式。浏览器供应商深知在每次页面加载时都提供全屏体验很令人讨厌，因此会要求用户通过手势进入全屏模式。但供应商也的确允许用户“安装”应用，安装行为是向操作系统发出的一个信号，表示用户想在平台上以应用的形式启动网页。

如下所述，在各主流移动平台上，使用元标记或清单文件实现起来相当简便。

#### iOS {#ios}

自从 iPhone 发布以来，用户就一直能将网络应用安装到主屏幕，并以全屏模式启动。

```html

```

> 如果 content 设置为 yes，则网络应用以全屏模式运行；&gt; 否则，不以全屏模式运行。 默认行为是使用 Safari 显示网络 &gt; 内容。 可以 &gt; 利用 window.navigator.standalone 只读布尔值 JavaScript 属性 &gt; 确定网页是否以全屏模式显示。[Apple](https://developer.apple.com/library/safari/documentation/AppleApplications/Reference/SafariHTMLRef/Articles/MetaTags.html)

#### Chrome（Android 版） {#chromeandroid}

Chrome 团队近期实现的一项功能可在用户已将页面添加到主屏幕的情况下指示浏览器以全屏模式启动页面。 这与 iOS Safari 模式类似。

```html

```

> 可以利用 Chrome（Android 版）的“Add to Home screen”菜单项 &gt; 将网络应用设置为将应用快捷方式图标添加到 &gt; 设备的主屏幕，让应用以全屏“应用模式”启动。&gt;[Google Chrome](https://developers.chrome.com/multidevice/android/installtohomescreen)

更好的选择是使用网络应用清单。

#### 网络应用清单（Chrome、Opera、Firefox、Samsung） {#chromeoperafirefoxsamsung}

[网络应用清单](https://developers.google.com/web/fundamentals/web-app-manifest?hl=zh-cn)是一个简单的 JSON 文件，使您（开发者）能够控制在用户可能看到应用的区域（例如手机主屏幕）中如何向用户显示应用，指示用户可以启动哪些功能，更重要的是说明启动方法。未来，清单将让您对应用进行更多控制，但现在我们只侧重于如何启动应用。

具体而言：

1. 将清单的相关信息告知浏览器
2. 说明启动方法

在创建清单并托管在网站上之后，只需要从所有包含应用的页面添加一个下面这样的 link 标记：

```html

```

Chrome（Android 版）从 38 版（2014 年 10 月）起就已支持清单，让您能够控制当网络应用安装到主屏幕时的显示方式（通过`short_name`、`name`和`icons`属性），以及当用户点击启动图标时应以何种方式启动应用（通过`start_url`、`display`和`orientation`）。

清单示例如下所示。其中并未详尽展示清单可能包含的内容。

```json

```

此功能是完全渐进式的功能，可通过它为支持该功能的浏览器用户打造更好、集成度更高的体验。

当用户将网站或应用添加到主屏幕时，其意图是将它当作应用对待。 这意味着，您的目标应该是将用户导向应用的功能而不是产品着陆页。 例如，如果用户需要登录应用，那么它就是适合启动的页面。

##### 实用程序应用 {#_5}

大多数实用程序应用都将立即受益于清单。对于您可能希望像移动平台上的所有其他应用一样独立启动的应用，要指示应用独立启动，请向网络应用清单添加以下内容：

```json

```

##### 游戏 {#_6}

大多数游戏都将立即受益于清单。绝大多数游戏都希望强制按特定屏幕方向以全屏模式启动。

如果您开发的是纵向滚动游戏或 Flappy Birds 之类的游戏，那么您很可能希望游戏始终以纵向模式显示。

```json

```

如果与之相反，您开发的是益智游戏或 X-Com 之类的游戏，那么您多半希望游戏始终采用横向屏幕方向。

```json

```

##### 新闻网站 {#_7}

在大多数情况下，新闻网站提供纯粹的内容型体验。可以预见的是，大多数开发者不会想到为新闻网站添加清单。 可以通过清单定义启动项（新闻网站的头版）和启动方式（全屏或正常浏览器标签形式）。

是否选择使用清单取决于您以及您认为用户喜欢以何种方式访问您提供的体验。 如果希望网站具有您认为应该具有的所有浏览器配色，可以将 display 设置为`browser`。

```json

```

如果希望新闻网站像大多数新闻中心型应用一样提供应用般的体验并从 UI 中移除所有网站式配色，可以通过将 display 设置为`standalone`来实现。

```json

```

### 弄虚作假：自动隐藏地址栏 {#_8}

可通过像下面这样自动隐藏地址栏来“伪造全屏模式”：

```js

```

注意：是朋友我才告诉你。办法有是有，虽然有效，却并非正途。 还是不要使用为好。— Paul

这是个相当简单的方法，页面加载时系统会指示浏览器地址栏让开。 遗憾的是，这种方法并未标准化，也未得到充分支持。 此外还必须解决大量兼容性问题。

例如，当用户导航返回时，浏览器往往会在页面上将地址栏恢复原位。 如果使用`window.scrollTo`进行替换，会给用户造成妨碍。 要想解决此问题，需要将最后位置存储在 localStorage 中，并处理边缘情况（例如，当用户在多个窗口中打开该页面时）。

## 用户体验指导原则 {#_9}

当您构建可充分利用全屏模式的网站时，需要注意若干潜在的用户体验变化，才能打造出得到用户喜爱的服务。

### 不要依赖导航控件 {#_10}

iOS 没有硬件返回按钮或刷新手势。因此，必须确保用户能在应用内四处导航而不被锁入。

在所有主流平台上都可以轻松检测到您是在全屏模式还是安装模式下运行。

#### iOS {#ios_1}

在 iOS 上，可以利用`navigator.standalone`布尔值来确认用户是否是从主屏幕启动的。

```js

```

#### 网络应用清单（Chrome、Opera、Samsung） {#chromeoperasamsung}

以安装应用形式启动时，Chrome 并非运行在真正的全屏体验下，因此`document.fullscreenElement`返回 null，并且 CSS 选择器不起作用。

当用户通过在网站上使用手势来请求全屏时，有标准 Fullscreen API 可以使用，其中包括下面这样可调整 UI 来响应全屏状态的 CSS 伪选择器

```css

```

如果用户从主屏幕启动网站，`display-mode`媒体查询将按照网络应用清单中的定义进行设置。 在纯粹全屏的情况下，其内容将是：

```css

```

如果用户以独立模式启动应用，`display-mode`媒体查询将是`standalone`：

```css

```

#### Firefox {#firefox}

当用户通过网站请求全屏，或者用户以全屏模式启动应用时，所有标准 Fullscreen API 都可使用，其中包括下面这样可调整 UI 来响应全屏状态的 CSS 伪选择器：

```css

```

#### Internet Explorer {#internet_explorer}

在 IE 中，CSS 伪类缺少连字符，但在其他方面的作用与 Chrome 和 Firefox 类似。

```css

```

#### 规范 {#_11}

规范中的拼写匹配 IE 使用的语法。

```css

```

### 保持用户的全屏体验 {#_12}

有时 Fullscreen API 可能有点吹毛求疵。浏览器供应商不想把用户锁定在全屏页面中，因此他们开发了相应的机制，只要满足条件，便可立即摆脱全屏模式。

这意味着您无法让构建的全屏网站跨越多个页面，这是因为：

* 利用 ‘window.location = "http://example.com"\` 以编程方式更改网址会摆脱全屏模式。
* 用户点击页面内的外部链接时将会退出全屏模式。
* 通过
  `navigator.pushState`
  API 更改网址也会摆脱全屏体验。

如果想保持用户的全屏体验，可以采用以下这两个方案：

1. 利用可安装网络应用机制进入全屏模式。
2. 利用 \# 片段管理 UI 和应用状态。

通过使用 \#syntax 更新网址 \(window.location = "\#somestate"\) 以及侦听`window.onhashchange`事件，可以利用浏览器自身的历史堆栈管理应用状态变化，允许用户使用其硬件返回按钮，或者利用如下 history API 提供简单的编程返回按钮体验：

```js

```

### 让用户选择进入全屏模式的时机 {#_13}

没有什么比网站的意外行为更让用户恼火。 当用户导航到网站时，不要试图诱骗他们进入全屏模式。

不要截取第一个触摸事件并调用`requestFullscreen()`。

1. 这很令人讨厌。
2. 浏览器可能决定在未来的某个时间点就允许应用占据全屏提示用户。

如果想以全屏模式启动应用，可以考虑采用各平台的安装体验。

### 不要滥发信息骚扰用户，让他们将应用安装到主屏幕 {#_14}

如果计划通过安装应用机制提供全屏体验，请为用户着想。

* 谨慎小心。利用横幅或页脚告知用户他们可以安装应用。

* 如果他们关闭了提示，不要再次显示。

* 用户首次访问时，除非他们对您的服务感到满意，否则可能并不想安装应用。可以考虑在用户与网站的交互产生了积极印象后再提示他们安装。

* 如果用户经常访问网站却没有安装应用，那么他们未来也不太可能安装应用。不要不断地滥发信息骚扰用户。

## 结论 {#_15}

尽管我们尚未开发出完全标准化并且全面实现的 API，但利用这篇文章中提供的指引，无论使用什么客户端，您都可以轻松地打造出充分利用用户整个屏幕的体验。



