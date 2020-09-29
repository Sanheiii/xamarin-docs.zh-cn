---
title: 在 Xamarin 中通过 NSUserActivity 进行搜索
description: 本文档介绍如何为 NSUserActivity 编制索引，使其可在聚光灯和 Safari 中搜索。 本文介绍如何在搜索结果中响应 NSUserActivity 的选择。
ms.prod: xamarin
ms.assetid: 0B28B284-C7C9-4C0D-A782-D471FBBC4CAE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 29ccf115facf9a086db473301f7dfb548a80dc9f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431110"
---
# <a name="search-with-nsuseractivity-in-xamarinios"></a>在 Xamarin 中通过 NSUserActivity 进行搜索

`NSUserActivity` 是在 iOS 8 中引入的，用于提供用于提交的数据。
它允许你在应用程序的特定部分中创建活动，然后将这些活动传递到其他 iOS 设备上运行的应用程序的另一个实例。 然后，接收设备可以继续在前一设备上启动的活动，并从用户离开的位置继续。 有关使用移交的详细信息，请参阅我们 [的移交文档简介](~/ios/platform/handoff.md) 。

新到 iOS 9 的 `NSUserActivity` 索引可以 (公开和私下) 并从聚光灯搜索和 Safari 中搜索。 通过将标记 `NSUserActivity` 为可搜索并添加可索引的元数据，可以在 iOS 设备上的搜索结果中列出该活动。

