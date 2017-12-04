# 安全性和身份

安全性是一个重要话题，您可以从以下几点入手。

## 对传输中的数据进行加密 {#_1}

[安全的 HTTP，也称为 HTTPS](encrypt-in-transit/why-https-hl=zh-cn.html)，是最关键的安全功能之一，许多现代 API 和[Progressive Web App](../../progressive-web-apps/index-hl=zh-cn.html)都需要使用它。人们对 HTTPS 有一个普遍的错误认识，认为只有处理敏感通信的网站才需要 HTTPS。隐私和安全性并不是使用 HTTPS 保护用户的充分理由，服务工作线程、Payment Request API 等许多新的浏览器功能也需要 HTTPS。

[在服务器上启用 HTTPS](encrypt-in-transit/enable-https-hl=zh-cn.html)

## 内容安全政策 {#header}

内容安全政策或 CSP 提供一个丰富的指令集，让您可以对允许页面加载的资源和资源加载位置进行精细控制。  
[了解详情](csp/index-hl=zh-cn.html)

## 防止混合内容 {#header_1}

实现 HTTPS 时比较耗时的一项任务是查找和修复具有 HTTPS 和 HTTP 的混合内容。 幸运的是，有一些工具可帮助您处理此任务。  
[使用入门](prevent-mixed-content/what-is-mixed-content-hl=zh-cn.html)

## 相关资源 {#_2}

### Chrome DevTools {#chrome_devtools}

* [了解安全问题](https://developers.google.com/web/tools/chrome-devtools/security?hl=zh-cn)



