# 自适应网页设计模式

> 作者：Pete LePage

自适应网页设计模式正在快速发展，但只有少数几种成熟的模式可以跨桌面设备和移动设备流畅运行。

自适应网页所用的大部分布局可以归类为五种模式之一：mostly fluid、column drop、layout shifter、tiny tweaks 和 off canvas。在某些情况下，页面可能使用组合模式，例如 column drop 和 off canvas。这些模式最初由[Luke Wroblewski](http://www.lukew.com/ff/entry.asp?1514)确定，为任何自适应页面提供了可靠的起点。

### 模式 {#_1}

出于简单易懂上的考虑，下面每个示例都是使用[`flexbox`](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Flexible_boxes)通过真实标记创建的，一般在主要容器`div`内容纳了三个内容`div`。每个示例都先从最小的视图开始，然后在必要时加上断点。 现代浏览器[能很好地支持 flexbox 布局模式](http://caniuse.com/#search=flexbox)，尽管可能仍需要供应商前缀才能实现最佳支持。

## Mostly Fluid {#mostly_fluid}

Mostly fluid 模式主要由流动网格组成。在较大和中等屏幕上，它通常保持相同大小，只在更宽的屏幕上调整边距。

在较小屏幕上，流动网格使主内容自动重排，同时各列垂直排列。 此模式的一个主要优点是，在小屏幕和大屏幕之间通常只需要一个视图断点。

![](https://developers.google.com/web/fundamentals/design-and-ux/responsive/imgs/mostly-fluid.svg)[试一下](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/responsive/mostly-fluid.html)

在最小的视图中，每个内容`div`都垂直排列。在屏幕宽度达到 600px 时，主要内容`div`保持`width: 100%`，而辅助`div`在主要`div`下面显示为两列。宽度超过 800px 时，容器`div`变为固定宽度并在屏幕上居中。

使用此模式的网站包括：

* [A List Apart](http://mediaqueri.es/ala/)
* [Media Queries](http://mediaqueri.es/)
* [SimpleBits](http://simplebits.com/)

```css
.container {
  display: -webkit-flex;
  display: flex;
  -webkit-flex-flow: row wrap;
  flex-flow: row wrap;
}

.c1, .c2, .c3, .c4, .c5 {
  width: 100%;
}

@media (min-width: 600px) {
  .c2, .c3, .c4, .c5 {
    width: 50%;
  }
}

@media (min-width: 800px) {
  .c1 {
    width: 60%;
  }
  .c2 {
    width: 40%;
  }
  .c3, .c4, .c5 {
    width: 33.33%;
  }
}

@media (min-width: 800px) {
  .container {
    width: 800px;
    margin-left: auto;
    margin-right: auto;
  }
}
```

## Column drop {#column_drop}

对于全宽度的多列布局，当窗口宽度太窄不能容纳内容时，Column drop 就将各列垂直排列。

最终使所有列都垂直排列。为此布局模式选择视图断点时要依据内容，并随不同设计而改变。

![](https://developers.google.com/web/fundamentals/design-and-ux/responsive/imgs/column-drop.svg)[试一下](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/responsive/column-drop.html)

与 Mostly fluid 示例类似，内容在最小视图中垂直排列，但当屏幕宽度超过 600px 时，主要内容和辅助内容`div`占用屏幕的全宽度。`div`的顺序是使用 CSS 属性的顺序进行设置的。 在 800px 时，使用全屏宽度来显示全部三个内容`div`。

使用此模式的网站包括：

* [Modernizr](https://modernizr.com/)
* [Wee Nudge](http://weenudge.com/)

```css
.container {
  display: -webkit-flex;
  display: flex;
  -webkit-flex-flow: row wrap;
  flex-flow: row wrap;
}

.c1, .c2, .c3 {
  width: 100%;
}

@media (min-width: 600px) {
  .c1 {
    width: 60%;
    -webkit-order: 2;
    order: 2;
  }

  .c2 {
    width: 40%;
    -webkit-order: 1;
    order: 1;
  }

  .c3 {
    width: 100%;
    -webkit-order: 3;
    order: 3;
  }
}


@media (min-width: 800px) {
  .c2 {
    width: 20%;
  }

  .c3 {
    width: 20%;
  }
}
```

## Layout shifter {#layout_shifter}

Layout shifter 模式是自适应性最强的模式，在多种屏幕宽度上采用多个断点。

此布局的关键是内容移动的方式，而不是自动重排并放到其他列下面。 由于每个主要视图断点之间的显著差异，其维护更复杂，并且可能涉及元素内的更改，而不只是总体内容布局的更改。

![](https://developers.google.com/web/fundamentals/design-and-ux/responsive/imgs/layout-shifter.svg)[试一下](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/responsive/layout-shifter.html)

这个简化示例显示了 Layout shifter 模式，在较小的屏幕上内容垂直排列，但在屏幕变大时就会发生显著变化，左侧一个`div`，右侧排列两个`div`。

使用此模式的网站包括：

* [Food Sense](http://foodsense.is/)
* [Seminal 自适应设计示例](http://alistapart.com/d/responsive-web-design/ex/ex-site-FINAL.html)
* [Andersson-Wise Architects](http://www.anderssonwise.com/)

```css
.container {
  display: -webkit-flex;
  display: flex;
  -webkit-flex-flow: row wrap;
  flex-flow: row wrap;
}

.c1, .c2, .c3, .c4 {
  width: 100%;
}

@media (min-width: 600px) {
  .c1 {
    width: 25%;
  }

  .c4 {
    width: 75%;
  }

}

@media (min-width: 800px) {
  .container {
    width: 800px;
    margin-left: auto;
    margin-right: auto;
  }
}
```

## Tiny tweaks {#tiny_tweaks}

Tiny tweaks 只对布局进行细微的更改，例如调整字体大小、调整图像大小或对内容进行极其细微的移动。

它在单列布局上表现很好，例如单页面线性网站和文本为主的文章。

![](https://developers.google.com/web/fundamentals/design-and-ux/responsive/imgs/tiny-tweaks.svg)[试一下](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/responsive/tiny-tweaks.html)

顾名思义，使用本示例时，屏幕大小改变时发生的变化不大。当屏幕宽度增加时，字体大小和内边距也变大。

使用此模式的网站包括：

* [Ginger Whale](http://gingerwhale.com/)
* [Future Friendly](http://futurefriendlyweb.com/)

```css
.c1 {
  padding: 10px;
  width: 100%;
}

@media (min-width: 500px) {
  .c1 {
    padding: 20px;
    font-size: 1.5em;
  }
}

@media (min-width: 800px) {
  .c1 {
    padding: 40px;
    font-size: 2em;
  }
}
```

## Off canvas {#off_canvas}

Off canvas 模式不是垂直排列内容，而是将不常用的内容（可能是导航或应用的菜单）放在屏幕之外，只在屏幕足够大时才显示。在较小屏幕上，只需点击就能显示内容。

![](https://developers.google.com/web/fundamentals/design-and-ux/responsive/imgs/off-canvas.svg)[试一下](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/responsive/off-canvas.html)

此示例不是垂直排列内容，而是使用`transform: translate(-250px, 0)`将两个内容`div`隐藏在屏幕之外。然后通过给元素添加 open 类来使其可见，使用 JavaScript 来显示这些 div。 当屏幕变宽时，从元素中去掉屏幕外的定位，并且让它显示在可见视口内。

注意，在本示例中，Safari for iOS 6 和 Android 浏览器不支持`flexbox`的`flex-flow: row nowrap`功能，因此我们必须回退到绝对定位。

使用此模式的网站包括：

* [HTML5Rocks 文章](http://www.html5rocks.com/en/tutorials/developertools/async-call-stack/)
* [Google Nexus](https://www.google.com/nexus/)
* [Facebook 的移动网站](https://m.facebook.com/)

```css
body {
  overflow-x: hidden;
}

.container {
  display: block;
}

.c1, .c3 {
  position: absolute;
  width: 250px;
  height: 100%;

  /*
    This is a trick to improve performance on newer versions of Chrome
    #perfmatters
  */
  -webkit-backface-visibility: hidden;
  backface-visibility: hidden; 

  -webkit-transition: -webkit-transform 0.4s ease-out;
  transition: transform 0.4s ease-out;

  z-index: 1;
}

.c1 {
  /*
  Using translate3d as a trick to improve performance on older versions of Chrome
  See: http://aerotwist.com/blog/on-translate3d-and-layer-creation-hacks/
  #perfmatters
  */
  -webkit-transform: translate(-250px,0);
  transform: translate(-250px,0);
}

.c2 {
  width: 100%;
  position: absolute;
}

.c3 {
  left: 100%;
}

.c1.open {
  -webkit-transform: translate(0,0);
  transform: translate(0,0);
}

.c3.open {
  -webkit-transform: translate(-250px,0);
  transform: translate(-250px,0);
}

@media (min-width: 500px) {
  /* If the screen is wider then 500px, use Flexbox */
  .container {
    display: -webkit-flex;
    display: flex;
    -webkit-flex-flow: row nowrap;
    flex-flow: row nowrap;
  }
  .c1 {
    position: relative;
    -webkit-transition: none 0s ease-out;
    transition: none 0s ease-out;
    -webkit-transform: translate(0,0);
    transform: translate(0,0);
  }
  .c2 {
    position: static;
  }
}

@media (min-width: 800px) {
  body {
    overflow-x: auto;
  }
  .c3 {
    position: relative;
    left: auto;
    -webkit-transition: none 0s ease-out;
    transition: none 0s ease-out;
    -webkit-transform: translate(0,0);
    transform: translate(0,0);
  }
}
```