[![应用历史记录概述](nsuseractivity-images/apphistory01.png)](nsuseractivity-images/apphistory01.png#lightbox)

如果用户从应用程序中选择属于某个活动的搜索结果，将启动该应用，并将 `NSUserActivity` 重新启动并向用户显示所描述的活动。

的以下属性 `NSUserActivity` 用于支持应用搜索：

- `EligibleForHandoff` –如果 `true` 可在移交操作中使用此活动。
- `EligibleForSearch` –如果 `true` 将此活动添加到设备上的索引并显示在搜索结果中，则为。
- `EligibleForPublicIndexing` –如果 `true` 将此活动添加到 Apple 的基于云的索引，并向用户显示尚未在其 iOS 设备上安装应用的 (通过搜索) 。 有关更多详细信息，请参阅下面的 [公共搜索索引](#public-search-indexing) 部分。
- `Title` –为活动提供标题，并将其显示在搜索结果中。 用户还可以搜索标题的文本。
- `Keywords` –一个字符串数组，用于描述您的活动，这些活动将由最终用户进行编制索引和搜索。
- `ContentAttributeSet` – `CSSearchableItemAttributeSet` 用于进一步详细地描述活动，并在搜索结果中提供丰富的内容。
- `ExpirationDate` –如果你希望某个活动仅显示到给定日期，则可以在此处提供该日期。
- `WebpageURL` –如果可以在 web 上查看活动，或如果你的应用支持 Safari 的深层链接，你可以在此处设置要访问的链接。

## <a name="nsuseractivity-quickstart"></a>NSUserActivity 快速入门

按照以下说明 `NSUserActivity` 在你的应用程序中实现可搜索：

- [创建活动类型标识符](#creatingtypeid)
- [创建活动](#createactivity)
- [响应活动](#respondactivity)
- [公共搜索索引](#indexing)

为了使内容可供搜索，还有一些 [其他优点](#benefits) `NSUserActivity` 。

<a name="creatingtypeid"></a>

## <a name="creating-activity-type-identifiers"></a>创建活动类型标识符

在您可以创建搜索活动之前，您需要创建一个 _活动类型标识符_ 来识别它。 活动类型标识符是添加到 `NSUserActivityTypes` 应用的 **info.plist** 文件数组的短字符串，用于唯一标识给定的用户活动类型。 对于应用程序支持的每个活动，数组中将有一个条目，并将其公开给应用搜索。 

Apple 建议对活动类型标识符使用反向 DNS 样式表示法，以避免冲突。 例如： `com.company-name.appname.activity` 对于基于应用的特定活动或 `com.company-name.activity` 可跨多个应用运行的活动。

创建实例时，将使用活动类型标识符 `NSUserActivity` 来标识活动的类型。 当用户点击搜索结果时，如果某个活动继续出现，则活动类型 (连同应用的团队 ID) 确定要启动哪个应用以继续执行该活动。

若要创建所需的活动类型标识符以支持此行为，请编辑 **info.plist** 文件，并切换到 " **源** " 视图。 添加 `NSUserActivityTypes` 密钥并使用以下格式创建标识符：

[![Info.plist 编辑器中的 NSUserActivityTypes 键和必需的标识符](nsuseractivity-images/type01.png)](nsuseractivity-images/type01.png#lightbox)

在上面的示例中，我们为搜索活动创建了一个新的活动类型标识符 (`com.xamarin.platform`) 。 创建自己的应用时，将数组的内容替换为 `NSUserActivityTypes` 特定于应用支持的活动的活动类型标识符。

<a name="createactivity"></a>

## <a name="creating-an-activity"></a>创建活动

下面是为设备上索引托管搜索创建活动的示例：

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.BecomeCurrent();
```

可以通过设置的属性来添加更多详细信息 `ContentAttributeSet` `NSUserActivity` ，如下所示：

[![添加搜索详细信息概述](nsuseractivity-images/apphistory02.png)](nsuseractivity-images/apphistory02.png#lightbox)

通过使用 `ContentAttributeSet` ，您可以创建丰富的搜索结果，以吸引最终用户与之交互。

<a name="respondactivity"></a>

## <a name="responding-to-an-activity"></a>响应活动

若要对应用程序点击搜索结果 (`NSUserActivity`) ，请编辑 **AppDelegate.cs** 文件，然后重写 `ContinueUserActivity` 方法。 例如：

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    }

    return true;
}
```

请注意，这是用于响应移交请求的相同方法重写。 现在，如果用户在聚焦搜索结果中单击应用的链接，则会将应用置于前台 (或启动（如果尚未运行) ），并且将显示该链接所表示的内容、导航或功能：

[![从搜索还原以前的状态](nsuseractivity-images/apphistory03.png)](nsuseractivity-images/apphistory03.png#lightbox)

<a name="indexing"></a>

## <a name="public-search-indexing"></a>公共搜索索引

如前文所述，iOS 9 使你可以轻松地提供对用户已在给定 iOS 设备上发现并使用的应用内容和功能的搜索访问。 使用公共索引时，iOS 9 为尚未发现内容或功能 (或尚未安装应用) 的用户提供一种方法，使其在搜索中也不会获得这些结果。

公共索引的工作方式如下：

1. 为应用程序创建活动时，可以将其标记为 "公共"。
2. 公共活动发送到 Apple 并在云中编制索引。
3. 当更多用户与设备上的应用进行交互并使用活动所表示的特定功能或内容时，它会按级别提升。
4. 即使未安装该应用程序，也会向其他用户提供常用公共结果。
5. 如果活动包含 URL) ，则这些公共结果将显示在聚焦搜索和 Safari (中。

我们可以执行前面创建的专用搜索活动，并将其展开为公共：

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.EligibleForPublicIndexing = true;
activity.BecomeCurrent();
```

由于已通过设置为公共索引设置了活动 `EligibleForPublicIndexing = true` ，因此它并不意味着它会自动添加到 Apple 的公有云索引。 必须首先满足以下条件：

1. 它必须出现在搜索结果中，并被多个用户选择。 在达到活动参与阈值之前，结果将保持为私有。
2. 应用设置可防止任何特定于用户的数据被索引并公开。

<a name="benefits"></a>

## <a name="additional-benefits"></a>其他权益

通过 `NSUserActivity` 在应用中采用应用搜索，你还可以获得以下功能：

- **移交** -由于应用搜索使用与移交 (相同的机制公开内容、导航和/或功能 `NSUserActivity`) ，因此你可以轻松地允许应用的用户在一个设备上启动某个活动，并在另一台设备上继续操作。
- **Siri 建议** -除了 Siri 建议通常做出的标准建议外，还可自动建议应用中的在职。
- **Siri 智能提醒** -用户将能够要求 Siri 提醒用户应用中的活动。

## <a name="related-links"></a>相关链接

- [iOS 9 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [适用于开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [应用搜索编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
- [博客文章 & 示例](https://blog.xamarin.com/improve-discoverability-with-search-in-ios-9/)