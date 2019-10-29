---
title: Xamarin 中的应用搜索增强功能
description: 本文介绍了 Apple 在 iOS 10 中进行的应用搜索的增强功能，以及如何在 Xamarin 中实现这些功能。
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/15/2017
ms.openlocfilehash: f0d638d566290dd2ae0d8453133ee340d5b4ce3f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031582"
---
# <a name="app-search-enhancements-in-xamarinios"></a>Xamarin 中的应用搜索增强功能

_本文介绍了 Apple 在 iOS 10 中进行的应用搜索的增强功能，以及如何在 Xamarin 中实现这些功能。_

在 iOS 10 中，Apple 已对应用搜索进行了多项改进，例如众包的深层链接、应用内搜索、搜索验证结果的延续和可视化。 本文介绍如何在 Xamarin iOS 应用程序中实现这些功能。

## <a name="about-app-search-enhancements"></a>关于应用搜索增强功能

IOS 10 中的核心聚焦为应用搜索提供了多项增强功能，例如：

- **众包深层链接的热门程度（带有差异隐私）** -提供一种在搜索结果中升级深层链接的应用内容的方法。
- **应用内搜索**-使用新的 `CSSearchQuery` 类提供应用内聚焦搜索功能，类似于邮件、邮件和便笺应用的工作方式。
- **搜索延续**-允许用户在聚焦或 Safari 中开始搜索，然后打开应用并继续搜索。
- **验证结果的可视化效果**-Apple 的[应用搜索 API 验证工具](https://search.developer.apple.com/appsearch-validation-tool)现在会在执行测试时显示网站标记和深层链接的可视表示形式。
- **消息应用程序映像共享**-允许使用常用的应用程序内映像在消息中共享（通过消息应用扩展），以便在聚焦搜索中显示。

以下部分将更详细地介绍这些主题。

## <a name="crowdsourced-deep-link-popularity"></a>众包深层链接的热门程度

iOS 10 提供一种机制，用于计算常见的深层链接到应用的频率，并使用此信息来改进搜索结果中应用内容的排名，同时仍然使用差分来保护用户的身份*隐私*。

对于使用 `NSUserActivity` 对象提供深层链接 Url 并将 `EligibleForPublicIndexing` 属性设置为 `true`的应用程序，iOS 10 将*差异隐私哈希*的一个子集提交到 Apple 的服务器。 然后，将使用此信息在搜索结果中升级常用的应用内内容。

有关在 Xamarin iOS 应用程序中实现深层链接的详细信息，请参阅[使用 NSUserActivity 文档进行搜索](~/ios/platform/search/nsuseractivity.md)。

## <a name="in-app-searching"></a>应用内搜索

通过实现新的[CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery)类，应用程序可以提供聚光灯的搜索和匹配规则技术来查找自身内部的内容，而无需用户离开应用（类似于邮件、邮件和便笺应用的工作方式）。

通常，支持 `CSSearchQuery` 的应用程序不需要维护其自己的单独搜索索引。

## <a name="search-continuation"></a>搜索延续

在 iOS 9 中，Apple 引入了搜索 Api （如核心聚焦、`NSUserActivity` 和 web 标记），以便在应用程序中提供内容的深度，使用户能够使用聚光灯和 Safari 搜索界面搜索该内容。 有关更多详细信息，请参阅[新的搜索 api](~/ios/platform/search/index.md)文档。

在 iOS 10 Apple 中，通过允许用户在聚光灯或 Safari 中开始搜索，然后在打开应用时继续搜索。

若要实现此功能，请编辑应用的 `Info.plist` 文件，添加**布尔**类型的 `CoreSpotlightContinuation` 密钥，并将其值设置为 `YES`：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](app-search-enhancements-images/search01.png "Editing CoreSpotlightContinuation in the Info.plist file")](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](app-search-enhancements-images/searchw01.png "Editing CoreSpotlightContinuation in the Info.plist file")](app-search-enhancements-images/search01.png#lightbox)

-----

若要响应用户继续搜索结果（`NSUserActivity`），请编辑 `AppDelegate.cs` 文件并重写 `ContinueUserActivity` 方法。 例如:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchQuery.ContinuationActionType) {
            var search = userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString);
            // Continue user's search here...
        }
        break;
    }

    return true;
}
```

此代码将查找查询继续操作类型（`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`），然后从 `NSUserActivity` 类的用户信息字典（`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`）读取用户的当前查询。 在这里，应用需要采取措施来继续执行用户的搜索。

有关在 Xamarin iOS 应用中使用搜索的详细信息，请参阅[使用核心聚焦](~/ios/platform/search/corespotlight.md)文档进行搜索。

## <a name="visualization-of-validation-results"></a>验证结果的可视化效果

Apple 的[应用搜索 API 验证工具](https://search.developer.apple.com/appsearch-validation-tool)现在显示网站标记的可视化表示形式，并在执行测试时显示深层链接（包括[Schema.org](https://schema.org/)中定义的标记）。

通过使用验证工具，开发人员可以查看 Applebot Web 爬网程序为网站编制索引的信息，例如标题、说明、URL 和任何其他受支持的元素。

有关使用 Web 标记的详细信息，请参阅[使用 Web 标记进行搜索](~/ios/platform/search/web-markup.md)文档。

## <a name="message-app-image-sharing"></a>消息应用图像共享

如果消息应用扩展提供了用于在消息中共享的映像，则可以将该扩展配置为允许用户在消息中执行热门图像的聚焦搜索，而无需离开应用。

若要启用此功能，请执行以下操作：

1. 创建消息应用扩展。
2. 将 `com.apple.developer.associated-domains` 添加到应用的权利，并包括托管消息应用扩展所共享的映像的 web 域的列表。 为每个域指定 `spotlight-image-search` 服务。
3. 将 `apple-app-site-association` 文件添加到承载图像的网站。 此文件包含 `spotlight-image-search` 服务的字典，并包含应用的 ID，该 ID 是团队 ID 或应用 ID 前缀，后跟捆绑 ID。 该文件最多可包含500个路径和模式，这些路径和模式将按聚光灯编制索引，并包含在常用图像搜索中。 有关详细信息，请参阅 Apple 的[创建和上传关联文件](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4)文档。
4. 允许 Applebot 对网站爬网。 [有关 Applebot](https://support.apple.com/HT204683)文档，请参阅 Apple 的相关文档。

有关更多详细信息，请参阅我们的[消息应用集成](~/ios/platform/message-app-integration/index.md)文档。

## <a name="summary"></a>总结

本文介绍了 Apple 在 iOS 10 中进行的应用搜索的增强功能，以及如何在 Xamarin 中实现这些功能。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
