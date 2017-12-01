# 关键渲染路径

> 作者：Ilya Grigorik

\_优化关键渲染路径\_是指优先显示与当前用户操作有关的内容。

要提供快速的网络体验，浏览器需要做许多工作。这类工作大多数是我们这些网络开发者看不到的：我们编写标记，屏幕上就会显示出漂亮的页面。

但浏览器到底是如何使用我们的 HTML、CSS 和 JavaScript 在屏幕上渲染像素的呢？

从收到 HTML、CSS 和 JavaScript 字节到对其进行必需的处理，从而将它们转变成渲染的像素这一过程中有一些中间步骤，优化性能其实就是了解这些步骤中发生了什么 - 即**关键渲染路径**。

![](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/progressive-rendering.png?hl=zh-cn "渐进式页面渲染")

通过优化关键渲染路径，我们可以显著缩短首次渲染页面的时间。 此外，了解关键渲染路径还可以为构建高性能交互式应用打下基础。

处理交互式更新的过程是相同的，只是在连续循环中完成，理想情况下每秒可以处理 60 帧！不过，我们先来看一下浏览器如何显示简单的网页。

## 构建对象模型

浏览器渲染页面前需要先构建 DOM 和 CSSOM 树。因此，我们需要确保尽快将 HTML 和 CSS 都提供给浏览器。

#### TL;DR

* 字节 → 字符 → 令牌 → 节点 → 对象模型。
* HTML 标记转换成文档对象模型 \(DOM\)；CSS 标记转换成 CSS 对象模型 \(CSSOM\)。
* DOM 和 CSSOM 是独立的数据结构。
* Chrome DevTools Timeline 让我们可以捕获和检查 DOM 和 CSSOM 的构建和处理开销。

### 文档对象模型 \(DOM\)

```html
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <link href="style.css" rel="stylesheet">
    <title>Critical Path</title>
  </head>
  <body>
    <p>Hello <span>web performance</span> students!</p>
    <div><img src="awesome-photo.jpg"></div>
  </body>
</html>
```

让我们从可能的最简单情况入手：一个包含一些文本和一幅图片的普通 HTML 页面。浏览器如何处理此页面？

![](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/full-process.png?hl=zh-cn "DOM 构建流程")

