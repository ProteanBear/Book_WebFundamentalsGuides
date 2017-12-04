# 附：词汇表

## A {#a}

### Accelerated Mobile Pages\(AMP\) {#AMP}

Accelerated Mobile Pages are web pages that load very quickly. AMP pages are built with three core components: AMP HTML, AMP JS, and the AMP Cache. AMP HTML is HTML with some restrictions for reliable performance. AMP JS is a library that ensures the fast rendering of AMP HTML pages. The AMP Cache is a proxy-based content delivery network used to serve cached AMP HTML pages.

**See**[The AMP Project](https://www.ampproject.org/learn/overview/)

### add to home screen\(A2HS\) {#A2HS}

Add to home screen is a method where Progressive Web App developers can implement web app install banners to help users quickly and easily \(with one touch\) add web apps to their Android mobile device’s home screen, making it easy to launch and return to an app.

**See**[Web App Install Banners](app-install-banners/index-hl=zh-cn.html)

### application shell {#application-shell}

An application shell \(or app shell\) architecture is one way to build a Progressive Web App that reliably and instantly loads on your users' screens, similar to what you see in native applications. The app shell is the minimal HTML, CSS and JavaScript required to power the user interface and when cached offline can ensure instant, reliably good performance to users on repeat visits.

**See**[The App Shell Model](architecture/app-shell-hl=zh-cn.html)

## C {#c}

### content delivery network\(CDN\) {#CDN}

A CDN is a network of geographically distributed servers that cooperate to satisfy requests for content. CDNs optimize content delivery by distributing copies of files \(such as videos, images, HTML, CSS and JavaScript\) to multiple servers. This reduces latency by placing the content closer to the requestor. For example, if a user in India requests a web page from a Brazilian website, the request could be rerouted to deliver assets served from a local CDN server in Mumbai.

**See**[Content deliver network on Wikipedia](https://en.wikipedia.org/wiki/Content_delivery_network)

### custom element {#custom-element}

A Custom Element is a developer defined HTML tag. These elements are the foundation of Web Components and can be used to create any sort of UI.

**See**[Custom Elements v1: Reusable Web Components](https://developers.google.com/web/fundamentals/architecture/building-components/customelements?hl=zh-cn)

## D {#d}

### DOMContentLoaded\(DCL\) {#DCL}

Defined by the HTML specification:

> The DOMContentLoaded reports the time when the initial HTML document has been completely loaded and parsed, without waiting for stylesheets, images, and subframes to finish loading.



**See**[DCL on MDN](https://developer.mozilla.org/en-US/docs/Web/Events/DOMContentLoaded)

* [DCL in the HTML Specification](https://html.spec.whatwg.org/#event-domcontentloaded)

## F {#f}

### Fast, Integrated, Reliable, Engaging\(FIRE\) {#FIRE}

Progressive Web Apps \(PWA's\) should be fast, integrated, reliable, and engaging.

**See**[Progressive Web Apps](../progressive-web-apps/index-hl=zh-cn.html)

### fetch API {#fetch-api}

The fetch API is a promise-based JavaScript interface for requesting resources and reading responses. It provides a global fetch\(\) method that gives an easy, logical way to fetch resources asynchronously across the network. Fetch is a successor to the older XMLHttpRequest.

**See**[Working the with Fetch API](https://developers.google.com/web/ilt/pwa/working-with-the-fetch-api?hl=zh-cn)

* [Using Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)

### First Contentful Paint\(FCP\) {#FCP}

Defined by the Paint Timing API and available in Chrome M60+:

> First Contentful Paint reports the time when the browser first rendered any text, image \(including background images\), non-white canvas or SVG. This includes text with pending webfonts. This is the first time users could start consuming page content.



**See**[Paint Timing API](https://w3c.github.io/paint-timing/#first-contentful-paint)

* [Chrome Platform Status](https://www.chromestatus.com/feature/5688621814251520)
* [FCP on MDN](https://developer.mozilla.org/en-US/docs/Web/API/PerformancePaintTiming)

### First Meaningful Paint\(FMP\) {#FMP}

### First Paint\(FP\) {#FP}

Defined by the Paint Timing API and available in Chrome M60+:

> First Paint reports the time when the browser first rendered after navigation. This excludes the default background paint, but includes non-default background paint. This is the first key moment developers care about in page load – when the browser has started to render the page.



**See**[Paint Timing API](https://w3c.github.io/paint-timing/#first-paint)

* [Chrome Platform Status](https://www.chromestatus.com/feature/5688621814251520)
* [FP on MDN](https://developer.mozilla.org/en-US/docs/Web/API/PerformancePaintTiming)

### First, Last, Invert, Play\(FLIP\) {#FLIP}

FLIP is a technique to set up high-performance animations using CSS transforms. To avoid janking animations, start and end position are evaluated during the setup so that the animation doesn't have to do any expensive calculations.

**See**[FLIP your animations](https://aerotwist.com/blog/flip-your-animations/)

### flexbox {#flexbox}

The Flexible Box Layout Module \(Flexbox\) is an API that provides tools to make web content responsive. Flexbox provides an efficient way to lay out, align, and distribute space among items in a container, even when their size is unknown and/or dynamic. The API allows the rapid creation of complex, flexible layouts, and features that have historically proved difficult with CSS.

**See**[Flexbox on MDN](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox)

## H {#h}

### Hypertext Transfer Protocol Secure\(HTTPS\) {#HTTPS}

HTTPS is a protocol in which the connection between the server and client is encrypted, helping to protect users' information and prevent tampering. APIs such as the Service Worker, Google Maps API, and File API must be served over HTTPS. HTTPS is implemented using the Transport Layer Security \(TLS\) protocol. Although TLS supersedes Secure Sockets Layer \(SSL\), it is often referred to as SSL.

**See**[HTTPS on Wikipedia](https://en.wikipedia.org/wiki/HTTPS)

## L {#l}

### lifecycle callback {#lifecycle-callback}

Every Custom Element has a set of built-in lifecycle callbacks, or "reactions" that are called when the element is added/removed from the page, or has an attribute mutated.

**See**[Custom elements - lifecycles](https://developers.google.com/web/fundamentals/architecture/building-components/customelements?hl=zh-cn#reactions)

### Lighthouse {#lighthouse}

Lighthouse is an open-source, automated tool for improving the quality of web pages. You can run it against any web page, public or requiring authentication. It has audits for performance, accessibility, progressive web apps, and more.

**See**[Lighthouse](https://developers.google.com/web/tools/lighthouse/?hl=zh-cn)

## M {#m}

### mutation observer {#mutation-observer}

Mutation Observers are a web platform API to detect and react to change to the DOM tree. If an element is observed by a MutationObserver, events like appending a new element or removing and existing one will trigger a function to execute.

**See**[Detect DOM changes with Mutation Observers](https://developers.google.com/web/updates/2012/02/Detect-DOM-changes-with-Mutation-Observers?hl=zh-cn)

## O {#o}

### onload\(OL\) {#OL}

Defined by the HTML specification:

> The load event is fired when the page and its dependent resources have finished loading.



**See**[OL on MDN](https://developer.mozilla.org/en-US/docs/Web/Events/DOMContentLoaded)

* [OL in the HTML Specification](https://html.spec.whatwg.org/#event-load)

## P {#p}

### Progressive Web App\(PWA\) {#PWA}

Progressive Web Apps are user experiences that have the reach of the web, and are fast, integrated, reliable and engaging.

**See**[Progressive Web Apps](../progressive-web-apps/index-hl=zh-cn.html)

### promise {#promise}

A JavaScript promise is an object that is used as a placeholder for the eventual results of a deferred \(and possibly asynchronous\) computation. JavaScript promises are an addition to ECMAScript 6 that provide a cleaner, more intuitive way to deal with the completion \(or failure\) of asynchronous tasks.

**See**[JavaScript Promises](primers/promises-hl=zh-cn.html)

### push notifications {#push-notifications}

Web push notifications make it easy to re-engage with users by showing relevant, timely, and contextual notifications, even when the browser is closed.

**See**[Web Push Notifications](push-notifications/index-hl=zh-cn.html)

### Push, Render, Pre-cache, Lazy-load\(PRPL\) {#PRPL}

PRPL is a pattern for structuring and serving Progressive Web Apps \(PWAs\), with an emphasis on the performance of app delivery and launch.

**See**[The PRPL Pattern](performance/prpl-pattern/index-hl=zh-cn.html)

## R {#r}

### requestAnimationFrame\(rAF\) {#rAF}

### requestIdleCallback\(rIC\) {#rIC}

### Response, Animation, Idle, Load\(RAIL\) {#RAIL}

RAIL is a user-centric performance model. Every web app has these four distinct aspects to its life cycle, and performance fits into them in different ways.

**See**[Measure Performance with the RAIL Model](performance/rail-hl=zh-cn.html)

### responsive design {#responsive-design}

Responsive web design is an approach to web design aimed at making web pages visually appealing and performant on any form factor. In addition, it is important to understand that responsive web design tasks include offering the same content to a variety of devices for a single website.

**See**[Responsive Web Design Basics](design-and-ux/responsive/index-hl=zh-cn.html)

## S {#s}

### Service Worker {#service-worker}

Service workers essentially act as proxy servers that sit between web applications, and the browser and network \(when available\). They are intended to \(amongst other things\) enable the creation of effective offline experiences, intercept network requests and take appropriate action based on whether the network is available and updated assets reside on the server. They also allow access to push notifications and background sync APIs.

**See**[Introduction to Service Workers](primers/service-workers/index-hl=zh-cn.html)

* [Service Worker API](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)

### shadow DOM {#shadow-dom}

Shadow DOM introduces scoped CSS and DOM to the web platform. It lets developers write encapsulated UI components which can be used in any application.

**See**[Shadow DOM v1: Self-Contained Web Components](https://developers.google.com/web/fundamentals/architecture/building-components/shadowdom?hl=zh-cn)

### single page app\(SPA\) {#SPA}

A single page app is user-friendly app that performs more like a desktop app. Typically, SPA content is rendered dynamically using JavaScript, rather than opening a new page. As a result, single-page applications typically load only data \(e.g. in JSON or XML format\) rather than pre-rendered HTML. This decreases the data transferred on the wire.

* [Single-page application on Wikipedia](https://en.wikipedia.org/wiki/Single-page_application)

### splash screen {#splash-screen}

A splash screen is a graphic that is shown to users while an application is loading in the background, giving immediate feedback to the user while the application is launching.

## T {#t}

### Time To First Byte\(TTFB\) {#TTFB}

### Time To First Paint\(TTFP\) {#TTFP}

### Time To Interactive\(TTI\) {#TTI}

## W {#w}

### web app manifest {#web-app-manifest}

A web app manifest is a JSON-formatted file named manifest.json that is a centralized place to put metadata that controls how the web application appears to the user and how it can be launched. \(Do not confuse this with a manifest file used by AppCache.\) Some browsers, such as Chrome, use the web app manifest to enable 'add to homescreen'.

**See**[The Web App Manifest](web-app-manifest/index-hl=zh-cn.html)

* [Web App Manifest spec](https://w3c.github.io/manifest/)

### web components {#web-components}

Web components are a set of web platform APIs that allow you to create new custom, reusable, encapsulated HTML tags to use in web pages and web apps. Custom components and widgets build on the Web Component standards, will work across modern browsers, and can be used with any JavaScript library or framework that works with HTML.

**See**[Building Web Components](web-components/index-hl=zh-cn.html)

* [Introduction on webcomponents.org](https://www.webcomponents.org/introduction)



