---
title: 在 Xamarin 中通过核心聚焦搜索
description: 本文档介绍如何在 Xamarin iOS 应用程序中使用核心聚焦来提供应用内内容的链接。 本文介绍了如何创建、还原、更新和删除可搜索的项。
ms.prod: xamarin
ms.assetid: 1374914C-0F63-41BF-BD97-EBCEE86E57B1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 684e2b8ad5098dd9cecb14eb9c5e265e3ea53035
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436123"
---
# <a name="search-with-core-spotlight-in-xamarinios"></a>在 Xamarin 中通过核心聚焦搜索

核心聚焦是适用于 iOS 9 的新框架，该框架提供了一个类似数据库的 API，用于添加、编辑或删除应用中内容的链接。 使用核心聚焦添加的项将在 iOS 设备上的聚焦搜索中提供。

有关可以使用核心聚焦编制索引的内容类型的示例，请查看 Apple 的邮件、邮件、日历和便笺应用。 它们当前都使用核心聚光灯来提供搜索结果。

## <a name="creating-an-item"></a>创建项

下面是一个示例，其中创建了一个项，并使用核心聚光灯为其编制索引：

```csharp
using CoreSpotlight;
...

// Create attributes to describe an item
var attributes = new CSSearchableItemAttributeSet();
attributes.Title = "App Center Test";
attributes.ContentDescription = "Automatically test your app on 1,000 devices in the cloud.";

// Create item
var item = new CSSearchableItem ("1", "products", attributes);

// Index item
CSSearchableIndex.DefaultSearchableIndex.Index (new CSSearchableItem[]{ item }, (error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

此信息将在搜索结果中显示如下：

[![核心聚焦搜索结果概述](corespotlight-images/corespotlight01.png)](corespotlight-images/corespotlight01.png#lightbox)

## <a name="restoring-an-item"></a>还原项

当用户通过应用的核心聚焦点击添加到搜索结果中的项时，将 `AppDelegate` `ContinueUserActivity` 调用方法 (此方法也用于 `NSUserActivity`) 。 例如：

```csharp
public override bool ContinueUserActivity (UIApplication application,
   NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchableItem.ActionType.ToString ()) {
            // Display content for searchable item...
        }
        break;
    }

    return true;
}
```

请注意，这次我们要检查是否有的活动 `ActivityType` `CSSearchableItem.ActionType` 。

## <a name="updating-an-item"></a>更新项

在某些情况下，我们使用核心聚光灯创建的索引项需要修改，如需要更改标题或缩略图。 若要进行此更改，我们使用的方法与最初创建索引时使用的方法相同。
我们 `CSSearchableItem` 使用与创建项相同的 ID 创建新的，并附加一个 `CSSearchableItemAttributeSet` 包含修改后的属性的新的：

[![更新项概述](corespotlight-images/corespotlight02.png)](corespotlight-images/corespotlight02.png#lightbox)

将此项写入可搜索索引时，将用新信息更新现有项。

## <a name="deleting-an-item"></a>删除项

核心聚焦提供多种方法，用于在不再需要索引项时将其删除。

首先，可以按其标识符删除项，例如：

```csharp
// Delete Items by ID
CSSearchableIndex.DefaultSearchableIndex.Delete(new string[]{"1","16"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

接下来，可以按其域名删除一组索引项。 例如：

```csharp
// Delete by Domain Name
CSSearchableIndex.DefaultSearchableIndex.DeleteWithDomain(new string[]{"domain-name"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

最后，您可以删除具有以下代码的所有索引项：

```csharp
// Delete all index items
CSSearchableIndex.DefaultSearchableIndex.DeleteAll((error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

## <a name="additional-core-spotlight-features"></a>其他核心聚焦功能

核心聚焦具有以下功能，可帮助保持索引准确并保持最新状态：

- **批处理更新支持** –如果你的应用程序需要同时创建或修改大组索引，则可以通过一次调用将整个批处理发送到 `Index` 类的方法 `CSSearchableIndex` 。
- **响应索引更改** –使用您的 `CSSearchableIndexDelegate` 应用程序可以响应可搜索索引中的更改和通知。
- **应用数据保护** –使用数据保护类，可以对使用核心聚焦添加到可搜索索引的项实施安全性。

## <a name="related-links"></a>相关链接

- [iOS 9 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [适用于开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [应用搜索编程指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)