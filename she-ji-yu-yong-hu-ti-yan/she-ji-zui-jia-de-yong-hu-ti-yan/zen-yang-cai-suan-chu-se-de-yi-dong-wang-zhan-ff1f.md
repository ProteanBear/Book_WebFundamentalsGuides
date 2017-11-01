# 怎样才算出色的移动网站？

> 作者：PeteLePage



Google 和 AnswerLab 执行了一项[调查研究](https://www.google.com/think/multiscreen/whitepaper-sitedesign.html?utm_source=web-fundamentals&utm_term=chrome&utm_content=ux-landing&utm_campaign=web-fundamentals)来回答这一问题。

> 移动用户具有很强的目标导向。他们期望能够根据自身情况立即获得所需内容。

这项研究是通过与美国参与者进行长达 119 小时的亲自易用性实验来完成的。 研究要求参与者在各类移动网站上执行关键任务。iOS 和 Android 用户都包括在内，用户在其自己的手机上测试网站。对于每个网站，研究都要求参与者在完成侧重于转化的任务（如购物或预订）时明确表达自己的想法。

这项研究发现了 25 个移动网站设计原则，并将它们分成五个类别。

## 首页和网站导航 {#_1}

成功：让您的移动首页侧重于将用户与他们要寻找的内容联系起来。

### 让吸引注意力的元素前置居中 {#_2}

通过[菜单](https://developers.google.com/web/fundamentals/design-and-ux/responsive/)或“首屏线以下空间”（网页中不向下滚动便无法看到的部分）提供二级任务。

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/hpnav-cta-good.png)

**宜**

：将用户所有最常见的任务安排在便于访问的位置。

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/hpnav-cta-bad.png)

**忌**

：使用“了解详情”之类含糊的吸引注意力的元素浪费宝贵的首屏空间。

### 让菜单保持简短和亲切 {#_3}

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/hpnav-menus-good.png)

**宜**

：让菜单保持简短和亲切。

移动用户没有耐心通过浏览冗长的选项列表查找自己需要的内容。 请重新组织您的菜单，在不牺牲易用性的情况下尽可能减少菜单项。

### 简化返回首页的操作 {#_4}

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/hpnav-hp-good.png)

**宜**

：简化返回首页的操作。

用户期望在其点按移动页面左上角的徽标时能够返回首页，如果未提供该徽标或者徽标不起作用，会令他们感到失望。

### 别让推广信息喧宾夺主 {#_5}

大型应用安装插页广告（例如，隐藏内容并提示用户安装应用的整页推广信息）令用户反感，让他们难以执行任务。除了让用户反感外，使用应用安装插页广告的网站还无法通过[Google 移动易用性测试](https://search.google.com/test/mobile-friendly)，这可能对其搜索排名产生不良影响。

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/hpnav-promo-good.png)

**宜**

：推广信息应能轻松关闭，并且不应让用户在使用网站时分心。

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/hpnav-promo-bad.png)

**忌**

：插页广告（有时称作关门广告）常常令用户反感，让用户在使用网站时平添烦恼。

## 网站搜索 {#_6}

成功：帮助移动用户找到其正在急切寻找的内容。

### 让网站搜索可见 {#_7}

寻找信息的用户通常求助于搜索，因此搜索字段应是他们在您的页面上率先看到的内容。 不要将搜索框隐藏在菜单中。

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/ss-search-good.jpg)

**宜**

：让搜索可见

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/ss-search-bad.jpg)

**忌**

：将搜索隐藏在溢出菜单中

### 确保网站搜索结果相关 {#_8}

别让用户为了查找要寻找的内容而浏览多个页面的结果。 通过自动完成查询、更正错误拼写和提供相关查询建议简化用户的搜索操作。 为免于重复劳动，可以考虑使用[Google 自定义搜索](https://cse.google.com/cse/)之类的稳健产品。

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/ss-relevant-good.png)

**宜**

：Macy's 只返回儿童商品。

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/ss-relevant-bad.png)

**忌**

：返回任何包含儿童一词的商品的搜索结果。

