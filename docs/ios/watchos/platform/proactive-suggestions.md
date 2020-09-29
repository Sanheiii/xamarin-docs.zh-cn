---
title: Xamarin 中的 watchOS 主动建议
description: 本文介绍如何在 watchOS 3 应用程序中使用前瞻性建议，使系统能够自动向用户显示有用的信息。
ms.prod: xamarin
ms.assetid: 10CC9F16-963C-44F1-8B98-F09FB2310DFF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 246757ddc43c5480b26ab4c1360a036fd111dcfa
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435350"
---
# <a name="watchos-proactive-suggestions-in-xamarin"></a>Xamarin 中的 watchOS 主动建议

_本文介绍如何在 watchOS 3 应用程序中使用前瞻性建议，使系统能够自动向用户显示有用的信息。_

新到 watchOS 3 的前瞻性建议为用户提供了新闻方法，使用户能够在适当的时间自动向用户提供有用的信息，从而与 Xamarin iOS 应用程序进行联系。

## <a name="about-proactive-suggestions"></a>关于主动建议

WatchOS 3 中的新增功能 `NSUserActivity` 包含一个属性，该 `MapItem` 属性允许应用程序提供可在其他上下文中使用的位置信息。 例如，如果显示 "酒店" 的应用查看并提供一个 `MapItem` 位置，则在用户切换到地图应用时，他们只是在其上查看的旅馆位置可用。

应用使用 `NSUserActivity` 、MapKit、Media Player 和 UIKit 等技术集合向系统公开此功能。 此外，通过提供对应用程序的主动建议支持，它会免费获得更深入的 Siri 集成。

## <a name="location-based-suggestions"></a>基于位置的建议

WatchOS 3 的新手， `NSUserActivity` 该类包含一个属性，该 `MapItem` 属性允许开发人员提供可在其他上下文中使用的位置信息。 例如，如果应用显示餐馆评论，开发人员可将 `MapItem` 属性设置为用户在应用中查看的餐馆的位置。 如果用户切换到 Maps 应用，则餐馆的位置会自动可用。

如果应用支持应用搜索，则可以使用类的新地址组件 `CSSearchableItemAttributesSet` 来指定用户可能想要访问的位置。 通过设置 `MapItem` 属性，其他属性会自动填充。

除了设置 `Latitude` `Longitude` address 组件属性的和以外，还建议应用还提供 `NamedLocation` 和 `PhoneNumbers` 属性，因此 Siri 可以启动对该位置的调用。

## <a name="contextual-siri-reminders"></a>上下文 Siri 提醒

允许用户在以后查看当前正在应用中查看的内容时，用户可以使用 Siri 快速提醒。 例如，如果他们在应用中查看餐馆评论，则他们可以调用 Siri，并说 *"我在家时提醒我"。* Siri 会生成提醒，其中包含应用中评审的链接。

## <a name="implementing-proactive-suggestions"></a>实现前瞻性建议

向 Xamarin iOS 应用程序添加主动建议支持通常与实现几个 Api 或扩展应用程序可能已经实现的几个 Api 一样简单。

主动建议使用以下三种主要方式使用应用：

- **`NSUserActivity`** -帮助系统了解用户当前在屏幕上使用的信息。
- **位置建议** -如果应用提供或使用基于位置的信息，则这些 API 扩展提供了新的方法来跨应用共享此信息。

在应用程序中，通过实现以下内容来支持和：

- IOS 10 中的**上下文 Siri 提醒**已 `NSUserActivity` 扩展为允许 Siri 快速提醒查看当前在应用程序中查看的内容。
- **位置建议** -iOS 10 增强 `NSUserActivity` 了捕获在应用内查看的位置，并在整个系统的多个位置中进行升级。
- **上下文 Siri 请求**  -  `NSUserActivity`为在应用程序中提供的信息提供上下文以 Siri，以便用户可以从应用程序中获取指示或调用 Siri 的调用。

所有这些功能都有一个共同之处，它们都使用 `NSUserActivity` 一种窗体或其他功能来提供其功能。 

## <a name="nsuseractivity"></a>NSUserActivity

