---
title: 在 Xamarin 中搜索 Api
description: 本文介绍如何使用 iOS 9 提供的新应用搜索 Api，以允许用户在 Xamarin iOS 应用中搜索信息和功能。
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 63daa2f449685b39cb7d622790fd3bccd7590f62
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433220"
---
# <a name="search-apis-in-xamarinios"></a>在 Xamarin 中搜索 Api

_本文介绍如何使用 iOS 9 提供的应用搜索 Api，以允许用户在 Xamarin iOS 应用中搜索信息和功能。_

已在 iOS 9 中展开搜索，以提供更好的新方法来访问 Xamarin iOS 应用中的信息和功能。 使用新的应用搜索 Api，可通过聚焦和 Safari 搜索结果、移交和 Siri 提醒和建议使应用内容可供搜索。 这样，用户就可以快速访问应用中的活动和信息。

此外，新的搜索 Api 使您可以更轻松地在应用程序中集成搜索，而无需事先搜索实现。 因此，Apple 声称，通常需要几个小时才能使用应用搜索来统一搜索 iOS 9 应用的内容。

[![使用应用搜索可全面搜索的 iOS 9 应用内容示例](images/intro01.png)](images/intro01.png#lightbox)

应用搜索由三个单独的 Api 组成：

1. [**NSUserActivity**](nsuseractivity.md) -这是 Apple 在 iOS 8 中发布的移交 API 的扩展。 它用于使应用交互历史记录可供用户公开和私下) 。

2. [**核心聚焦**](corespotlight.md) -允许应用对其内容编制索引，使其显示在搜索结果中。 它的工作方式类似于数据库 API，可在其中添加和删除项，这是在应用程序中对私有内容进行索引的最佳方式。

3. [**WebMarkup**](web-markup.md) -适用于通过 web 界面提供其内容访问权限的应用， (不仅从应用) 内。 Web 内容可以使用将由 Apple 爬网的特殊链接进行标记，并在用户的 iOS 9 设备上提供与你的应用程序的深层链接。

## <a name="selecting-an-app-search-approach"></a>选择应用搜索方法

确定要实现的这些方法中的哪一种取决于您的应用程序提供的交互类型及其提供的内容类型。

使用以下指南：

- [**NSUserActivity**](nsuseractivity.md) –使用此框架可为公共内容和专用内容提供可搜索性，并在应用中同时可搜索性导航点。

- [**核心聚焦**](corespotlight.md) –使用此框架为存储在设备上的专用数据提供可搜索性。

- [**Web 标记**](web-markup.md) –使用此框架为应用程序提供可搜索性，这些应用程序不仅可以从应用内提供，还可以从应用的网站提供。

每个应用搜索方法都是独特的，可以单独使用，但是 Apple 设计它们一起工作。 使用多种方法来索引特定项时，请确保对每种方法使用相同的 **项 ID** ，以便单个链接一起工作。

使用多种方法不仅可以确保最终用户能够找到您的内容，而且还有助于改进您在搜索范围内的项目排名。

虽然排名过程对开发人员而言是透明的，但用户与给定项的交互很严重地与此排名 (例如，用户轻按) 的链接。
通过提供丰富的信息性项，可以确保用户 enticed 与内容进行交互，从而提高其排名。

## <a name="what-content-to-index"></a>要编制索引的内容

Apple 提供以下建议，其中包含在你的应用中为提供搜索索引的内容和操作：

- 用户从应用程序中查看、创建或特选的任何内容。
- 应用中的导航点和功能。
- 最近下载到设备的应用显示的新消息、内容或其他类型的项目。

## <a name="app-search-enhancements"></a>应用搜索增强功能

IOS 10 中的核心聚焦为应用搜索提供了多项增强功能，例如：

- **使用差异隐私)  (深层链接热门程度 ** -提供一种在搜索结果中升级深层链接的应用内容的方法。
- **应用内搜索** -使用新 `CSSearchQuery` 类提供应用内聚焦搜索功能，类似于邮件、邮件和便笺应用的工作方式。
- **搜索延续** -允许用户在聚焦或 Safari 中开始搜索，然后打开应用并继续搜索。
- **验证结果的可视化效果** -Apple 的 [应用搜索 API 验证工具](https://search.developer.apple.com/appsearch-validation-tool) 现在会在执行测试时显示网站标记和深层链接的可视表示形式。
- **消息应用程序映像共享** -允许通过消息应用扩展在消息 (中共享常用的应用程序映像，) 在聚焦搜索中显示。

若要了解详细信息，请参阅 [应用搜索增强](~/ios/platform/search/app-search-enhancements.md) 指南。

### <a name="proactive-suggestions"></a>主动建议

iOS 10 提供了新的方法，可让系统在适当的时间自动向用户提供有用的信息，从而促进应用程序的参与。 正如 iOS 9 提供的功能，可以使用聚光灯、移交和 Siri 建议以及 iOS 10 向应用添加深层搜索，应用程序可以从以下位置公开系统可向用户显示的功能：

- 应用切换器
- 锁定屏幕
- CarPlay
- Maps
- Siri 交互
- QuickType 建议 

应用使用 [NSUserActivity](xref:Foundation.NSUserActivity)、web 标记、核心聚焦、MapKit、Media Player 和 UIKit 等技术集合向系统公开此功能。

若要了解详细信息，请参阅 [前瞻性建议](~/ios/platform/search/proactive-suggestions.md) 指南。

## <a name="summary"></a>总结

本文介绍了 iOS 9 为 Xamarin iOS 应用提供的新搜索 API 功能。 它涵盖了 [NSUserActivity](nsuseractivity.md)、用于索引内容的 [核心聚光灯](corespotlight.md) 和 [Web 标记](web-markup.md) 方法。 完成时，只需简短地讨论何时应使用给定的搜索方法和应编制索引的内容类型。

## <a name="related-links"></a>相关链接

- [iOS 9 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [适用于开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [应用搜索编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)