### 实现过滤条件来缩小结果范围 {#_9}

研究参与者依靠[过滤条件](https://developers.google.com/custom-search/docs/structured_search)查找他们要寻找的内容，他们会放弃不提供有效过滤条件的网站。对搜索结果应用过滤条件，通过显示应用特定过滤条件时将会返回多少结果来帮助用户。

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/ss-filters-good.jpg)

**宜**

：为过滤提供便利。

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/ss-filters-bad.jpg)

**忌**

：隐藏过滤功能。

### 引导用户获得更相关的网站搜索结果 {#_10}

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/ss-guide-good.png "Zappos 通过询问用户的寻找内容来对其进行引导。")

**宜**

：通过沿正确方向引导用户来帮助他们查找其要寻找的内容。

对于具有多样化客户群的网站，请先提出几个问题，然后再呈现搜索框，并利用客户的回答作为搜索查询过滤条件来确保用户获得来自最相关客户群的结果。

## 商务和转化 {#_11}

成功：了解客户之旅，让用户根据自身情况进行转化。

### 让用户先探索、后表态 {#_12}

研究参与者对那些要求先行注册才能查看内容的网站感到失望，尤其是在他们不熟悉网站品牌的情况下。 尽管对您的业务而言客户信息不可或缺，但过早索要可能导致注册量减少。

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/cc-gates-good.png)

**宜**

：允许用户在不登录的情况下浏览网站。

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/cc-gates-bad.png)

**忌**

：在网站上过早提出登录或注册要求。

### 让用户以访客身份购买 {#_13}

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/cc-purchase-guest-good.png)

**宜**

：允许用户使用访客帐号购物。

研究参与者对访客结账的看法是“方便”、“简单”、“轻松”和“快速”。 用户对购物时强制他们注册帐号的网站感到恼火，尤其是在注册帐号的好处并不明确的情况下。

### 利用现有信息最大限度提高便利性 {#_14}

