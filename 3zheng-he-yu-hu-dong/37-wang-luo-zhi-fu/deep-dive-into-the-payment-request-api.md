# Deep Dive into the Payment Request API

> 作者：Matt Gaunt

# Deep Dive into the Payment Request API

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

## Feature Detect {#feature_detect}

Payment Request has been available since Chrome 53 for Android and has been enabled by default since Chrome 61 on desktop. Before we start covering how to use Payment Request, we should feature detect to ensure it's available and fallback to a traditional checkout page in browsers that don't support it.

The feature detect is simply:

```js
if(window.PaymentRequest) {
  // Use Payment Request API
} else {
  // Fallback to traditional checkout
  window.location.href = '/checkout/traditional';
}
```

> **Note:**Payment Request is only available on sites served over HTTPS.

## PaymentRequest Constructor {#paymentrequest_constructor}

Once you are ready to collect payment details from the user, you'll need to construct a new PaymentRequest object.

```js
const supportedPaymentMethods = [
  {
    supportedMethods: 'basic-card',
  }
];
const paymentDetails = {
  total: {
    label: 'Total',
    amount:{
      currency: 'USD',
      value: 0
    }
  }
};
// Options isn't required.
const options = {};

new PaymentRequest(
  supportedPaymentMethods,
  paymentDetails,
  options
);
```

