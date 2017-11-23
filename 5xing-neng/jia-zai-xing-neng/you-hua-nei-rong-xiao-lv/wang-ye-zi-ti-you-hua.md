# 网页字体优化

> 作者：Ilya Grigorik

字体是实现良好的设计、品牌推广、可读性和无障碍功能的基础。网页字体可实现所有上述目标以及其他目标：文本可选取、可搜索、可缩放并支持高 DPI，无论屏幕尺寸和分辨率如何，均可提供一致并且锐利的文本渲染。网页字体是实现良好的设计、用户体验和性能的关键所在。

网页字体优化是总体性能策略的一个关键部分。每个字体都是一项附加资源，并且某些字体可能会阻止文本的渲染，但不能仅仅因为网页使用了网页字体，就认为它只能降低渲染速度。相反，如果对字体进行优化，再通过制定明智的策略对字体在网页上的加载和应用方式作出规定，就可以帮助减小网页总大小，并缩短网页渲染时间。

## 网页字体详解 {#_1}

### TL;DR {#tldr_hide-from-toc}

* Unicode 字体可能包含数千种字形。
* 字体格式有四种：WOFF2、WOFF、EOT 和 TTF。
* 某些字体格式需要使用 GZIP 压缩。

_网页字体_是一个字形集合，而每个字形是描述字母或符号的矢量形状。因此，特定字体文件的大小由两个简单变量决定：每个字形矢量路径的复杂程度和特定字体中字形的数量。例如，Open Sans 是其中一种最流行的网页字体，包含 897 个字形，其中包括拉丁文、希腊文和西里尔文字符。

![](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/images/glyphs.png "字体字形表")

