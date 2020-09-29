---
title: Xamarin 中的移交
description: 本文介绍如何在 Xamarin iOS 应用程序中使用转换，以便在用户的其他设备上运行的应用程序之间传输用户活动。
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 955fa049e88b74796d5949518f6149f1e2a9527c
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435209"
---
# <a name="handoff-in-xamarinios"></a>Xamarin 中的移交

_本文介绍如何在 Xamarin iOS 应用程序中使用转换，以便在用户的其他设备上运行的应用程序之间传输用户活动。_

Apple 在 iOS 8 和 OS X Yosemite (10.10) 中引入了移交，以提供一种通用机制，让用户将其设备上启动的活动传输到另一台运行相同应用程序的设备，或其他支持相同活动的应用程序。

[![执行移交操作的示例](handoff-images/handoff02.png)](handoff-images/handoff02.png#lightbox)

本文将简要介绍如何在 Xamarin iOS 应用程序中启用活动共享并详细介绍了移交框架：

## <a name="about-handoff"></a>关于移交

移交 (在 iOS 8 和 OS X Yosemite (10.10) 中的 Apple 引入了连续性) ，因此，用户可以在其某个设备上启动活动 (iOS 或 Mac) 并在其设备上继续该相同活动 (由用户的 iCloud 帐户) 标识。

在 iOS 9 中扩展了移交，还支持新的增强搜索功能。 有关详细信息，请参阅我们的 [搜索增强](~/ios/platform/search/index.md) 文档。

例如，用户可以在其 iPhone 上启动一封电子邮件，并在其 Mac 上无缝地继续发送电子邮件，并在其中填充相同的所有消息信息，并将光标置于 iOS 中的相同位置。

共享同一 _团队 ID_ 的任何应用都有资格使用移交在应用中继续执行用户活动，前提是这些应用是通过 ITunes 应用商店交付的，或者是由适用于 Mac、企业或即席应用) 的已注册开发 (人员签署的。

任何 `NSDocument` 或 `UIDocument` 基于的应用程序都将自动提供提供支持，并需要最少的更改来支持提交。

### <a name="continuing-user-activities"></a>继续执行用户活动

`NSUserActivity`类 (以及对和) 的一些小更改 `UIKit` ，为 `AppKit` 定义用户的活动提供支持，这些活动可能会在用户的另一台设备上继续。

对于要传递到另一个用户设备的活动，必须将它封装在实例中，将其 `NSUserActivity` 标记为 _当前活动_，使其负载 (用于执行延续) 的数据，然后必须将活动传输到该设备。

移交会传递最少的信息来定义要继续的活动，同时通过 iCloud 同步更大的数据包。

在接收设备上，用户将收到一条通知，告知活动可用于继续。 如果用户选择继续新设备上的活动，则会启动指定的应用程序 (如果尚未运行) ，则使用中的负载 `NSUserActivity` 来重新启动活动。

[![继续执行用户活动的概述](handoff-images/handoffinteractions.png)](handoff-images/handoffinteractions.png#lightbox)

只有共享同一开发人员团队 ID 并响应给定 _活动类型_ 的应用才有资格继续进行。 应用在 `NSUserActivityTypes` 其 **info.plist** 文件的密钥下定义其支持的活动类型。 为此，正在继续的设备会根据团队 ID、活动类型和 _活动标题_（可选）选择应用程序以执行延续任务。

接收应用使用来自的字典中的信息 `NSUserActivity` `UserInfo` 来配置其用户界面并还原给定活动的状态，以便向最终用户显示无缝转换。

如果延续需要的信息超过了可以通过有效的形式发送的信息 `NSUserActivity` ，则继续执行的应用程序可以发送对原始应用程序的调用并建立一个或多个流来传输所需的数据。 例如，如果活动正在编辑包含多个图像的大型文本文档，则需要进行流式传输来传输继续接收设备上的活动所需的信息。 有关详细信息，请参阅下面的 [支持继续流](#supporting-continuation-streams) 部分。

如上所述， `NSDocument` 或基于的 `UIDocument` 应用程序自动提供内置支持。 有关详细信息，请参阅下面的 [基于文档的应用中的支持移交](#supporting-handoff-in-document-based-apps) 部分。

### <a name="the-nsuseractivity-class"></a>NSUserActivity 类

`NSUserActivity`类是切换交换中的主要对象，用于封装可用于继续的用户活动的状态。 应用将实例化 `NSUserActivity` 其支持的任何活动的副本，并希望在另一台设备上继续操作。 例如，文档编辑器将为当前打开的每个文档创建一个活动。 但是，最前面的窗口或选项卡中只显示最前面的文档 () 是 _当前活动_ 和可供继续的因此。

的实例 `NSUserActivity` 由其 `ActivityType` 和 `Title` 属性标识。 `UserInfo`Dictionary 属性用于携带有关活动状态的信息。 `NeedsSave` `true` 如果要延迟通过的委托加载状态信息，请将属性设置为 `NSUserActivity` 。 使用 `AddUserInfoEntries` 方法可根据需要将其他客户端中的新数据合并到 `UserInfo` 字典中，以保留活动状态。

### <a name="the-nsuseractivitydelegate-class"></a>NSUserActivityDelegate 类

`NSUserActivityDelegate`用于使中的信息保持最新 `NSUserActivity` `UserInfo` ，并与活动的当前状态保持同步。 当系统需要更新活动中的信息时 (例如在另一个设备) 之前继续，它将调用 `UserActivityWillSave` 委托的方法。

你将需要实现 `UserActivityWillSave` 方法并对 (（例如、等）进行任何更改 `NSUserActivity` `UserInfo` `Title` ) ，以确保它仍反映当前活动的状态。 当系统调用方法时 `UserActivityWillSave` ， `NeedsSave` 将清除标志。 如果修改活动的任何数据属性，则需要再次将设置 `NeedsSave` 为 `true` 。

`UserActivityWillSave`您可以选择 `UIKit` 自动拥有或管理用户活动，而不是使用上面提供的方法 `AppKit` 。 为此，请设置响应方对象的 `UserActivity` 属性并实现 `UpdateUserActivityState` 方法。 有关详细信息，请参阅下面的 [响应程序中的支持移交](#supporting-handoff-in-responders) 部分。

### <a name="app-framework-support"></a>应用框架支持

`UIKit` (iOS) 和 `AppKit` (OS X) 为 `NSDocument` 、响应方 (`UIResponder` / `NSResponder`) 和 `AppDelegate` 类中的移交提供内置支持。 虽然每个 OS 的实现方式略有不同，但基本机制和 Api 是相同的。

#### <a name="user-activities-in-document-based-apps"></a>基于文档的应用中的用户活动

基于文档的 iOS 和 OS X 应用程序自动提供内置的移交支持。 若要激活此支持，需要 `NSUbiquitousDocumentUserActivityType` `CFBundleDocumentTypes` 在应用的 **info.plist** 文件中为每个条目添加一个密钥和值。

如果此项存在，则 `NSDocument` 和 `UIDocument` `NSUserActivity` 将自动为指定的类型的基于 iCloud 的文档创建实例。 你将需要为应用支持的每种类型的文档提供一种活动类型，并且多个文档类型可以使用相同的活动类型。 `NSDocument`和都 `UIDocument` `UserInfo` `NSUserActivity` 用其属性的值自动填充的属性 `FileURL` 。

在 OS X 上，在 `NSUserActivity` `AppKit` 文档的窗口变成主窗口时，由管理的和与响应方关联的将自动成为当前活动。 在 iOS 上，对于 `NSUserActivity` 由管理的对象 `UIKit` ，你必须 `BecomeCurrent` 显式调用方法，或者 `UserActivity` `UIViewController` 当应用程序到达前台时，将文档的属性设置为。

`AppKit` 将 `UserActivity` 在 OS X 上自动还原以这种方式创建的任何属性。如果 `ContinueUserActivity` 方法返回或未实现，则会发生这种情况 `false` 。 在这种情况下，将使用的方法打开文档 `OpenDocument` `NSDocumentController` ，然后它将接收 `RestoreUserActivityState` 方法调用。

有关详细信息，请参阅下面的 [基于文档的应用中的支持移交](#supporting-handoff-in-document-based-apps) 部分。

#### <a name="user-activities-and-responders"></a>用户活动和响应程序

`UIKit` `AppKit` 如果将用户活动设置为响应方对象的属性，则和都可以自动管理该活动 `UserActivity` 。 如果已修改状态，则需要将 `NeedsSave` 响应方的属性设置 `UserActivity` 为 `true` 。 系统将 `UserActivity` 在需要时通过调用其方法来更新状态，从而在需要时自动保存 `UpdateUserActivityState` 。

如果多个响应程序共享单个 `NSUserActivity` 实例，则 `UpdateUserActivityState` 当系统更新用户活动对象时，会收到回调。 响应方在此时需要调用 `AddUserInfoEntries` 方法来更新 `NSUserActivity` 的 `UserInfo` 字典，以反映当前的活动状态。 在 `UserInfo` 每次调用前清除字典 `UpdateUserActivityState` 。

若要取消自身与活动之间的关联，响应方可以将其 `UserActivity` 属性设置为 `null` 。 当应用程序框架托管 `NSUserActivity` 实例没有更多关联的响应程序或文档时，它会自动失效。

有关详细信息，请参阅下面的 [响应程序中的支持移交](#supporting-handoff-in-responders) 部分。

#### <a name="user-activities-and-the-appdelegate"></a>用户活动和 AppDelegate

在 `AppDelegate` 处理移交延续时，你的应用程序是其主要入口点。 当用户响应移交通知时，会启动相应的应用程序 (如果尚未运行) 并 `WillContinueUserActivityWithType` `AppDelegate` 将调用的方法。 此时，应用程序应通知用户延续任务正在启动。

`NSUserActivity`调用的方法时，将提供实例 `AppDelegate` `ContinueUserActivity` 。 此时，应配置应用的用户界面，并继续执行给定的活动。

有关详细信息，请参阅下面的 [实现移交](#implementing-handoff) 部分。

## <a name="enabling-handoff-in-a-xamarin-app"></a>在 Xamarin 应用中启用切换

由于移交施加了安全要求，因此必须在 Apple 开发人员门户和 Xamarin iOS 项目文件中正确配置使用移交框架的 Xamarin iOS 应用。

执行以下操作：

1. 登录到 [Apple 开发人员门户](https://developer.apple.com)。
2. 单击 " **证书、标识符 & 配置文件**。
3. 如果尚未执行此操作，请单击 " **标识符** "，为应用创建一个 ID (例如 `com.company.appname`) ，否则编辑现有 ID。
4. 确保已针对给定 ID 检查 **iCloud** 服务：

    [![为给定 ID 启用 iCloud 服务](handoff-images/provision01.png)](handoff-images/provision01.png#lightbox)
5. 保存所做更改。
6. 单击 "**预配配置文件**  >  **开发**"，为你的应用创建新的开发预配配置文件：

    [![为应用程序创建新的开发预配配置文件](handoff-images/provision02.png)](handoff-images/provision02.png#lightbox)
7. 下载并安装新的预配配置文件，或使用 Xcode 下载并安装该配置文件。
8. 编辑 Xamarin iOS 项目选项，确保使用的是刚才创建的预配配置文件：

    [![选择刚刚创建的预配配置文件](handoff-images/provision03.png)](handoff-images/provision03.png#lightbox)
9. 接下来，请编辑 **info.plist** 文件，并确保使用的是用于创建预配配置文件的应用 ID：

    [![设置应用 ID](handoff-images/provision04.png)](handoff-images/provision04.png#lightbox)
10. 滚动到 " **背景模式** " 部分，并检查以下各项：

    [![启用所需的后台模式](handoff-images/provision05.png)](handoff-images/provision05.png#lightbox)
11. 保存对所有文件所做的更改。

设置好这些设置后，应用程序便可访问移交框架 Api。 有关预配的详细信息，请参阅 [设备预配](~/ios/get-started/installation/device-provisioning/index.md) 和 [预配应用](~/ios/get-started/installation/device-provisioning/index.md) 指南。

## <a name="implementing-handoff"></a>实现移交

用户活动可以在用同一开发人员团队 ID 进行签名并支持相同活动类型的应用之间继续。 在 Xamarin iOS 应用中实施移交要求你创建一个用户活动对象 (在 `UIKit` 或 `AppKit`) 中，更新对象的状态以跟踪活动，并在接收设备上继续活动。

### <a name="identifying-user-activities"></a>确定用户活动

实施移交的第一步是确定你的应用程序支持的用户活动类型，并查看哪些活动适合在另一台设备上继续进行。 例如： ToDo 应用可能支持将项编辑为一个用户活动类型，并支持将可用项列表作为另一个 _用户活动类型_进行浏览。

应用程序可以根据需要创建任意数量的用户活动类型，一个用于应用程序提供的任何函数。 对于每个用户活动类型，应用需要跟踪类型的活动开始和结束的时间，并需要维护最新的状态信息，以在另一台设备上继续该任务。

用户活动可以在具有相同团队 ID 的任何应用上继续进行，而在发送和接收应用之间不存在任何一对一映射。 例如，给定的应用可以创建四种不同类型的活动，这些活动由另一台设备上的不同应用程序使用。 这是应用 (Mac 版本之间的常见情况，这种情况可能有许多功能和功能) 和 iOS 应用程序，其中的每个应用程序较小，重点关注特定任务。

### <a name="creating-activity-type-identifiers"></a>创建活动类型标识符

_活动类型标识符_是添加到 `NSUserActivityTypes` 应用的**info.plist**文件数组的短字符串，用于唯一标识给定的用户活动类型。 对于应用程序支持的每个活动，数组中将有一个条目。 Apple 建议对活动类型标识符使用反向 DNS 样式表示法，以避免冲突。 例如： `com.company-name.appname.activity` 对于基于应用的特定活动或 `com.company-name.activity` 可跨多个应用运行的活动。

创建实例时，将使用活动类型标识符 `NSUserActivity` 来标识活动的类型。 如果在另一台设备上继续活动，则活动类型 (连同应用的团队 ID) 确定要启动哪个应用以继续执行该活动。

例如，我们将创建一个名为 **MonkeyBrowser** 的示例应用 () [下载](/samples/xamarin/ios-samples/ios8-monkeybrowser) 。 此应用将显示四个选项卡，每个选项卡都在 web 浏览器视图中打开了不同的 URL。 用户可以继续运行该应用的不同 iOS 设备上的任何选项卡。

若要创建所需的活动类型标识符以支持此行为，请编辑 **info.plist** 文件，并切换到 " **源** " 视图。 添加 `NSUserActivityTypes` 密钥，并创建以下标识符：

[![Info.plist 编辑器中的 NSUserActivityTypes 键和必需的标识符](handoff-images/type01.png)](handoff-images/type01.png#lightbox)

我们创建了四个新的活动类型标识符，一个用于示例 **MonkeyBrowser** 应用中的每个选项卡。 创建自己的应用时，将数组的内容替换为 `NSUserActivityTypes` 特定于应用支持的活动的活动类型标识符。

### <a name="tracking-user-activity-changes"></a>跟踪用户活动更改

当我们创建类的新实例时 `NSUserActivity` ，我们将指定一个 `NSUserActivityDelegate` 实例来跟踪对活动状态的更改。 例如，以下代码可用于跟踪状态更改：

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;

namespace MonkeyBrowse
{
    public class UserActivityDelegate : NSUserActivityDelegate
    {
        #region Constructors
        public UserActivityDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void UserActivityReceivedData (NSUserActivity userActivity, NSInputStream inputStream, NSOutputStream outputStream)
        {
            // Log
            Console.WriteLine ("User Activity Received Data: {0}", userActivity.Title);
        }

        public override void UserActivityWasContinued (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity Was Continued: {0}", userActivity.Title);
        }

        public override void UserActivityWillSave (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity will be Saved: {0}", userActivity.Title);
        }
        #endregion
    }
}
```

`UserActivityReceivedData`当延续流接收到发送设备中的数据时，将调用方法。 有关详细信息，请参阅下面的 [支持继续流](#supporting-continuation-streams) 部分。

`UserActivityWasContinued`当另一个设备已从当前设备中接管活动时，将调用方法。 根据活动的类型（如向 ToDo 列表中添加新项），应用可能需要中止发送设备上的活动。

在对 `UserActivityWillSave` 活动所做的任何更改保存并在本地可用设备上同步之前，将调用方法。 在 `UserInfo` 发送实例之前，可以使用此方法对该实例的属性进行最后一分钟的更改 `NSUserActivity` 。

### <a name="creating-a-nsuseractivity-instance"></a>创建 NSUserActivity 实例

应用希望能够在另一台设备上继续提供的每个活动都必须封装在一个 `NSUserActivity` 实例中。 应用程序可以根据需要创建任意数量的活动，这些活动的性质取决于相关应用程序的功能和功能。 例如，电子邮件应用程序可以创建一个用于创建新邮件的活动，另一个用于读取消息。

对于我们的示例应用， `NSUserActivity` 每次用户在选项卡式 web 浏览器视图中输入新的 URL 时，都会创建一个新的。 以下代码存储给定选项卡的状态：

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSUserActivity UserActivity { get; set; }
...

UserActivity = new NSUserActivity (UserActivityTab1);
UserActivity.Title = "Weather Tab";
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

它 `NSUserActivity` 使用上面创建的用户活动类型之一创建新的，并为活动提供可读的标题。 它会附加到上面创建的实例， `NSUserActivityDelegate` 以监视状态更改，并通知 iOS 此用户活动是当前活动。

### <a name="populating-the-userinfo-dictionary"></a>填充用户信息字典

如上所述， `UserInfo` 类的属性 `NSUserActivity` 是 `NSDictionary` 键/值对的，用于定义给定活动的状态。 中存储的值 `UserInfo` 必须是以下类型之一： `NSArray` 、 `NSData` 、 `NSDate` 、 `NSDictionary` 、、、 `NSNull` 、 `NSNumber` `NSSet` `NSString` 或 `NSURL` 。 `NSURL` 指向 iCloud 文档的数据值将自动进行调整，以使其指向接收设备上的相同文档。

在上面的示例中，我们创建了一个 `NSMutableDictionary`  对象，并在其中填充了一个提供用户当前在给定选项卡上查看的 URL 的键。 `AddUserInfoEntries` 用户活动的方法用于使用将用于在接收设备上还原活动的数据更新活动：

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple 建议将发送到使得的信息保持最小，以确保将活动及时发送到接收设备。 如果需要更大的信息，则需要发送与要编辑的文档相关联的图像，你应该使用继续流。 有关更多详细信息，请参阅下面的 [支持继续流](#supporting-continuation-streams) 部分。

### <a name="continuing-an-activity"></a>继续活动

移交会自动通知本地 iOS 和 OS X 设备，这些设备在物理上接近于始发设备，并登录到相同的 iCloud 帐户，这是可持续用户活动的可用性。 如果用户选择继续在新设备上执行某个活动，系统将根据 "团队 ID" 和 "活动类型" (启动相应的应用程序，) 以及 `AppDelegate` 需要继续执行该操作的信息。

首先， `WillContinueUserActivityWithType` 调用方法，以便应用程序可以通知用户延续任务即将开始。 我们使用示例应用程序的 **AppDelegate.cs** 文件中的以下代码来处理延续任务：

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSString UserActivityTab2 = new NSString ("com.xamarin.monkeybrowser.tab2");
public NSString UserActivityTab3 = new NSString ("com.xamarin.monkeybrowser.tab3");
public NSString UserActivityTab4 = new NSString ("com.xamarin.monkeybrowser.tab4");
...

public FirstViewController Tab1 { get; set; }
public SecondViewController Tab2 { get; set;}
public ThirdViewController Tab3 { get; set; }
public FourthViewController Tab4 { get; set; }
...

public override bool WillContinueUserActivity (UIApplication application, string userActivityType)
{
    // Report Activity
    Console.WriteLine ("Will Continue Activity: {0}", userActivityType);

    // Take action based on the user activity type
    switch (userActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Inform view that it's going to be modified
        Tab1.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Inform view that it's going to be modified
        Tab2.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Inform view that it's going to be modified
        Tab3.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Inform view that it's going to be modified
        Tab4.PreparingToHandoff ();
        break;
    }

    // Inform system we handled this
    return true;
}
```

在上面的示例中，每个视图控制器都向注册 `AppDelegate` 并具有一个公共 `PreparingToHandoff` 方法，该方法显示活动指示器和一条消息，告知用户要将活动移交给当前设备。 示例：

```csharp
private void ShowBusy(string reason) {

    // Display reason
    BusyText.Text = reason;

    //Define Animation
    UIView.BeginAnimations("Show");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0.5f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PreparingToHandoff() {
    // Inform caller
    ShowBusy ("Continuing Activity...");
}
```

`ContinueUserActivity` `AppDelegate` 将调用以实际继续给定的活动。 同样，从我们的示例应用程序：

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

`PerformHandoff`每个视图控制器的公共方法实际上完成了移交，并还原了当前设备上的活动。 在此示例中，它在给定的选项卡中显示与用户在其他设备上浏览的 URL 相同的 URL。 示例：

```csharp
private void HideBusy() {

    //Define Animation
    UIView.BeginAnimations("Hide");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PerformHandoff(NSUserActivity activity) {

    // Hide busy indicator
    HideBusy ();

    // Extract URL from dictionary
    var url = activity.UserInfo ["Url"].ToString ();

    // Display value
    URL.Text = url;

    // Display the give webpage
    WebView.LoadRequest(new NSUrlRequest(NSUrl.FromString(url)));

    // Save activity
    UserActivity = activity;
    UserActivity.BecomeCurrent ();

}
```

`ContinueUserActivity`方法包括一个 `UIApplicationRestorationHandler` ，您可以为基于文档或响应程序的活动恢复调用。 调用时，需要将 `NSArray` 或可还原的对象传递给还原处理程序。 例如：

```csharp
completionHandler (new NSObject[]{Tab4});
```

对于传递的每个对象， `RestoreUserActivityState` 将调用其方法。 然后，每个对象可以使用字典中的数据 `UserInfo` 来还原其自己的状态。 例如：

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

对于基于文档的应用，如果未实现 `ContinueUserActivity` 方法或返回 `false` ，则 `UIKit` `AppKit` 可以自动恢复活动。 有关详细信息，请参阅下面的 [基于文档的应用中的支持移交](#supporting-handoff-in-document-based-apps) 部分。

### <a name="failing-handoff-gracefully"></a>正常移交失败

由于移交依赖于收集松散连接的 iOS 和 OS X 设备之间的信息传输，因此传输过程有时会失败。 应该将应用程序设计为适当地处理这些故障，并通知用户发生的任何情况。

发生故障时， `DidFailToContinueUserActivitiy` `AppDelegate` 将调用的方法。 例如：

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

应使用提供的 `NSError` 向用户提供有关失败的信息。

## <a name="native-app-to-web-browser-handoff"></a>本机应用到 Web 浏览器的切换

如果未在所需的设备上安装适当的本机应用，用户可能需要继续执行某个活动。 在某些情况下，基于 web 的界面可能会提供所需的功能，并且活动仍可继续。 例如，用户的电子邮件帐户可能提供用于撰写和阅读消息的 web 基本 UI。

如果原始的本机应用程序知道 web 界面 (的 URL，以及用于标识要继续) 的给定项的所需语法，则它可以在实例的属性中对此信息进行编码 `WebpageURL` `NSUserActivity` 。 如果接收设备没有安装适当的本机应用来处理延续，则可以调用提供的 web 界面。

## <a name="web-browser-to-native-app-handoff"></a>Web 浏览器到本机应用的切换

如果用户在始发设备上使用基于 web 的界面，而接收设备上的本机应用声明属性的域部分 `WebpageURL` ，则系统将使用该应用来处理延续任务。 新设备将接收 `NSUserActivity` 标记活动类型的实例， `BrowsingWeb` 并且 `WebpageURL` 将包含用户访问的 URL， `UserInfo` 字典将为空。

对于要参与这种类型的提交的应用程序，它必须 `com.apple.developer.associated-domains` 使用格式 (的权限声明域， `<service>:<fully qualified domain name>` 例如： `activity continuation:company.com`) 。

如果指定的域与 `WebpageURL` 属性的值匹配，则提交会从该域的网站下载已批准的应用 id 列表。 该网站必须在名为 **apple-app-association** (的已签名 JSON 文件中提供已批准 id 的列表，例如 `https://company.com/apple-app-site-association`) 。

此 JSON 文件包含一个字典，该字典指定窗体中的应用 Id 列表 `<team identifier>.<bundle identifier>` 。 例如：

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

若要对 JSON 文件 (进行签名，使其具有正确 `Content-Type` 的 `application/pkcs7-mime`) ，请使用 **终端** 应用和 `openssl` 命令，其中包含由 iOS 信任的证书颁发机构颁发的证书和密钥 ([https://support.apple.com/kb/ht5012](https://support.apple.com/kb/ht5012) 有关列表) ，请参阅。 例如：

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

此 `openssl` 命令输出你放置在网站上的 **apple-site 关联** URL 中的已签名 JSON 文件。 例如：

```csharp
https://example.com/apple-app-site-association.
```

应用将接收其 `WebpageURL` 域处于其权限的任何活动 `com.apple.developer.associated-domains` 。 只有 `http` 和 `https` 协议支持，任何其他协议都将引发异常。

## <a name="supporting-handoff-in-document-based-apps"></a>支持基于文档的应用的移交

如上所述，在 iOS 和 OS X 上，如果应用的 **info.plist** 文件包含的密钥，则基于文档的应用将自动支持基于 iCloud 的文档的移交 `CFBundleDocumentTypes` `NSUbiquitousDocumentUserActivityType` 。 例如：

```xml
<key>CFBundleDocumentTypes</key>
<array>
    <dict>
        <key>CFBundleTypeName</key>
        <string>NSRTFDPboardType</string>
        . . .
        <key>LSItemContentTypes</key>
        <array>
        <string>com.myCompany.rtfd</string>
        </array>
        . . .
        <key>NSUbiquitousDocumentUserActivityType</key>
        <string>com.myCompany.myEditor.editing</string>
    </dict>
</array>
```

在此示例中，字符串是一个反向 DNS 应用指示符，其中包含追加的活动的名称。 如果以这种方式输入，则不需要在 info.plist 文件的数组中重复活动类型项 `NSUserActivityTypes` 。 **Info.plist**

自动创建的用户活动对象 (可通过文档的 `UserActivity` 属性进行访问，) 可由应用中的其他对象引用，还可用于在延续时还原状态。 例如，跟踪项选择和文档位置。 `NeedsSave`如果状态发生更改，则需要将此活动属性设置为 `true` ，并更新 `UserInfo` 方法中的字典 `UpdateUserActivityState` 。

此 `UserActivity` 属性可从任何线程中使用，并且符合 (KVO) 协议的键-值观察，因此它可用于在文档移入和移出 iCloud 时保持同步。 `UserActivity`当文档关闭时，属性将无效。

有关详细信息，请参阅 Apple [在基于文档的应用文档中的用户活动支持](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5) 。

## <a name="supporting-handoff-in-responders"></a>支持响应中的提交

可以 `UIResponder` 通过设置事件的属性，将从 iOS 或 `NSResponder` OS X) 继承的响应方 (相关联 `UserActivity` 。 系统将 `UserActivity` 在适当的时间自动保存属性，调用响应方的 `UpdateUserActivityState` 方法，使用方法将当前数据添加到用户活动对象  `AddUserInfoEntriesFromDictionary` 。

## <a name="supporting-continuation-streams"></a>支持延续流

在某些情况下，继续执行活动所需的信息量无法通过初始的移交负载高效传输。 在这些情况下，接收应用可以在其自身与源应用之间建立一个或多个流以传输数据。

原始应用会将实例的 `SupportsContinuationStreams` 属性设置 `NSUserActivity` 为 `true` 。 例如：

```csharp
// Create a new user Activity to support this tab
UserActivity = new NSUserActivity (ThisApp.UserActivityTab1){
    Title = "Weather Tab",
    SupportsContinuationStreams = true
};
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

然后，接收应用可以 `GetContinuationStreams` `NSUserActivity` 在其中调用的方法 `AppDelegate` 来建立流。 例如：

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

在始发设备上，User 活动委托通过调用其方法来接收流， `DidReceiveInputStream` 以提供在恢复设备上继续用户活动所请求的数据。

你将使用 `NSInputStream` 来提供对流数据的只读访问权限以及 `NSOutputStream` 提供只写访问权限。 应在请求和响应方式中使用流，其中接收应用请求更多数据，而原始应用提供此流。 这样，在源设备上写入输出流的数据将从继续设备上的输入流中读取，反之亦然。

即使在需要延续流的情况下，这两个应用程序之间的通信也应该降到最低。

有关详细信息，请参阅 [使用延续流](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13) 的 Apple 文档。

## <a name="handoff-best-practices"></a>移交最佳实践

通过移交实现用户活动的无缝延续的成功实现需要认真设计，因为涉及到所有的组件。 Apple 建议采用适用于启用了提交的应用的以下最佳做法：

- 设计你的用户活动，以要求最小的有效负载将活动状态关联到继续。 有效负载越大，延续开始所用的时间就越长。
- 如果必须传输大量数据才能成功继续，请考虑配置和网络开销所涉及的成本。
- 大型 Mac 应用通常会创建由 iOS 设备上的多个更小的、特定于任务的应用处理的用户活动。 不同的应用程序和操作系统版本应设计为可以很好地协同工作或故障转移。
- 指定活动类型时，请使用反向 DNS 表示法以避免冲突。 如果某个活动特定于给定应用，则其名称应包含在类型定义中 (例如 `com.myCompany.myEditor.editing`) 。 如果活动可以跨多个应用，请从定义中删除应用名称 (例如 `com.myCompany.editing`) 。
- 如果应用需要更新用户活动 (状态 `NSUserActivity`) 请将 `NeedsSave` 属性设置为 `true` 。 在适当的时间，切换将调用委托的 `UserActivityWillSave` 方法，以便可以 `UserInfo` 根据需要更新字典。
- 由于移交过程可能无法在接收设备上立即进行初始化，因此你应该实现 `AppDelegate` 的 `WillContinueUserActivity` ，并通知用户延续即将开始。

## <a name="example-handoff-app"></a>提交示例应用

作为在 Xamarin iOS 应用程序中使用移交的示例，我们在本指南中包含了 [**MonkeyBrowser**](/samples/xamarin/ios-samples/ios8-monkeybrowser) 示例应用。 该应用具有四个选项卡，用户可以使用这些选项卡浏览 web，每个选项卡都具有给定的活动类型：天气、喜爱、咖啡休息和工作。

在任何选项卡上，当用户输入新的 URL 并点击 " **开始** " 按钮时，将 `NSUserActivity` 为该选项卡创建一个新的，其中包含用户当前正在浏览的 url：

[![提交示例应用](handoff-images/handoff01.png)](handoff-images/handoff01.png#lightbox)

如果用户的另一台设备已安装 **MonkeyBrowser** 应用，使用相同的用户帐户登录到 iCloud，位于同一网络上，并且与上述设备密切相关，则 "切换" 活动将显示在 "主屏幕" (在左下角) ：

[![显示在左下角的主屏幕上的移交活动](handoff-images/handoff02.png)](handoff-images/handoff02.png#lightbox)

如果用户向上拖动到 "切换" 图标上，将启动该应用，并且中指定的用户活动 `NSUserActivity` 将继续在新设备上：

[![新设备上的用户活动继续](handoff-images/handoff03.png)](handoff-images/handoff03.png#lightbox)

如果用户活动已成功发送到另一台 Apple 设备，则发送设备 `NSUserActivity` 将收到对其的方法的调用， `UserActivityWasContinued` `NSUserActivityDelegate` 以使其知道用户活动已成功传输到另一台设备。

## <a name="summary"></a>总结

本文提供了用于在用户的多个 Apple 设备之间继续执行用户活动的移交框架简介。 接下来，它展示了如何在 Xamarin iOS 应用程序中启用和实施移交。 最后，它讨论了不同类型的移交继续功能，并提供了移交最佳实践。

## <a name="related-links"></a>相关链接

- [iOS 9 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [MonkeyBrowser 示例](/samples/xamarin/ios-samples/ios8-monkeybrowser)
- [适用于开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 中的新增功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [HomeKit 用户界面指南](https://developer.apple.com/homekit/ui-guidelines/)
- [HomeKit Framework 参考](https://developer.apple.com/library/ios/home_kit_framework_ref)