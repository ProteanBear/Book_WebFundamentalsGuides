# Payment Request API：集成指南

> 作者：Eiji Kitamura、Dave Gash、Zach Koch

Dogfood：`PaymentRequest`仍处于开发阶段。虽然我们认为其稳定性足以满足实现的要求，但可能仍需作出改动。 我们会持续更新本页，以时刻反映 API 的最新状况（[M56 变更](https://docs.google.com/document/d/1I8ha1ySrPWhx80EB4CVPmThkD4ILFM017AfOA5gEFg4/edit?hl=zh-cn)）。在此期间，为让您免于受到可能不具有向后兼容性的 API 变更的影响，我们提供了可嵌入网站的[shim](https://storage.googleapis.com/prshim/v1/payment-shim.js)。这个 shim 可平息两个主流 Chrome 版本的任何 API 差异。

在线购物非常方便，但通常存在令人沮丧的体验，在移动设备上购物尤其如此。虽然移动流量在不断增长，但移动购物转化率仅占所有已完成购物活动的三分之一。换言之，移动设备用户的购物放弃率是桌面设备用户的两倍。为何？

![](https://developers.google.com/web/fundamentals/payments/images/1_why_users_abandon.png?hl=zh-cn)

_用户放弃移动设备购物单的原因_

在线购物单需要大量用户操作、难以使用，加载和刷新缓慢，并且需要多个步骤才能完成。这是因为在线支付的两大要素（即安全和便利）之间通常互为矛盾，左支右绌。

导致用户放弃交易的大部分问题都均与购物单有着直接关系。每个应用或网站均有自己的数据输入和验证流程，用户通常发现，在每个应用的购物点他们都需要输入相同的信息。此外，应用开发者力求创建支持多种独特支付方式的购物流程；即使是支付方式要求的细小差异也可能导致表单填写和提交过程变得相当复杂。

任何能或多或少改进这些问题的系统都值得提倡。我们已经尝试借助[自动填充](https://developers.google.com/web/updates/2015/06/checkout-faster-with-autofill?hl=zh-cn)功能来帮助解决问题，但我们现在有了更全面的解决方案。

## Payment Request API 简介 {#introducing}

Payment Request API 是一个旨在_消灭结账表单_的系统。它显著改进了购物流程期间的用户工作流，为用户提供更一致的体验，并让电商公司能够轻松地利用各种完全不同的支付方式。Payment Request API 不是一种新的支付方式，也不与支付处理机构直接整合；它是一种旨在实现以下目标的处理层：

* 让浏览器充当商家、用户和支付处理机构的中介
* 尽可能标准化支付通信流程
* 为不同的安全支付方式提供无缝支持
* 适用于任何移动或非移动浏览器、设备或平台

Payment Request API 是一种开放式的跨浏览器标准，可以取代传统的结账流程，让商家能够在单个 API 调用中请求和接受任何付款。Payment Request API 允许网页在用户提供输入时与 User Agent 交换信息，然后核准或拒绝支付请求。

最重要的是，浏览器起到中介作用，为实现快速结账所需的全部信息均能储存在浏览器中，因此用户只需点击一次便可确认支付。

### 支付交易流程 {#transaction-process}

利用 Payment Request API，可同时为用户和商家打造尽可能无缝的交易流程。

![](https://developers.google.com/web/fundamentals/payments/images/4_the_payment_transaction_process.png?hl=zh-cn)

_支付交易流程_

流程一开始，商家网站首先创建新的`PaymentRequest`，并将购买所需的全部信息传递给浏览器：应支付的金额、期望使用的货币，以及网站接受的支付方式。浏览器确定网站所接受的支付方式和用户在目标设备上安装的支付方式之间的兼容性。

![](https://developers.google.com/web/fundamentals/payments/images/5_9_payment_request_ui.png?hl=zh-cn)

支付请求界面

然后，浏览器向用户显示支付 UI，用户选择支付方式并授权执行交易。支付方式可以简单直接，比如使用浏览器已存储的信用卡，也可能冷僻难解，比如使用专门针对网站支付开发的第三方应用（此功能即将实现）。用户授权执行交易后，所有必要的支付详细信息都将直接发回网站。例如，对于信用卡支付，网站将返回卡号、持卡人姓名、有效期和银行卡验证码 \(CVC\)。

Payment Request 也可以扩展为返回额外信息，例如收货地址和选项、付款人电子邮件和电话等。这样，无需要向用户显示结账表单，您即可获得完成支付所需的全部信息。

新流程有三重好处：从用户角度看，请求、授权、支付和结果等以往所有冗长的交互现在可一步完成；从网站角度看，只需调用一次 JavaScript API；从支付方式角度看，流程没有发生任何变化。

## 使用 Payment Request API {#using}

### 加载 Payment Request API shim {#payment_request_api_shim}

为减轻追赶这一现行标准 API 的压力，我们强烈建议在代码的`<head>`部分添加此 shim。 此 shim 将随 API 的变化而更新，并会尽力让代码能够至少在 Chrome 的 2 个主要版本上正常运行。

```
<
script
src
=
"https://storage.googleapis.com/prshim/v1/payment-shim.js"
>


```

### 创建 PaymentRequest {#create-paymentrequest}

创建 PaymentRequest 的第一步是通过调用[`PaymentRequest`](https://www.w3.org/TR/payment-request/#paymentrequest-constructor)构造函数创建一个[`PaymentRequest`](https://www.w3.org/TR/payment-request/#paymentrequest-interface)对象。此步骤通常（并非总是）与用户发起的、表示其想要执行购买的操作相关。对象使用包含所需数据的参数构造。

```
var
 request 
=
new
PaymentRequest
(


  methodData
,
// required payment method data


  details
,
// required information about transaction


  options     
// optional parameter for things like shipping, etc.


);


```

_PaymentRequest 构造函数_

#### methodData 参数 {#methoddata-parameter}

`methodData`参数包含支持的支付方式列表，以及有关支付方式的额外信息（如相关）。此序列含有`PaymentMethodData`字典，其中包括与应用预期接受的支付方式相关的标准标识符，以及任何支付方式的特定数据。如需了解更多详情，请参见[Payment Request API 架构](https://w3c.github.io/browser-payment-api/specs/architecture.html)。

目前，`PaymentRequest`在 Chrome 中仅支持以下标准信用卡：“`amex`”、“`diners`”、“`discover`”、“`jcb`”、“`maestro`”、“`mastercard`”、“`unionpay`”和“`visa`”。

```
var
 methodData 
=
[


{


    supportedMethods
:
[
"visa"
,
"mastercard"
]


}


]


```

_支付方式和数据_

#### details 参数 {#details-parameter}

`details`参数包含有关交易的信息。有两个主要元素：total，反映要支付的总额和要使用的货币；可选的`displayItems`集，显示最终金额包含哪些分项。此参数并非用作明细项目列表，而是订单主要组成部分的摘要：总价、折扣、税、运费等。

![](https://developers.google.com/web/fundamentals/payments/images/6_order_summary.png?hl=zh-cn)

支付请求界面

需要注意的是，Payment Request API 不执行计算。也就是说，它不会也不能确保显示的分项总和等于应付总金额。这些计算由开发者负责。因此，您应始终确保分项之和与总金额一致。此外，`PaymentRequest`不支持退款，因此金额应始终为正（但单独的分项可以为负，比如折扣）。

浏览器将根据您的定义渲染标签，并根据用户的语言区域自动应用正确的货币格式。请注意，应使用与内容相同的语言渲染标签。

```
var
 details 
=
{


  displayItems
:
[


{


      label
:
"Original donation amount"
,


      amount
:
{
 currency
:
"USD"
,
 value 
:
"65.00"
},
// US$65.00


},


{


      label
:
"Friends and family discount"
,


      amount
:
{
 currency
:
"USD"
,
 value 
:
"-10.00"
},
// -US$10.00


      pending
:
true
// The price is not determined yet


}


],


  total
:
{


    label
:
"Total"
,


    amount
:
{
 currency
:
"USD"
,
 value 
:
"55.00"
},
// US$55.00


}


}


```

_交易详情_

`pending`常用于显示运费或税额等取决于收货地址或收货选项的项目。Chrome 会在 UI 中指示支付请求的待定字段。

`details`中所使用的重复值或计算值可指定为字符串字面量或各字符串变量。

```
var
 currency 
=
"USD"
;


var
 amount 
=
"65.00"
;


var
 discount 
=
"-10.00"
;


var
 total 
=
"55.00"
;


```

_PaymentRequest 变量_

### 显示 PaymentRequest {#display-paymentrequest}

![](https://developers.google.com/web/fundamentals/payments/images/7_display_payment_request.png?hl=zh-cn)

支付请求界面

通过调用[`show()`](https://www.w3.org/TR/payment-request/#show)方法激活`PaymentRequest`界面。此方法会调用一个原生 UI，让用户检查购物详情、添加或更改信息并最终进行支付。用户接受或拒绝支付请求时，将返回可解析[`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)（带有其`then()`方法和回调函数）。

```
request
.
show
().
then
(
function
(
paymentResponse
)
{


// Process paymentResponse here


  paymentResponse
.
complete
(
"success"
);


}).
catch
(
function
(
err
)
{


  console
.
error
(
"Uh oh, something bad happened"
,
 err
.
message
);


});


```

_PaymentRequest show 方法_

### 取消 PaymentRequest {#abort-paymentrequest}

您可以通过调用[`abort()`](https://www.w3.org/TR/payment-request/#abort)方法主动取消`PaymentRequest`。这在购物会话超时或交易期间购物车中的商品售罄时特别有用。

如果应用需要在调用`show()`方法之后、promise 尚未解析之前（例如，如果商品已下架，或者用户没有在指定时间内确认订单）取消支付请求，则可使用此方法。

如果您取消请求，将需要创建一个新的`PaymentRequest`实例，才能再次调用`show()`。

```
var
 paymentTimeout 
=
 window
.
setTimeout
(
function
()
{


  window
.
clearTimeout
(
paymentTimeout
);


  request
.
abort
().
then
(
function
()
{


    console
.
log
(
'Payment timed out after 20 minutes.'
);


}).
catch
(
function
()
{


    console
.
log
(
'Unable to abort.'
);


});


},
20
*
60
*
1000
);
/* 20 minutes */


```

_PaymentRequest abort 方法_