选取字体时，重要的是考虑它支持的字符集。如果您需要将页面内容本地化成多种语言，就应该使用一种能够为用户带来一致的外观和体验的字体。例如，[Google 的 Noto 字体系列](https://www.google.com/get/noto/)旨在支持全世界的语言。但请注意，Noto（含所有语言）ZIP 格式下载文件的总大小超过了 130MB。

显然，在网页上使用字体需要某些细致的设计，以确保字体不影响性能。幸运的是，网络平台提供了所有必要的基元，本指南的其余部分将通过实际操作让您了解如何两全其美。

### 网页字体格式 {#_2}

目前网络上使用的字体容器格式有四种：[EOT](https://en.wikipedia.org/wiki/Embedded_OpenType)、[TTF](https://en.wikipedia.org/wiki/TrueType)、[WOFF](https://en.wikipedia.org/wiki/Web_Open_Font_Format)和[WOFF2](https://www.w3.org/TR/WOFF2/)。遗憾的是，尽管选择范围很广，但仍然缺少在所有新旧浏览器上都能使用的单一通用格式：EOT 只有[IE 支持](http://caniuse.com/#feat=eot)，TTF 获得了[部分 IE 支持](http://caniuse.com/#search=ttf)，WOFF 获得了最广泛的支持，但[在某些较旧的浏览器上不受支持](http://caniuse.com/#feat=woff)，而 WOFF 2.0 支持[对许多浏览器来说尚未实现](http://caniuse.com/#feat=woff2)。

那我们该怎么办？不存在在所有浏览器上都能使用的单一格式，这意味着我们需要提供多种格式才能实现一致的体验：

* 将 WOFF 2.0 变体提供给支持它的浏览器。
* 将 WOFF 变体提供给大多数浏览器。
* 将 TTF 变体提供给旧 Android（4.4 版以下）浏览器。
* 将 EOT 变体提供给旧 IE（IE9 版以下）浏览器。

注：从技术上讲，还有另一种容器格式，即[SVG 字体容器](http://caniuse.com/svg-fonts)，但 IE 或 Firefox 从不支持它，并且现在 Chrome 也放弃了对它的支持。因此，它的用途很有限，本指南中有意将其忽略。

### 通过压缩减小字体大小 {#_3}

字体是字形的集合，其中的每个字形都是一组描述字母形状的路径。各个字形不同，但它们仍然包含大量相似信息，这些信息可通过 GZIP 或兼容的压缩工具进行压缩：

* EOT 和 TTF 格式默认情况下不进行压缩。提供这些格式时，确保您的服务器配置为应用[GZIP 压缩](optimize-encoding-and-transfer.html#text-compression-with-gzip)。
* WOFF 具有内建压缩。确保您的 WOFF 压缩工具使用了最佳压缩设置。
* WOFF2 采用自定义预处理和压缩算法，提供的文件大小压缩率比其他格式高大约 30%。如需了解详细信息，请参阅[WOFF 2.0 评估报告](http://www.w3.org/TR/WOFF20ER/)。

最后，值得注意的是，某些字体格式包含附加的元数据，如[字体提示](https://en.wikipedia.org/wiki/Font_hinting)和[字距调整](https://en.wikipedia.org/wiki/Kerning)信息，这些信息在某些平台上可能并非必要信息，这样便可进一步优化文件大小。查询您的字体压缩工具是否提供了优化选项，如果您这样做，请确保您有适合的基础架构来测试这些优化过的字体并将它们提供给每个特定浏览器。例如，Google Fonts 为每一种字体维护有 30 多种优化过的变体，并自动检测和提供适合每一个平台和浏览器的最佳变体。

注：考虑使用[Zopfli 压缩](http://en.wikipedia.org/wiki/Zopfli)处理 EOT、TTF 和 WOFF 格式。Zopfli 是一种兼容 zlib 的压缩工具，提供的文件大小压缩率比 gzip 高大约 5%。

## 通过 @font-face 定义字体系列 {#font-face}

### TL;DR {#tldr_hide-from-toc_1}

* 利用`format()`提示指定多种字体格式。
* 对大型 Unicode 字体进行子集内嵌以提高性能。使用 Unicode-range 子集内嵌，并为较旧的浏览器提供手动子集内嵌回退。
* 减少风格字体变体的数量以改进网页和文本渲染性能。

您可以通过 @font-face CSS at-rule 定义特定字体资源的位置、其样式特性及其应该用于的 Unicode 代码点。可组合使用上述 @font-face 声明来构建“字体系列”，浏览器将使用该系列来评估哪些字体资源需要下载并应用到当前网页。

### 格式选择 {#_4}

每个 @font-face 声明都提供字体系列的名称，它充当多个声明的逻辑组、[字体属性](http://www.w3.org/TR/css3-fonts/#font-prop-desc)（如样式、粗细和拉伸）以及为字体资源指定位置优先级列表的[src 描述符](http://www.w3.org/TR/css3-fonts/#src-desc)。

```css
@font-face {
  font-family: 'Awesome Font';
  font-style: normal;
  font-weight: 400;
  src: local('Awesome Font'),
       url('/fonts/awesome.woff2') format('woff2'), 
       url('/fonts/awesome.woff') format('woff'),
       url('/fonts/awesome.ttf') format('truetype'),
       url('/fonts/awesome.eot') format('embedded-opentype');
}

@font-face {
  font-family: 'Awesome Font';
  font-style: italic;
  font-weight: 400;
  src: local('Awesome Font Italic'),
       url('/fonts/awesome-i.woff2') format('woff2'), 
       url('/fonts/awesome-i.woff') format('woff'),
       url('/fonts/awesome-i.ttf') format('truetype'),
       url('/fonts/awesome-i.eot') format('embedded-opentype');
}
```

首先，请注意以上示例使用两种样式（normal 和 italic）来定义单个 Awesome Font 系列，其中的每个样式均指向一个不同的字体资源集。每个`src`描述符则又包含一个用逗号分隔的资源变体优先级列表：

* `local()`指令用于引用、加载和使用安装在本地的字体。
* `url()`指令用于加载外部字体，它可以包含可选的`format()`提示，指示由提供的网址引用的字体格式。

注：除非您引用的是其中一种默认系统字体，在实践中，用户很少将其安装在本地，特别是在移动设备上，在移动设备上‘安装’附加字体实际上根本无法实现。因此，您应该始终提供一个外部字体位置列表。

当浏览器确定需要字体时，它会按指定顺序循环访问提供的资源列表，并尝试加载相应的资源。例如，接着上面的示例：

1. 浏览器执行页面布局并确定需要使用哪些字体变体来渲染网页上的指定文本。
2. 对于每一种必需字体，浏览器会检查字体文件是否位于本地。
3. 如果字体文件不在本地，则浏览器会遍历外部定义：
   * 如果存在格式提示，则浏览器会在启动下载之前检查其是否支持。如果浏览器不支持此提示，则会前进到下一格式提示。
   * 如果不存在格式提示，则浏览器会下载资源。

您可以将本地和外部指令与相应的格式提示相结合来指定所有可用字体格式，其余工作交由浏览器进行处理。浏览器确定需要哪些资源，并选择最佳格式。

注：字体变体的指定顺序很重要。浏览器将选取其支持的第一种格式。因此，如果您希望较新的浏览器使用 WOFF2，则应将 WOFF2 声明置于 WOFF 之上，依此类推。

### Unicode-range 子集内嵌 {#unicode-range}

除了样式、粗细和拉伸等字体属性外，还可以通过 @font-face 规则定义各资源支持的 Unicode 代码点集。

这样一来，便可将大型 Unicode 字体拆分成较小的子集（例如拉丁文、西里尔文和希腊文子集），并且只下载在特定网页上渲染文本所需的字形。

您可以通过[unicode-range 描述符](http://www.w3.org/TR/css3-fonts/#descdef-unicode-range)指定一个用逗号分隔的范围值列表，其中的每个值都可能采用下列三种不同形式中的一种：

* 单一代码点（例如 U+416）
* 间隔范围（例如 U+400-4ff）：表示范围的开始代码点和结束代码点
* 通配符范围（例如 U+4??）：“?”字符表示任何十六进制数字

例如，您可以将 Awesome Font 系列拆分成拉丁文和日文子集，其中的每个子集将由浏览器根据需要下载：

```css
@font-face {
  font-family: 'Awesome Font';
  font-style: normal;
  font-weight: 400;
  src: local('Awesome Font'),
       url('/fonts/awesome-l.woff2') format('woff2'), 
       url('/fonts/awesome-l.woff') format('woff'),
       url('/fonts/awesome-l.ttf') format('truetype'),
       url('/fonts/awesome-l.eot') format('embedded-opentype');
  unicode-range: U+000-5FF; /* Latin glyphs */
}

@font-face {
  font-family: 'Awesome Font';
  font-style: normal;
  font-weight: 400;
  src: local('Awesome Font'),
       url('/fonts/awesome-jp.woff2') format('woff2'), 
       url('/fonts/awesome-jp.woff') format('woff'),
       url('/fonts/awesome-jp.ttf') format('truetype'),
       url('/fonts/awesome-jp.eot') format('embedded-opentype');
  unicode-range: U+3000-9FFF, U+ff??; /* Japanese glyphs */
}
```

注：Unicode-range 子集内嵌对亚洲语言特别重要，因为在亚洲语言中，字形数量比西方语言多得多，标准的“完整”字体常常以兆字节而非千字节计量。

您可以通过使用 Unicode range 子集，以及为字体的每个样式变体使用单独的文件，定义一个下载起来更快速并且更高效的复合字体系列。访问者将只下载其需要的变体和子集，并且不会强制他们下载可能永远不会在网页上看到或使用的子集。 不过，unicode-range 也有一个小缺陷：[并非所有浏览器都支持它](http://caniuse.com/#feat=font-unicode-range)。 某些浏览器会简单地忽略 unicode-range 提示并下载所有变体，另一些浏览器则可能根本不会处理 @font-face 声明。

要解决此问题，对于较旧的浏览器，您需要回退到“手动子集内嵌”。由于旧浏览器因不够智能而无法只选择必要的子集，也无法构建复合字体，因此您必须回退以提供包含所有必要子集的单一字体资源，并向浏览器隐藏其余子集。例如，如果网页只使用拉丁文字符，那么您可以去除其他字形，并将该特定子集作为一个独立资源提供。

1. **您如何确定需要哪些子集？**
   * 如果浏览器支持 unicode-range 子集内嵌，则它会自动选择正确的子集。网页只需提供子集文件并在 @font-face 规则中指定相应的 unicode-range。
   * 如果浏览器不支持 unicode-range 子集内嵌，则网页需要隐藏所有多余的子集，即开发者必须指定需要的子集。
2. **您如何生成字体子集？**
   * 使用开源的
     [pyftsubset 工具](https://github.com/behdad/fonttools/)
     对您的字体进行子集内嵌和优化。
   * 某些字体服务允许通过自定义查询参数手动内嵌子集，您可以利用这些参数手动指定网页需要的子集。请查阅您的字体提供商的文档。

### 字体选择和合成 {#_5}

每个字体系列都由多个样式变体（常规、加粗、倾斜）和适用于每个样式的多个粗细组成，其中的每个粗细又可能包含迥异的字形形状 - 例如不同的间距、大小调整或完全不同的形状。

![](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/images/font-weights.png "字体粗细")

例如，上图以图解方式说明了一个提供三种不同加粗粗细的字体系列： 400（常规）、700（加粗）和 900（特粗）。浏览器会将所有其他中间变体（以灰色表示）自动映射到最接近的变体。

> 如果指定的某个粗细不存在对应的字体，则使用相近粗细的字体。一般而言，加粗粗细映射到粗细较粗的字体，而较细粗细则映射到粗细较细的字体。
>
> > [CSS3 字体匹配算法](http://www.w3.org/TR/css3-fonts/#font-matching-algorithm)

倾斜变体也适用类似的逻辑。字体设计者控制其将产生哪些变体，而您控制将在网页上使用哪些变体。由于每个变体都会单独下载，因此最好将变体数量保持在较低水平。 例如，您可以为 Awesome Font 系列定义两种加粗变体：

```css
@font-face {
  font-family: 'Awesome Font';
  font-style: normal;
  font-weight: 400;
  src: local('Awesome Font'),
       url('/fonts/awesome-l.woff2') format('woff2'), 
       url('/fonts/awesome-l.woff') format('woff'),
       url('/fonts/awesome-l.ttf') format('truetype'),
       url('/fonts/awesome-l.eot') format('embedded-opentype');
  unicode-range: U+000-5FF; /* Latin glyphs */
}

@font-face {
  font-family: 'Awesome Font';
  font-style: normal;
  font-weight: 700;
  src: local('Awesome Font'),
       url('/fonts/awesome-l-700.woff2') format('woff2'), 
       url('/fonts/awesome-l-700.woff') format('woff'),
       url('/fonts/awesome-l-700.ttf') format('truetype'),
       url('/fonts/awesome-l-700.eot') format('embedded-opentype');
  unicode-range: U+000-5FF; /* Latin glyphs */
}
```

上例声明的 Awesome Font 系列由两项资源组成，它们涵盖同一拉丁文字形集 \(U+000-5FF\)，但提供两种不同的“粗细”：常规 \(400\) 和加粗 \(700\)。不过，如果您的其中一个 CSS 规则指定了一种不同的字体粗细，或者将 font-style 属性设置为 italic，那会怎么样？

* 如果未找到精确字体匹配项，浏览器将以最接近的匹配项替代。
* 如果未找到样式匹配项（例如，在上例中未声明任何倾斜变体），则浏览器将合成其自己的字体变体。

![](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/images/font-synthesis.png "字体合成")

> 字体创造者还应注意，合成方法可能不适用于西里尔文那样的文字系统，在这些文字系统中，斜体形式在形状方面非常不同。使用某种实际的斜体字体总是比依赖某个合成版本要好。
>
> > [CSS3 font-style](http://www.w3.org/TR/css3-fonts/#propdef-font-style)

上例以图解方式说明了 Open-Sans 的实际字体与合成字体结果之间的差异。所有合成变体都是依据单个 400 粗细的字体生成的。您可以看出，结果存在显著差异。其中并未详细说明如何生成加粗和倾斜变体。因此，结果将因浏览器的不同而发生变化，并且与字体的相关度极高。

注：为获得最好的一致性和视觉效果，您不应该依赖字体合成，而应最大限度减少使用的字体变体的数量并指定其位置，这样一来，只有在网页使用它们时，浏览器才会进行下载。不过，在某些情况下，合成的变体[或许是可行的选择](https://www.igvita.com/2014/09/16/optimizing-webfont-selection-and-synthesis/)，不过请谨慎使用。

