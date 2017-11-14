# Deep Dive into the Payment Request API

> 作者：Matt Gaunt

#  Deep Dive into the Payment Request API

![](https://developers.google.com/web/images/contributors/mattgaunt.jpg?hl=zh-cn "Matt Gaunt")

**By**

[MattGaunt](../../resources/contributors/index-hl=zh-cn.html#mattgaunt)

Matt is a contributor to Web

**Fundamentals**

![](https://developers.google.com/web/fundamentals/payments/images/deep-dive/pr-top.png?hl=zh-cn "Payment request UI on desktop and mobile Chrome.")

Payment request UI on desktop and mobile Chrome.

In this guide we'll explore the ins and outs of the Payment Request API, looking at how our input affects the payment request UI, how we can request information from the user \(like name and phone number\), and how the final payment and user information is passed to your site.

If you are unsure of what the Payment Request API is or why it's useful, check out the [introduction article](index-hl=zh-cn.html).

One thing to keep in the back of your mind when working with the Payment Request API is that the API manages the UI but performs no arithmetic; it will simply display whatever input you pass to it. Throughout the examples we'll discuss what this means for the developer and user.

> **Dogfood: **PaymentRequest is still in development. While we think it's stable enough to implement, it may continue to change. We'll [keep this page updated](https://developers.google.com/web/updates/2017/01/payment-request-updates?hl=zh-cn) to always reflect the current status of the API. Meanwhile, to protect yourself from API changes that may be backwards incompatible, we're offering [a shim](#paymentrequest_shim) that can be embedded on your site. The shim will paper over any API differences for two major Chrome versions.



