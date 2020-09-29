---
title: Xamarin 中的高级用户通知
description: 本文更深入地介绍了 iOS 10 中引入的用户通知框架。 它讨论用户通知、用户通知界面、媒体附件、自定义用户界面等。
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: c5a352ce2bdf4df5e19e49c52966c6230af1f4b2
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435324"
---
# <a name="advanced-user-notifications-in-xamarinios"></a>Xamarin 中的高级用户通知

用户通知框架是 iOS 10 的新功能，它允许传递和处理本地和远程通知。 使用此框架，应用或应用扩展可以通过指定一组条件（如位置或一天的时间）来计划本地通知的传送。

## <a name="about-user-notifications"></a>关于用户通知

新的用户通知框架允许传递和处理本地和远程通知。 使用此框架，应用或应用扩展可以通过指定一组条件（如位置或一天的时间）来计划本地通知的传送。

此外，在将本地和远程通知传递给用户的 iOS 设备时，应用或扩展可以接收 (并可能修改) 本地和远程通知。

新用户通知 UI 框架允许应用或应用扩展在向用户显示本地和远程通知时，对其外观进行自定义。

此框架提供了以下方法，应用程序可以向用户提供通知：

- **视觉对象警报** -通知从屏幕顶部滚动为横幅的位置。
- **Sound 和 Vibrations** -可与通知相关联。
- **应用图标添加徽章** -其中，应用图标显示标记为新内容可用的徽章。 如未读电子邮件的数目。

此外，根据用户的当前上下文，会有不同的方法显示通知：

- 如果设备未锁定，则通知将从屏幕顶部向下滚动为横幅。
- 如果设备被锁定，通知将显示在用户的锁定屏幕上。
- 如果用户错过了通知，他们可以打开通知中心，并在其中查看任何可用的等待通知。

Xamarin iOS 应用有两种类型的用户通知可以发送：

- **本地通知** -这些应用由本地安装在用户设备上的应用发送。
- **远程通知** -从远程服务器发送，并显示给用户或触发应用内容的后台更新。

有关详细信息，请参阅我们的 [增强用户通知](~/ios/platform/user-notifications/enhanced-user-notifications.md) 文档。

## <a name="the-new-user-notification-interface"></a>新用户通知界面

IOS 10 中的用户通知提供了一个新的 UI 设计，提供了更多内容，例如，可在锁屏上显示的标题、副标题和可选媒体附件，作为设备顶部或通知中心的横幅。

无论在 iOS 10 中显示用户通知的位置如何，它都具有相同的外观和外观，并且具有相同的特性和功能。

在 iOS 8 中，Apple 引入了可操作的通知，开发人员可在其中将自定义操作附加到通知，并允许用户在不启动应用的情况下执行通知。 在 iOS 9 中，使用 "快速回复" 的 Apple 增强了可操作通知，用户可以使用该通知通过文本输入对通知进行响应。

由于用户通知是 iOS 10 中用户体验的一个更重要部分，因此 Apple 进一步扩展了可操作的通知，以支持3D 触摸，其中用户按了通知并显示自定义用户界面，以便与通知进行丰富的交互。

当显示自定义用户通知 UI 时，如果用户与任何附加到通知的操作进行交互，则可以立即更新自定义 UI，以提供与更改内容有关的反馈。

用户通知 UI API 是 iOS 10 的新功能，它允许 Xamarin iOS 应用轻松利用这些新的用户通知 UI 功能。

## <a name="adding-media-attachments"></a>添加媒体附件

在用户之间共享的较常见的项目之一是照片，因此，iOS 10 添加了将媒体项 (如照片) 直接附加到通知的功能，用户可以在其中向用户显示并随时提供通知内容的其余部分。

但是，因为即使是在发送小图像时所涉及的大小，但将其附加到远程通知有效负载是不切实际的。 若要处理这种情况，开发人员可以在 iOS 10 中使用新的服务扩展，从另一个源 (（例如 CloudKit 数据存储) 下载图像，并将其附加到通知的内容，然后将其显示给用户。

