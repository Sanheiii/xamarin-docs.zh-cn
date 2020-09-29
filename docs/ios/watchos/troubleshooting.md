---
title: watchOS 故障排除
description: 本文档介绍了通过 Xamarin 进行 watchOS 开发的已知问题和解决方法。 它介绍了有问题的图像、手动添加接口控制器文件、从命令行启动监视应用程序等。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 7e56eed866cb647bd654370d587b02bcaba04d4e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432806"
---
# <a name="watchos-troubleshooting"></a>watchOS 故障排除

本页包含可能遇到的问题的其他信息和解决方法。

- [已知问题](#knownissues)

- [从图标图像中删除 Alpha 通道](#noalpha)

- [手动添加 Xcode Interface Builder 的接口控制器文件](#add) 。

- [从命令行启动 WatchApp](#command_line)。

<a name="knownissues"></a>

## <a name="known-issues"></a>已知问题

### <a name="general"></a>常规

<a name="deploy"></a>

- 较早版本的 Visual Studio for Mac 错误地将 **AppleCompanionSettings** 图标之一显示为88x88 像素;如果尝试提交到 App Store，则会导致 **缺少图标错误** 。
    此图标应为87x87 像素 (29 个单位的 **@3x** Retina 屏幕) 。 无法在 Visual Studio for Mac 中解决此问题-在 Xcode 中编辑图像资产，或者手动编辑文件 **Contents.js** 。

- 如果未[正确设置](~/ios/watchos/get-started/project-references.md)监视扩展项目的**Info.plist > WKApp 捆绑 Id**与 WATCH 应用的**捆绑 id**相匹配，则调试器将无法连接，并且 Visual Studio for Mac 将等待消息 *"等待调试程序连接"*。

- 在 **通知** 模式下支持调试，但可能不可靠。 重试有时会起作用。 确认 "监视" 应用的 " **info.plist** " `WKCompanionAppBundleIdentifier` 设置为与 "iOS 父/容器" 应用的捆绑标识符 (（即在 iPhone) 上运行的应用程序。

- iOS 设计器不显示用于速览或通知界面控制器的入口点箭头。

- 不能将两个添加 `WKNotificationControllers` 到情节提要。
    解决方法： `notificationCategory` STORYBOARD XML 中的元素始终以相同的方式插入 `id` 。 若要解决此问题，你可以添加两个 (或更多) 通知控制器，在文本编辑器中打开情节提要文件，然后手动将 `id` 元素更改为唯一。

    [![在文本编辑器中打开情节提要文件，并手动将 id 元素更改为唯一](troubleshooting-images/duplicate-id-sml.png)](troubleshooting-images/duplicate-id.png#lightbox)

- 尝试启动应用程序时，可能会看到错误 "尚未生成应用程序"。 如果启动项目设置为监视扩展项目，则会在 **清除** 后出现这种情况。
    解决方法是选择 " **生成" > "全部重新生成** "，然后重新启动应用。

### <a name="visual-studio"></a>Visual Studio

对于 Watch 工具包，iOS 设计器支持 *要求* 正确配置解决方案。 如果未设置项目引用 (请参阅 [如何设置引用](~/ios/watchos/get-started/project-references.md)) 然后，设计图面将不能正常工作。

<a name="noalpha"></a>

## <a name="removing-the-alpha-channel-from-icon-images"></a>从图标图像中删除 Alpha 通道

图标不应包含 alpha 通道 (alpha 通道定义图像) 的透明区域，否则应用程序存储提交过程中将拒绝应用程序，并出现类似于下面的错误：

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

可以轻松地使用 **预览版** 应用删除 Mac OS X 上的 alpha 通道：

1. 在 **预览** 中打开图标图像，然后选择 " **文件" > 导出**。

2. 如果出现 alpha 通道，则显示的对话框将包括 **alpha** 复选框。

    ![如果存在 alpha 通道，则显示的对话框将包括 Alpha 复选框](troubleshooting-images/remove-alpha-sml.png)

3. *Untick* " **Alpha** " 复选框，并将文件 **保存** 到正确的位置。

4. 图标图像现在应传递 Apple 的验证检查。

<a name="add"></a>

## <a name="manually-adding-interface-controller-files"></a>手动添加接口控制器文件

> [!IMPORTANT]
> Xamarin 的 WatchKit 支持包括在 iOS 设计器中设计观看情节提要 (同时 Visual Studio for Mac 和 Visual Studio) ，这不需要下述步骤。 只需在 "Visual Studio for Mac 属性" pad 中为接口控制器指定类名称，将自动创建 c # 代码文件。

*如果* 你使用的是 Xcode Interface Builder，请按照以下步骤为你的 watch 应用程序创建新的接口控制器并启用与 Xcode 的同步，以便可以在 c # 中使用插座和操作：

1. 在 Xcode 中打开 "监视" 应用的 **界面。情节提要** **Interface Builder**。

    ![在 Xcode 中打开情节提要 Interface Builder](troubleshooting-images/add-6.png)

2. 将新的拖 `InterfaceController` 到情节提要：

    ![InterfaceController](troubleshooting-images/add-1.png)

3. 你现在可以将控件拖到接口控制器上 (例如。 标签和按钮) 但你仍无法创建插座或操作，因为不存在 **.h** 头文件。 以下步骤将导致创建所需的 **.h** 头文件。

    ![布局中的按钮](troubleshooting-images/add-2.png)

4. 关闭情节提要并返回到 Visual Studio for Mac。 在 "**监视应用扩展**项目" 中创建新的 c # 文件**MyInterfaceController.cs** (或任何所) 需的名称， (不) 情节提要的监视应用本身。 添加以下代码 (更新命名空间、classname 和构造函数名称) ：

    ```csharp
    using System;
    using WatchKit;
    using Foundation;

    namespace WatchAppExtension  // remember to update this
    {
        public partial class MyInterfaceController // remember to update this
        : WKInterfaceController
        {
            public MyInterfaceController // remember to update this
            (IntPtr handle) : base (handle)
            {
            }
            public override void Awake (NSObject context)
            {
                base.Awake (context);
                // Configure interface objects here.
                Console.WriteLine ("{0} awake with context", this);
            }
            public override void WillActivate ()
            {
                // This method is called when the watch view controller is about to be visible to the user.
                Console.WriteLine ("{0} will activate", this);
            }
            public override void DidDeactivate ()
            {
                // This method is called when the watch view controller is no longer visible to the user.
                Console.WriteLine ("{0} did deactivate", this);
            }
        }
    }
    ```

5. 在 "**监视应用扩展**" 项目中创建另一个新的 c # 文件**MyInterfaceController.designer.cs** ，并添加以下代码。 请确保更新命名空间、classname 和 `Register` 属性：

    ```csharp
    using Foundation;
    using System.CodeDom.Compiler;

    namespace HelloWatchExtension  // remember to update this
    {
        [Register ("MyInterfaceController")] // remember to update this
        partial class MyInterfaceController  // remember to update this
        {
            void ReleaseDesignerOutlets ()
            {
            }
        }
    }
    ```

    > [!TIP]
    > 您可以选择 () 使此文件成为第一个文件的子节点，方法是将该文件拖到 Visual Studio for Mac Solution Pad 中的其他 c # 文件中。 它将如下所示：

    ![解决方案板](troubleshooting-images/add-5.png)

6. 选择 " **生成" > "生成所有** "，以便 Xcode 同步通过使用的特性) 来识别新类 (`Register` 。

7. 右键单击 "监视应用情节提要" 文件并选择 "  **打开方式" > Xcode "Interface Builder**，重新打开情节提要：

    ![在 Interface Builder 中打开情节提要](troubleshooting-images/add-6.png)

8. 选择新的接口控制器，并为其指定上面定义的类名，例如。 `MyInterfaceController`.
    如果一切都正常工作，则它应自动显示在 **类：** 下拉列表中，你可以从该下拉列表中选择它。

    ![设置自定义类](troubleshooting-images/add-4.png)

9. 选择 Xcode 中的 " **助手编辑器** " 视图 (带有两个重叠圆圈的图标) 以便您可以并排查看情节提要和代码：

    ![助手编辑器工具栏项](troubleshooting-images/add-7.png)

    当焦点位于 "代码" 窗格中时，请确保查看的是  **.h** 头文件，如果不在痕迹导航栏中右键单击并选择正确的文件 (**MyInterfaceController**) 

    ![选择 MyInterfaceController](troubleshooting-images/add-8.png)

10. 你现在可以通过 **Ctrl +** 从情节提要拖到 **.h** 头文件来创建输出口和操作。

    ![创建输出口和操作](troubleshooting-images/add-9.png)

    当你放开拖动时，系统将提示你选择是创建输出口还是使用操作，并选择其名称：

    !["输出口" 和 "操作" 对话框](troubleshooting-images/add-a.png)

11. 保存情节提要更改并关闭 Xcode 后，将返回 Visual Studio for Mac。 它将检测头文件更改，并自动将代码添加到 **designer.cs** 文件中：

    ```csharp
    [Register ("MyInterfaceController")]
    partial class MyInterfaceController
    {
        [Outlet]
        WatchKit.WKInterfaceButton myButton { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (myButton != null) {
                myButton.Dispose ();
                myButton = null;
            }
        }
    }
    ```

你现在可以引用控件 (或实现 c # 中的操作) ！

<a name="command_line"></a>

## <a name="launching-the-watch-app-from-the-command-line"></a>从命令行启动 Watch 应用

> [!IMPORTANT]
> 默认情况下，你可以在普通应用模式下启动 "监视应用"，也可以使用 Visual Studio for Mac 和 Visual Studio 中的[自定义执行参数](~/ios/watchos/get-started/installation.md#custommodes)**一目了然**或**通知**模式。

你还可以使用命令行来控制 iOS 模拟器。 用于启动监视应用的命令行工具是 **mtouch**。

下面是一个完整示例， (作为终端) 中的单行执行：

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

你需要更新以反映你的应用程序的参数 `launchsimwatch` ：

### <a name="--launchsimwatch"></a>--launchsimwatch

适用于 iOS 应用的主应用捆绑包的完整路径 *，其中包含 watch 应用和扩展*。

> [!NOTE]
> 你需要提供的路径适用于 *iPhone 应用程序*应用程序文件，即，将部署到 iOS 模拟器并包含监视扩展和监视应用的路径。

示例：

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

## <a name="notification-mode"></a>通知模式

若要测试应用的 [**通知** 模式](~/ios/watchos/platform/notifications.md)，请将 `watchlaunchmode` 参数设置为， `Notification` 并提供包含测试通知负载的 JSON 文件的路径。

负载参数对于通知模式是 *必需* 的。

例如，将以下参数添加到 mtouch 命令：

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```

## <a name="other-arguments"></a>其他参数

其余参数如下所述：

### <a name="--sdkroot"></a>--sdkroot

必需。 指定 Xcode (6.2 或更高版本的路径) 。

示例：

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>--设备

要执行的模拟器设备。 这可以通过两种方式来指定：使用特定设备的 udid 或使用运行时和设备类型的组合。

确切的值因计算机而异，可使用 Apple 的 **simctl** 工具进行查询：

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl list
```

**UDID**

示例：

```bash
--device=:v2:udid=AAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE
```

**运行时和设备类型**

示例：

```bash
--device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
```

## <a name="related-links"></a>相关链接

- [WatchKitCatalog (示例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables (示例) ](/samples/xamarin/ios-samples/watchos-watchtables)