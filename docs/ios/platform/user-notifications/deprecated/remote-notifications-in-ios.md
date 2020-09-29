---
title: IOS 中的推送通知
description: 本文档介绍如何在 iOS 9 及更早版本中使用推送通知。 它讨论了证书，注册 Apple 推送通知网关服务 (APNS) 等。
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: f9ef6e833fe48bb2b124f02d741a39e4141e56e6
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436366"
---
# <a name="push-notifications-in-ios"></a>IOS 中的推送通知

> [!IMPORTANT]
> 本部分中的信息适用于 iOS 9 及更早版本。 对于 iOS 10 及更高版本，请参阅 [用户通知框架指南](~/ios/platform/user-notifications/index.md) ，以支持 ios 设备上的本地和远程通知。

推送通知应保持简短并只包含足够的数据，以便通知移动应用程序应该联系服务器应用程序进行更新。 例如，当新电子邮件到达时，服务器应用程序将仅通知移动应用程序已收到新电子邮件。 通知将不包含新的电子邮件本身。 移动应用程序随后会在适当的时候从服务器中检索新的电子邮件

IOS 中的推送通知中心是 *Apple 推送通知网关服务 (APNS) *。 这是 Apple 提供的一种服务，负责将通知从应用程序服务器路由到 iOS 设备。
下图说明了适用于 iOS 的推送通知拓扑： ![ 此图说明了适用于 ios 的推送通知拓扑](remote-notifications-in-ios-images/image4.png)

