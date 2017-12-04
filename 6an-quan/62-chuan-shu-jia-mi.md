# HTTPS

> 作者：Kayce Basques、Chris Palmer、Matt Gaunt

## 为什么说 HTTPS 很重要

您应始终使用 HTTPS 保护您的所有网站，即使这些网站并不处理敏感的通信。 除了为网站和用户的个人信息提供关键的安全性和数据完整性外，许多新的浏览器功能，特别是[Progressive Web App](../../../progressive-web-apps/index-hl=zh-cn.html)所需的那些功能也要求使用 HTTPS。

#### TL;DR

* 善意的或恶意的入侵者会利用您的网站和用户之间传输的每个未受保护的资源。
* 许多入侵者都会查看汇总的行为以识别您的用户。
* HTTPS 不仅可阻止您的网站被滥用，也是许多先进功能不可或缺的一部分，可作为类似应用功能（如服务工作线程）的实现技术。

### HTTPS 可保护您的网站的完整性。

HTTPS 有助于防止入侵者篡改您的网站和用户浏览器之间的通信。 入侵者包括故意进行恶意攻击的攻击者，以及合法但具有侵犯性的公司，如将广告注入网页的 ISP 或酒店。

入侵者会利用未受保护的通信欺骗您的用户提供敏感信息或安装恶意软件，或将他们自己的广告插入您的资源中。例如，有些第三方向网站注入可能会损害用户体验和产生安全漏洞的广告。

入侵者会利用您的网站和用户之间传输的每个未受保护的资源。 图像、Cookie、脚本、HTML 等都会被利用。 入侵在网络中随时都会发生，包括入侵用户的电脑、Wi-Fi 热点或已泄露的 ISP 等。

### HTTPS 可保护您的用户的隐私和安全

HTTPS 可防止入侵者能够被动地侦听您的网站和您的用户之间的通信。

