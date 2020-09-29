---
title: 其他 iOS 9 框架更改
description: 本文档介绍 iOS 9 中引入的其他框架更改。 它讨论了 AVFoundation、AVKit 和 CloudKit。
ms.prod: xamarin
ms.assetid: CFDE1FC4-9327-402B-95A0-581D4AA0E9D5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 8a4b1b5ed3089cb7044850cf19629fa063e43703
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436351"
---
# <a name="additional-ios-9-frameworks-changes"></a>其他 iOS 9 框架更改

_本文介绍适用于 iOS 9 的现有框架的其他较小的更改或增强功能。_

[![iOS 9 徽标](additional-framework-changes-images/ios9-sml.png)](additional-framework-changes-images/ios9.png#lightbox)

除了对 iOS 的重大更改之外，Apple 还在 iOS 9 中对多个现有框架进行了修改和改进。

## <a name="avfoundation-framework-additions"></a>添加 AVFoundation 框架

在 AVFoundation 框架中， [AVSpeechSynthesisVoice](xref:AVFoundation.AVSpeechSynthesisVoice) 类现在允许除了语言外，还可以通过标识符指定语音。

例如，下面的代码获取所有可用声音的列表：

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

然后，可以通过将其中一种语音设置为 AVSpeachUtterance 类的实例的属性，来使用其中一种语音 `Voice` 。 [AVSpeachUtterance](xref:AVFoundation.AVSpeechUtterance)

现在， [AVQueuePlayer](xref:AVFoundation.AVQueuePlayer) 类支持队列中的混合 internet 流和基于文件的媒体。 以前的版本只能将相同类型的媒体排队。

有关详细信息，请参阅 Apple 的 [AVSpeechSynthesisVoice 参考](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice)。

## <a name="avkit-framework-additions"></a>添加 AVKit 框架

若要使用全新的图画 (PIP) 功能，AVKit 框架包括新 `AVPictureInPictureController` 类和 [AVPlayerViewController](xref:AVKit.AVPlayerViewController) 类：

- **AVPictureInPictureController** -此类允许 iOS 9 应用响应用户在 iPad 上以浮动、可调整大小的 PIP 窗口播放视频的用户。
- **AVPlayerViewController** -管理 `AVPlayer` 用于在 iPad 上以浮动、可调整大小的 PIP 窗口呈现视频的控制器。

有关详细信息，请参阅 [iPad 的多任务](~/ios/platform/introduction-to-ios9/index.md#multitasking) 文档和 Apple 的 [AVPictureInPictureController 参考](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController) 和 [AVPlayerViewController 参考](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController)。

## <a name="introducing-cloudkit-web-services"></a>CloudKit Web 服务简介

CloudKit 框架可简化访问 iCloud 的应用程序的开发。 这包括检索应用程序数据和资产权限以及能够安全地存储应用程序信息。 此工具包允许用户使用其 iCloud Id 访问应用程序，而无需共享个人信息。

新的 _CloudKit Web 服务_ 框架提供了一个可在网站中加入的 JavaScript 库 (CloudKit JS) ，它可提供对与 Xamarin iOS 应用相同的基于 CloudKit 的数据和内容的访问权限。

> [!IMPORTANT]
> 必须先定义该数据库的架构，然后才能使用 CloudKit JS 从 CloudKit 数据库中访问、呈现或更新内容。

有关详细信息，请参阅以下文档：

- [CloudKit 简介](~/ios/data-cloud/intro-to-cloudkit.md) -介绍如何在 Xamarin iOS 应用中使用 CloudKit。
- [CloudKit 快速入门](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987) -Apple 对 CloudKit 的介绍。
- [CLOUDKIT JS Reference](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359) -Apple 的 CloudKit JS 文档。
- CloudKit 目录：使用 CloudKit 和 CloudKit JS[对 CloudKit (Cocoa 和 JavaScript 的简介) ](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599) Apple 的示例应用。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)，用于帮助开发人员正确处理欧盟一般数据保护条例 (GDPR)。

## <a name="foundation-framework-additions"></a>基础框架添加

Apple 在 iOS 9 中包含对基础框架的以下更改：

### <a name="changes-to-nsbundle"></a>对 NSBundle 的更改

为 iOS 9 的 [NSBundle](xref:Foundation.NSBundle) 类进行了以下更改：

- `GetPreservationPriorityForTag (NSString tag)` -获取具有给定标记的资源当前保留的优先级。 有效值范围 `0.0` 为到 `1.0` ，将首先清除优先级最低的资源。
- `SetPreservationPriorityForTag (double priority, NSSet tags)` -为具有给定标记的资源设置当前保留的优先级。 有效值范围 `0.0` 为到 `1.0` ，将首先清除优先级最低的资源。

有关详细信息，请参阅 Apple 的 [NSBundle 参考](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle)。

### <a name="changes-to-nsprocessinfo"></a>对 NSProcessInfo 的更改

在 iOS 设备上运行的每个进程都有一个 _进程信息代理_ (PIA) 。 使用 [NSProcessInfo](xref:Foundation.NSProcessInfo) 类提供有关当前 PIA 的信息，并控制给定进程的电源和散热管理。

例如，若要控制进程的自动终止，可以使用以下代码：

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

有关详细信息，请参阅 Apple 的 [NSProcessInfo 参考](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo)。

### <a name="reacting-to-low-power-mode"></a>响应低能耗模式

使用 `LowPowerModeEnabled` [NSProcessInfo](xref:Foundation.NSProcessInfo) 类的属性来确定是否已在运行应用的 iOS 设备上启用低功耗模式。 例如：

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>HealthKit Framework 更改

Apple 在 iOS 9 中包含对 [HealthKit](xref:HealthKit) 框架的以下更改：

- 支持批量删除和删除 HealthKit 数据库中的条目。 有关详细信息，请参阅 Apple 的 [HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject)、 [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery) 和 [HKHealthStore 类引用](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708) 。
- 已将新的跟踪类别和特性添加到 `HKQuantityTypeIdentifier` 类 (例如 `UVExposure`) 和 `HKCategoryTypeIdentifier` 类 (如 `OvulationTestResult`) 。 

请参阅 [HealthKit 文档简介](~/ios/platform/healthkit.md) ，了解有关在 Xamarin 中使用 HealthKit 的详细信息。

## <a name="local-authentication-framework-changes"></a>本地身份验证框架更改

Apple 在 iOS 9 中包括对 [本地身份验证](xref:LocalAuthentication) 框架的以下更改：

- 使用 `EvaluateAccessControl` `EvaluatePolicy` [LAContext](xref:LocalAuthentication.LAContext) 类的和方法，你现在可以重新使用之前成功解除锁定尝试的 Touch ID 匹配项。
- 能够获取当前注册的手指的列表。
- 当在身份验证中添加或删除手指时，支持跟踪。
- 在密钥链调用中使用 _身份验证上下文_ 的功能，并支持对密钥链访问控制列表进行评估。
- 能够从代码中取消用户提示。

有关详细信息，请参阅 [TOUCH id 和具有 Xamarin 的人脸 id](~/ios/platform/touch-id-face-id.md)。

### <a name="lacontext-changes"></a>LAContext 更改

为 iOS 9 的 [LAContext](xref:LocalAuthentication.LAContext) 类进行了以下更改：

- **TouchIdAuthenticationMaximumAllowableReuseDuration** -返回可重复使用触控 ID 身份验证的最大时间量。
- **EvaluatedPolicyDomainState** -获取或设置已评估的策略的状态。
- **MaxBiometryFailures** -已在 iOS 9 中进行了折旧。
- **TouchIdAuthenticationAllowableReuseDuration** 获取或设置触摸 ID 身份验证可重复使用的时间长度。
- **EvaluateAccessControl** -异步计算身份验证策略。
- **无效** -使给定的 touch ID 身份验证无效。
- **IsCredentialSet** - `true` 如果当前设置了凭据，则返回。
- **SetCredentialType** 设置给定的凭据类型。

有关更多详细信息，请参阅 Apple 的 [LAContext 参考](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:) 。

## <a name="mapkit-framework-changes"></a>MapKit Framework 更改

Apple 在 iOS 9 中包含对 [MapKit](xref:MapKit) 框架的以下更改：

- MapKit 现在支持使用 [MKLaunchOptions](xref:MapKit.MKLaunchOptions) 和 [MKDirections](xref:MapKit.MKLaunchOptions) 类直接在传输方向上启动地图应用，并查询传输预计到达 (ETA 时间) 。
- MapKit 和 [CLGeocoder](xref:CoreLocation.CLGeocoder) 类返回的搜索结果还可以提供结果的时区。
- 你现在可以使用 MKAnnotationView 类的属性完全自定义 iOS 应用显示的地图批注 `DetailCalloutAccessoryView` 。 [MKAnnotationView](xref:MapKit.MKAnnotationView)

有关详细信息，请参阅我们的 [IOS 地图](~/ios/user-interface/controls/ios-maps/index.md) 和 [演练-浏览 MapKit 文档中的批注和覆盖](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md) 。有关详细信息，请参阅 CLGeocoder 中的映射和批注和 Apple 的 [参考](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder) 。

## <a name="passkit-framework-additions"></a>添加 PassKit 框架

Apple 在 iOS 9 中包含对 [PassKit](xref:PassKit) 框架的以下更改：

- Apple Pay 现在同时支持商店借记卡和信用卡，同时支持发现卡。 有关详细信息，请参阅 Apple 的[PKPaymentRequest 类参考](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832)的**付款网络**部分。
- 直接从 Xamarin iOS 应用程序中，可以将支付网络和卡颁发者添加到 Apple Pay。 有关更多详细信息，请参阅 Apple 的 [PKAddPaymentPassViewController 类引用](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116) 。

请参阅 [PassKit 文档简介](~/ios/platform/passkit.md) ，了解有关在 Xamarin 中使用 PassKit 的详细信息。

## <a name="safari-services-framework-additions"></a>Safari 服务框架添加

Apple 在 iOS 9 中包含对 [Safari 服务](xref:SafariServices) 框架的以下更改：

- 现在可以使用新的 [SFSafariViewController](xref:SafariServices.SFSafariViewController) 类在 Xamarin iOS 应用程序中显示 web 内容。 它可与 Safari 应用共享网站数据和 cookie，其中包含一些 Safari 功能 (例如，读者和自动填充) 。 [SFSafariViewController](xref:SafariServices.SFSafariViewController) 的功能是 " **完成** " 按钮，当用户完成查看 web 内容时，该按钮会将用户返回到应用。

由于 [SFSafariViewController](xref:SafariServices.SFSafariViewController) 类是为显示 web 内容的单个页面而定制的，因此，应考虑使用它来替换现有 Xamarin iOS 应用中的任何 [WKWebKit](xref:WebKit.WKWebView) 或 [UIWebView](xref:UIKit.UIWebView) 控件。

### <a name="displaying-a-website"></a>显示网站

下面的代码是在另一个视图控制器中调用 [SFSafariViewController](xref:SafariServices.SFSafariViewController) 的示例：

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>UIKit Framework 更改

Apple 为 iOS 9 的 [UIKit](xref:UIKit) 框架的几个元素提供了许多增强功能。 以下各节将详细说明这些更改。

### <a name="3d-touch-events"></a>3D 触摸事件

在 iOS 9 和 iPhone 6s 和 iPhone 6s Plus 的新手中，3D 触控为你的 iOS 应用程序增加了压力敏感度。 因此，如果你的应用在 iOS 9 上运行 (或更高版本) 并且 iOS 设备能够支持3D 触摸，则压力更改将导致 `TouchesMoved` 引发事件。

由于这种行为发生了这种更改，因此，应准备好 iOS 应用程序 `TouchesMoved` ，以便更频繁地调用事件，即使 X/Y 坐标未更改也是如此。

有关详细信息，请参阅 [3D Touch 指南简介](~/ios/platform/3d-touch.md) 。

### <a name="document-open-in-place-functionality"></a>文档开放功能

通过使用 `FinishedLaunching (application, launchOptions)` `WillFinishLaunching (Application, launchOptions)` [UIApplicationDelegate](xref:UIKit.UIApplicationDelegate) 类的或方法，现在可以打开文档并将其就地修改 (而不是处理) 的副本。

若要支持新的开放功能，请将 `LSSupportsOpeningDocumentsInPlace` 密钥添加到你的 Xamarin iOS 应用的 **info.plist** 文件，其值为 `YES` 。

有关更多详细信息，请参阅 Apple 的 [UIApplicationDelegate 参考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) 。

### <a name="enhanced-touch-events"></a>增强的触摸事件

Apple 在 iOS 9 中为触控事件提供了多项增强功能。 这些功能包括使用触摸预测和获取对显示刷新之间的中间接触的访问。

有关更多详细信息，请参阅 Apple 的 [适用于 iOS 的事件处理指南](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html) 。

### <a name="fetching-tailored-content"></a>正在获取定制内容

新 `NSDataAsset` 类允许 Xamarin iOS 应用提取针对当前正在运行的 iOS 设备的内存和图形功能定制的内容。

### <a name="new-layout-anchors"></a>新布局定位点

New `NSLayoutAnchor` 和 `NSLayoutDimension` layout 定位点类适用于 [UIView](xref:UIKit.UIView) 类的新定位点属性， (例如 `LeadingAnchor` 和) ， `WidthAnchor` 以便在 iOS 9 中更轻松地进行布局。

[有关详细](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView)[信息，请](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension)参阅[统一情节提要文档简介](~/ios/user-interface/storyboards/unified-storyboards.md)，了解有关在 Xamarin ios 应用中使用自动版式和大小类的详细信息和 Apple 的[NSLayoutAnchor 参考](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor)。

### <a name="new-readable-content-margins"></a>新的可读内容边距

新 `UILayoutGuide` 类可用于提供可读内容边距，并定义视图内内容的绘制区域。 有关详细信息，请参阅 Apple 的 [UILayoutGuide 参考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide) 。

### <a name="text-input-in-notifications-modifications"></a>通知修改中的文本输入

[UIUserNotificationAction](xref:UIKit.UIUserNotificationAction)类有一个新的 `Behavior` 属性，可用于支持通知中的文本输入。

### <a name="uiapplicationdelegate-changes"></a>UIApplicationDelegate 更改

虽然 Apple 未正式弃用，但建议 `FinishedLaunching (UIApplication application)` 使用或方法替换对 [UIApplicationDelegate](xref:UIKit.UIApplicationDelegate) 类的方法的所有调用 `FinishedLaunching (UIApplication application, NSDictionary launchOptions)` `WillFinishLaunching (UIApplication application, NSDictionary launchOptions)` 。

有关更多详细信息，请参阅 Apple 的 [UIApplicationDelegate 参考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) 。

### <a name="uikit-dynamics-changes"></a>UIKit Dynamics 更改

Apple 在 iOS 9 中包括对 UIKit Dynamics 的以下更改：

- Dynamics 现在为非矩形冲突边界提供支持。
- 新的可自定义 `UIFieldBehavior` 类用于支持各种字段类型。
- 类中已添加其他附件类型 `UIAttachmentBehavior` 。

有关更多详细信息，请参阅 Apple 的 [UIAttachment 参考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior) 。

### <a name="uipickerview-and-uidatepicker-changes"></a>UIPickerView 和 UIDatePicker 更改

在 iOS 9 之前， [UIPickerView](xref:UIKit.UIPickerView) 和 [UIDatePicker](xref:UIKit.UIDatePicker) 控件是不可调整大小的，将自动调整大小以填充其容器的宽度 (通常是应用运行) 的 iOS 设备的宽度。

在 iOS 9 中，这种自动调整大小将不再发生，并且控件将在所有 iOS 设备上以320点宽度呈现，而不考虑屏幕大小和方向。

若要更正此情况，请使用 "自动布局" 和 "大小类" 将控件的宽度固定到父容器的边缘 (视图) 并指定所需的高度。 有关在 Xamarin iOS 应用中使用自动布局和大小类的详细信息，请参阅我们的 [统一情节提要文档简介](~/ios/user-interface/storyboards/unified-storyboards.md) 。

### <a name="new-uitextinputassistantitem-class"></a>新的 UITextInputAssistantItem 类

`UITextInputAssistantItem`在_快捷方式栏_中使用 "新建类" 布局栏按钮组。 快捷方式栏是软键盘中提供的一种新区域，用于提供键入的快捷方式。

## <a name="related-links"></a>相关链接

- [iOS 9 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [iOS 9 简介](~/ios/platform/introduction-to-ios9/index.md)
- [适用于开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 中的新增功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)