如上所述， `NSUserActivity` 帮助系统了解用户当前在屏幕上使用的信息。 `NSUserActivity` 是一种轻型状态缓存机制，用于在用户浏览应用时捕获用户的活动。 例如，查看餐馆应用：

[![餐馆应用](proactive-suggestions-images/activity02.png)](proactive-suggestions-images/activity02.png#lightbox)

具有以下交互：

1. 用户使用应用时，会创建一个， `NSUserActivity` 以便在以后重新创建该应用的状态。
2. 如果用户搜索餐馆，则会遵循相同的创建活动模式。
3. 再次查看结果时。 在最后一种情况下，用户正在查看某个位置，在 iOS 10 中，系统更了解某些概念 (如位置或通信交互) 。

仔细查看最后一个屏幕：

[![NSUserActivity 有效负载](proactive-suggestions-images/activity03.png)](proactive-suggestions-images/activity03.png#lightbox)

此时，该应用程序正在创建 `NSUserActivity` ，并且已填充了信息以便以后重新创建状态。 该应用程序还包含某些元数据，如位置的名称和地址。 创建此活动后，应用程序会让 iOS 知道它代表用户的当前状态。

然后，该应用会确定是否会通过无线方式播发活动，并将其保存为位置建议的临时值，或添加到设备聚焦索引以在搜索结果中显示。

有关移交和聚焦搜索的详细信息，请参阅我们的 [移交简介](~/ios/platform/handoff.md) 和 [IOS 9 全新搜索 api](~/ios/platform/search/index.md) 指南。

### <a name="creating-an-activity"></a>创建活动

创建活动之前，需要创建一个活动类型标识符来识别它。 活动类型标识符是添加到 `NSUserActivityTypes` 应用 `Info.plist` 文件（用于唯一标识给定用户活动类型）数组的短字符串。 对于应用程序支持的每个活动，数组中将有一个条目，并将其公开给应用搜索。 有关更多详细信息，请参阅 [创建活动类型标识符引用](~/ios/platform/search/nsuseractivity.md) 。

查看活动示例：

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Inform system of Activity
activity.BecomeCurrent();
```

使用活动类型标识符创建新活动。 接下来，创建一些定义活动的元数据，以便以后可以还原此状态。 然后，为活动提供一个有意义的标题，并将其附加到用户信息。 最后，会启用某些功能，并将活动发送到系统。

上面的代码可以进一步增强，以包括通过进行以下更改向活动提供上下文的元数据：

```csharp
...

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Inform system of Activity
activity.BecomeCurrent();
```

如果开发人员有一个网站，该网站能够与应用程序显示相同的信息，则该应用程序可以包含 URL，并且内容可以在未通过移交)  (安装应用的其他设备上显示：

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>还原活动

若要对应用程序点击搜索结果 (`NSUserActivity`) ，请编辑 **AppDelegate.cs** 文件并重写 `ContinueUserActivity` 方法。 例如：

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

确保这是与上面创建的活动相同的活动类型标识符 (`com.xamarin.platform`) 。 应用使用存储在中的信息 `NSUserActivity` 将状态还原回用户离开的位置。

### <a name="benefits-of-creating-an-activity"></a>创建活动的好处

由于上面提供的代码量最少，应用现在可以利用三个新的 iOS 10 功能：

- **Handoff**
- **聚光灯搜索**
- **上下文 Siri 提醒**

以下部分将介绍如何启用另外两个新的 iOS 10 功能：

- **位置建议**
- **上下文 Siri 请求**

### <a name="location-based-suggestions"></a>基于位置的建议 

请看上方餐馆搜索应用的示例。 如果它已经实现 `NSUserActivity` 并正确填充了所有的元数据和属性，则用户将能够执行以下操作：

1. 在应用程序中查找要与朋友见面的餐厅。
2. 如果用户切换到 Maps 应用，则会自动将餐馆的地址建议为目标。
3. 这甚至适用于支持)  (第三方应用程序 `NSUserActivity` ，因此用户可以切换到 "应用程序"，并将餐馆的地址自动作为目标提供。
4. 它还向 Siri 提供上下文，因此用户可以在餐馆应用中调用 Siri，并请求 *"获取行车路线 ..."* 和 Siri 将向用户正在查看的餐厅提供行车路线。

上述所有功能都有一个共同之处，它们都表示建议最初来自何处。 在上述示例中，它是虚构的餐馆评论应用。

watchOS 3 已得到了增强，可通过对现有框架进行几项小型修改和添加操作来为应用程序启用此功能：

- `NSUserActivity` 具有用于捕获在应用内查看的位置信息的附加字段。
- 已对 MapKit 和 CoreSpotlight 进行了几项添加以捕获位置。
- 位置感知功能已添加到系统中的 Siri、Maps、多任务和其他应用。

若要实现基于位置的建议，请从上面提供的相同活动代码开始：

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");

// Inform system of Activity
activity.BecomeCurrent();
```

如果应用使用的是 MapKit，则只需要将当前地图添加 `MKMapItem` 到活动即可：

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

如果应用未使用 MapKit，则它可以采用 "应用搜索" 并为 "位置" 指定以下新属性：

```csharp
// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
...

attributes.NamedLocation = "Apple Inc.";
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

请详细了解上述代码。 首先，每个实例中都需要位置的名称：

```csharp
attributes.NamedLocation = "Apple Inc.";
```

然后，基于文本的实例所需的基于文本的说明 (例如 QuickType 键盘) ：

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

纬度和经度是可选的，但请确保将用户路由到应用程序要将其发送到的确切位置：

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

通过设置电话号码，应用可以获取对 Siri 的访问权限，以便用户可以从应用程序中调用 Siri，方法如下所示：

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

最后，该应用程序可以指示该实例是否适用于导航和电话呼叫：

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

## <a name="activities-best-practices"></a>活动最佳实践

Apple 建议在使用活动时采用以下最佳做法：

- 用于 `NeedsSave` 迟缓负载更新。
- 确保保持对当前活动的强引用。
- 仅传输包含足够信息的小负载以还原状态。
- 请确保活动类型标识符是唯一的，并使用反向 DNS 表示法指定它们进行描述性。 

## <a name="consuming-location-suggestions"></a>使用位置建议

下一部分将介绍来自 (系统其他部分（如 Maps 应用) 或其他第三方应用）的消费位置建议。

## <a name="routing-apps-and-locations-suggestions"></a>路由应用和位置建议

本部分将直接查看路由应用中的使用位置建议。 为了使路由应用添加此功能，开发人员将利用现有框架， `MKDirectionsRequest` 如下所示：

- 在多任务中升级应用程序。
- 将应用注册为路由应用。
- 处理使用 MapKit 对象启动应用程序的情况 `MKDirectionsRequest` 。
- 为 watchOS 提供根据用户参与建议应用的能力。

当应用程序使用 MapKit 对象启动时 `MKDirectionsRequest` ，它应自动开始向请求的位置提供用户指示，或提供一个用户界面，使用户能够轻松开始获取行车路线。 例如：

```csharp
using System;
using Foundation;
using UIKit;
using MapKit;
using CoreLocation;

namespace MonkeyChat
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate, IUISplitViewControllerDelegate
    {
        ...

        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            if (MKDirectionsRequest.IsDirectionsRequestUrl (url)) {
                var request = new MKDirectionsRequest (url);
                var coordinate = request.Destination?.Placemark.Location?.Coordinate;
                var address = request.Destination.Placemark.AddressDictionary;
                if (coordinate.IsValid()) {
                    var geocoder = new CLGeocoder ();
                    geocoder.GeocodeAddress (address, (place, err) => {
                        // Handle the display of the address

                    });
                }
            }

            return true;
        }
    }
}
```

请查看此代码的详细信息。 它将对其进行测试，以确定它是否为有效的目标请求：

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

如果是，则它 `MKDirectionsRequest` 从 URL 创建：

```csharp
var request = new MKDirectionsRequest(url);
```

WatchOS 3 中的新增功能，可将应用发送到不具有地理坐标的地址，因为这会导致开发人员需要对地址进行编码：

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address

});

```

## <a name="summary"></a>总结

本文介绍了前瞻性建议，并展示了开发人员如何使用它们来驱动到适用于 watchOS 的 Xamarin iOS 应用的流量。 本文介绍了实现前瞻性建议并提供使用准则的步骤。

## <a name="related-links"></a>相关链接

- [watchOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2bwatchOS)
- [SiriKit 编程指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)