人们对 HTTPS 有一个普遍的错误认识，认为只有处理敏感通信的网站才需要 HTTPS。 每个未受保护的 HTTP 请求都可能暴露与您的用户行为和身份有关的信息。尽管访问一次未受保护的网站可能看上去无害，但一些入侵者会查看汇总的用户浏览活动，以推断他们的行为和意图，从而进行[去匿名化](https://en.wikipedia.org/wiki/De-anonymization)攻击，查出匿名用户的身份。例如，员工可能在阅读未受保护的医疗文章时不经意地向其雇主泄露敏感的健康信息。

### HTTPS 是网络的未来发展方向

强大的全新网络平台功能，如拍照或使用`getUserMedia()`录制音频，或通过服务工作线程启用离线应用体验，或构建 Progressive Web App，在执行前均需要用户的明确许可。还将更新许多较旧的 API，以要求执行权限，如[geolocation](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation/Using_geolocation)API。HTTPS 是这些新功能和更新的 API 的权限工作流的一个关键组件。

## 重要的安全术语

### TL;DR {#tldr_hide-from-toc}

* 公钥/私钥用于给浏览器与服务器之间的消息签名并将消息解密。
* 证书颁发机构 \(CA\) 是一个组织，对公钥与公共 DNS 名称（例如“www.foobar.com”）之间的映射进行证实。
* 证书签名请求 \(CSR\) 是一种数据格式，将一个公钥与拥有该公钥的实体的某些相关元数据绑定在一起

## 什么是公钥和私钥对？ {#_1}

**公钥/私钥对**是一对很大的数字，可用作加密密钥和解密密钥，并且共用一种特别的数学关系。一种常见的密钥对系统是[**RSA 加密系统**](https://en.wikipedia.org/wiki/RSA_%28cryptosystem%29)。**公钥**用于加密消息，并且消息只能使用对应的**私钥**来解密。您的网络服务器将其公钥公布到全球，客户端（例如网络浏览器）将使用此密钥来建立一个与您的服务器安全通信的通道。

## 什么是证书颁发机构？ {#_2}

**证书颁发机构 \(CA\)**是一个组织，对公钥和与公共 DNS 名称（例如“www.foobar.com”）之间的映射进行证实。例如，客户端如何知道特定公钥是否为 www.foobar.com 的真实公钥？按理说，无法知道。CA 证实特定密钥是特定网站的真实密钥，它使用自己的私钥来[**加密签名**](https://en.wikipedia.org/wiki/RSA_%28cryptosystem%29#Signing_messages)该网站的公钥。此签名在计算上是无法伪造的。浏览器（和其他客户端）维护**信任锚存储库**，它包含知名 CA 拥有的公钥，并且它们使用这些公钥来**加密验证**CA 的签名。

**X.509 证书**是一种数据格式，将一个公钥与拥有该公钥的实体的某些相关元数据绑定在一起。 就网络而言，密钥的所有者是网站运营商，并且重要的元数据是网络服务器的 DNS 名称。当客户端连接 HTTPS 网络服务器时，网络服务器提供其证书供客户端验证。 客户端验证此证书没有过期，DNS 名称与客户端正尝试连接的服务器名称一致，并且已知的信任锚 CA 已给此证书签名。在大多数情况下，CA 并不直接给网络服务器证书签名；通常有一个**证书链**将一个信任锚链接到一个或多个中间签名者，最终链接到网络服务器自己的证书（**终端实体**）。

## 什么是证书签名请求？ {#_3}

**证书签名请求 \(CSR\)**是一种数据格式，就像证书，将一个公钥与拥有该公钥的实体的某些相关元数据绑定在一起。但是，客户端并不解释 CSR；由 CA 解释。当您希望让 CA 证实您的网络服务器公钥时，就向 CA 发送 CSR。 CA 验证 CSR 中的信息，并使用它来生成证书。然后 CA 向您发送最终证书，您在网络服务器上安装该证书（或更可能是证书链）和私钥。

## 在服务器上启用 HTTPS

### TL;DR {#tldr_hide-from-toc}

* 创建一个 2048 位 RSA 公钥/私钥对。
* 生成一个嵌入您的公钥的证书签名请求 \(CSR\)
* 将 CSR 与证书颁发机构 \(CA\) 共享以接收最终证书或证书链。
* 将最终证书安装在非网络可访问的位置，例如`/etc/ssl`（Linux 和 Unix）或 IIS 需要它的位置 \(Windows\)。

## 生成密钥和证书签名请求 {#_1}

此部分使用 openssl 命令行程序（大部分 Linux、BSD 和 Mac OS X 系统均附带此程序）来生成私钥/公钥和 CSR。

### 生成一个公钥/私钥对 {#_2}

我们首先生成一个 2048 位 RSA 密钥对。较短的密钥，如 1024 位，不足以抵御暴力猜测攻击。 较长的密钥，如 4096 位，则有点过度。 长远来看，随着计算机处理开销降低，密钥长度会增加。 目前 2048 是最佳长度。

用于生成 RSA 密钥对的命令为：

```
openssl genrsa -out www.example.com.key 2048
```

这将生成以下输出：

```
Generating RSA private key, 2048 bit long modulus
.+++
.......................................................................................+++
e is 65537 (0x10001)
```

### 生成证书签名请求 {#_3}

在此步骤中，您将公钥和有关贵组织及网站的信息嵌入到证书签名请求（或 CSR）中。_openssl_命令以交互方式要求您提供所需的元数据。

运行以下命令：

```
openssl req -new -sha256 -key www.example.com.key -out www.example.com.csr
```

系统将输出以下内容：

```
You are about to be asked to enter information that will be incorporated
into your certificate request

What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:CA
State or Province Name (full name) [Some-State]:California
Locality Name (for example, city) []:Mountain View
Organization Name (for example, company) [Internet Widgits Pty Ltd]:Example, Inc.
Organizational Unit Name (for example, section) []:Webmaster Help Center Example
Team
Common Name (e.g. server FQDN or YOUR name) []:www.example.com
Email Address []:webmaster@example.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```

为确保 CSR 的有效性，请运行以下命令：

```
openssl req -text -in www.example.com.csr -noout
```

响应结果应如下所示：

```
Certificate Request:
    Data:
        Version: 0 (0x0)
        Subject: C=CA, ST=California, L=Mountain View, O=Google, Inc.,
OU=Webmaster Help Center Example Team,
CN=www.example.com/emailAddress=webmaster@example.com
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:ad:fc:58:e0:da:f2:0b:73:51:93:29:a5:d3:9e:
                    f8:f1:14:13:64:cc:e0:bc:be:26:5d:04:e1:58:dc:
                    ...
                Exponent: 65537 (0x10001)
        Attributes:
            a0:00
    Signature Algorithm: sha256WithRSAEncryption
         5f:05:f3:71:d5:f7:b7:b6:dc:17:cc:88:03:b8:87:29:f6:87:
         2f:7f:00:49:08:0a:20:41:0b:70:03:04:7d:94:af:69:3d:f4:
         ...
```

### 将 CSR 提交给证书颁发机构 {#csr}

对于不同的证书颁发机构 \(CA\)，需要使用不同的方法将 CSR 发送给他们。 这些方法可能包括在其网站上使用表单、以电子邮件或其他方式发送 CSR。 一些 CA（或其经销商）甚至可能将其中部分或全部流程自动化（在某些情况下，包括密钥对和 CSR 的生成）。

将 CSR 发送给 CA 并按照他们的说明接收最终证书或证书链。

对于为您的公钥进行证实的服务，不同 CA 的收费将有所不同。

还可以选择将密钥映射到多个 DNS 名称，包括多个独立名称（例如 example.com、www.example.com、example.net 和 www.example.net 的全部）或“通配符”名称（例如 \*.example.com）。

例如，某个 CA 目前提供以下价格：

* 标准：16 美元/年，适用于 example.com 和 www.example.com。
* 通配符：150 美元/年，适用于 example.com 和 \*.example.com。

按这些价格，当您有 9 个以上子域名时，通配符证书比较划算；您也可以只购买一个或多个单名称证书。 （例如，如果您有五个以上子域名，在服务器上启用 HTTPS 时，您可能发现通配符证书更方便。）

注：记住，在通配符证书中，通配符只适用于一个 DNS 标签。对 \*.example.com 有效的证书将对 foo.example.com 和 bar.example.com 有效，但对于 foo.bar.example.com 无效。

将证书复制到所有前端服务器的非网络可访问位置，例如`/etc/ssl`（Linux 和 Unix）或 IIS 需要它们的位置 \(Windows\)。

## 在服务器上启用 HTTPS {#https}

在服务器上启用 HTTPS 是确保网页安全的关键一步。

* 使用 Mozilla 的服务器配置工具来设置服务器以支持 HTTPS。
* 定期使用 Qualys 便捷的 SSL 服务器测试来测试网站，并确保得分至少为 A 或 A+。

此时，您必须做出关键的操作决定。选择下列其中一项：

* 给为网络服务器提供内容的每个主机名指定一个独立的 IP 地址。
* 使用基于名称的虚拟托管。

如果您一直针对每个主机名使用独立的 IP 地址，则可以轻松地让所有客户端支持 HTTP 和 HTTPS。

但是，大多数网站运营商使用基于名称的虚拟托管以节约 IP 地址，另一个原因是这样通常更方便。 Windows XP 上的 IE 和 2.3 版以前的 Android 的问题是，它们不理解[服务器名称指示](https://en.wikipedia.org/wiki/Server_Name_Indication)\(SNI\)，而这对 HTTPS 基于名称的虚拟托管非常重要。

将来有一天（希望很快），不支持 SNI 的客户端将被现代软件代替。 监控请求日志中的 User Agent 字符串，以了解何时已有足够的用户迁移到现代软件。 （您可以决定您的阈值；可能是 &lt; 5%，或 &lt; 1%。）

如果您的服务器上还没有 HTTPS 服务，请立即启用（无需将 HTTP 重定向到 HTTPS；参见下文）。 配置网络服务器以使用您购买并安装的证书。 您可能发现[Mozilla 便捷的配置生成器](https://mozilla.github.io/server-side-tls/ssl-config-generator/)很有用。

如果您有许多主机名/子域名，它们每个都需要使用正确的证书。

警告：如果您已完成上述步骤，但您使用 HTTPS 仅仅是为了将客户端重定向回 HTTP，那么，请马上停止这么做。参考下一部分以确保 HTTPS 和 HTTP 顺畅工作。

注：最终，您应将 HTTP 请求重定向到 HTTPS 并使用 HTTP 严格传输安全 \(HSTS\)。不过，现在不是向这种做法进行迁移的合适阶段；请参考“将 HTTP 重定向到 HTTPS”和“打开严格传输安全和安全 Cookie”。

现在，以及您网站的整个生命周期中，使用[Qualys 便捷的 SSL 服务器测试](https://www.ssllabs.com/ssltest/)来检查您的 HTTPS 配置。 您的网站得分应为 A 或 A+；将导致等级较低的任何因素均视为错误。（今天的 A 在明天会变成 B，因为针对算法和协议的攻击始终在改进！）

## 使站内网址变成相对网址 {#_4}

由于您的网站同时运行 HTTP 和 HTTPS，不管哪种协议，都应当尽可能顺畅运行。 一个重要的因素是对站内链接使用相对网址。

确保站内网址和外部网址与协议无关；即确保使用相对路径或省去协议，例如`//example.com/something.js`。

当您通过 HTTPS 提供一个包括 HTTP 资源的页面（称为[混合内容](../prevent-mixed-content/what-is-mixed-content-hl=zh-cn.html)）时会出现问题。 浏览器将警告用户，已失去 HTTPS 的全部能力。事实上，如果是主动混合内容（脚本、插件、CSS、iframe），则浏览器通常根本不会加载或执行此内容，从而导致页面残缺。

注：在 HTTP 页面中包括 HTTPS 资源完全没问题。

此外，当您链接到您网站中的其他页面时，用户可能从 HTTPS 降级为 HTTP。

当您的页面包括了使用_http://_架构的完全限定站内网址时，会出现这些问题。

不建议的做法— 我们不建议使用完全限定站内网址。

```html
<h1>Welcome To Example.com</h1>
<script src="http://example.com/jquery.js"></script>
<link rel="stylesheet" href="http://assets.example.com/style.css"/>
<img src="http://img.example.com/logo.png"/>;
<p>Read this nice <a href="http://example.com/2014/12/24/">new
post on cats!</a></p>
<p>Check out this <a href="http://foo.com/">other cool
site.</a></p>
```

换句话说，使站内网址尽可能是相对地址：协议相对（省去协议，以`//example.com`开头）或主机相对（以相对路径开头，例如`/jquery.js`）。

建议做法— 我们建议您使用协议相对站内网址。

```html
<h1>Welcome To Example.com</h1>
<script src="//example.com/jquery.js"></script>
<link rel="stylesheet" href="//assets.example.com/style.css"/>
<img src="//img.example.com/logo.png"/>;
<p>Read this nice <a href="//example.com/2014/12/24/">new
post on cats!</a></p>
<p>Check out this <a href="http://foo.com/">other cool
site.</a></p>
```

建议做法— 我们建议您使用相对站内网址。

```html
<h1>Welcome To Example.com</h1>
<script src="/jquery.js"></script>
<link rel="stylesheet" href="//assets.example.com/style.css"/>
<img src="//img.example.com/logo.png"/>;
<p>Read this nice <a href="/2014/12/24/">new
post on cats!</a></p>
<p>Check out this <a href="http://foo.com/">other cool
site.</a></p>
```

通过脚本实现，而不是手动操作。如果网站内容在数据库中，则在数据库的开发副本中测试您的脚本。 如果网站内容由简单文件组成，则要在文件的开发副本中测试您的脚本。 像平常一样，只有在更改通过 QA 后，才会将更改推送到生产平台中。可以使用[Bram van Damme 的脚本](https://github.com/bramus/mixed-content-scan)或类似脚本来检测网站中的混合内容。

在链接到其他网站（而不是包括其他网站的资源）时，请勿更改协议，因为您不能控制这些网站的运行方式。

成功：为确保大型网站的迁移更顺利，我们建议采用协议相对网址。如果您还不确定是否能够完全部署 HTTPS，则强制网站的所有子资源使用 HTTPS 可能会弄巧成拙。可能会有一段时间，您对 HTTPS 觉得新奇，并且 HTTP 网站仍必须像往常一样运行。但从长远看，您将完成迁移并锁定 HTTPS（请参考接下来两个部分）。

如果网站依赖第三方（例如 CDN、jquery.com）提供的脚本、图像或其他资源，则有两个选择：

* 对这些资源使用协议相对网址。如果该第三方不提供 HTTPS，请求他们提供。 大多数已经提供，包括 jquery.com。
* 从您控制的并且同时提供 HTTP 和 HTTPS 的服务器上提供资源。 这通常是个好点子，因为您可以更好地控制网站的外观、性能和安全。 此外，您不必信任第三方，尽管他们总是很不错。

注：请记住，您还需要更改样式表、JavaScript、重定向规则、`<link>`标记和 CSP 声明中的站内网址，而不仅是 HTML 页面。

## 将 HTTP 重定向到 HTTPS {#http_https}

您需要在页面的最前面放一个[规范链接](https://support.google.com/webmasters/answer/139066?hl=zh-cn)，以告诉搜索引擎 HTTPS 是访问您网站的最佳方法。

在页面中设置`<link rel="canonical" href="https://…"/>`标记。这样可帮助搜索引擎确定访问您网站的最佳方法。

## 打开严格传输安全和安全 Cookie {#cookie}

此时，您已准备好“锁定”使用 HTTPS。

* 使用 HTTP 严格传输安全 \(HSTS\) 来避免 301 重定向产生的开销。
* 始终在 Cookie 上设置安全标记。

首先，使用[严格传输安全](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security)来告诉客户端，它们始终应通过 HTTPS 来连接您的服务器，即使在访问`http://`引用时也是如此。

这样可挫败[SSL 剥离](http://www.thoughtcrime.org/software/sslstrip/)之类的攻击，还能避免我们在[将 HTTP 重定向到 HTTPS](#redirect-http-to-https)时启用的`301 redirect`产生的往返开销。

注：如果您的网站在其传输层安全协议 \(TLS\) 配置中出现过错误（例如过期证书），则已将您的网站注明为已知 HSTS 主机的客户端可能出现[硬故障](https://tools.ietf.org/html/rfc6797#section-12.1)。通过此方式显式设计 HSTS 可确保网络攻击者无法欺骗客户端访问没有 HTTPS 的网站。在确认您的网站运营足够可靠之前，不要启用 HSTS，以避免部署 HTTPS 时总是出现证书验证错误。

通过设置`Strict-Transport-Security`标头来打开 HTTP 严格传输安全 \(HSTS\)。[OWASP 的 HSTS 页面有说明链接](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security)，提供了针对各种服务器软件的说明。

大多数网络服务器提供相似的功能来添加自定义标头。

注：`max-age`的计算单位为秒。您可以从较小的值开始，并随着您越来越熟练自如地运营纯 HTTPS 网站而逐步增加`max-age`。

还要务必确保客户端从不通过 HTTP 发送 Cookie（例如用于身份验证或网站偏好）。 例如，如果用户的身份验证 Cookie 将在明文中暴露，则其整个会话的安全保障将被破坏 — 即使其他的一切都正确无误！

因此，更改您的网络应用，以便始终在其设置的 Cookie 上设置安全标记。[此 OWASP 网页解释了如何在多个应用框架中设置安全标记](https://www.owasp.org/index.php/SecureFlag)。 每个应用框架都采用一种方法来设置此标记。

大多数网络服务器都提供一种简单的重定向功能。使用`301 (Moved Permanently)`来告诉搜索引擎和浏览器，此 HTTPS 版本是规范的，并将用户从 HTTP 重定向到网站的 HTTPS 版本。

## 迁移问题 {#_5}

对于从 HTTP 迁移到 HTTPS，许多开发者有着合情合理的顾虑。Google 网站站长团队提供了一些[非常好的指导](https://plus.google.com/+GoogleWebmasters/posts/eYmUYvNNT5J?hl=zh-cn)。

### 搜索排名 {#_6}

Google 使用[HTTPS 用作肯定性的搜索质量指标](https://googlewebmastercentral.blogspot.com/2014/08/https-as-ranking-signal.html)。Google 还发布一个指南，说明在维护其搜索排名时[如何传输、移动或迁移您的网站](https://support.google.com/webmasters/topic/6029673?hl=zh-cn)。

Bing 也发布了[网站站长指南](http://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a)。

### 性能 {#_7}

当内容和应用层优化得当时（请参考[Steve Souders 的论著](https://stevesouders.com/)以获取很好的建议），相对于应用的总体开销而言，其余的传输层安全协议 \(TLS\) 性能问题一般都是小问题。此外，您可以减少和分摊那些开销。 （如需 TLS 优化建议和一般建议，请参考 IlyaGrigorik 撰写的[高性能浏览器网络](http://chimera.labs.oreilly.com/books/1230000000545)。） 另请参考 Ivan Ristic 的[OpenSSL 手册](https://www.feistyduck.com/books/openssl-cookbook/)和[SSL 和 TLS 的防弹衣](https://www.feistyduck.com/books/bulletproof-ssl-and-tls/)。

在某些情况下，TLS 可以提高性能，主要是可以采用 HTTP/2 所带来的结果。 Chris Palmer[在 Chrome 开发峰会 2014 上做过一个演讲，讨论 HTTPS 和 HTTP/2 的性能](https://developers.google.com/web/shows/cds/2014/tls-all-the-things?hl=zh-cn)。

### 引用站点标头 {#_8}

当用户从您的 HTTPS 网站链接到其他 HTTP 网站时，User Agent 不会发送引用站点标头。如果这是个问题，有多种方法可解决：

* 其他网站应迁移到 HTTPS。如果被引用网站可以完成本指南中的[在服务器上启用 HTTPS](#enable-https-on-your-servers)部分，则可以将您网站中指向他们网站的链接从`http://`更改为`https://`，或可以使用协议相对链接。
* 为解决引用站点标头的各种问题，可使用新的[引用站点政策标准](http://www.w3.org/TR/referrer-policy/#referrer-policy-delivery-meta)。

由于各搜索引擎正在迁移到 HTTPS，将来，当您迁移到 HTTPS 时，可能会看到更多的引用站点标头。

注意：根据[HTTP RFC](https://tools.ietf.org/html/rfc2616#section-15.1.3)，如果引用页面是通过安全协议传输的，则客户端**不能**在（非安全）HTTP 请求中包括引用站点标头字段。

### 广告收入 {#_9}

通过展示广告来赚钱的网站运营商希望确保迁移到 HTTPS 不会降低广告曝光量。 但是，由于混合内容的安全问题，HTTP`<iframe>`在 HTTPS 页面中不起作用。 这里就存在一个棘手的集体行动问题：在广告商通过 HTTPS 发布广告之前，网站运营商无法在不损失广告收入的情况下迁移到 HTTPS；但是在网站运营商迁移到 HTTPS 之前，广告商没有动力来通过 HTTPS 发布广告。

广告商至少应通过 HTTPS 提供广告服务（例如完成本页面中的“在服务器上启用 HTTPS”部分）。 许多广告商已经这样做了。您应当请求完全不提供 HTTPS 的广告商至少开始提供 HTTPS。

您可能希望推迟完成[使站内网址变成相对网址](#make-intrasite-urls-relative)，直到有足够数量的广告商能正常地互操作。