The constructor takes three arguments. The[first argument](#defining_supported_payment_methods)defines which forms of payment you can accept; for example, you may only accept 'visa' and 'mastercard'. The[paymentDetails](#defining_payment_details)argument defines the total and display items. The[optional third argument](#defining_options_optional42)is an object used to request additional information from the user; for example, you can request the payer's name, email address, and phone number.

All of these options affect the UI presented to the user as well as the amount of information the browser will need to collect from them.

Constructing a new`PaymentRequest`object can be done at any point in your app. Nothing will be shown to the user until you call its`show()`method.

```js
const request = new PaymentRequest(
  supportedPaymentMethods,
  paymentDetails,
  options
);

// Call when you wish to show the UI to the user.
request.show()
.then(...).catch(...);
```

Let's start by looking at the arguments we can pass into the`PaymentRequest`constructor, starting with the supported payment methods.

### Defining Supported Payment Methods {#defining_supported_payment_methods}

The Payment Request API is designed to support credit and debit card payments as well as third party payment methods \(such as Android Pay\).

You must supply an array of objects indicating your supported payment methods where each payment method must include a`supportedMethods`parameter that identifies the payment method. Each object can contain an optional data object.

```js
const supportedPaymentMethods = [
  {
    supportedMethods: 'name-of-payment-method',
    data: {
      // Optional data for this payment method
    }
  }
];

new PaymentRequest(supportedPaymentMethods, paymentDetails, options);
```

First we'll look at how to define support for credit and debit cards, followed by a brief look at supporting Android Pay.

### Payment Method: 'basic-card' {#payment_method_basic-card}

To support credit and debit cards, we need to change the`supportedMethods`parameter to contain 'basic-card', like so:

```js
const creditCardPaymentMethod = {
  supportedMethods: 'basic-card',
};

const supportedPaymentMethods = [creditCardPaymentMethod];

new PaymentRequest(supportedPaymentMethods, paymentDetails, options);
```

If the user has no cards set up they'll be prompted to add details, otherwise an existing card will be selected for them.

![](https://developers.google.com/web/fundamentals/payments/images/deep-dive/pr-demo-basic-card-only.png?hl=zh-cn "Example of basic-card support in the Payment Request API.")

Example of basic-card support in the Payment Request API.

At the time of writing, Chrome supports 'amex', 'diners', 'discover', 'jcb', 'maestro', 'mastercard', 'unionpay', 'mir', and 'visa', which you can see listed across the top of the UI.

To restrict the supported cards, we can add the optional data parameter and define`supportedNetworks`. The following code restricts the accepted cards to visa, mastercard and amex.

```js
const creditCardPaymentMethod = {
  supportedMethods: 'basic-card',
};

const bobPayPaymentMethod = {
  supportedMethods: "https://example.com/bobpay",
  data: {
    merchantIdentifier: "XXXX",
    bobPaySpecificField: true
  }
};

const supportedPaymentMethods = [
  creditCardPaymentMethod,
  bobPayPaymentMethod
];

new PaymentRequest(supportedPaymentMethods, paymentDetails, order);
```

The payment request UI will restrict the accepted cards and the user will be prevented from selecting other cards:

![](https://developers.google.com/web/fundamentals/payments/images/deep-dive/pr-demo-basic-card-visa-mastercard-amex.png?hl=zh-cn "Example of reduced the list of supported cards in the Payment Request API.")

Example of reduced the list of supported cards in the Payment Request API.

In the screenshot above, the user must add a payment. If browsers have this information readily available, they have the option of pre-selecting an appropriate payment for the user. This means that the wider your card support, the higher the odds are of the browser being able to pre-populate these details, speeding up the checkout flow. For example, if I had a card saved in Chrome, the UI would start with a suitable card already selected:

![](https://developers.google.com/web/fundamentals/payments/images/deep-dive/pr-demo-pre-selected-visa-short-blackout.png?hl=zh-cn "Cards will be preselected if available.")

Cards will be preselected if available.

The`supportedTypes`parameter tells Chrome which types of cards to filter out, i.e. if a merchant defined`supportedTypes`as`['credit', 'debit']`, Chrome would strip out any 'prepaid' cards the user has.

This means that`supportedTypes`**does not guarantee**that the final card you receive will be a supported type. The user can enter details for a new card, which could be an unsupported type, and the Payment Request API will allow this. The`supportedTypes`option is**just**for filtering out existing cards. Merchants still need to check the card type on their backend.

Chrome version 61 added support for the`supportedTypes`option. In older versions of Chrome you would receive the following console warning:

`Cannot yet distinguish credit, debit, and prepaid cards.`

It's safe to use this option. The only difference is that some cards wouldn't be filtered automatically for the user.

```js
const androidPayPaymentMethod = {
  supportedMethods: 'https://android.com/pay',
  data: {
    merchantName: 'Android Pay Demo',
    merchantId: '00000000000000000000',
    environment: 'TEST',
    allowedCardNetworks: ['AMEX', 'DISCOVER', 'MASTERCARD', 'VISA'],
    paymentMethodTokenizationParameters: {
      tokenizationType: 'GATEWAY_TOKEN',
      parameters: {
        'gateway': 'stripe',
        'stripe:publishableKey': 'xx_demo_xxxxxxxxxxxxxxxxxxxxxxxx',
        'stripe:version': '2016-07-06',
      },
    },
  },
};
```

![](https://developers.google.com/web/fundamentals/payments/images/deep-dive/pr-demo-android-and-cards-short-blackout.png?hl=zh-cn "Android Pay example in the payment request UI.")

Android Pay example in payment request UI.

We won't go into details of how to add Android Pay in this article,[we have a section dedicated to that here](android-pay-hl=zh-cn.html).

Edge Cases

There are some edge cases to be aware of when defining your supported payment methods.

**Unsupported Payment Methods**If you try to call`show()`on a`PaymentRequest`object and there are no supported payment methods, the returned promise will reject immediately with the following error:

`DOMException: The payment method is not supported`

This shouldn't be a problem if you include 'basic-card' as a supported payment method. If, however, you only support a third party payment method, like Android Pay, there is a strong chance that it won't be supported by a browser that supports the Payment Request API.

**Third Party Payment Method Skipping the Payment Request UI**In the screenshot above you can see "Android Pay" as the pre-selected payment option. This has occurred because the example supports both Android Pay and basic cards. If you define Android Pay as your**only**payment method and the browser supports it, the browser can \(and Chrome does, at the time of writing\) skip the payment request UI altogether after the`show()`method is called. Users will be taken straight to the Android Pay app to complete the payment.

### Defining Payment Details {#defining_payment_details}

The second argument we need to pass to the`PaymentRequest`constructor is the payment details object. This object contains the total for the order and an optional array of display items \(i.e. a high level breakdown of the total\).

#### Transaction Details: Total {#transaction_details_total}

The contents of the`total`parameter should contain a`label`parameter and an`amount`parameter consisting of a`currency`and`value`. A basic example would be:

```

```

This controls the "order summary" section of the UI:

![](https://developers.google.com/web/fundamentals/payments/images/deep-dive/pr-demo-order-summary.png?hl=zh-cn "How the total parameter affects the UI.")

How the total parameter affects the UI.

The`total`parameter is the only\_required\_piece of information in the payment details object.

The`label`can be any piece of text you like, the`currency`must be a string currency code following the[ISO 4217 standard](https://www.iso.org/iso-4217-currency-codes.html), and the`value`is the amount for the order.

To give another example, we can define the total as:

```

```

This produces the following order summary:

![](https://developers.google.com/web/fundamentals/payments/images/deep-dive/pr-demo-order-summary-example-short.png?hl=zh-cn "Another example of how the total parameter affects the UI.")

Another example of how the total parameter affects the UI.

#### Transaction Details: Display Items {#transaction_details_display_items}

Display items can used to provide a high level breakdown of the total. This would typically include subtotal, tax, deductions, and shipping cost.

The format of the display items should be an array of items following the same structure as the total \(i.e., a`label`and an`amount`with`currency`and`value`\).

```

```

If we provided the above example, we'd get the following UI.

![](https://developers.google.com/web/fundamentals/payments/images/deep-dive/display-items.png?hl=zh-cn "Displaying the items from the payment details object.")

Displaying the items from the payment details object.

The order of the items in the`displayItems`array will dictate their display order in the UI.

Please bear in mind that`displayItems`are not designed to display a long list of items. You should use this for high level entries instead of an itemized list, for example subtotal, discount, tax and shipping cost.

It's worth repeating that the the

`PaymentRequest`

API does not perform any arithmetic. If you look at the above example, all the items values do not add up to the total. This is because we've set the total to have a value of zero.

**It is the responsibility of your web app to calculate the correct total.**

