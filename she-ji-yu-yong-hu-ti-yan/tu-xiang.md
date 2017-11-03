# 图像

> 作者：Pete LePage

自适应网页设计意味着，不仅我们的布局能根据设备特性而变化，内容也可以。 例如，高分辨率 \(2x\) 显示屏上高分辨率图形可保证清晰度。 当浏览器宽度为 800px 时，一张 50% 宽度的图像或许很适合，但在一款很窄的手机上，则会占用太多屏幕空间，并且缩小图像来适应较小的屏幕时，耗费的带宽开销却是一样的。

## 艺术指导 {#_1}

![](https://developers.google.com/web/fundamentals/design-and-ux/responsive/img/art-direction.png "艺术指导示例")

其余时候，图像也许需要更大幅度的修改：更改比例、裁剪甚至更换整个图像。 在这种情况下，更改图像通常称作艺术指导。 请访问[responsiveimages.org/demos/](https://responsiveimages.org/demos/)，查看更多示例。

## Responsive Images {#responsive-images}

[![](https://developers.google.com/web/shows/udacity/img/ud882.png)](https://www.udacity.com/course/responsive-images--ud882)

Did you know that images account for more than 60% of the bytes on average needed to load a web page?

In this course you will learn how to work with images on the modern web, so that your images look great and load quickly on any device.

Along the way, you will pick up a range of skills and techniques to smoothly integrate responsive images into your development workflow. By the end of the course, you will be developing with images that adapt and respond to different viewport sizes and usage scenarios.

This is a free course offered through[Udacity](https://www.udacity.com/)

[Take Course](https://www.udacity.com/course/responsive-images--ud882)

## 标记中的图像 {#_2}

`img`元素很强大，它可下载、解码然后渲染内容，而现代浏览器支持众多图像格式。跨设备使用图像与桌面端并无二致，只需做一些微小调整，就能构建美好体验。

### TL;DR {#tldr_hide-from-toc}

* 为图像使用相对尺寸，防止它们意外溢出容器。
* 如果您要根据设备特性（亦称艺术指导）指定不同图像，则使用`picture`元素。
* 在`img`元素中使用`srcset`及`x`描述符，引导浏览器从不同密度图像中选择、使用最佳的一张。
* 如果您的页面仅有一两个图像，且这些图像没有在您的网站上的其他地方使用，可考虑使用内联图像以减少文件请求。

### 图像使用相对尺寸 {#_3}

在指定图像宽度时，请记得使用相对单位，以防止它们意外溢出视口。 例如，`width: 50%;`将图片宽度设置为包含元素宽度的 50%（并非视口或实际像素大小的 50%）。

因为 CSS 允许内容溢出其容器，因此可能需要使用 max-width: 100% 来保证图像及其他内容不会溢出。例如：

```css
img, embed, object, video {
  max-width: 100%;
}
```

请务必借助`img`元素的`alt`属性提供有意义的描述；这些描述有助于提高您的网站的可访问性，因为它们能提供语境给屏幕阅读器及其他辅助性技术。

### 高 DPI 设备上使用`srcset`来增强`img` {#dpi_srcset_img}

`srcset`属性增强了`img`元素的行为，让您能够轻松地针对不同设备特性提供多种图片文件。类似于 CSS 原生的`image-set`[CSS 函数](https://developers.google.com/web/fundamentals/design-and-ux/responsive/images#use-image-set-to-provide-high-res-images)，`srcset`也允许浏览器根据设备特性选择最佳图像，例如，在 2x 显示屏上使用 2x 图像，将来甚至允许在网络带宽有限时对 2x 设备使用 1x 图像。

```html
<img src="photo.png" srcset="photo@2x.png 2x" ...>
```

在不支持`srcset`的浏览器上，浏览器只需使用`src`属性指定的默认图像文件。 正因如此，无论设备能力如何，一定要始终包含一个在任何设备上都能显示的 1x 图像。如果`srcset`受支持，则会在进行任何请求之前对逗号分隔的图像/条件列表进行解析，并且只会下载和显示最合适的图像。

尽管条件可以包含从像素密度到宽度和高度等各种参数，但目前只有像素密度能够得到很好的支持。 为使当前行为和未来特性保持平衡，请继续在属性中只提供 2x 图像。

### 在自适应图像中用`picture`实现艺术指导 {#picture}

![](https://developers.google.com/web/fundamentals/design-and-ux/responsive/img/art-direction.png "艺术指导示例")

若要根据设备特性更改图像（也称为艺术指导），请使用`picture`元素。`picture`元素定义了一个声明性解决办法，可根据设备大小、设备分辨率、屏幕方向等不同特性来提供一个图像的多个版本。

Dogfood：`picture`元素刚开始在浏览器上实现。虽然目前还不是每个浏览器都支持，但我们推荐使用它，因为它具备出色的向后兼容性，还可以使用[Picturefill polyfill](http://picturefill.responsiveimages.org/)。如需了解更多详细信息，请参阅[ResponsiveImages.org](http://responsiveimages.org/#implementation)网站。

当图像源存在多种密度，或自适应设计要求在部分类型屏幕上显示不太一样的图像时，可使用`picture`元素。类似于`video`元素，它可以包含多个`source`元素，使得根据媒体查询或图片格式指定不同图片文件成为可能。

```html
<picture>
  <source media="(min-width: 800px)" srcset="head.jpg, head-2x.jpg 2x">
  <source media="(min-width: 450px)" srcset="head-small.jpg, head-small-2x.jpg 2x">
  <img src="head-fb.jpg" srcset="head-fb-2x.jpg 2x" alt="a head carved out of wood">
</picture>
```

[试一下](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/media/media.html)

上面的例子中，如果浏览器宽度至少为 800px，则将根据设备分辨率使用`head.jpg`或`head-2x.jpg`。如果浏览器宽度在 450px 和 800px 之间，则将根据设备分辨率使用`head-small.jpg`或`head-small-2x.jpg`。对于屏幕宽度小于 450px，且不支持`picture`元素向后兼容的情况，浏览器将渲染`img`元素，因此要始终包含该元素。

#### 相对大小的图像 {#_4}

如果不知道图像的最终尺寸，则很难给图像源指定一个密度描述符。 对于宽度与浏览器宽度成比例的图像更是如此，图像尺寸是可变的，具体取决于浏览器的尺寸。

提供的每个图像的尺寸可以通过添加宽度描述符和图像元素的大小来指定，而不是提供固定的图像尺寸和密度，从而允许浏览器自动计算有效的像素密度，然后选择下载最佳图像。

```html
<img src="lighthouse-200.jpg" sizes="50vw"
     srcset="lighthouse-100.jpg 100w, lighthouse-200.jpg 200w,
             lighthouse-400.jpg 400w, lighthouse-800.jpg 800w,
             lighthouse-1000.jpg 1000w, lighthouse-1400.jpg 1400w,
             lighthouse-1800.jpg 1800w" alt="a lighthouse">
```

[试一下](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/media/sizes.html)

在上面的例子中，渲染了一张宽度为视口宽度一半 \(`sizes="50vw"`\) 的图像，根据浏览器的宽度及其设备像素比，允许浏览器选择正确的图像，而不考虑浏览器窗口有多大。例如，下面的表格显示了浏览器会选择哪张图像：

| 浏览器宽度 | 设备像素比 | 使用的图像 | 有效分辨率 |
| :--- | :--- | :--- | :--- |
| 400px | 1 | `200.png` | 1x |
| 400px | 2 | `400.png` | 2x |
| 320px | 2 | `400.png` | 2.5x |
| 600px | 2 | `800.png` | 2.67x |
| 640px | 3 | `1000.png` | 3.125x |
| 1100px | 1 | `1400.png` | 1.27x |

#### 考虑自适应图像中的断点 {#_5}

在许多情况下，图像尺寸可能会根据网站的布局断点发生变化。 例如，在一个小屏幕上，您可能想要图像占满视口的全宽，在大屏幕上，则应只占一小部分。

```html
<img src="400.png" 
     sizes="(min-width: 600px) 25vw, (min-width: 500px) 50vw, 100vw"
     srcset="100.png 100w, 200.png 200w, 400.png 400w,
             800.png 800w, 1600.png 1600w, 2000.png 2000w" alt="an example image">
```

[试一下](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/media/breakpoints.html)

上面例子中的`sizes`属性使用多个媒体查询来指定图片尺寸。 当浏览器宽度大于 600px 时，图像占据视口宽度的 25%，浏览器宽度在 500px 到 600px 之间时，图像占据视口宽度的 50%，如果低于 500px，图像为全宽。

### 让产品图像可扩展 {#_6}

![](https://developers.google.com/web/fundamentals/design-and-ux/responsive/img/sw-make-images-expandable-good.png "带有可扩展产品图像的 J. Crews 网站")

带有可扩展产品图像的 J. Crew 网站。

客户想要看到他们买的是什么。在零售网站上，用户希望能够看到高分辨率的产品特写，以更好地看清细节，[调查对象](https://developers.google.com/web/fundamentals/getting-started/principles/#make-product-images-expandable)如果没能看到商品特写会感到非常沮丧。

J. Crew 网站提供了一个很好的可点按、可展开图像的例子。一个消失中的叠层表示图像可点按，点按后提供放大的图像细节。

### 其他图像技术 {#_7}

#### 压缩的图像 {#_8}

[压缩的图像技术](http://www.html5rocks.com/en/mobile/high-dpi/#toc-tech-overview)可以给所有设备提供一个高度压缩的 2x 图像，无论设备的实际能力如何。根据图像的类型和压缩级别，图像质量可能看似没有变化，但文件大小却明显减小。

[试一下](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/media/compressive.html)

注意：请慎用压缩技术，因为它需要增加内存和解码成本。在内存和处理能力有限的低端设备上，调整大图像以适应较小屏幕的开销非常大，并且费时费力。

#### JavaScript 图像替换 {#javascript}

JavaScript 图像替换技术检查设备能力，然后“做正确的事”。 您可以通过`window.devicePixelRatio`确定设备像素比，获取屏幕的宽度和高度，甚至可通过`navigator.connection`或发出假请求来执行某种网络连接嗅探。收集了所有这些信息后，您就可以决定加载哪个图像。

此方法的一大缺陷是，使用 JavaScript 意味着您将延迟加载图像，至少要等到先行解析器结束。 这意味着，图像要等到`pageload`事件触发后才能开始下载。此外，浏览器很可能会同时下载 1x 和 2x 图像，导致页面重量增加。

#### 内联图像：光栅图像和矢量图像 {#_9}

创建和存储图像有两种全然不同的方式，这将决定您如何以自适应方式部署图像。

**光栅图像**：如相片及其他通过单个颜色点网格表示的图像。光栅图像可来自照相机或扫描仪，也可以借助 HTML canvas 元素创建。可使用 PNG、JPEG 和 WebP 之类的格式存储光栅图像。

**矢量图像**：如徽标和艺术线条，是由一系列的曲线、直线、形状、填充色和渐变色定义的。矢量图像可通过 Adobe Illustrator 或 Inkscape 之类的程序创建，或使用 SVG 等矢量格式在代码中手动写入。

##### SVG {#svg}

使用 SVG 可在网页中包含自适应矢量图像。矢量文件格式比光栅文件格式有优势的地方在于，浏览器可以渲染任何大小的矢量图像。矢量格式描述的是图像的几何图形，即该图像是如何通过线条、曲线和颜色等构造的。而光栅格式仅提供与单个颜色点有关的信息，因此，在进行缩放时，浏览器必须猜测如何填充空白。

下面是同一个图像的两个版本：左边是一个 PNG 图像，右边是一个 SVG 图像。SVG 图像在任何尺寸看上去都非常棒，而它旁边的 PNG 图像在显示尺寸较大时会变得模糊。

![](https://developers.google.com/web/fundamentals/design-and-ux/responsive/img/html5.png "HTML5 徽标，PNG 格式")![](https://developers.google.com/web/fundamentals/design-and-ux/responsive/img/html5.svg "HTML5 徽标，SVG 格式")

如果您要减少页面发出的文件请求数量，您可以使用 SVG 或 Data URI 格式为图像内联编写代码。如果您查看此页面的源代码，就会发现下面的这两个徽标均被声明为内联：Data URI 和 SVG。

![](data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz4NCjwhLS0gR2VuZXJhdG9yOiB%0A      BZG9iZSBJbGx1c3RyYXRvciAxNi4wLjAsIFNWRyBFeHBvcnQgUGx1Zy1JbiAuIFNWRyBWZXJzaW%0A      9uOiA2LjAwIEJ1aWxkIDApICAtLT4NCjwhRE9DVFlQRSBzdmcgUFVCTElDICItLy9XM0MvL0RUR%0A      CBTVkcgMS4xLy9FTiIgImh0dHA6Ly93d3cudzMub3JnL0dyYXBoaWNzL1NWRy8xLjEvRFREL3N2%0A      ZzExLmR0ZCI+DQo8c3ZnIHZlcnNpb249IjEuMSIgaWQ9IkxheWVyXzEiIHhtbG5zPSJodHRwOi8%0A      vd3d3LnczLm9yZy8yMDAwL3N2ZyIgeG1sbnM6eGxpbms9Imh0dHA6Ly93d3cudzMub3JnLzE5OT%0A      kveGxpbmsiIHg9IjBweCIgeT0iMHB4Ig0KCSB3aWR0aD0iMzk2Ljc0cHgiIGhlaWdodD0iNTYwc%0A      HgiIHZpZXdCb3g9IjI4MS42MyAwIDM5Ni43NCA1NjAiIGVuYWJsZS1iYWNrZ3JvdW5kPSJuZXcg%0A      MjgxLjYzIDAgMzk2Ljc0IDU2MCIgeG1sOnNwYWNlPSJwcmVzZXJ2ZSINCgk+DQo8Zz4NCgk8Zz4%0A      NCgkJPGc+DQoJCQk8cG9seWdvbiBmaWxsPSIjRTQ0RDI2IiBwb2ludHM9IjQwOS43MzcsMjQyLj%0A      UwMiA0MTQuMjc2LDI5My4zNjIgNDc5LjgyOCwyOTMuMzYyIDQ4MCwyOTMuMzYyIDQ4MCwyNDIuN%0A      TAyIDQ3OS44MjgsMjQyLjUwMiAJCQkNCgkJCQkiLz4NCgkJCTxwYXRoIGZpbGw9IiNFNDREMjYi%0A      IGQ9Ik0yODEuNjMsMTEwLjA1M2wzNi4xMDYsNDA0Ljk2OEw0NzkuNzU3LDU2MGwxNjIuNDctNDU%0A    uMDQybDM2LjE0NC00MDQuOTA1SDI4MS42M3ogTTYxMS4yODMsNDg5LjE3Ng0KCQkJCUw0ODAsNT%0A    I1LjU3MlY0NzQuMDNsLTAuMjI5LDAuMDYzTDM3OC4wMzEsNDQ1Ljg1bC02Ljk1OC03Ny45ODVoM%0A    jIuOThoMjYuODc5bDMuNTM2LDM5LjYxMmw1NS4zMTUsMTQuOTM3bDAuMDQ2LTAuMDEzdi0wLjAw%0A    NA0KCQkJCUw0ODAsNDIyLjM1di03OS4zMmgtMC4xNzJIMzY4Ljg1M2wtMTIuMjA3LTEzNi44NzF%0A    sLTEuMTg5LTEzLjMyNWgxMjQuMzcxSDQ4MHYtNDkuNjY4aDE2Mi4xN0w2MTEuMjgzLDQ4OS4xNz%0A    Z6Ii8+DQoJCQk8cG9seWdvbiBmaWxsPSIjRjE2NTI5IiBwb2ludHM9IjQ4MCwxOTIuODMzIDYwN%0A    C4yNDcsMTkyLjgzMyA2MDMuMDU5LDIwNi4xNTkgNjAwLjc5NiwyMzEuMzM4IDU5OS44LDI0Mi41%0A    MDIgNTk5LjY0LDI0Mi41MDIgDQoJCQkJNDgwLDI0Mi41MDIgNDgwLDI5My4zNjIgNTgxLjg5Niw%0A    yOTMuMzYyIDU5NS4yOCwyOTMuMzYyIDU5NC4wNjgsMzA2LjY5OSA1ODIuMzk2LDQzNy40NTggNT%0A    gxLjY0OSw0NDUuODUgNDgwLDQ3NC4wMjEgDQoJCQkJNDgwLDQ3NC4wMyA0ODAsNTI1LjU3MiA2M%0A    TEuMjgzLDQ4OS4xNzYgNjQyLjE3LDE0My4xNjYgNDgwLDE0My4xNjYgCQkJIi8+DQoJCQk8cG9s%0A    eWdvbiBmaWxsPSIjRjE2NTI5IiBwb2ludHM9IjU0MC45ODgsMzQzLjAyOSA0ODAsMzQzLjAyOSA%0A    0ODAsNDIyLjM1IDUzNS4yMjQsNDA3LjQ0NSAJCQkiLz4NCgkJCTxwb2x5Z29uIGZpbGw9IiNFQk%0A    VCRUIiIHBvaW50cz0iNDE0LjI3NiwyOTMuMzYyIDQwOS43MzcsMjQyLjUwMiA0NzkuODI4LDI0M%0A    i41MDIgNDc5LjgyOCwyNDIuMzggNDc5LjgyOCwyMjMuNjgyIA0KCQkJCTQ3OS44MjgsMTkyLjgz%0A    MyAzNTUuNDU3LDE5Mi44MzMgMzU2LjY0NiwyMDYuMTU5IDM2OC44NTMsMzQzLjAyOSA0NzkuODI%0A    4LDM0My4wMjkgNDc5LjgyOCwyOTMuMzYyIAkJCSIvPg0KCQkJPHBvbHlnb24gZmlsbD0iI0VCRU%0A    JFQiIgcG9pbnRzPSI0NzkuODI4LDQ3NC4wNjkgNDc5LjgyOCw0MjIuNCA0NzkuNzgyLDQyMi40M%0A    TMgNDI0LjQ2Nyw0MDcuNDc3IDQyMC45MzEsMzY3Ljg2NCANCgkJCQkzOTQuMDUyLDM2Ny44NjQg%0A    MzcxLjA3MiwzNjcuODY0IDM3OC4wMzEsNDQ1Ljg1IDQ3OS43NzEsNDc0LjA5NCA0ODAsNDc0LjA%0A    zIDQ4MCw0NzQuMDIxIAkJCSIvPg0KCQkJPHBvbHlnb24gcG9pbnRzPSIzNDMuNzg0LDUwLjIyOS%0A    AzNjYuODc0LDUwLjIyOSAzNjYuODc0LDc1LjUxNyAzOTIuMTE0LDc1LjUxNyAzOTIuMTE0LDAgM%0A    zY2Ljg3MywwIDM2Ni44NzMsMjQuOTM4IA0KCQkJCTM0My43ODMsMjQuOTM4IDM0My43ODMsMCAz%0A    MTguNTQ0LDAgMzE4LjU0NCw3NS41MTcgMzQzLjc4NCw3NS41MTcgCQkJIi8+DQoJCQk8cG9seWd%0A    vbiBwb2ludHM9IjQyNS4zMDcsMjUuMDQyIDQyNS4zMDcsNzUuNTE3IDQ1MC41NDksNzUuNTE3ID%0A    Q1MC41NDksMjUuMDQyIDQ3Mi43NzksMjUuMDQyIDQ3Mi43NzksMCA0MDMuMDg1LDAgDQoJCQkJN%0A    DAzLjA4NSwyNS4wNDIgNDI1LjMwNiwyNS4wNDIgCQkJIi8+DQoJCQk8cG9seWdvbiBwb2ludHM9%0A    IjUwOC41MzcsMzguMDg2IDUyNS45MTQsNjQuOTM3IDUyNi4zNDksNjQuOTM3IDU0My43MTQsMzg%0A    uMDg2IDU0My43MTQsNzUuNTE3IDU2OC44NTEsNzUuNTE3IDU2OC44NTEsMCANCgkJCQk1NDIuNT%0A    IyLDAgNTI2LjM0OSwyNi41MzQgNTEwLjE1OSwwIDQ4My44NCwwIDQ4My44NCw3NS41MTcgNTA4L%0A    jUzNyw3NS41MTcgCQkJIi8+DQoJCQk8cG9seWdvbiBwb2ludHM9IjY0Mi4xNTYsNTAuNTU1IDYw%0A    Ni42Niw1MC41NTUgNjA2LjY2LDAgNTgxLjQxMiwwIDU4MS40MTIsNzUuNTE3IDY0Mi4xNTYsNzU%0A    uNTE3IAkJCSIvPg0KCQkJPHBvbHlnb24gZmlsbD0iI0ZGRkZGRiIgcG9pbnRzPSI0ODAsNDc0Lj%0A    AyMSA1ODEuNjQ5LDQ0NS44NSA1ODIuMzk2LDQzNy40NTggNTk0LjA2OCwzMDYuNjk5IDU5NS4yO%0A    CwyOTMuMzYyIDU4MS44OTYsMjkzLjM2MiANCgkJCQk0ODAsMjkzLjM2MiA0NzkuODI4LDI5My4z%0A    NjIgNDc5LjgyOCwzNDMuMDI5IDQ4MCwzNDMuMDI5IDU0MC45ODgsMzQzLjAyOSA1MzUuMjI0LDQ%0A    wNy40NDUgNDgwLDQyMi4zNSA0NzkuODI4LDQyMi4zOTYgDQoJCQkJNDc5LjgyOCw0MjIuNCA0Nz%0A    kuODI4LDQ3NC4wNjkgCQkJIi8+DQoJCQk8cG9seWdvbiBmaWxsPSIjRkZGRkZGIiBwb2ludHM9I%0A    jQ3OS44MjgsMjQyLjM4IDQ3OS44MjgsMjQyLjUwMiA0ODAsMjQyLjUwMiA1OTkuNjQsMjQyLjUw%0A    MiA1OTkuOCwyNDIuNTAyIDYwMC43OTYsMjMxLjMzOCANCgkJCQk2MDMuMDU5LDIwNi4xNTkgNjA%0A    0LjI0NywxOTIuODMzIDQ4MCwxOTIuODMzIDQ3OS44MjgsMTkyLjgzMyA0NzkuODI4LDIyMy42OD%0A    IgCQkJIi8+DQoJCTwvZz4NCgk8L2c+DQo8L2c+DQo8L3N2Zz4NCg==)

SVG 在移动设备和桌面设备上能够得到[很好的支持](http://caniuse.com/svg-html5)，[优化工具](https://sarasoueidan.com/blog/svgo-tools/)可以大大减小 SVG 尺寸。以下两个内联 SVG 徽标看起来完全相同，但一个大约 3KB，另一个仅 2KB：



##### Data URI {#data_uri}

通过 Data URI 可以添加文件，如图像，使用以下格式将`img`元素的 src 设置为 Base64 编码的字符串进行内联：

```html
<img src="data:image/svg+xml;base64,[data]">
```

上述 HTML5 徽标的代码开头类似于下面所示：

```html
<img src="data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz4NCjwhLS0gR2VuZXJhdG9yOiB
BZG9iZSBJbGx1c3RyYXRvciAxNi4wLjAsIFNWRyBFeHBvcnQgUGx1Zy1JbiAuIFNWRyBWZXJzaW ...">
```

（完整版本长度超过 5000 字符！）

可使用拖放式工具（例如[jpillora.com/base64-encoder](https://jpillora.com/base64-encoder)）转换二进制文件，如将图像转换为 Data URI 格式。与 SVG 相似，移动设备和桌面设备浏览器都能为 Data URI[提供很好的支持](http://caniuse.com/datauri)。

##### 在 CSS 中进行内联 {#css}

Data URI 和 SVG 还可以内联到 CSS 中，移动设备和桌面设备均提供此支持。下面是两个外观完全相同的图像，在 CSS 中作为背景图片实现；一个是 Data URI，另一个是 SVG：



##### 内联的优缺点 {#_10}

图像的内联代码可能很冗长，特别是 Data URI 格式的图像，那么您为什么要使用它呢？为了减少 HTTP 请求！SVG 和 Data URI 可实现通过一个请求检索整个网页，包括图像、CSS 和 JavaScript。

缺点：

* 与来自外部`src`的图像相比，在移动设备上 Data URI 格式图像的显示速度要[慢得多](https://www.mobify.com/blog/data-uris-are-slow-on-mobile/)。
* Data URI 会显著增加 HTML 请求的大小。
* Data URI 会增加标记和工作流的复杂性。
* Data URI 格式的图像比二进制格式的图像大很多（最多大 30%），因此不会减小总下载大小。
* Data URI 无法缓存，因此必须为使用它们的每一个页面分别进行下载。
* IE 6 和 7 不支持 Data URI，IE8 仅提供有限支持。
* 对于 HTTP/2，减少资产请求的次数将使优先级下降。

由于所有格式都可以自适应，因此，您需要测试哪一种格式效果最佳。使用开发者工具衡量下载文件大小、请求的次数以及总延迟时间。对于光栅图像，Data URI 有时候非常有用，例如，如果主页只有一两张图像且这些图像没有在其他地方使用，则可使用 Data URI。如果您需要内联矢量图像，SVG 是一个比较好的选择。

## CSS 中的图像 {#css_1}

CSS`background`属性是一个用于为元素添加复杂图像的工具，功能强大，可用于轻松添加多个图像、重复图像以及执行其他操作。与媒体查询结合使用时，background 属性变得更加强大，能够根据屏幕分辨率、视口大小等有条件地加载图像。

### TL;DR {#tldr_hide-from-toc_1}

* 使用最适合显示屏特性的图像，考虑屏幕尺寸、设备分辨率及页面布局。
* 结合使用媒体查询和`min-resolution`及`-webkit-min-device-pixel-ratio`针对高分辨率显示屏更改 CSS 中的`background-image`属性。
* 除了标记中的 1x 图像外，使用 srcset 提供高分辨率图像。
* 使用 JavaScript 图像替换技术时或为低分辨率设备提供高度压缩的高分辨率图像时，请考虑性能成本。

### 利用媒体查询有条件地加载图像或提供艺术指导 {#_11}

媒体查询不仅影响页面布局，还可以用于有条件地加载图像，或是基于视口宽度提供艺术指导。

以下面的示例为例，在较小屏幕上，只会`small.png`并将其应用于内容`div`，而在较大屏幕上，会将`background-image: url(body.png)`应用于 body，`background-image: url(large.png)`应用于内容`div`。

```css
.example {
  height: 400px;
  background-image: url(small.png);
  background-repeat: no-repeat;
  background-size: contain;
  background-position-x: center;
}

@media (min-width: 500px) {
  body {
    background-image: url(body.png);
  }
  .example {
    background-image: url(large.png);
  }
}
```

[试一下](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/media/conditional-mq.html)

### 使用 image-set 提供高分辨率图像 {#image-set}

CSS 中的`image-set()`函数增强了`background`属性的行为，使它能够轻松地针对不同设备特性提供多种图像文件。这样，浏览器就可以根据设备特性选择最佳图像，例如，在 2x 显示屏上使用 2x 图像，或在网络带宽有限时在 2x 的设备上使用 1x 图像。

```css
background-image: image-set(
  url(icon1x.jpg) 1x,
  url(icon2x.jpg) 2x
);
```

除了加载合适的图像外，浏览器也会相应地调整其大小。 换句话说，浏览器假设 2x 图像是 1x 图像的两倍大，因此会将 2x 图像缩小一半，最后图像在页面上看上去就一样大。

对`image-set()`的支持目前还很少，仅 Chrome 和 Safari 上通过`-webkit`供应商前缀提供支持。 对于`image-set()`不受支持的情况，应注意加入备用图像，例如：

```css
.sample {
  width: 128px;
  height: 128px;
  background-image: url(icon1x.png);
  background-image: -webkit-image-set(  
    url(icon1x.png) 1x,  
    url(icon2x.png) 2x  
  );  
  background-image: image-set(  
    url(icon1x.png) 1x,  
    url(icon2x.png) 2x  
  );
}
```

[试一下](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/media/image-set.html)

上例中，会在支持 image-set 的浏览器中加载合适的资产，否则将改为加载 1x 资产。 需要特别注意的是，支持`image-set()`的浏览器非常少，大部分浏览器会加载 1x 资产。

### 利用媒体查询提供高分辨率图像或艺术指导 {#_12}

媒体查询可根据[设备像素比](http://www.html5rocks.com/en/mobile/high-dpi/#toc-bg)创建规则，让您可以针对 2x 和 1x 显示屏分别指定不同的图像。

```css
@media (min-resolution: 2dppx),
(-webkit-min-device-pixel-ratio: 2)
{
  /* High dpi styles & resources here */
}
```

Chrome、Firefox 和 Opera 都支持标准的`(min-resolution: 2dppx)`，Safari 和 Android 浏览器则均需要不带`dppx`单位的旧版供应商前缀语法。请谨记，这些样式只有在设备与媒体查询匹配时才被加载，且必须为基础案例指定样式。 这样也能够确保当浏览器不支持分辨率特有的媒体查询时，一些内容仍可以得到渲染。

```css
.sample {
  width: 128px;
  height: 128px;
  background-image: url(icon1x.png);
}

@media (min-resolution: 2dppx), /* Standard syntax */ 
(-webkit-min-device-pixel-ratio: 2)  /* Safari & Android Browser */ 
{
  .sample {
    background-size: contain;
    background-image: url(icon2x.png);
  }
}
```

[试一下](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/media/media-query-dppx.html)

您也可以使用 min-width 语法根据视口大小显示备用图像。 此方法的好处是，如果媒体查询不匹配，则图像不会被下载。 例如，只有在浏览器宽度等于 500px 或更大时，`bg.png`才会被下载，然后应用于`body`：

```css
@media (min-width: 500px) {
  body {
    background-image: url(bg.png);
  }
}
```

## 为图标使用 SVG {#svg_1}

为页面添加图标时，尽可能使用 SVG 图标，某些情况下，请使用 unicode 字符。

### TL;DR {#tldr_hide-from-toc_2}

* 为图标使用 SVG 或 unicode，而不是光栅图像。

### 使用 unicode 替代简单图标 {#unicode}

许多字体支持各种各样的 unicode 字形，可用于替代图像。 与图像不同，unicode 字体可以缩放，不管在屏幕上的显示大小如何，显示效果都很好。

除了常见的字符集，unicode 可能包含的符号有箭头 \(←\)、数学运算符 \(√\)、几何形状 \(★\)、控制图片 \(▶\)、音符 \(♬\)、希腊字母 \(Ω\)，甚至象棋子 \(♞\)。

添加 unicode 字符的方式与指定的字符实体一样：`&#XXXX`，其中`XXXX`代表 unicode 字符编码。 例如：

```html
You're a super &#9733;
```

You're a super ★

### 使用 SVG 替代复杂图标 {#svg_2}

对于更为复杂的图标需求，SVG 图标通常要轻量、易用，并且可以通过 CSS 设置样式。 SVG 的优势比光栅图像多很多：

* 它们是矢量图形，可以无限缩放。
* 颜色、阴影、透明度以及动画等 CSS 效果非常简单明了。
* SVG 图像能够直接内联到文档中。
* 它们是有语义的。
* 它们通过适当的属性提供更佳的可访问性。

```html
With SVG icons, you can either add icons using inline SVG, like 
this checkmark:
  <svg version="1.1" xmlns="http://www.w3.org/2000/svg" 
       xmlns:xlink="http://www.w3.org/1999/xlink" 
       width="32" height="32" viewBox="0 0 32 32">
    <path d="M27 4l-15 15-7-7-5 5 12 12 20-20z" fill="#000000"></path>
  </svg>
or by using an image tag, like this credit card icon:
<img src="credit.svg">.
```

[试一下](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/media/icon-svg.html)

### 谨慎使用图标字体 {#_13}

![](https://developers.google.com/web/fundamentals/design-and-ux/responsive/img/icon-fonts.png "使用 FontAwesome 作为其图标字体的页面示例。")

[使用 FontAwesome 作为其图标字体的页面示例。](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/media/icon-font.html)

图标字体很流行，使用也方便，但与 SVG 图标相比，还是有些缺陷。

* 它们是可以无限缩放的矢量图形，但可能会做抗锯齿处理，导致图标不如预期的那样锐利。
* 有限的 CSS 样式设置。
* 要实现像素完美定位效果很难，具体取决于行高、字符间距等。
* 它们没有语义，可能很难与屏幕阅读器及其他辅助性技术结合使用。
* 除非正确设置了作用域，否则它们可能造成文件很大，却只使用了一小部分可用图标的情况。

```html
With Font Awesome, you can either add icons by using a unicode
entity, like this HTML5 logo (<span class="awesome">&#xf13b;</span>)
or by adding special classes to an &lt;i&gt; element like the CSS3
logo (<i class="fa fa-css3"></i>).
```

[试一下](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/media/icon-font.html)

免费及收费的图标字体有数百种，包括[Font Awesome](https://fortawesome.github.io/Font-Awesome/)、[Pictos](http://pictos.cc/)和[Glyphicons](https://glyphicons.com/)。

请务必在对图标的需求与额外增加的 HTTP 请求及文件大小之间取得平衡。 例如，如果只需要少量图标，或许使用图像或 image sprites 会更合适。

## 优化图像以提升性能 {#_14}

图像通常占据了下载字节的大部分，通常也占据了网页上大量的视觉空间。 因此，优化图像通常可以最大限度地减少从网站下载的字节数以及提高网站性能：浏览器需要下载的字节越少，占用客户端的带宽就越少，浏览器下载并渲染所有资产的速度就越快。

### TL;DR {#tldr_hide-from-toc_3}

* 不要只是随意地选择图像格式，要了解不同的可用格式，并使用最适合的格式。
* 在您的工作流程中引入图像优化与压缩工具，减小文件大小。
* 将常用图像放入 image sprites 中，减少 http 请求数量。
* 考虑在图像滚动进入视图后才加载图像，以缩短页面初始加载时间，减小初始页面重量。

### 选择正确的格式 {#_15}

有两种图像类型可以考虑：[矢量图像](https://en.wikipedia.org/wiki/Vector_graphics)与[光栅图像](https://en.wikipedia.org/wiki/Raster_graphics)。对于光栅图像，您还需要选择正确的压缩格式，例如：`GIF`、`PNG`、`JPG`。

**光栅图像**，如相片及其他通过单个的点或像素网格表示的图像。 光栅图像通常来自照相机或扫描仪，也可以在浏览器中借助`canvas`元素创建。 随着图像尺寸的增加，文件大小也相应地增加。 如果光栅图像放大时超过其初始尺寸，则会变得模糊，因为浏览器需要猜测如何填补缺失的像素。

**矢量图像**，如徽标和艺术线条，是由一系列的曲线、直线、形状和填充色定义的。 矢量图像使用 Adobe Illustrator 或 Inkscape 之类的程序创建，并保存为矢量格式，如[`SVG`](https://css-tricks.com/using-svg/)。由于矢量图像是建立在简单基元上的，因此，可以进行无损质量的缩放，且文件大小不变。在选择正确的格式时，务必综合考虑图像的源格式（光栅图像还是矢量图像）及内容（颜色、动画、文本等等）。没有一种格式能够适用所有图像类型，它们各有优劣。

在选择正确的格式时，先参考以下指导准则：

* 摄影图像使用`JPG`。
* 徽标和艺术线条等矢量插画及纯色图形使用`SVG`。 如果矢量插画不可用，试试`WebP`或`PNG`。
* 使用`PNG`而非`GIF`，因为前者可以提供更丰富的颜色和更好的压缩比。
* 长动画考虑使用`<video>`，它能提供更好的图像质量，还允许用户控制回放。

### 减小文件大小 {#_16}

可通过在保存图像后对其进行“后处理”来大幅减小图片文件的大小。 图像压缩工具有很多种 – 有损与无损压缩、在线压缩、GUI 压缩及命令行压缩工具。 在可能的情况下，最好自动进行图像优化，这样在您的工作流程中，它就是一等公民。

有多种工具可用来对`JPG`和`PNG`文件做进一步的无损压缩，而不会对图像质量造成影响。 对于`JPG`，试试[jpegtran](http://jpegclub.org/)或[jpegoptim](http://freshmeat.net/projects/jpegoptim/)（只在 Linux 上可用；通过 –strip-all 选项运行）。对于`PNG`，试试[OptiPNG](http://optipng.sourceforge.net/)或[PNGOUT](http://www.advsys.net/ken/util/pngout.htm)。

### 使用 image sprites {#image_sprites}

![](https://developers.google.com/web/fundamentals/design-and-ux/responsive/img/sprite-sheet.png "示例中使用的 image sprites 表")

CSS 精灵是一项技术，可将许多图像合并到一个“精灵表”图像中。 然后，通过指定元素背景图像（精灵表）以及指定用于显示正确部分的位移，可以使用各个图像。

```css
.sprite-sheet {
  background-image: url(sprite-sheet.png);
  width: 40px;
  height: 25px;
}

.google-logo {
  width: 125px;
  height: 45px;
  background-position: -190px -170px;
}

.gmail {
  background-position: -150px -210px; 
}

.maps {
  height: 40px;
  background-position: -120px -165px;
}
```

[试一下](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/media//image-sprite.html)

精灵化的好处是，减少了读取多张图像所需的下载数量，而且能保持启用缓存。

### 考虑延缓加载 {#_17}

在首屏包含许多图像的长页面中，延缓加载能极大提高加载速度，可以按需加载图像，或者在主要内容加载和渲染完成之后加载图像。除了性能上的提升外，使用延缓加载能产生无限滚动的体验。

在创建无限滚动的页面时，需要注意，由于内容是可见后才加载的，因此搜索引擎可能永远看不到该内容。 此外，用户想查看页脚信息也不可能了，因为总是有新内容被加载。

## 完全避免使用图像 {#_18}

有时，最好的图像并不真是图像。如果可以，请使用浏览器的原生功能实现相同或类似的效果。浏览器可以生成之前需要图像才能生成的视觉效果。 这意味着，浏览器不再需要下载单独的图像文件，从而避开糟糕的图像缩放问题。 图标可以使用 unicode 或特殊的图标字体来呈现。

### 将文本放在标记内，而不要嵌入图像内 {#_19}

如果可以，文本要单独放置，而不要嵌入图像内。例如将图像用作标题，或直接在图像中放入电话号码或地址等联系信息会使用户无法复制和粘贴信息；屏幕阅读器也无法识别这些信息，而且这些内容无法自动调整。请改为将文本放在标记中，并在必要时使用网络字体以获得所需的样式。

### 使用 CSS 替换图像 {#css_2}

现代浏览器可以使用 CSS 功能创建之前需要图像才能实现的样式。 例如，复杂的渐变可以使用`background`属性创建，阴影可以使用`box-shadow`创建，而圆角可以使用`border-radius`属性添加。

Lorem ipsum dolor sit amet, consectetur adipiscing elit.Pellentesque sit amet augue eu magna scelerisque porta ut ut dolor.Nullam placerat egestas nisl sed sollicitudin.Fusce placerat, ipsum ac vestibulum porta, purus dolor mollis nunc, pharetra vehicula nulla nunc quis elit. Duis ornare fringilla dui non vehicula.In hac habitasse platea dictumst.Donec ipsum lectus, hendrerit malesuada sapien eget, venenatis tempus purus.

```html
<style>
  div#noImage {
    color: white;
    border-radius: 5px;
    box-shadow: 5px 5px 4px 0 rgba(9,130,154,0.2);
    background: linear-gradient(rgba(9, 130, 154, 1), rgba(9, 130, 154, 0.5));
  }
</style>
```

请注意，使用这些技巧需要呈现周期性，这一点在移动设备上非常显著。 如果过度使用，您会失去您可能已经获得的好处，而且可能会降低页面性能。