为注册用户记忆并[预填首选项](https://developers.google.com/web/fundamentals/design-and-ux/input/forms/#label-and-name-inputs-properly)。为新用户提供熟悉的第三方结账服务。

### 为复杂任务使用点击呼叫按钮 {#_15}

在具备呼叫能力的设备上，[点击呼叫链接](https://developers.google.com/web/fundamentals/native-hardware/click-to-call/)可让用户通过简单地触按链接来拨打电话。在大多数移动设备上，用户会在拨号前收到确认，或者为用户提供一个菜单，询问用户应如何处理号码。

### 为在其他设备上完成任务提供便利 {#_16}

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/cc-other-device-good.png)

**宜**

：让用户能够方便地继续在其他设备上浏览或购物。

用户经常想在其他设备上完成任务。例如，他们可能想在更大的屏幕上查看某个项目。 或者他们可能工作繁忙，需要稍后完成任务。 通过让用户能够[在社交网络上分享项目](https://developers.google.com/web/fundamentals/discovery-and-monetization/social-discovery/)，或允许用户直接在网站内通过电子邮件向自己发送链接，为这些客户之旅提供支持。

## 表单输入 {#_17}

成功：通过易用型表单提供顺畅的无缝式转化体验。

### 精简信息输入 {#_18}

用户按回车键时自动前进到下一字段。一般而言，用户必须执行的触按越少，体验越佳。

### 选择最简单的输入 {#_19}

为每个情境使用最[合适的输入类型](https://developers.google.com/web/fundamentals/design-and-ux/input/forms/choose-the-best-input-type)。 使用[`datalist`](https://developers.google.com/web/fundamentals/design-and-ux/input/forms/choose-the-best-input-type#offer-suggestions-during-input-with-datalist)之类的元素为字段提供建议值。

### 为日期选择提供可视化日历 {#_20}

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/forms-calendar-good.png)

**宜**

：尽可能使用日历小部件。

明确标示开始日期和结束日期。用户应不必单纯为了安排日期而离开网站去查看日历应用。

### 通过标示和实时验证最大限度减少表单错误 {#_21}

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/forms-multipart-good.png)

**宜**

：尽可能预填充内容。

正确标示输入并实时验证输入。

### 设计高效的表单 {#_22}

充分利用[自动填充](https://developers.google.com/web/fundamentals/design-and-ux/input/forms/#label-and-name-inputs-properly#use-metadata-to-enable-auto-complete)，让用户能借助预填充的数据轻松填写表单。 使用已知信息预先填充字段。 例如，在检索收货和账单地址时，尝试使用[`requestAutocomplete`](https://developers.google.com/web/fundamentals/design-and-ux/input/forms/use-request-auto-complete)，或让用户能够将其收货地址复制到其账单地址（反之亦然）。

## 易用性和机型 {#_23}

成功：通过可增强移动用户使用体验的微小改进来取悦他们。

### 对您的整个网站进行移动优化 {#_24}

使用可随用户设备的尺寸和能力而变化的[自适应布局](https://developers.google.com/web/fundamentals/design-and-ux/responsive/)。 研究参与者发现混合使用桌面和移动优化页面的网站甚至比单纯使用桌面页面的网站还要难以使用。

### 别让用户进行捏拉缩放 {#_25}

用户对垂直滚动网站感到顺手，水平滚动则不然。 避免使用大型、固定宽度的元素。利用[CSS 媒体查询](https://developers.google.com/web/fundamentals/design-and-ux/responsive/#use-css-media-queries-for-responsiveness)为不同屏幕应用不同的样式。

不要创建只能在特定[视口宽度](https://developers.google.com/web/fundamentals/design-and-ux/responsive/#set-the-viewport)下正常显示的内容。强制用户水平滚动的网站无法通过[Google 移动易用性测试](https://search.google.com/test/mobile-friendly)，可能对其搜索排名产生不良影响。

### 让产品图像可扩展 {#_26}

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/sw-make-images-expandable-good.png)

**宜**

：让产品图像可扩展并便于查看细节。

零售客户期望网站允许其[查看产品的高分辨率特写](https://developers.google.com/web/fundamentals/design-and-ux/media/images#make-product-images-expandable)。研究参与者对无法查看所购买的产品感到失望。

### 告诉用户哪个方向效果最好 {#_27}

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/us-orientation.jpg)

**宜**

：告诉用户哪个方向效果最好。

研究参与者往往一直使用同一屏幕方向，直至系统提示其进行切换。 同时采用横向和纵向设计，或鼓励用户切换至最佳方向。 确保即使用户忽略切换方向的建议，仍可完成您的重要行为召唤。

### 将您的用户留在单一浏览器窗口内 {#_28}

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/sw-single-browser-good.png)

**宜**

：Macy's 通过在网站上提供优惠券将用户留在其网站上。

用户可能难以在窗口之间切换，并且可能找不到返回网站的路径。 避免启动新窗口的行为召唤。识别任何可能导致用户离开您的网站的流程，并提供相应功能将他们留在您的网站上。例如，如果您接受优惠券，请直接在网站上提供，而不要让用户被迫前往其他网站寻找优惠。

### 避免使用“完整网站”标示 {#_29}

当研究参与者看到用于切换“完整网站”（即桌面网站）和“移动网站”的选项时，会认为移动网站缺少内容而改为选择“完整”网站，这会将他们导向桌面网站。

### 明确您为何需要用户的位置 {#_30}

用户应始终明了您为何索要其[位置](https://developers.google.com/web/fundamentals/native-hardware/user-location/)。 研究参与者试图预订其他城市的酒店，而旅行网站却在检测到其位置后改为提供其所在城市的酒店，这令他们感到困惑。默认情况下将位置字段留空，让用户通过“Find Near Me”之类的明确行为召唤选择填充这些字段。

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/sw-navigation-good.png)

**宜**

：始终在手势操作时请求获取用户的位置。

![](https://developers.google.com/web/fundamentals/design-and-ux/principles/images/sw-navigation-bad.png)

**忌**

：在网站加载首页时立即请求提供位置会导致不好的用户体验。



