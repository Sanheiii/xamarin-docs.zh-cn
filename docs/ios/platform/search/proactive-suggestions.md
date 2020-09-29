---
title: Xamarin 中的主动建议简介
description: 本文介绍如何在 Xamarin iOS 应用程序中使用前瞻性建议，使系统能够自动向用户显示有用的信息。
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 85879026639dbf92e1c85881e57124e802803bb3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436982"
---
# <a name="introduction-to-proactive-suggestions-in-xamarinios"></a>Xamarin 中的主动建议简介

_本文介绍如何在 Xamarin iOS 应用程序中使用前瞻性建议，使系统能够自动向用户显示有用的信息。_

从 iOS 10 开始，主动建议为用户提供了新闻方法，使用户能够在适当的时间自动向用户提供有用的信息。

iOS 10 提供了新的方法，可让系统在适当的时间自动向用户提供有用的信息，从而促进应用程序的参与。 就像 iOS 9 提供了向应用程序添加深层搜索的能力，使用聚光灯、移交和 Siri 建议 (查看 [新的搜索 api](~/ios/platform/search/index.md)) ，使用 iOS 10，应用程序可以从以下位置公开系统可向用户显示的功能：

- 应用切换器
- 锁定屏幕
- CarPlay
- Maps
- Siri 交互
- QuickType 建议

此应用使用一系列技术（如 `NSUserActivity` web 标记、核心聚焦、MapKit、Media Player 和 UIKit）向系统公开此功能。 此外，通过提供对应用程序的主动建议支持，它会免费获得更深入的 Siri 集成。

## <a name="location-based-suggestions"></a>基于位置的建议

IOS 10 新增， `NSUserActivity` 该类包含一个 `MapItem` 属性，该属性允许开发人员提供可在其他上下文中使用的位置信息。 例如，如果应用显示餐馆评论，开发人员可将 `MapItem` 属性设置为用户在应用中查看的餐馆的位置。 如果用户切换到 Maps 应用，则餐馆的位置会自动可用。

如果应用支持应用搜索，则可以使用类的新地址组件 `CSSearchableItemAttributesSet` 来指定用户可能想要访问的位置。 通过设置 `MapItem` 属性，其他属性会自动填充。

除了设置 `Latitude` `Longitude` address 组件属性的和以外，还建议应用还提供 `NamedLocation` 和 `PhoneNumbers` 属性，因此 Siri 可以启动对该位置的调用。

## <a name="web-markup-based-suggestions"></a>基于 Web 标记的建议