对于要由服务扩展修改的远程通知，其有效负载必须标记为可变。 例如：

```csharp
{
    aps : {
        alert : "New Photo Available",
        mutable-content: 1
    },
    my-attachment : "https://example.com/photo.jpg"
}
```

请查看以下过程概述：

[![添加媒体附件过程](advanced-user-notifications-images/extension02.png)](advanced-user-notifications-images/extension02.png#lightbox)

通过 APNs) 将远程通知传递到设备 (后，服务扩展可以通过任何所 (需的方式（例如) ）下载所需的映像 `NSURLSession` ，并在收到图像后，它可以修改通知的内容并将其显示给用户。

下面是如何在代码中处理此过程的示例：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyNotification
{
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Constructors
        public NotificationService (IntPtr handle) : base(handle)
        {
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            // Get file URL
            var attachementPath = request.Content.UserInfo.ObjectForKey (new NSString ("my-attachment"));
            var url = new NSUrl (attachementPath.ToString ());

            // Download the file
            var localURL = new NSUrl ("PathToLocalCopy");

            // Create attachment
            var attachmentID = "image";
            var options = new UNNotificationAttachmentOptions ();
            NSError err;
            var attachment = UNNotificationAttachment.FromIdentifier (attachmentID, localURL, options , out err);

            // Modify contents
            var content = request.Content.MutableCopy() as UNMutableNotificationContent;
            content.Attachments = new UNNotificationAttachment [] { attachment };

            // Display notification
            contentHandler (content);
        }

        public override void TimeWillExpire ()
        {
            // Handle service timing out
        }
        #endregion
    }
}
```

从 APNs 收到通知时，将从内容中读取图像的自定义地址，并从服务器下载文件。 然后， `UNNotificationAttachement` 使用唯一 ID 和图像的本地位置作为)  (创建 `NSUrl` 。 将创建通知内容的可变副本并添加媒体附件。 最后，通过调用来向用户显示通知 `contentHandler` 。

一旦将附件添加到通知，系统就会接管该文件的移动和管理。

除了上面提供的远程通知外，还支持从本地通知创建媒体附件，其中创建了 `UNNotificationAttachement` 通知，并将其内容附加到了通知。

IOS 10 中的通知支持图像 (静态和 Gif) 、音频或视频的图像附件，并且当向用户显示通知时，系统将自动为每种类型的附件显示正确的自定义 UI。

> [!NOTE]
> 应注意优化媒体大小和从远程 (服务器下载媒体所需的时间，或将媒体用于本地通知) ，因为系统会在运行应用服务扩展时对这两种情况施加严格限制。 例如，请考虑发送图像的缩小版本或视频的小剪辑，使其显示在通知中。

## <a name="creating-custom-user-interfaces"></a>创建自定义用户界面

若要为其用户通知创建自定义用户界面，开发人员需要将通知内容扩展添加 (新的 iOS 10) 添加到应用的解决方案。

通过通知内容扩展，开发人员可以将他们自己的视图添加到通知 UI，并绘制出所需的任何内容。 从 iOS 12 开始，通知内容扩展支持交互式 UI 控件（如按钮和滑块）。 有关详细信息，请参阅 [iOS 12 文档中的交互式通知](~/ios/platform/introduction-to-ios12/notifications/interactive.md) 。

若要支持用户与用户通知交互，应创建自定义操作，并将其注册到系统，并在将其与系统一起使用之前附加到通知。 将调用通知内容扩展插件来处理这些操作。 有关自定义操作的更多详细信息，请参阅[增强的用户通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)文档中的 "[使用通知操作](~/ios/platform/user-notifications/enhanced-user-notifications.md)" 部分。

向用户显示带有自定义 UI 的用户通知时，它将包含以下元素：

[![使用自定义 UI 元素的用户通知](advanced-user-notifications-images/customui01.png)](advanced-user-notifications-images/customui01.png#lightbox)

如果用户与在通知) 下显示的自定义操作 (进行交互，则可以更新用户界面，以便向用户提供反馈，就像调用给定操作时所发生的情况。

### <a name="adding-a-notification-content-extension"></a>添加通知内容扩展

若要在 Xamarin iOS 应用中实现自定义用户通知 UI，请执行以下操作：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中打开应用的解决方案。
2. 在**Solution Pad**中右键单击解决方案名称，然后选择 "**添加**" "  >  **添加新项目**"。
3. 选择 " **iOS**  >  **扩展**  >  **通知内容扩展**"，然后单击 "**下一步**" 按钮： 

    [![选择通知内容扩展](advanced-user-notifications-images/notify01.png)](advanced-user-notifications-images/notify01.png#lightbox)
4. 输入扩展的 **名称** ，然后单击 " **下一步** " 按钮： 

    [![输入扩展的名称](advanced-user-notifications-images/notify02.png)](advanced-user-notifications-images/notify02.png#lightbox)
5. 如果需要，请调整 **项目名称** 和/或 **解决方案名称** ，并单击 " **创建** " 按钮： 

    [![调整项目名称和/或解决方案名称](advanced-user-notifications-images/notify03.png)](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio for Mac 中打开应用的解决方案。
2. 在 **解决方案资源管理器** 中右键单击解决方案名称，然后选择 " **添加 >" 新建项目 ...**"。
3. 选择 " **Visual c # > IOS 扩展 > 通知内容扩展**：

    [![选择通知内容扩展](advanced-user-notifications-images/notify01.w157-sml.png)](advanced-user-notifications-images/notify01.w157.png#lightbox)
4. 输入扩展的 **名称** ，然后单击 **"确定"** 按钮。

-----

向解决方案添加通知内容扩展时，将在扩展的项目中创建三个文件：

1. `NotificationViewController.cs` -这是通知内容扩展插件的主视图控制器。
2. `MainInterface.storyboard` -开发人员在 iOS 设计器中将通知内容扩展的可见 UI 布局。
3. `Info.plist` -控制通知内容扩展插件的配置。

默认文件如下所 `NotificationViewController.cs` 示：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

        }
        #endregion
    }
}
```