远程通知本身是 JSON 格式的字符串，遵循[iOS 开发人员文档](https://developer.apple.com/devcenter/ios/index.action)中的[本地和推送通知编程指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)中[的 "通知负载"](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1)部分指定的格式和协议。

Apple 维护两个 APNS 环境：一个 *沙箱* 和一个 *生产* 环境。 沙盒环境适用于在开发阶段进行测试，可在 `gateway.sandbox.push.apple.com` TCP 端口2195中找到。 生产环境应在已部署的应用程序中使用，并且可在 `gateway.push.apple.com` TCP 端口2195中找到。

## <a name="requirements"></a>要求

推送通知必须遵循 APNS 体系结构规定的以下规则：

- **256 字节消息限制** -通知的整个消息大小不得超过256个字节。
- **无回执确认** -APNS 不向发送方提供消息将其发送给目标接收方的任何通知。 如果设备无法访问且发送了多个顺序通知，则除了最近的所有通知都将丢失。 仅将最新的通知传送到设备。
- **每个应用程序都需要一个安全的证书** -必须通过 SSL 来与 APNS 通信。

## <a name="creating-and-using-certificates"></a>创建和使用证书

上一部分中所述的每个环境都需要各自的证书。 本部分将介绍如何创建证书，如何将其与预配配置文件相关联，以及如何获取用于 PushSharp 的个人信息交换证书。

1. 若要创建证书，请在 Apple 网站上的 iOS 预配门户上，如以下屏幕截图所示 (注意左侧) 的 "应用 Id" 菜单项：

    [![Apple 网站上的 iOS 预配门户](remote-notifications-in-ios-images/image5new.png)](remote-notifications-in-ios-images/image5new.png#lightbox)

2. 接下来，导航到应用 ID 部分，并创建新的应用 ID，如以下屏幕截图所示：

    [![导航到 "应用 Id" 部分，并创建新的应用 ID](remote-notifications-in-ios-images/image6new.png)](remote-notifications-in-ios-images/image6new.png#lightbox)

3. 单击该 **+** 按钮后，可以输入应用 ID 的描述和捆绑标识符，如下面的屏幕截图所示：

    [![输入应用 ID 的描述和捆绑标识符](remote-notifications-in-ios-images/image7new.png)](remote-notifications-in-ios-images/image7new.png#lightbox)

4. 请确保选择 " **显式应用 ID** "，并且包标识符不以结尾  `*` 。 这将创建适用于多个应用程序的标识符，并且推送通知证书必须适用于单个应用程序。

5. 在 "应用服务" 下，选择 **推送通知**：

    [![选择推送通知](remote-notifications-in-ios-images/image8new.png)](remote-notifications-in-ios-images/image8new.png#lightbox)

6. 并按 " **提交** " 确认新应用 ID 的注册：

    [![确认新的应用 ID 注册](remote-notifications-in-ios-images/image9new.png)](remote-notifications-in-ios-images/image9new.png#lightbox)

7. 接下来，必须为应用 ID 创建证书。 在左侧导航栏中，浏览到 " **证书" >** "，然后选择 `+` 按钮，如以下屏幕截图所示：

    [![为应用 ID 创建证书](remote-notifications-in-ios-images/image10new.png)](remote-notifications-in-ios-images/image8.png#lightbox)

8. 选择是要使用开发证书还是使用生产证书：

    [![选择开发或生产证书](remote-notifications-in-ios-images/image11new.png)](remote-notifications-in-ios-images/image11new.png#lightbox)

9. 然后选择刚刚创建的新应用 ID：

    [![选择刚创建的新应用 ID](remote-notifications-in-ios-images/image12new.png)](remote-notifications-in-ios-images/image12new.png#lightbox)

10. 这会显示一些说明，指导你完成使用 Mac 上的**密钥链 Access**应用程序创建*证书签名请求*的过程。

11. 创建证书后，必须将其用作生成过程的一部分，以对应用程序进行签名，使其可以注册 APNs。 这需要创建并安装使用该证书的预配配置文件。

12. 若要创建开发预配配置文件，请导航到 " **预配配置文件** " 部分，并使用刚刚创建的应用 Id 执行创建该配置文件的步骤。

13. 创建预配配置文件后，打开  **Xcode 管理器** 并进行刷新。 如果创建的预配配置文件未出现，则可能需要从 iOS 预配门户下载配置文件并手动导入它。 下面的屏幕截图显示了一个添加了预配配置文件的组织程序的示例：  
    [![此屏幕截图显示添加了预配配置文件的组织程序的示例](remote-notifications-in-ios-images/image13new.png)](remote-notifications-in-ios-images/image13new.png#lightbox)

14. 此时，我们需要将 Xamarin 项目配置为使用这个新创建的预配配置文件。 这是在 " **IOS 捆绑签名**" 选项卡下的 "**项目选项**" 对话框中完成的，如以下屏幕截图所示：  
    [![将 Xamarin iOS 项目配置为使用这个新创建的预配配置文件](remote-notifications-in-ios-images/image11.png)](remote-notifications-in-ios-images/image11.png#lightbox)

此时，应用程序配置为使用推送通知。 但是，证书仍需要几个步骤。 此证书采用的是 DER 格式，它不与 PushSharp 兼容，这需要 (PKCS12) 证书的个人信息交换。 若要转换证书，使其可供 PushSharp 使用，请执行以下最终步骤：

1. **下载证书文件** -登录到 IOS 设置门户，选择 "证书" 选项卡，选择与正确的配置文件关联的证书，然后选择 "  **下载** "。
1. **打开密钥链访问** -这是应用程序是 OS X 中的密码管理系统的 GUI 界面。
1. **导入证书** -如果尚未安装基于证书，则  **文件 .。。** 从 "密钥链访问" 菜单导入项目。 导航到上面导出的证书，然后选择该证书。
1. **导出证书** -展开证书以便显示关联的私钥，右键单击密钥并选择 "导出"。 系统将提示你输入导出文件的文件名和密码。

此时，我们已完成了证书。 我们创建了一个证书，用于对 iOS 应用程序进行签名，并将该证书转换为可在服务器应用程序中与 PushSharp 一起使用的格式。 接下来，让我们看看 iOS 应用程序如何与 APNS 交互。

## <a name="registering-with-apns"></a>向 APNS 注册

在 iOS 应用程序可以接收远程通知之前，它必须向 APNS 注册。 APNS 将生成唯一的设备令牌并将其返回给 iOS 应用程序。 然后，iOS 应用程序将获取设备令牌，然后向应用程序服务器注册自身。 完成所有操作后，注册完成后，应用程序服务器可以将通知推送到移动设备。

理论上，每次 iOS 应用程序将自身注册到 APNS 时，设备令牌可能会发生更改，但实际上不会发生这种情况。 作为一种优化方法，应用程序可以缓存最新的设备令牌，并仅在更改后更新应用程序服务器。 下图说明了注册和获取设备令牌的过程：

 ![此图说明了注册和获取设备令牌的过程](remote-notifications-in-ios-images/image12.png)

在 `FinishedLaunching` 应用程序委托类的方法中，通过在 `RegisterForRemoteNotificationTypes` 当前对象上调用来处理 APNS 注册 `UIApplication` 。 当 iOS 应用程序注册 APNS 时，它还必须指定要接收的远程通知的类型。 这些远程通知类型在枚举中声明 `UIRemoteNotificationType` 。 以下代码片段举例说明了 iOS 应用程序如何注册来接收远程警报和徽章通知：

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
    var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

    UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications ();
} else {
    UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
    UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
}
```

APNS 注册请求发生在后台-收到响应时，iOS 将调用 `RegisteredForRemoteNotifications` 类中的方法 `AppDelegate` ，并传递已注册的设备令牌。 标记将包含在 `NSData` 对象中。 以下代码片段演示了如何检索 APNS 提供的设备令牌：

```csharp
public override void RegisteredForRemoteNotifications (
UIApplication application, NSData deviceToken)
{
    // Get current device token
    var DeviceToken = deviceToken.Description;
    if (!string.IsNullOrWhiteSpace(DeviceToken)) {
        DeviceToken = DeviceToken.Trim('<').Trim('>');
    }

    // Get previous device token
    var oldDeviceToken = NSUserDefaults.StandardUserDefaults.StringForKey("PushDeviceToken");

    // Has the token changed?
    if (string.IsNullOrEmpty(oldDeviceToken) || !oldDeviceToken.Equals(DeviceToken))
    {
        //TODO: Put your own logic here to notify your server that the device token has changed/been created!
    }

    // Save new device token
    NSUserDefaults.StandardUserDefaults.SetString(DeviceToken, "PushDeviceToken");
}
```

如果注册由于某些原因而失败 (例如，设备未连接到 Internet) ，则 iOS 将 `FailedToRegisterForRemoteNotifications` 对应用程序委托类调用。 下面的代码段演示如何向用户显示警报，通知他们注册失败：

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>设备令牌内务处理

设备令牌将过期或随时间推移而变化。 由于这种情况，服务器应用程序需要进行一些房屋清洗，并清除这些过期或已更改的令牌。 当应用程序作为推送通知发送到具有过期令牌的设备时，APN 将记录并保存该过期令牌。 然后，服务器可以查询 APNS 来找出已过期的令牌。

用于提供 *反馈服务* 的 APNS-一种 HTTPS 终结点，通过创建的证书进行身份验证，以便发送推送通知并发送回有关令牌已过期的数据。 此已被 Apple 弃用并删除。

相反，有一个新的 HTTP 状态代码，用于表示之前由反馈服务报告的情况：

> 410-主题的设备令牌不再处于活动状态。

此外， `timestamp` 响应正文中将出现一个新的 JSON 数据密钥：

> 如果： status 标头中的值为410，则此项的值是上次确认该主题的设备令牌不再有效的时间。
>
> 停止推送通知，直到设备使用更高的时间戳向提供程序注册令牌。

## <a name="summary"></a>总结

本部分介绍 iOS 中有关推送通知的主要概念。 其中介绍了 Apple 推送通知网关服务 (APNS) 的角色。 然后，它介绍了如何创建和使用对 APNS 至关重要的安全证书。 最后，此文档已讨论了应用程序服务器如何使用 *反馈服务* 停止跟踪过期的设备令牌。

## <a name="related-links"></a>相关链接

- [通知-演示本地和远程通知 (示例) ](/samples/xamarin/ios-samples/notifications)
- [面向开发人员的本地和推送通知](https://developer.apple.com/notifications/)
- [UIApplication](/dotnet/api/uikit.uiapplication)
- [UIRemoteNotificationType](/dotnet/api/uikit.UIRemoteNotificationType)