添加了 iOS 9，使其能够在网站中包含结构化数据标记（丰富用户在聚焦和 Safari 搜索结果中看到的内容） (参阅 " [通过 Web 标记搜索](~/ios/platform/search/web-markup.md) ") 。 iOS 10 增加了包含基于位置的标记 (如[Schema.org](https://schema.org/)) 定义的[PostalAddress](https://schema.org/PostalAddress) ，以进一步增强用户体验。 例如，如果用户在网站上查看标记为 "页面" 的位置，则当其打开地图时，系统可以建议相同的位置。

## <a name="text-based-suggestions"></a>基于文本的建议

UIKit 已在 iOS 10 中展开，以包括[UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits)类的[TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype)属性，以指定文本区域中内容的语义含义。 利用此信息，系统通常可以自动选择相应的键盘类型，提高自动更正建议并主动集成其他应用程序和网站中的信息。

例如，如果用户在标记的文本字段中输入文本 `UITextContentType.FullStreetAddress` ，则系统可以建议使用用户最近查看的位置自动填充字段。

## <a name="media-based-suggestions"></a>基于媒体的建议

如果应用使用 [MPPlayableContentManager](xref:MediaPlayer.MPPlayableContentManager) API 播放媒体，则 iOS 10 允许用户在锁屏界面上查看唱片集画面并通过应用播放媒体。

## <a name="contextual-siri-reminders"></a>上下文 Siri 提醒

允许用户在以后查看当前正在应用中查看的内容时，用户可以使用 Siri 快速提醒。 例如，如果他们在应用中查看餐馆评论，则他们可以调用 Siri，并说 *"我在家时提醒我"。* Siri 会生成提醒，其中包含应用中评审的链接。

## <a name="contact-based-suggestions"></a>基于联系人的建议

允许应用的 "联系人" (和联系相关信息) 显示在 iOS 设备上的 **联系人** 应用中，以及存储在系统中的所有用户联系人。

## <a name="ride-sharing-based-suggestions"></a>基于建议的基于共享的建议

如果应用程序使用 [MKDirectionsRequest](xref:MapKit.MKDirectionsRequest) API，则 iOS 10 会在用户可能想要一段时间后，将其作为应用程序切换器中的一个选项提供。 还必须通过 `MKDirectionsModeRideShare` 在其文件中指定 [MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html) 密钥的来将应用注册为应用程序共享应用 `Info.plist` 。

如果应用仅支持 "向共享"，则系统建议将以 *"获取 ..."* 开头，如果支持其他类型的路由方向 (例如审核或自行车) ，系统将使用 *"获取行车路线 ..."*

> [!IMPORTANT]
> 应用接收的 [MKMapItem](xref:MapKit.MKMapItem) 对象可能不包括经度和纬度信息，并且需要地理编码。

## <a name="implementing-proactive-suggestions"></a>实现前瞻性建议

向 Xamarin iOS 应用程序添加主动建议支持通常与实现几个 Api 或扩展应用程序可能已经实现的几个 Api 一样简单。

主动建议使用以下三种主要方式使用应用：

- ** `NSUserActivity` 和 Schema.org**  -  `NSUserActivity`帮助系统了解用户当前在屏幕上使用的信息。 Schema.org 将类似的功能添加到网页。
- **位置建议** -如果应用提供或使用基于位置的信息，则这些 API 扩展提供了新的方法来跨应用共享此信息。
- **媒体应用建议** -系统可根据用户与 iOS 设备交互的上下文来升级应用及其媒体内容。

在应用程序中，通过实现以下内容来支持和：

- **Handoff**  -  移交 `NSUserActivity`已在 iOS 8 中添加以支持移交，这允许开发人员在一个设备上启动活动，然后在另一台设备上继续操作 (参阅[移交简介](~/ios/platform/handoff.md)) 。
- **聚焦搜索** -iOS 9 增加了利用 (查看聚焦搜索结果中的应用内容的功能， `NSUserActivity` 请参阅使用 [核心聚光灯搜索](~/ios/platform/search/corespotlight.md)) 。
- IOS 10 中的**上下文 Siri 提醒**已 `NSUserActivity` 扩展为允许 Siri 快速提醒查看用户当前在应用程序中查看的内容。
- **位置建议** -iOS 10 增强 `NSUserActivity` 了捕获在应用内查看的位置，并在整个系统的多个位置中进行升级。
- **上下文 Siri 请求**  -  `NSUserActivity`为在应用程序中提供的信息提供上下文以 Siri，以便用户可以从应用程序中获取指示或调用 Siri 的调用。
- **联系人交互** -iOS 10 中的新增功能 `NSUserActivity` 允许从联系人应用程序 (升级通信应用，) 作为备用通信方法。

所有这些功能都有一个共同之处，它们都使用 `NSUserActivity` 一种窗体或其他功能来提供其功能。 

## <a name="nsuseractivity"></a>NSUserActivity

如上所述， `NSUserActivity` 帮助系统了解用户当前在屏幕上使用的信息。 `NSUserActivity` 是一种轻型状态缓存机制，用于在用户浏览应用时捕获用户的活动。 例如，查看餐馆应用：

[![NSUserActivity 轻型状态缓存机制](proactive-suggestions-images/activity02.png)](proactive-suggestions-images/activity02.png#lightbox)

具有以下交互：

1. 用户使用应用时，会创建一个， `NSUserActivity` 以便在以后重新创建该应用的状态。
2. 如果用户搜索餐馆，则会遵循相同的创建活动模式。
3. 再次查看结果时。 在最后一种情况下，用户正在查看某个位置，在 iOS 10 中，系统更了解某些概念 (如位置或通信交互) 。

仔细查看最后一个屏幕：

[![NSUserActivity 详细信息](proactive-suggestions-images/activity03.png)](proactive-suggestions-images/activity03.png#lightbox)

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

开发人员需要确保这是与上面创建的活动相同的活动类型标识符 (`com.xamarin.platform`) 。 应用使用存储在中的信息 `NSUserActivity` 将状态还原回用户离开的位置。

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
2. 当用户使用 "多任务应用切换器" 移出应用时，系统会自动在屏幕底部显示 "建议" () 使用其喜爱的导航应用来向餐馆显示行车路线。
3. 如果用户切换到 "消息" 应用程序并开始键入 *"我们的会面"*，QuickType 键盘将自动建议在餐馆的地址中粘贴内容。
4. 如果用户切换到 Maps 应用，则会自动将餐馆的地址建议为目标。
5. 这甚至适用于支持)  (第三方应用程序 `NSUserActivity` ，因此用户可以切换到 "应用程序"，并将餐馆的地址自动作为目标提供。
6. 它还向 Siri 提供上下文，因此用户可以在餐馆应用中调用 Siri，并请求 *"获取行车路线 ..."* 和 Siri 将向用户正在查看的餐厅提供行车路线。

上述所有功能都有一个共同之处，它们都表示建议最初来自何处。 在上述示例中，它是虚构的餐馆评论应用。

iOS 10 已增强，可通过对现有框架进行几项小型修改和添加操作来为应用程序启用此功能：

- `NSUserActivity` 具有用于捕获在应用内查看的位置信息的附加字段。
- 已对 MapKit 和 CoreSpotlight 进行了几项添加以捕获位置。
- 位置感知功能已添加到 Siri、Maps、键盘、多任务和系统中的其他应用。

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

那么，基于文本的实例（例如 QuickType 键盘)  (）需要基于文本的说明：

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

纬度和经度是可选的，但请确保将用户路由到应用程序要将其发送到的确切位置，因此应将其包含在内：

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

通过设置电话号码，应用可以获取对 Siri 的访问权限，以便用户可以从应用程序中调用 Siri，如 *"调用此位置"*：

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

最后，该应用程序可以指示该实例是否适用于导航和电话呼叫：

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>实现联系人交互

IOS 10 中的新增技术，通信应用通过联系人卡片深度集成到 "联系人" 应用。 对于已经实现了联系人交互的应用程序，用户可以向其联系人中的特定人员添加给定的应用程序信息。 例如，如果它们点击了卡片顶部的 "快速操作" 按钮来发送消息，则附加的应用程序将被列为用于发送消息的选项。

如果选择了第三方应用，则可以将其视为默认方式，以便在用户下一次要与给定人员联系时向其显示消息。

在应用程序中实现联系人交互 `NSUserActivity` ，并使用 iOS 10 中引入的新意向框架。 有关使用意向的详细信息，请参阅 [了解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md) 和 [实现 SiriKit](~/ios/platform/sirikit/implementing-sirikit.md) 指南。

#### <a name="donating-interactions"></a>捐赠交互

了解应用程序如何捐赠交互：

[![捐赠交互概述](proactive-suggestions-images/activity04.png)](proactive-suggestions-images/activity04.png#lightbox)

应用程序创建一个 `INInteraction` 对象，该对象**Intent**包含 `INIntent`) 、**参与者**和**元数据** (意向。 **意向**表示用户操作，例如进行视频呼叫或发送短信。 **参与者**包含接收通信的人。 **元数据**定义了附加信息（例如，成功发送消息等）。

开发人员绝不会直接创建或的 `INIntent` 实例 `INIntentResponse` ，而是使用特定的子类 (基于该应用程序的任务，而不是代表从这些父类继承的用户) 。 例如， `INSendMessageIntent` `INSendMessageIntentResponse` 用于发送短信。 

完全填充交互后，调用 `DonateInteraction` 方法以通知系统交互可供使用。

当用户从联系人卡片与应用程序交互时，将与进行交互，并将 `NSUserActivity` 其用于启动应用程序：

[![交互与用于启动应用的 NSUserActivity 捆绑在一起。](proactive-suggestions-images/activity05.png)](proactive-suggestions-images/activity05.png#lightbox)

请看下面的发送消息意向示例：

```csharp
using System;
using Foundation;
using UIKit;
using Intents;

namespace MonkeyNotification
{
  public class DonateInteraction
  {
    #region Constructors
    public DonateInteraction ()
    {
    }
    #endregion

    #region Public Methods
    public void SendMessageIntent (string text, INPerson from, INPerson[] to)
    {

      // Create App Activity
      var activity = new NSUserActivity ("com.xamarin.message");

      // Define details
      var info = new NSMutableDictionary ();
      info.Add (new NSString ("message"), new NSString (text));

      // Populate Activity
      activity.Title = "Sent MonkeyChat Message";
      activity.UserInfo = info;

      // Enable capabilities
      activity.EligibleForSearch = true;
      activity.EligibleForHandoff = true;
      activity.EligibleForPublicIndexing = true;

      // Inform system of Activity
      activity.BecomeCurrent ();

      // Create message Intent
      var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);

      // Create Intent Reaction
      var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);

      // Create interaction
      var interaction = new INInteraction (intent, response);

      // Donate interaction to the system
      interaction.DonateInteraction ((err) => {
        // Handle donation error
        ...
      });
    }
    #endregion
  }
}
```

查看此代码的详细信息，它会创建并填充 (的实例， `NSUserActivity` 如上面) [创建活动](#creating-an-activity) 部分所示。 接下来，它创建一个 `INSendMessageIntent` 从) 继承的 (实例 `INIntent` ，并使用所发送消息的详细信息填充该实例：

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

`INSendMessageIntentResponse`创建并传递 `NSUserActivity` 上面创建的：

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

`INInteraction`从发送消息意向构建 (`INSendMessageIntent`) 和响应 (`INSendMessageIntentResponse`) 刚刚创建的：

```csharp
var interaction = new INInteraction (intent, response);
```

最后，系统是交互的通知：

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
  // Handle donation error
  ...
});
```

在应用可以响应成功或失败的情况下传入完成处理程序。

### <a name="activities-best-practices"></a>活动最佳实践

Apple 建议在使用活动时采用以下最佳做法：

- 用于 `NeedsSave` 迟缓负载更新。
- 确保保持对当前活动的强引用。
- 仅传输包含足够信息的小负载以还原状态。
- 请确保活动类型标识符是唯一的，并使用反向 DNS 表示法指定它们进行描述性。 

## <a name="schemaorg"></a>Schema.org

如上所示， `NSUserActivity` 帮助系统了解用户当前在屏幕上使用的信息。 Schema.org 将类似的功能添加到网页。

Schema.org 可以提供与网站的相同类型的基于位置的交互。 Apple 设计好新位置建议，以便在 Safari 中查看，就像在本机应用中一样。

一些 Schema.org 背景：

- 它提供开放式 web 标记词汇标准。
- 它的工作方式是在网页中包含结构化元数据。
- 有超过500个架构表示各个可用的概念。
- 通过在网站上实现它，开发人员可以获得 `NSUserActivity` 在本机应用中使用的一些优点。

架构在结构树（其中，特定类型，例如 *餐馆*）继承自其他泛型类型（如 *本地业务*）中进行排列。 有关详细信息，请参阅 [Schema.org](https://schema.org)。

例如，如果网页包含以下数据：

```html
<script type="application/ld+json">
{
  "@context":"http://schema.org",
  "@type":"Restaurant",
  "telephone":"(415) 781-1111",
  "url":"https://www.yanksing.com",
  "address":{
    "@type":"PostalAddress",
    "streetAddress":"101 Spear St",
    "addressLocality":"San Francisco",
    "postalCode":"94105",
    "addressRegion":"CA"
  },
  "aggregateRating":{
    "@type":"AggregateRating",
    "ratingValue":"3.5",
    "reviewCount":"2022"
  }
}
</script>
```

如果用户在 Safari 中访问此页，然后切换到另一个应用程序，则会捕获该页中的位置信息并将其作为位置建议提供给系统 (的其他部分，如以上) 的活动中所示。

Safari 将提取符合以下任一架构属性的网页上的任何内容：

- **PostalAddress**
- **GeoCoordinates**
- 电话属性。

有关详细信息，请参阅 " [通过 Web 标记搜索](~/ios/platform/search/web-markup.md) " 指南。

## <a name="consuming-location-suggestions"></a>使用位置建议

下一部分将介绍来自 (系统其他部分（如 Maps 应用) 或其他第三方应用）的消费位置建议。

应用可通过两种主要方式来使用位置建议：

- 通过 QuickType 键盘。
- 直接通过在路由应用中使用位置信息。

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>位置建议和 QuickType 键盘

如果应用处理基于文本格式的地址，则应用可以通过 QuickType UI 利用位置建议。 iOS 10 将扩展 QuickType UI，并提供以下功能：

- 应用可为 UI 中的文本字段添加有关语义意向的提示。
- 应用程序可以在应用程序中获取主动建议。
- 该应用可从增强的自动更正中获益。

`TextContentType`IOS 10 中的文本字段控件的新属性允许开发人员为用户要在给定字段中输入的值定义语义意向。 例如：

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

会告诉系统应用程序要求用户在给定字段中输入完整的街道地址。 当用户在此字段中输入值时，这将允许 QuickType 键盘自动提供键盘上的位置建议。

下面是一些可供静态类中的开发人员使用的常见类型 `UITextContentType` ：

- `Name`
- `GivenName`
- `FamilyName`
- `Location`
- `FullStreetAddress`
- `AddressCityAndState`
- `TelephoneNumber`
- `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>路由应用和位置建议

本部分将直接查看路由应用中的使用位置建议。 为了使路由应用添加此功能，开发人员将利用现有框架， `MKDirectionsRequest` 如下所示：

- 在多任务中升级应用程序。
- 将应用注册为路由应用。
- 处理使用 MapKit 对象启动应用程序的情况 `MKDirectionsRequest` 。
- 为了使 iOS 能够在适当的时间根据用户参与向用户提供向用户提供的建议。

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

IOS 10 中的新增功能，可向应用发送不具有地理坐标的地址，原因是开发人员需要对地址进行编码：

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
  // Handle the display of the address
  
});

```

## <a name="media-app-suggestions"></a>媒体应用建议

如果应用程序处理任何类型的媒体（如播客应用程序）或流媒体内容（如音频或视频）以及 iOS 10，则可以利用系统中的媒体建议。

对于处理媒体的应用，iOS 支持以下行为：

- iOS 会根据用户以前的行为，升级用户可能使用的应用。
- 与应用相关的建议将显示在 "聚光灯" 和 "今日" 视图中。
- 如果应用程序满足以下某个触发器，则可能会将其提升为锁定屏幕建议：
  - 插入耳机或 Bluetooth 设备后，即可建立连接。
  - 进入汽车后。
  - 在家中或工作后。 

通过在 iOS 10 中加入简单的 API 调用，开发人员可以为媒体应用程序的用户创建更吸引人的锁屏体验。 通过使用 `MPPlayableContentManager` 类管理媒体播放， (的完全媒体控件，如音乐应用显示的控件) 将显示在应用的锁定屏幕上。

```csharp
using System;
using MediaPlayer;
using UIKit;

namespace MonkeyPlayer
{
  public class PlayableContentDelegate : MPPlayableContentDelegate
  {
    #region Constructors
    public PlayableContentDelegate ()
    {
    }
    #endregion

    #region Override methods
    public override void InitiatePlaybackOfContentItem (MPPlayableContentManager contentManager, Foundation.NSIndexPath indexPath, Action<Foundation.NSError> completionHandler)
    {
      // Access the media item to play
      var item = LoadMediaItem (indexPath);

      // Populate the lock screen
      PopulateNowPlayingItem (item);

      // Prep item to be played
      var status = PreparePlayback (item);

      // Call completion handler
      completionHandler (null);
    }
    #endregion

    #region Public Methods
    public MPMediaItem LoadMediaItem (Foundation.NSIndexPath indexPath)
    {
      var item = new MPMediaItem ();

      // Load item from media store
      ...

      return item;
    }

    public void PopulateNowPlayingItem (MPMediaItem item)
    {
      // Get Info Center and album art
      var infoCenter = MPNowPlayingInfoCenter.DefaultCenter;
      var albumArt = (item.Artwork == null) ? new MPMediaItemArtwork (UIImage.FromFile ("MissingAlbumArt.png")) : item.Artwork;

      // Populate Info Center
      infoCenter.NowPlaying.Title = item.Title;
      infoCenter.NowPlaying.Artist = item.Artist;
      infoCenter.NowPlaying.AlbumTitle = item.AlbumTitle;
      infoCenter.NowPlaying.PlaybackDuration = item.PlaybackDuration;
      infoCenter.NowPlaying.Artwork = albumArt;
    }

    public bool PreparePlayback (MPMediaItem item)
    {
      // Prepare media item for playback
      ...

      // Return results
      return true;
    }
    #endregion
  }
}
```

## <a name="summary"></a>总结

本文介绍了前瞻性建议，并展示了开发人员如何使用它们来驱动向 Xamarin iOS 应用程序的流量。 本文介绍了实现前瞻性建议并提供使用准则的步骤。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [SiriKit 编程指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)