1. **转换：**浏览器从磁盘或网络读取 HTML 的原始字节，并根据文件的指定编码（例如 UTF-8）将它们转换成各个字符。
2. **令牌化：**浏览器将字符串转换成[W3C HTML5 标准](http://www.w3.org/TR/html5/)规定的各种令牌，例如，“&lt;html&gt;”、“&lt;body&gt;”，以及其他尖括号内的字符串。每个令牌都具有特殊含义和一组规则。
3. **词法分析：**发出的令牌转换成定义其属性和规则的“对象”。
4. **DOM 构建：**最后，由于 HTML 标记定义不同标记之间的关系（一些标记包含在其他标记内），创建的对象链接在一个树数据结构内，此结构也会捕获原始标记中定义的父项-子项关系：_HTML_对象是_body_对象的父项，_body_是_paragraph_对象的父项，依此类推。

![](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/dom-tree.png?hl=zh-cn "DOM 树")

**整个流程的最终输出是我们这个简单页面的文档对象模型 \(DOM\)，浏览器对页面进行的所有进一步处理都会用到它。**

浏览器每次处理 HTML 标记时，都会完成以上所有步骤：将字节转换成字符，确定令牌，将令牌转换成节点，然后构建 DOM 树。这整个流程可能需要一些时间才能完成，有大量 HTML 需要处理时更是如此。

![](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/dom-timeline.png?hl=zh-cn "在 DevTools 中追踪 DOM 构建")

注：我们假定您对 Chrome DevTools 有着基础程度的熟悉，即您知道如何捕获网络瀑布，或记录时间线。如果您需要快速温习相关内容，请查看[Chrome DevTools 文档](https://developers.google.com/web/tools/chrome-devtools/?hl=zh-cn)；如果您未接触过 DevTools，我们建议您学习 Codeschool[Discover DevTools](http://discover-devtools.codeschool.com/)课程。

如果您打开 Chrome DevTools 并在页面加载时记录时间线，就可以看到执行该步骤实际花费的时间。在上例中，将一堆 HTML 字节转换成 DOM 树大约需要 5 毫秒。对于较大的页面，这一过程需要的时间可能会显著增加。创建流畅动画时，如果浏览器需要处理大量 HTML，这很容易成为瓶颈。

DOM 树捕获文档标记的属性和关系，但并未告诉我们元素在渲染后呈现的外观。那是 CSSOM 的责任。

### CSS 对象模型 \(CSSOM\)

在浏览器构建我们这个简单页面的 DOM 时，在文档的 head 部分遇到了一个 link 标记，该标记引用一个外部 CSS 样式表：style.css。由于预见到需要利用该资源来渲染页面，它会立即发出对该资源的请求，并返回以下内容：

```css
body { font-size: 16px }
p { font-weight: bold }
span { color: red }
p span { display: none }
img { float: right }
```

我们本可以直接在 HTML 标记内声明样式（内联），但让 CSS 独立于 HTML 有利于我们将内容和设计作为独立关注点进行处理：设计人员负责处理 CSS，开发者侧重于 HTML，等等。

与处理 HTML 时一样，我们需要将收到的 CSS 规则转换成某种浏览器能够理解和处理的东西。因此，我们会重复 HTML 过程，不过是为 CSS 而不是 HTML：

![](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/cssom-construction.png?hl=zh-cn "CSSOM 构建步骤")

CSS 字节转换成字符，接着转换成令牌和节点，最后链接到一个称为“CSS 对象模型”\(CSSOM\) 的树结构内：

![](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/cssom-tree.png?hl=zh-cn "CSSOM 树")

CSSOM 为何具有树结构？为页面上的任何对象计算最后一组样式时，浏览器都会先从适用于该节点的最通用规则开始（例如，如果该节点是 body 元素的子项，则应用所有 body 样式），然后通过应用更具体的规则（即规则“向下级联”）以递归方式优化计算的样式。

以上面的 CSSOM 树为例进行更具体的阐述。_span_标记内包含的任何置于 body 元素内的文本都将具有 16 像素字号，并且颜色为红色 — font-size 指令从 body 向下级联至 span。不过，如果某个 span 标记是某个段落 \(p\) 标记的子项，则其内容将不会显示。

还请注意，以上树并非完整的 CSSOM 树，它只显示了我们决定在样式表中替换的样式。每个浏览器都提供一组默认样式（也称为“User Agent 样式”），即我们不提供任何自定义样式时所看到的样式，我们的样式只是替换这些默认样式（例如[默认 IE 样式](http://www.iecss.com/)）。

要了解 CSS 处理所需的时间，您可以在 DevTools 中记录时间线并寻找“Recalculate Style”事件：与 DOM 解析不同，该时间线不显示单独的“Parse CSS”条目，而是在这一个事件下一同捕获解析和 CSSOM 树构建，以及计算的样式的递归计算。

![](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/cssom-timeline.png?hl=zh-cn "在 DevTools 中追踪 CSSOM 构建")

我们的小样式表需要大约 0.6 毫秒的处理时间，影响页面上的 8 个元素 — 虽然不多，但同样会产生开销。不过，这 8 个元素从何而来呢？CSSOM 和 DOM 是独立的数据结构！结果证明，浏览器隐藏了一个重要步骤。接下来，让我们谈一谈将 DOM 与 CSSOM 关联在一起的[渲染树](render-tree-construction-hl=zh-cn.html)。

## 渲染树构建、布局及绘制

CSSOM 树和 DOM 树合并成渲染树，然后用于计算每个可见元素的布局，并输出给绘制流程，将像素渲染到屏幕上。优化上述每一个步骤对实现最佳渲染性能至关重要。

在前面介绍构建对象模型的章节中，我们根据 HTML 和 CSS 输入构建了 DOM 树和 CSSOM 树。 不过，它们都是独立的对象，分别网罗文档不同方面的信息：一个描述内容，另一个则是描述需要对文档应用的样式规则。我们该如何将两者合并，让浏览器在屏幕上渲染像素呢？

#### TL;DR

* DOM 树与 CSSOM 树合并后形成渲染树。
* 渲染树只包含渲染网页所需的节点。
* 布局计算每个对象的精确位置和大小。
* 最后一步是绘制，使用最终渲染树将像素渲染到屏幕上。

第一步是让浏览器将 DOM 和 CSSOM 合并成一个“渲染树”，网罗网页上所有可见的 DOM 内容，以及每个节点的所有 CSSOM 样式信息。

![](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/render-tree-construction.png "将 DOM 与 CSSOM 合并以形成渲染树")

为构建渲染树，浏览器大体上完成了下列工作：

1. 从 DOM 树的根节点开始遍历每个可见节点。

   * 某些节点不可见（例如脚本标记、元标记等），因为它们不会体现在渲染输出中，所以会被忽略。
   * 某些节点通过 CSS 隐藏，因此在渲染树中也会被忽略，例如，上例中的 span 节点---不会出现在渲染树中，---因为有一个显式规则在该节点上设置了“display: none”属性。

2. 对于每个可见节点，为其找到适配的 CSSOM 规则并应用它们。

3. 发射可见节点，连同其内容和计算的样式。

注：简单提一句，请注意`visibility: hidden`与`display: none`是不一样的。前者隐藏元素，但元素仍占据着布局空间（即将其渲染成一个空框），而后者 \(`display: none`\) 将元素从渲染树中完全移除，元素既不可见，也不是布局的组成部分。

最终输出的渲染同时包含了屏幕上的所有可见内容及其样式信息。**有了渲染树，我们就可以进入“布局”阶段。**

到目前为止，我们计算了哪些节点应该是可见的以及它们的计算样式，但我们尚未计算它们在设备[视口](../../design-and-ux/responsive/index.html#set-the-viewport)内的确切位置和大小---这就是“布局”阶段，也称为“自动重排”。

为弄清每个对象在网页上的确切大小和位置，浏览器从渲染树的根节点开始进行遍历。让我们考虑下面这样一个简单的实例：

```html
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <title>Critial Path: Hello world!</title>
  </head>
  <body>
    <div style="width: 50%">
      <div style="width: 50%">Hello world!</div>
    </div>
  </body>
</html>
```

以上网页的正文包含两个嵌套 div：第一个（父）div 将节点的显示尺寸设置为视口宽度的 50%，---父 div 包含的第二个 div---将其宽度设置为其父项的 50%；即视口宽度的 25%。

![](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/layout-viewport.png "计算布局信息")

布局流程的输出是一个“盒模型”，它会精确地捕获每个元素在视口内的确切位置和尺寸：所有相对测量值都转换为屏幕上的绝对像素。

最后，既然我们知道了哪些节点可见、它们的计算样式以及几何信息，我们终于可以将这些信息传递给最后一个阶段：将渲染树中的每个节点转换成屏幕上的实际像素。这一步通常称为“绘制”或“栅格化”。

上述步骤都需要浏览器完成大量工作，所以相当耗时。不过，Chrome DevTools 可以帮助我们对上述所有三个阶段进行深入的了解。让我们看一下最初“hello world”示例的布局阶段：

![](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/layout-timeline.png "在 DevTools 中评估布局")

* “Layout”事件在时间线中捕获渲染树构建以及位置和尺寸计算。
* 布局完成后，浏览器会立即发出“Paint Setup”和“Paint”事件，将渲染树转换成屏幕上的像素。

执行渲染树构建、布局和绘制所需的时间将取决于文档大小、应用的样式，以及运行文档的设备：文档越大，浏览器需要完成的工作就越多；样式越复杂，绘制需要的时间就越长（例如，单色的绘制开销“较小”，而阴影的计算和渲染开销则要“大得多”）。

最后将在视口中看到下面的网页：

![](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/device-dom-small.png "完成渲染的 Hello World 网页")

下面简要概述了浏览器完成的步骤：

1. 处理 HTML 标记并构建 DOM 树。
2. 处理 CSS 标记并构建 CSSOM 树。
3. 将 DOM 与 CSSOM 合并成一个渲染树。
4. 根据渲染树来布局，以计算每个节点的几何信息。
5. 将各个节点绘制到屏幕上。

我们的演示网页看起来可能很简单，实际上却需要完成相当多的工作。如果 DOM 或 CSSOM 被修改，您只能再执行一遍以上所有步骤，以确定哪些像素需要在屏幕上进行重新渲染。

**\_优化关键渲染路径\_就是指最大限度缩短执行上述第 1 步至第 5 步耗费的总时间。**这样一来，就能尽快将内容渲染到屏幕上，此外还能缩短首次渲染后屏幕刷新的时间，即为交互式内容实现更高的刷新率。

## 阻塞渲染的 CSS

默认情况下，CSS 被视为阻塞渲染的资源，这意味着浏览器将不会渲染任何已处理的内容，直至 CSSOM 构建完毕。请务必精简您的 CSS，尽快提供它，并利用媒体类型和查询来解除对渲染的阻塞。

在[渲染树构建](render-tree-construction.html)中，我们看到关键渲染路径要求我们同时具有 DOM 和 CSSOM 才能构建渲染树。这会给性能造成严重影响：**HTML 和 CSS 都是阻塞渲染的资源。**HTML 显然是必需的，因为如果没有 DOM，我们就没有可渲染的内容，但 CSS 的必要性可能就不太明显。如果我们在 CSS 不阻塞渲染的情况下尝试渲染一个普通网页会怎样？

#### TL;DR

* 默认情况下，CSS 被视为阻塞渲染的资源。
* 我们可以通过媒体类型和媒体查询将一些 CSS 资源标记为不阻塞渲染。
* 浏览器会下载所有 CSS 资源，无论阻塞还是不阻塞。

![](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/nytimes-css-device.png "使用 CSS 的纽约时报")

使用 CSS 的纽约时报

![](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/nytimes-nocss-device.png "不使用 CSS 的纽约时报")

不使用 CSS 的纽约时报 \(FOUC\)

上例展示了纽约时报网站使用和不使用 CSS 的显示效果，它证明了为何要在 CSS 准备就绪之前阻塞渲染，---没有 CSS 的网页实际上无法使用。右侧的情况通常称为“内容样式短暂失效”\(FOUC\)。浏览器将阻塞渲染，直至 DOM 和 CSSOM 全都准备就绪。

> _**CSS 是阻塞渲染的资源。需要将它尽早、尽快地下载到客户端，以便缩短首次渲染的时间。**_

不过，如果我们有一些 CSS 样式只在特定条件下（例如显示网页或将网页投影到大型显示器上时）使用，又该如何？如果这些资源不阻塞渲染，该有多好。

我们可以通过 CSS“媒体类型”和“媒体查询”来解决这类用例：

```html
<link href="style.css" rel="stylesheet">
<link href="print.css" rel="stylesheet" media="print">
<link href="other.css" rel="stylesheet" media="(min-width: 40em)">
```

[媒体查询](../../design-and-ux/responsive/index.html#use-css-media-queries-for-responsiveness)由媒体类型以及零个或多个检查特定媒体特征状况的表达式组成。例如，上面的第一个样式表声明未提供任何媒体类型或查询，因此它适用于所有情况，也就是说，它始终会阻塞渲染。第二个样式表则不然，它只在打印内容时适用---或许您想重新安排布局、更改字体等等，因此在网页首次加载时，该样式表不需要阻塞渲染。最后，最后一个样式表声明提供由浏览器执行的“媒体查询”：符合条件时，浏览器将阻塞渲染，直至样式表下载并处理完毕。

通过使用媒体查询，我们可以根据特定用例（比如显示或打印），也可以根据动态情况（比如屏幕方向变化、尺寸调整事件等）定制外观。**声明您的样式表资产时，请密切注意媒体类型和查询，因为它们将严重影响关键渲染路径的性能。**

让我们考虑下面这些实例：

```html
<link href="style.css"    rel="stylesheet">
<link href="style.css"    rel="stylesheet" media="all">
<link href="portrait.css" rel="stylesheet" media="orientation:portrait">
<link href="print.css"    rel="stylesheet" media="print">
```

第一个声明阻塞渲染，适用于所有情况。

* 第二个声明同样阻塞渲染：“all”是默认类型，如果您不指定任何类型，则隐式设置为“all”。因此，第一个声明和第二个声明实际上是等效的。
* 第三个声明具有动态媒体查询，将在网页加载时计算。根据网页加载时设备的方向，portrait.css 可能阻塞渲染，也可能不阻塞渲染。
* 最后一个声明只在打印网页时应用，因此网页首次在浏览器中加载时，它不会阻塞渲染。

最后，请注意“阻塞渲染”仅是指浏览器是否需要暂停网页的首次渲染，直至该资源准备就绪。无论哪一种情况，浏览器仍会下载 CSS 资产，只不过不阻塞渲染的资源优先级较低罢了。

## 使用 JavaScript 添加交互

JavaScript 允许我们修改网页的方方面面：内容、样式以及它如何响应用户交互。 不过，JavaScript 也会阻止 DOM 构建和延缓网页渲染。 为了实现最佳性能，可以让您的 JavaScript 异步执行，并去除关键渲染路径中任何不必要的 JavaScript。

#### TL;DR

* JavaScript 可以查询和修改 DOM 与 CSSOM。
* JavaScript 执行会阻止 CSSOM。
* 除非将 JavaScript 显式声明为异步，否则它会阻止构建 DOM。

JavaScript 是一种运行在浏览器中的动态语言，它允许我们对网页行为的几乎每一个方面进行修改：我们可以通过在 DOM 树中添加和移除元素来修改内容；我们可以修改每个元素的 CSSOM 属性；我们可以处理用户输入，等等。为进行说明，让我们用一个简单的内联脚本对之前的“Hello World”示例进行扩展：

```html
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <link href="style.css" rel="stylesheet">
    <title>Critical Path: Script</title>
  </head>
  <body>
    <p>Hello <span>web performance</span> students!</p>
    <div><img src="awesome-photo.jpg"></div>
    <script>
      var span = document.getElementsByTagName('span')[0];
      span.textContent = 'interactive'; // change DOM text content
      span.style.display = 'inline';  // change CSSOM property
      // create a new element, style it, and append it to the DOM
      var loadTime = document.createElement('div');
      loadTime.textContent = 'You loaded this page on: ' + new Date();
      loadTime.style.color = 'blue';
      document.body.appendChild(loadTime);
    </script>
  </body>
</html>
```

JavaScript 允许我们进入 DOM 并拉取对隐藏的 span 节点的引用 - 该节点可能未出现在渲染树中，却仍然存在于 DOM 内。然后，在我们获得引用后，就可以更改其文本（通过 .textContent），甚至可以将其计算的 display 样式属性从“none”替换为“inline”。现在，我们的页面显示“**Hello interactive students!**”。

* JavaScript 还允许我们在 DOM 中创建、样式化、追加和移除新元素。从技术上讲，我们的整个页面可以是一个大的 JavaScript 文件，此文件能够逐一创建元素并对其进行样式化。尽管这种方法可行，但是在实践中，使用 HTML 和 CSS 要简单得多。在 JavaScript 函数的第二部分，我们会创建一个新的 div 元素，设置其文本内容，对其进行样式化，然后将其追加到正文中。

![](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/device-js-small.png "网页预览")

我们通过以上示例修改了现有 DOM 节点的内容和 CSS 样式，并为文档添加了一个全新的节点。我们的网页不会赢得任何设计奖，但它说明了 JavaScript 赋予我们的能力和灵活性。

不过，尽管 JavaScript 为我们带来了许多功能，不过也在页面渲染方式和时间方面施加了更多限制。

首先，请注意上例中的内联脚本靠近网页底部。为什么呢？您真应该亲自尝试一下。如果我们将脚本移至_span\_元素之上，您就会注意到脚本运行失败，并提示在文档中找不到对任何 \_span_元素的引用 - 即_getElementsByTagName\(‘span'\)_会返回_null_。这透露出一个重要事实：我们的脚本在文档的何处插入，就在何处执行。当 HTML 解析器遇到一个 script 标记时，它会暂停构建 DOM，将控制权移交给 JavaScript 引擎；等 JavaScript 引擎运行完毕，浏览器会从中断的地方恢复 DOM 构建。

换言之，我们的脚本块找不到网页中任何靠后的元素，因为它们尚未接受处理！或者，稍微换个说法：**执行我们的内联脚本会阻止 DOM 构建，也就延缓了首次渲染。**

在网页中引入脚本的另一个微妙事实是，它们不仅可以读取和修改 DOM 属性，还可以读取和修改 CSSOM 属性。实际上，我们在示例中就是这么做的：将 span 元素的 display 属性从 none 更改为 inline。最终结果如何？我们现在遇到了竞态问题。

如果浏览器尚未完成 CSSOM 的下载和构建，而我们却想在此时运行脚本，会怎样？答案很简单，对性能不利：**浏览器将延迟脚本执行和 DOM 构建，直至其完成 CSSOM 的下载和构建。**

简言之，JavaScript 在 DOM、CSSOM 和 JavaScript 执行之间引入了大量新的依赖关系，从而可能导致浏览器在处理以及在屏幕上渲染网页时出现大幅延迟：

* 脚本在文档中的位置很重要。
* 当浏览器遇到一个 script 标记时，DOM 构建将暂停，直至脚本完成执行。
* JavaScript 可以查询和修改 DOM 与 CSSOM。
* JavaScript 执行将暂停，直至 CSSOM 就绪。

“优化关键渲染路径”在很大程度上是指了解和优化 HTML、CSS 和 JavaScript 之间的依赖关系谱。

## 解析器阻止与异步 JavaScript {#javascript}

默认情况下，JavaScript 执行会“阻止解析器”：当浏览器遇到文档中的脚本时，它必须暂停 DOM 构建，将控制权移交给 JavaScript 运行时，让脚本执行完毕，然后再继续构建 DOM。我们在前面的示例中已经见过内联脚本的实用情况。实际上，内联脚本始终会阻止解析器，除非您编写额外代码来推迟它们的执行。

通过 script 标签引入的脚本又怎样？让我们还用前面的例子，将代码提取到一个单独文件中：

```html
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <link href="style.css" rel="stylesheet">
    <title>Critical Path: Script External</title>
  </head>
  <body>
    <p>Hello <span>web performance</span> students!</p>
    <div><img src="awesome-photo.jpg"></div>
    <script src="app.js"></script>
  </body>
</html>
```

**app.js**

```js
var span = document.getElementsByTagName('span')[0];
span.textContent = 'interactive'; // change DOM text content
span.style.display = 'inline';  // change CSSOM property
// create a new element, style it, and append it to the DOM
var loadTime = document.createElement('div');
loadTime.textContent = 'You loaded this page on: ' + new Date();
loadTime.style.color = 'blue';
document.body.appendChild(loadTime);
```

无论我们使用 &lt;script&gt; 标记还是内联 JavaScript 代码段，您都可以期待两者能够以相同方式工作。 在两种情况下，浏览器都会先暂停并执行脚本，然后才会处理剩余文档。不过，**如果是外部 JavaScript 文件，浏览器必须停下来，等待从磁盘、缓存或远程服务器获取脚本，这就可能给关键渲染路径增加数十至数千毫秒的延迟。**

默认情况下，所有 JavaScript 都会阻止解析器。由于浏览器不了解脚本计划在页面上执行什么操作，它会作最坏的假设并阻止解析器。向浏览器传递脚本不需要在引用位置执行的信号既可以让浏览器继续构建 DOM，也能够让脚本在就绪后执行；例如，在从缓存或远程服务器获取文件后执行。

为此，我们可以将脚本标记为\_异步\_：

```html
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <link href="style.css" rel="stylesheet">
    <title>Critical Path: Script Async</title>
  </head>
  <body>
    <p>Hello <span>web performance</span> students!</p>
    <div><img src="awesome-photo.jpg"></div>
    <script src="app.js" async></script>
  </body>
</html>
```

向 script 标记添加异步关键字可以指示浏览器在等待脚本可用期间不阻止 DOM 构建，这样可以显著提升性能。

