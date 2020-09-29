---
title: 在 Xamarin 中用 Web 标记搜索
description: 本文档介绍如何创建链接回 Xamarin iOS 应用的基于 web 的搜索结果。 它讨论了如何启用 web 内容索引，从而使你的应用程序的网站可以发现、使用智能应用横幅、通用链接等。
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: b578d1d171c6b8e91e76758f4c979fbc8a1b6eaa
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436995"
---
# <a name="search-with-web-markup-in-xamarinios"></a>在 Xamarin 中用 Web 标记搜索

对于通过网站提供对其内容的访问权限的应用程序 (不仅可在应用) 内进行标记，web 内容可以通过 Apple 爬网的特殊链接进行标记，并在用户的 iOS 9 设备上提供对应用的深层链接。

如果你的 iOS 应用已经支持移动深层链接，并且你的网站提供了指向应用中内容的深层链接，Apple 的 _Applebot_ web 爬网程序将为此内容编制索引，并自动将其添加到其云索引中：

[![云索引概述](web-markup-images/webmarkup01.png)](web-markup-images/webmarkup01.png#lightbox)

Apple 会在聚焦搜索和 Safari 搜索结果中显示这些结果。
如果用户点击其中一个结果 (并安装了应用) 则会将其转到应用中的内容：

[![从搜索结果中的网站进行深层链接](web-markup-images/webmarkup02.png)](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>启用 Web 内容索引

要使你的应用内容可使用 Web 标记搜索，需要执行四个步骤：

1. 通过在 iTunes Connect 中将其定义为 **支持** 或 **市场营销** 网站，确保 Apple 能够发现并索引应用的网站。
2. 确保应用的网站包含实现移动深层链接所需的标记。 有关更多详细信息，请参阅下面的部分。
3. 启用 iOS 应用中的深层链接处理。
4. 为应用的网站上显示的结构化数据添加标记，以向最终用户提供丰富且具有吸引力的结果。 尽管此步骤并非严格需要，但我们强烈建议使用此步骤。

以下各节将详细介绍这些步骤。

## <a name="make-your-apps-website-discoverable"></a>使你的应用程序的网站可发现

若要让 Apple 查找应用的网站，最简单的方法是在将应用提交到 Apple 时使用 iTunes Connect 将其用作 **支持** 或 **营销** 网站。

## <a name="using-smart-app-banners"></a>使用智能应用横幅

在网站上提供智能应用横幅，以向应用提供清晰的链接。 如果应用尚未安装，Safari 会自动提示用户安装应用。 否则，可以点击 " **查看** " 链接从网站启动你的应用。 例如，若要创建智能应用横幅，可以使用以下代码：

```html
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

有关详细信息，请参阅 Apple [通过智能应用横幅文档提升应用](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html) 。

## <a name="using-universal-links"></a>使用通用链接

对于 iOS 9 的新手，通用链接通过提供以下内容提供更好的智能应用横幅或现有自定义 URL 方案的替代方案：

- **唯一** –同一个 URL 不能由多个网站声明。
- **安全** –网站需要一个签名证书，以确保网站拥有并有效地链接到你的应用程序。
- **灵活** –最终用户可以控制 URL 是启动网站还是应用程序。
- **通用** –可以使用相同的 URL 来定义网站和应用内容。

## <a name="using-twitter-cards"></a>使用 Twitter 卡

可以使用 Twitter 卡提供指向应用内容的深层链接。 例如：

```html
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

有关详细信息，请参阅 Twitter 的 [Twitter 卡协议](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/overview/abouts-cards) 文档。

## <a name="using-facebook-app-links"></a>使用 Facebook 应用链接

可使用 Facebook 应用链接提供指向应用内容的深层链接。 例如：

```html
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

有关详细信息，请参阅 Facebook 的 [应用链接](https://developers.facebook.com/docs/applinks) 文档。

## <a name="opening-deep-links"></a>打开深层链接

需要在 Xamarin iOS 应用中添加对打开和显示深层链接的支持。 编辑 **AppDelegate.cs** 文件并重写 `OpenURL` 方法以处理自定义 URL 格式。 例如：

```csharp
public override bool OpenUrl (UIApplication application, NSUrl url, string sourceApplication, NSObject annotation)
{

  // Handling a URL in the form http://company.com/appname/?123
  try {
    var components = new NSUrlComponents(url,true);
    var path = components.Path;
    var query = components.Query;

    // Is this a known format?
    if (path == "/appname") {
      // Display the view controller for the content
      // specified in query (123)
      return ContentViewController.LoadContent(query);
    }
  } catch {
    // Ignore issue for now
  }

  return false;
}
```

在上面的代码中，我们将查找包含的 URL `/appname` ，并将 `query` 此示例中 (的值传递 `123`) 到应用中的自定义视图控制器，以便向用户显示请求的内容。

## <a name="providing-rich-results-with-structured-data"></a>通过结构化数据提供丰富的结果

通过包含结构化数据标记，您可以向最终用户提供丰富的搜索结果，而不仅仅是标题和说明。 使用结构化数据标记，包括图像、特定于应用的数据 (例如评级) 和操作。

通过引诱更多用户与它们进行交互，可以更好地利用各种结果，从而帮助改进基于云的搜索索引中的排名。

提供结构化数据标记的一种方法是使用打开关系图。 例如：

```html
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

有关详细信息，请参阅 [开放图形](https://ogp.me) 网站。

结构化数据标记的另一种常见格式是架构. org 的微数据格式。 例如：

```html
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
  <span itemprop="ratingValue">4** stars -
  <span itemprop="reviewCount">255** reviews
```

相同的信息可以用架构. org 的 JSON-LD 格式表示：

```html
<script type="application/ld+json">
  "@content":"http://schema.org",
  "@type":"AggregateRating",
  "ratingValue":"4",
  "reviewCount":"255"
</script>
```

下面显示了网站中的元数据的示例，该示例向最终用户提供丰富的搜索结果：

[![通过结构化数据标记丰富的搜索结果](web-markup-images/deeplink01.png)](web-markup-images/deeplink01.png#lightbox)

Apple 目前支持 schema.org 中的以下架构类型：

- AggregateRating
- ImageObject
- InteractionCount
- 产品
- 组织
- PriceRange
- 配方
- SearchAction

有关这些方案类型的详细信息，请参阅 [schema.org](https://schema.org)。

## <a name="providing-actions-with-structured-data"></a>提供结构化数据的操作

特定类型的结构化数据将允许最终用户操作搜索结果。 目前支持以下操作：

- 拨打电话号码。
- 获取给定地址的映射方向。
- 播放音频或视频文件。

例如，定义用于拨打电话号码的操作可能如下所示：

```html
<div itemscope itemtype="http://schema.org/Organization">
  <span itemprop="telephone">(408) 555-1212**
```

当向最终用户显示此搜索结果时，结果中会显示一个小的手机图标。 如果用户点击该图标，将调用指定的数字。

以下 HTML 将添加一个操作，以便从搜索结果播放音频文件：

```html
<div itemscope itemtype="http://schema.org/AudioObject">
  <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**
```

最后，以下 HTML 将添加一个操作以从搜索结果中获取说明：

```html
<div itemscope itemtype="http://schema.org/PostalAddress">
  <span itemprop="streetAddress">1 Infinite Loop**
  <span itemprop="addressLocality">Cupertino**
  <span itemprop="addressRegion">CA**
  <span itemprop="postalCode">95014**
```

有关详细信息，请参阅 Apple 的 [应用搜索开发人员网站](https://developer.apple.com/ios/search/)。

## <a name="related-links"></a>相关链接

- [iOS 9 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [适用于开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [应用搜索编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)