`DidReceiveNotification`当用户扩展通知时，将调用方法，以便通知内容扩展可以使用的内容填充自定义 UI `UNNotification` 。 在上面的示例中，已向视图添加标签，并将其公开给名称为的代码， `label` 并用于显示通知的正文。

### <a name="setting-the-notification-content-extensions-categories"></a>设置通知内容扩展的类别

系统需要知道如何根据它所响应的特定类别查找应用的通知内容扩展。 执行以下操作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 双击 `Info.plist` **Solution Pad** 中的扩展文件，将其打开进行编辑。
2. 切换到“源”视图。
3. 展开该 `NSExtension` 密钥。
4. `UNNotificationExtensionCategory`在此示例的 "事件-邀请") 中，用扩展所属的类别的值添加密钥作为 Type **String** (： 

    [![添加 UNNotificationExtensionCategory 项](advanced-user-notifications-images/customui02.png)](advanced-user-notifications-images/customui02.png#lightbox)
5. 保存所做更改。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 双击 `Info.plist` **解决方案资源管理器** 中的扩展文件，将其打开进行编辑。
2. 展开该 `NSExtension` 密钥。
3. `UNNotificationExtensionCategory`在此示例的 "事件-邀请") 中，用扩展所属的类别的值添加密钥作为 Type **String** (： 

    [![添加 UNNotificationExtensionCategory 项](advanced-user-notifications-images/customui02w.png)](advanced-user-notifications-images/customui02w.png#lightbox)
4. 保存所做更改。

-----

通知内容扩展类别 (`UNNotificationExtensionCategory`) 使用用于注册通知操作的相同类别值。 如果应用将为多个类别使用同一 UI，请将切换 `UNNotificationExtensionCategory` 到类型 **阵列** ，并提供所需的所有类别。 例如：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![通知内容扩展类别](advanced-user-notifications-images/customui03.png)](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![通知内容扩展类别](advanced-user-notifications-images/customui03w.png)](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>隐藏默认通知内容

如果自定义通知 UI 将显示与默认通知相同的内容 (标题、副标题和正文会自动显示在通知 UI) 的底部，则可以通过将密钥添加到密钥，将其 `UNNotificationExtensionDefaultContentHidden` `NSExtensionAttributes` 作为类型 **布尔** 值添加到 `YES` 扩展的文件中，从而隐藏此默认信息 `Info.plist` ：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![查找默认信息](advanced-user-notifications-images/customui04.png)](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![查找默认信息](advanced-user-notifications-images/customui04w.png)](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>设计自定义 UI

若要设计通知内容扩展插件的自定义用户界面，请双击该 `MainInterface.storyboard` 文件以在 IOS 设计器中将其打开以进行编辑，并将生成所需的接口所需的元素拖到 (例如 `UILabels` 和 `UIImageViews`) 中。

> [!NOTE]
> 从 iOS 12，通知内容扩展可以包含交互式控件，如按钮和文本字段。 有关详细信息，请参阅 [iOS 12 文档中的交互式通知](~/ios/platform/introduction-to-ios12/notifications/interactive.md) 。

UI 已布局并且向 c # 代码公开所需的控件后，打开 `NotificationViewController.cs` 以进行编辑，并修改 `DidReceiveNotification` 方法以便在用户展开通知时填充 UI。 例如：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

        }
        #endregion
    }
}
```

### <a name="setting-the-content-area-size"></a>设置内容区域大小

若要调整向用户显示的内容区域的大小，请按以下代码将 `PreferredContentSize` 方法中的属性设置 `ViewDidLoad` 为所需的大小。 还可以通过在 iOS 设计器中将约束应用于视图来调整此大小，开发人员可以选择最适合自己的方法。

由于在调用通知内容扩展之前通知系统已在运行，因此，内容区域将以完全大小开始，并在向用户显示时向下动态显示所请求的大小。

若要消除这种影响，请在文件中编辑 `Info.plist` 扩展，并将 `UNNotificationExtensionInitialContentSizeRatio` 键的键设置 `NSExtensionAttributes` 为类型 **Number** ，并将值设置为表示所需的比率。 例如：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![UNNotificationExtensionInitialContentSizeRatio 键](advanced-user-notifications-images/customui05.png)](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![UNNotificationExtensionInitialContentSizeRatio 键](advanced-user-notifications-images/customui05w.png)](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>在自定义 UI 中使用媒体附件

由于媒体附件 ([如上所示](#adding-media-attachments)) 是通知负载的一部分，因此可以在通知内容扩展中对其进行访问和显示，就像在默认通知 UI 中一样。

例如，如果上面的自定义 UI 包含 `UIImageView` 向 c # 代码公开的，则可以使用以下代码将其从媒体附件填充：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }
        #endregion
    }
}
```

