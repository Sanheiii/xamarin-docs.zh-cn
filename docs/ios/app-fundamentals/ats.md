---
title: Xamarin 中的应用传输安全
description: 应用传输安全 (ATS) 强制 (internet 资源（如应用的后端服务器) 和应用）之间的安全连接。
ms.prod: xamarin
ms.assetid: F8C5E444-2D05-4D9B-A2EF-EB052CD6F007
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/13/2017
ms.openlocfilehash: 476c19f22755775f7b2b8b1d4205747b4bc6b313
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432525"
---
# <a name="app-transport-security-in-xamarinios"></a>Xamarin 中的应用传输安全

_应用传输安全 (ATS) 强制 (internet 资源（如应用的后端服务器) 和应用）之间的安全连接。_

本文将介绍应用传输安全在 iOS 9 应用上强制实施的安全更改，以及 [这对你的 Xamarin ios 项目的意义](#xamarinsupport)，它将介绍 [ATS 配置选项](#config) ，并将介绍如何在需要时 [选择退出 ATS](#optout) ATS。 因为默认情况下启用 ATS，所以任何不安全的 internet 连接将在 iOS 9 应用中引发异常 (除非已明确允许) 。

## <a name="about-app-transport-security"></a>关于应用传输安全

如上所述，ATS 确保 iOS 9 和 OS X El Capitan 中的所有 internet 通信都符合安全连接最佳做法，从而防止直接通过应用或正在使用的库泄露敏感信息。

对于现有应用，请尽可能实现 `HTTPS` 协议。 对于新的 Xamarin iOS 应用，你应 `HTTPS` 在与 internet 资源通信时独占使用。 此外，必须使用 TLS 版本1.2 和 "向前保密" 加密高级 API 通信。

与 [NSUrlConnection](xref:Foundation.NSUrlConnection)、 [CFUrl](xref:CoreFoundation.CFUrl) 或 [NSUrlSession](xref:Foundation.NSUrlSession) 建立的任何连接都默认使用 ATS，此连接在为 iOS 9 和 OS X 10.11 (El Capitan) 生成的应用中使用。

## <a name="default-ats-behavior"></a>默认 ATS 行为

由于默认情况下，ATS 在为 iOS 9 和 OS X 10.11 (El Capitan) 中生成的应用中启用了，因此使用 [NSUrlConnection](xref:Foundation.NSUrlConnection)、 [CFUrl](xref:CoreFoundation.CFUrl) 或 [NSUrlSession](xref:Foundation.NSUrlSession) 的所有连接都将受到 ATS 安全要求。 如果连接不满足这些要求，它们将失败并出现异常。

### <a name="ats-connection-requirements"></a>ATS 连接要求

对于所有 internet 连接，ATS 将强制实施以下要求：

- 所有连接密码必须使用 "向前保密"。 请参阅下面的接受的密码列表。
- 传输层安全性 (TLS) 协议必须是版本1.2 或更高版本。
- 至少有一个 SHA256 指纹，其中包含2048位或更高的 RSA 密钥，或者256位或更大的椭圆曲线 (ECC) 密钥必须用于所有证书。

同样，由于默认情况下在 iOS 9 中启用了 ATS，因此尝试建立不满足这些要求的连接将导致引发异常。

<a name="ATS-Compatible-Ciphers"></a>

### <a name="ats-compatible-ciphers"></a>ATS 兼容密码

ATS 受保护的 internet 通信会接受以下转发机密密码类型：

- `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA`
- `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`

有关使用 iOS internet 通信类的详细信息，请参阅 Apple 的 [NSURLConnection 类引用](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/index.html#//apple_ref/doc/uid/TP40003755)、 [CFURL 引用](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFURLRef/index.html#//apple_ref/doc/uid/20001206) 或 [NSURLSession 类引用](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/index.html#//apple_ref/doc/uid/TP40013435)。

<a name="xamarinsupport"></a>

## <a name="supporting-ats-in-xamarinios"></a>支持 Xamarin 中的 ATS

由于 iOS 9 和 OS X El Capitan 中默认启用 ATS，因此，如果你的 Xamarin 应用或它使用的任何库或服务与 internet 建立连接，则你需要采取某种措施，否则连接将导致引发异常。

对于现有应用，Apple 建议你尽快支持该 `HTTPS` 协议。 如果无法连接到不支持的第三方 web 服务 `HTTPS` ，或者如果支持不 `HTTPS` 实用，可以选择退出 ATS。 有关更多详细信息，请参阅下面的 [ATS](#optout) 部分。

对于新的 Xamarin iOS 应用，应 `HTTPS` 在与 internet 资源通信时独占使用。 同样，在某些情况下可能会 (如使用第三方 web 服务) 无法实现这一点，你需要选择退出 ATS。

此外，ATS 强制使用 TLS 版本1.2 和转发机密来加密高级 API 通信。 有关更多详细信息，请参阅上面的 [ATS 连接要求](#ats-connection-requirements) 和 [ATS 兼容的密码](#ats-compatible-ciphers) 部分。

尽管你可能不熟悉 TLS ([传输层安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security)) 它是 SSL ([安全套接字) 层](https://en.wikipedia.org/wiki/Transport_Layer_Security) 的后续版本，它提供了一系列加密协议来强制通过网络连接进行安全性。

TLS 级别由正在使用的 web 服务控制，因此在应用程序的控件之外。 和都 `HttpClient` `ModernHttpClient` 应该自动使用服务器支持的最高级别的 TLS 加密。

根据要与之通信的服务器 (尤其是第三方服务) 时，可能需要禁用 "向前保密" 或选择较低的 TLS 级别。 有关更多详细信息，请参阅下面的 [配置 ATS 选项](#configuring-ats-options) 部分。

> [!IMPORTANT]
> 应用传输安全不适用于使用 **托管 HTTPClient 实现**的 Xamarin 应用。 仅适用于使用 CFNetwork **HTTPClient 实现** 或 **NSURLSession HTTPClient 实现** 的连接。

### <a name="setting-the-httpclient-implementation"></a>设置 HTTPClient 实现

若要设置 iOS 应用使用的 HTTPClient 实现，请双击 "**解决方案资源管理器**中的**项目**以打开**项目选项**。 导航到 " **IOS 生成** "，然后在 " **HttpClient 实现** " 下拉列表中选择所需的客户端类型：

![设置 iOS 生成选项](ats-images/client01.png)

#### <a name="managed-handler"></a>托管处理程序

托管处理程序是完全托管的 HttpClient 处理程序，它与早期版本的 Xamarin 一起提供，是默认处理程序。

优点：

- 它与 Microsoft .NET 和早期版本的 Xamarin 最兼容。

缺点：

- 它未与 iOS (完全集成，例如，仅限于 TLS 1.0) 。
- 它通常比本机 Api 慢得多。
- 它需要更多托管代码，并创建更大的应用。

#### <a name="cfnetwork-handler"></a>CFNetwork 处理程序

基于 CFNetwork 的处理程序基于本机 `CFNetwork` 框架。

优点：

- 使用本机 API 以获得更好的性能和更小的可执行文件大小。
- 添加了对较新标准（如 TLS 1.2）的支持。

缺点：

- 需要 iOS 6 或更高版本。
- 不可用于 watchOS。
- 某些 HttpClient 功能和选项不可用。

#### <a name="nsurlsession-handler"></a>NSUrlSession 处理程序

基于 NSUrlSession 的处理程序基于本机 `NSUrlSession` API。

优点：

- 使用本机 API 以获得更好的性能和更小的可执行文件大小。
- 添加了对较新标准（如 TLS 1.2）的支持。

缺点：

- 需要 iOS 7 或更高版本。
- 某些 HttpClient 功能和选项不可用。

## <a name="diagnosing-ats-issues"></a>诊断 ATS 问题

尝试在 iOS 9 中直接或从 web 视图连接到 internet 时，可能会收到如下格式的错误：

> 应用传输安全已阻止明文 HTTP (`http://www.-the-blocked-domain.com`) 资源负载，因为它不安全。 临时异常可以通过应用的 info.plist 文件进行配置。

在 iOS9 中，应用传输安全 (ATS) 强制 (internet 资源（如应用的后端服务器) 和应用）之间的安全连接。 此外，ATS 要求使用协议进行通信 `HTTPS` ，并使用 TLS 版本1.2 和正向保密加密来对高级 API 通信进行加密。

由于默认情况下，ATS 在为 iOS 9 和 OS X 10.11 (El Capitan) 中生成的应用程序中启用，因此使用或的所有连接 `NSURLConnection` `CFURL` `NSURLSession` 都将受到 ATS 安全要求。 如果连接不满足这些要求，它们将失败并出现异常。

Apple 还提供了 [TLSTool 示例应用](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2) ，可将 (或转码编译为 Xamarin 和 c # ) ，并用于诊断 ATS/TLS 问题。 有关如何解决此问题的信息，请参阅下面的 ATS 部分中的 "选择 [退出](#optout) " 部分。

<a name="config"></a>

## <a name="configuring-ats-options"></a>配置 ATS 选项

可以通过在应用的 **info.plist** 文件中设置特定密钥的值，来配置 ATS 的多个功能。 以下项可用于控制 ATS (_缩进，以显示它们的嵌套_) ：

```
NSAppTransportSecurity
    NSAllowsArbitraryLoads
    NSAllowsArbitraryLoadsInWebContent
    NSExceptionDomains
    <domain-name-for-exception-as-string>
        NSExceptionMinimumTLSVersion
        NSExceptionRequiresForwardSecrecy
        NSExceptionAllowsInsecureHTTPLoads
        NSRequiresCertificateTransparency
        NSIncludesSubdomains
        NSThirdPartyExceptionMinimumTLSVersion
        NSThirdPartyExceptionRequiresForwardSecrecy
        NSThirdPartyExceptionAllowsInsecureHTTPLoads
```

每个密钥都具有以下类型和含义：

- **NSAppTransportSecurity** (`Dictionary`) -包含 ATS 的所有设置键和值。
- **NSAllowsArbitraryLoads** (`Boolean`) -如果 `YES` 将对中 **未** 列出的任何域禁用 ATS `NSExceptionDomains` 。 对于列出的域，将使用指定的安全设置。
- **NSAllowsArbitraryLoadsInWebContent** (`Boolean`) -如果 `YES` 将允许在 APPLE 传输安全 (ATS) 保护仍为应用的其余部分启用时，网页可正确加载。
- **NSExceptionDomains** (`Dictionary`) -域的集合，以及 ATS 应对给定域使用的安全设置。
- **\<domain-name-for-exception-as-string>** (`Dictionary`) -给定域 (异常的集合，例如： `www.xamarin.com`).
- **NSExceptionMinimumTLSVersion** (`String`) -最小 TLS 版本为 `TLSv1.0` ， `TLSv1.1` 或 `TLSv1.2` (默认) 。
- **NSExceptionRequiresForwardSecrecy** (`Boolean`) -如果 `NO` 域不必使用带有转发安全的密码。 默认值为 `YES`。
- **NSExceptionAllowsInsecureHTTPLoads** (`Boolean`) -如果 `NO` (默认) 则与此域的所有通信都必须在 `HTTPS` 协议中。
- **NSRequiresCertificateTransparency** (`Boolean`) -如果 `YES` 域的安全套接字层 (SSL) 必须包含有效的透明数据。 默认值为 `NO`。
- **NSIncludesSubdomains** (`Boolean`) -如果 `YES` 这些设置覆盖此域的所有子域。 默认值为 `NO`。
- **NSThirdPartyExceptionMinimumTLSVersion** (`String`) -当域是开发人员的控件之外的第三方服务时使用的 TLS 版本。
- **NSThirdPartyExceptionRequiresForwardSecrecy** (`Boolean`) -如果 `YES` 第三方域需要 "向前保密"。
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** (`Boolean`) -如果 `YES` ATS 允许与第三方域进行不安全的通信。

<a name="optout"></a>

### <a name="opting-out-of-ats"></a>退出 ATS

尽管 Apple 强烈建议使用 `HTTPS` 协议和安全地与基于 internet 的信息通信，但有时这并不总是可行。 例如，如果要与第三方 web 服务进行通信，或在应用程序中使用 internet 交付的广告。

如果你的 Xamarin iOS 应用必须向不安全的域发出请求，则对你的应用的 **info.plist** 文件的以下更改将禁用 ATS 强制用于给定域的安全性默认值：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSExceptionDomains</key>
    <dict>
        <key>www.the-domain-name.com</key>
        <dict>
            <key>NSExceptionMinimumTLSVersion</key>
            <string>TLSv1.0</string>
            <key>NSExceptionRequiresForwardSecrecy</key>
            <false/>
            <key>NSExceptionAllowsInsecureHTTPLoads</key>
            <true/>
            <key>NSIncludesSubdomains</key>
            <true/>
        </dict>
    </dict>
</dict>
```

在 Visual Studio for Mac 中，双击 `Info.plist` " **解决方案资源管理器**中的文件，切换到" **源** "视图并添加上述项：

[![Info.plist 文件的源视图](ats-images/ats01.png)](ats-images/ats01.png#lightbox)

如果你的应用程序需要从非安全站点中加载和显示 web 内容，请将以下内容添加到你的应用程序的 **info.plist** 文件中，以允许在 Apple 传输 SECURITY (ATS) 保护仍为应用的其余部分启用时正确加载网页：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

还可以选择对应用的 **info.plist** 文件进行以下更改，以完全禁用所有域和 internet 通信的 ATS：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

在 Visual Studio for Mac 中，双击 `Info.plist` " **解决方案资源管理器**中的文件，切换到" **源** "视图并添加上述项：

[![Info.plist 文件的源视图](ats-images/ats02.png)](ats-images/ats02.png#lightbox)

> [!IMPORTANT]
> 如果你的应用程序需要连接到不安全的网站，则应 **始终** 使用将该域输入为异常， `NSExceptionDomains` 而不是使用完全关闭 ATS `NSAllowsArbitraryLoads` 。 `NSAllowsArbitraryLoads` 只应在极端紧急情况下使用。

同样，如果切换到安全连接不可用或不实用， _只_ 应将 ATS 禁用。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了应用传输安全 (ATS) ，并介绍了它强制与 internet 进行安全通信的方式。 首先，我们介绍了 ATS 在 iOS 9 上运行的 Xamarin iOS 应用所需的更改。 然后我们介绍了如何控制 ATS 的功能和选项。 最后，我们在 Xamarin iOS 应用中介绍了 ATS。

## <a name="related-links"></a>相关链接

- [iOS 9 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [适用于开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)