因为媒体附件由系统进行管理，所以它不在应用的沙箱中。 扩展需要通过调用方法来通知系统它需要访问文件 `StartAccessingSecurityScopedResource` 。 扩展完成后，需要调用 `StopAccessingSecurityScopedResource` 以释放其连接。

### <a name="adding-custom-actions-to-a-custom-ui"></a>将自定义操作添加到自定义 UI

自定义操作按钮可用于向自定义通知 UI 添加交互。 有关自定义操作的更多详细信息，请参阅[增强的用户通知](~/ios/platform/user-notifications/enhanced-user-notifications.md)文档中的 "[使用通知操作](~/ios/platform/user-notifications/enhanced-user-notifications.md)" 部分。

除了自定义操作外，通知内容扩展还可以响应以下内置操作：

- **默认操作** -当用户点击某个通知打开应用并显示给定通知的详细信息时，则为。
- **取消操作** -当用户关闭给定通知时，此操作将发送到应用程序。

通知内容扩展还可以在用户调用其中一个自定义操作时更新他们的 UI，例如，当用户点击 " **接受** 自定义操作" 按钮时将日期显示为 "已接受"。 此外，通知内容扩展可以告知系统延迟通知 UI 的消除，以便用户可以在通知关闭之前查看操作的效果。

这是通过实现 `DidReceiveNotification` 包含完成处理程序的方法的第二个版本来完成的。 例如：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;
using CoreGraphics;

namespace myApp {
    public class NotificationViewController : UIViewController, UNNotificationContentExtension {

        public override void ViewDidLoad() {
            base.ViewDidLoad();

            // Adjust the size of the content area
            var size = View.Bounds.Size
            PreferredContentSize = new CGSize(size.Width, size.Width/2);
        }

        public void DidReceiveNotification(UNNotification notification) {

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = Content.UserInfo["location"] as string;
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments[0];
                if (attachment.Url.StartAccessingSecurityScopedResource()) {
                    EventImage.Image = UIImage.FromFile(attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch(response.ActionIdentifier){
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
    }
}
```

通过将 `Server.PostEventResponse` 处理程序添加到 `DidReceiveNotification` 通知内容扩展的方法中，该扩展插件 *必须* 处理所有自定义操作。 扩展还可以通过更改将自定义操作转发到包含应用程序 `UNNotificationContentExtensionResponseOption` 。 例如：

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>使用自定义 UI 中的文本输入操作

根据应用程序和通知的设计，有时可能需要用户在通知中输入文本 (如) 回复消息。 与标准通知一样，通知内容扩展可以访问内置文本输入操作。

例如：

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Computed Properties
        // Allow to take input
        public override bool CanBecomeFirstResponder {
            get { return true; }
        }

        // Return the custom created text input view with the
        // required buttons and return here
        public override UIView InputAccessoryView {
            get { return InputView; }
        }
        #endregion

        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Private Methods
        private UNNotificationCategory MakeExtensionCategory ()
        {

            // Create Accept Action
            ...

            // Create decline Action
            ...

            // Create Text Input Action
            var commentID = "comment";
            var commentTitle = "Comment";
            var textInputButtonTitle = "Send";
            var textInputPlaceholder = "Enter comment here...";
            var commentAction = UNTextInputNotificationAction.FromIdentifier (commentID, commentTitle, UNNotificationActionOptions.None, textInputButtonTitle, textInputPlaceholder);

            // Create category
            var categoryID = "event-invite";
            var actions = new UNNotificationAction [] { acceptAction, declineAction, commentAction };
            var intentIDs = new string [] { };
            var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);

            // Return new category
            return category;

        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Is text input?
            if (response is UNTextInputNotificationResponse) {
                var textResponse = response as UNTextInputNotificationResponse;
                Server.Send (textResponse.UserText, () => {
                    // Close Notification
                    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
                });
            }

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch (response.ActionIdentifier) {
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
        #endregion
    }
}
```

此代码创建一个新的文本输入操作，并将其添加到) 方法中的扩展的类别 (`MakeExtensionCategory` 。 在 `DidReceive` 重写方法中，它通过以下代码处理用户输入文本：

```csharp
// Is text input?
if (response is UNTextInputNotificationResponse) {
    var textResponse = response as UNTextInputNotificationResponse;
    Server.Send (textResponse.UserText, () => {
        // Close Notification
        completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
    });
}
```

如果设计调用将自定义按钮添加到文本输入字段，请添加以下代码以包含它们：

```csharp
// Allow to take input
public override bool CanBecomeFirstResponder {
    get {return true;}
}

// Return the custom created text input view with the
// required buttons and return here
public override UIView InputAccessoryView {
    get {return InputView;}
}
```

当用户触发注释操作时，视图控制器和自定义文本输入字段都需要激活：

```csharp
// Update UI when the user interacts with the
// Notification
Server.PostEventResponse += (response) {
    // Take action based on the response
    switch(response.ActionIdentifier){
    ...
    case "comment":
        BecomeFirstResponder();
        TextField.BecomeFirstResponder();
        break;
    }

    // Close Notification
    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);

};
```

## <a name="summary"></a>总结

本文大致介绍了如何在 Xamarin iOS 应用中使用新的用户通知框架。 本文介绍了如何将媒体附件添加到本地和远程通知，并介绍了如何使用新的用户通知 UI 创建自定义通知 ui。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [UserNotifications Framework 参考](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [本地和远程通知编程指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)