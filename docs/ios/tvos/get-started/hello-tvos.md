---
title: 你好，tvOS 快速入门指南
description: 本指南逐步讲解如何创建你的第一个 Xamarin tvOS 应用及其开发工具链。 它还引入了 Xamarin 设计器，该设计器将 UI 控件公开给代码，并阐释了如何生成、运行和测试 tvOS 应用程序。
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: 35f0bc0668c6f80ee00b1253b16a8ac71770a9be
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030819"
---
# <a name="hello-tvos-quick-start-guide"></a>你好，tvOS 快速入门指南

_本指南逐步讲解如何创建你的第一个 Xamarin tvOS 应用及其开发工具链。它还引入了 Xamarin 设计器，该设计器将 UI 控件公开给代码，并阐释了如何生成、运行和测试 tvOS 应用程序。_

Apple 已发布第5代 Apple TV，即 Apple TV 4K，它运行 tvOS 11。

Apple TV 平台面向开发人员，使他们能够创建丰富的沉浸式应用程序，并通过 Apple TV 的内置应用商店进行发布。

如果你熟悉 Xamarin iOS 开发，你应发现过渡到 tvOS 的方式非常简单。 大多数 Api 和功能都是相同的，但很多公共 Api 都无法使用（如 WebKit）。 此外，使用 Siri 远程处理会引发一些设计挑战，这些挑战在基于触摸屏的 iOS 设备中不存在。

本指南将介绍如何在 Xamarin 应用中使用 tvOS。 有关 tvOS 的详细信息，请参阅 apple [For APPLE TV 4k](https://developer.apple.com/tvos/)文档。

## <a name="overview"></a>概述

使用 tvOS，可以使用在*Swift* （或*目标-C*） C#和*Xcode*中进行开发时使用的相同 OS X 库和接口控件在和 .net 中开发完全本机的 Apple 电视应用程序。

此外，由于 tvOS 应用是用C#和 .net 编写的，因此可以通过 Xamarin、Xamarin 和 Xamarin for Mac 应用共享通用的后端代码;同时提供每个平台的本机体验。

本文介绍了使用 tvOS 和 Visual Studio 创建 Apple TV 应用所需的关键概念，逐步引导你完成构建基本**的 Hello，tvOS**应用程序，该应用对按钮的点击次数进行计数：

[![](hello-tvos-images/run05.png "Example app run")](hello-tvos-images/run05.png#lightbox)

我们将介绍以下概念：

- **Visual Studio for Mac** – Visual Studio for Mac 简介，以及如何创建 tvOS 应用程序。
- **TvOS 应用的剖析**-tvOS 应用的组成。
- **创建用户界面**–如何使用 Xamarin Designer for iOS 来创建用户界面。
- **部署和测试**–如何在 tvOS 模拟器和实际 tvOS 硬件上运行和测试应用。

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>在 Visual Studio for Mac 中启动新的 tvOS 应用

如前文所述，我们将创建一个名为 `Hello-tvOS` 的 Apple 电视应用程序，该应用程序将单个按钮和标签添加到主屏幕。 点击此按钮后，标签会显示已点击次数。

若要开始操作，请执行以下操作：

1. 启动 Visual Studio for Mac：

    [![](hello-tvos-images/setup01.png "Visual Studio for Mac")](hello-tvos-images/setup01.png#lightbox)
2. 单击屏幕左上角的 "**新建解决方案 ...** " 链接以打开 "**新建项目**" 对话框。
3. 选择**tvOS** > **应用** > **单视图应用**，然后单击 "**下一步**" 按钮：

    [![](hello-tvos-images/setup02.png "Select Single View App")](hello-tvos-images/setup02.png#lightbox)
4. 输入**应用名称**`Hello, tvOS`，输入**组织标识符**，并单击 "**下一步**" 按钮：

    [![](hello-tvos-images/setup04.png "Enter Hello, tvOS")](hello-tvos-images/setup04.png#lightbox)
5. 输入**项目名称**`Hello_tvOS`，并单击 "**创建**" 按钮：

    [![](hello-tvos-images/setup03.png "Enter HellotvOS")](hello-tvos-images/setup03.png#lightbox)

Visual Studio for Mac 将创建新的 tvOS 应用，并显示已添加到应用程序解决方案的默认文件：

 [![](hello-tvos-images/project01.png "The default files view")](hello-tvos-images/project01.png#lightbox)

Visual Studio for Mac 使用**解决方案**和**项目**，与 Visual Studio 完全相同。 解决方案是可以容纳一个或多个项目的容器;项目可以包含应用程序、支持库、测试应用程序等。在这种情况下，Visual Studio for Mac 已为您创建了一个解决方案和一个应用程序项目。

如果需要，可以创建一个或多个包含公用共享代码的代码库项目。 这些库项目可用于应用程序项目，也可与其他 tvOS 应用项目（或基于代码类型的 Xamarin、Xamarin 和 Xamarin）共享，就像构建标准的 .NET 应用程序一样。

## <a name="anatomy-of-a-xamarintvos-app"></a>TvOS 应用的剖析

如果熟悉 iOS 编程，就会注意到这里有很多相似之处。 事实上，tvOS 9 是 iOS 9 的一个子集，因此，很多概念都将跨过这里。

让我们看一下项目中的文件：

- `Main.cs` – 此文件包含应用的主入口点。 启动应用时，其中会包含运行的首个类和方法。
- `AppDelegate.cs` –此文件包含主应用程序类，该类负责侦听来自操作系统的事件。
- `Info.plist` –此文件包含应用程序属性，如应用程序名称、图标等。
- `ViewController.cs` –这是表示主窗口并控制其生命周期的类。
- `ViewController.designer.cs` –此文件包含可帮助你与主屏幕用户界面集成的管道代码。
- `Main.storyboard` –主窗口的 UI。 此文件可以由 Xamarin Designer for iOS 创建和维护。

在以下部分中，我们将简要介绍一下其中一些文件。 稍后我们将更详细地探讨这些问题，但最好立即了解其基础知识。

### <a name="maincs"></a>Main.cs

`Main.cs` 文件包含静态 `Main` 方法，该方法创建新的 tvOS 应用程序实例，并传递将处理 OS 事件的类的名称，在本例中为 `AppDelegate` 类：

```csharp
using UIKit;

namespace Hello_tvOS
{
    public class Application
    {
        // This is the main entry point of the application.
        static void Main (string[] args)
        {
            // if you want to use a different Application Delegate class from "AppDelegate"
            // you can specify it here.
            UIApplication.Main (args, null, "AppDelegate");
        }
    }
}
```

### <a name="appdelegatecs"></a>AppDelegate.cs

`AppDelegate.cs` 文件包含我们的 `AppDelegate` 类，该类负责创建窗口和侦听 OS 事件：

```csharp
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    // The UIApplicationDelegate for the application. This class is responsible for launching the
    // User Interface of the application, as well as listening (and optionally responding) to application events from iOS.
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        // class-level declarations

        public override UIWindow Window {
            get;
            set;
        }

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Override point for customization after application launch.
            // If not required for your application you can safely delete this method

            return true;
        }

        public override void OnResignActivation (UIApplication application)
        {
            // Invoked when the application is about to move from active to inactive state.
            // This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message)
            // or when the user quits the application and it begins the transition to the background state.
            // Games should use this method to pause the game.
        }

        public override void DidEnterBackground (UIApplication application)
        {
            // Use this method to release shared resources, save user data, invalidate timers and store the application state.
            // If your application supports background execution this method is called instead of WillTerminate when the user quits.
        }

        public override void WillEnterForeground (UIApplication application)
        {
            // Called as part of the transition from background to active state.
            // Here you can undo many of the changes made on entering the background.
        }

        public override void OnActivated (UIApplication application)
        {
            // Restart any tasks that were paused (or not yet started) while the application was inactive.
            // If the application was previously in the background, optionally refresh the user interface.
        }

        public override void WillTerminate (UIApplication application)
        {
            // Called when the application is about to terminate. Save data, if needed. See also DidEnterBackground.
        }
    }
}
```

除非您之前生成了 iOS 应用程序，否则此代码可能不熟悉，但这种情况非常简单。 我们来看一下重要的行。

首先，让我们看一下类级变量声明：

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

`Window` 属性提供对主窗口的访问。 tvOS 使用所谓的*模型视图控制器*（MVC）模式。 通常，对于你创建的每个窗口（以及 windows 中的许多其他功能），都有一个控制器，该控制器负责窗口的生命周期（如显示它、向其添加新视图（控件）等）。

接下来，我们使用 `FinishedLaunching` 方法。 此方法在应用程序实例化后运行，并负责实际创建应用程序窗口并开始在其中显示视图。 由于我们的应用程序使用情节提要来定义其 UI，因此此处不需要其他代码。

模板中提供了许多其他方法，例如 `DidEnterBackground` 和 `WillEnterForeground`。 如果应用程序中未使用应用程序事件，则可以安全地删除这些事件。

### <a name="viewcontrollercs"></a>ViewController.cs

`ViewController` 类是主窗口的控制器。 这意味着它负责主窗口的生命周期。 稍后我们将详细介绍这一点，现在我们来简单了解一下：

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
    }
}
```

#### <a name="viewcontrollerdesignercs"></a>ViewController.Designer.cs

主窗口类的设计器文件现在为空，但在使用 iOS 设计器创建用户界面时，将自动填充该文件 Visual Studio for Mac：

```csharp
using Foundation;

namespace HellotvOS
{
    [Register ("ViewController")]
    partial class ViewController
    {
        void ReleaseDesignerOutlets ()
        {
        }
    }
}
```

我们通常并不关心设计器文件，因为它们是由 Visual Studio for Mac 自动管理的，只是提供必要的管道代码，使用户能够访问我们在应用程序中添加到任何窗口或视图的控件。

现在，我们已经创建了 tvOS 应用，并对其组件有了基本的了解，接下来让我们看看如何创建 UI。

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>创建用户界面

无需使用 Xamarin Designer for iOS 创建 tvOS 应用的用户界面，可以直接从C#代码创建 UI，但这超出了本文的范围。 为了简单起见，我们将在本教程的其余部分使用 iOS 设计器创建 UI。

若要开始创建 UI，请在**解决方案资源管理器**中双击 `Main.storyboard` 文件，在 IOS 设计器中将其打开以进行编辑：

[![](hello-tvos-images/designer01.png "The Main.storyboard file in the Solution Explorer")](hello-tvos-images/designer01.png#lightbox)

这会启动设计器，如下所示：

[![](hello-tvos-images/designer02.png "The Designer")](hello-tvos-images/designer02.png#lightbox)

有关 iOS 设计器及其工作原理的详细信息，请参阅 Xamarin Designer for iOS 指南[简介](~/ios/user-interface/designer/introduction.md)。

现在，我们可以开始向 tvOS 应用程序的设计图面添加控件。

请执行以下操作：

1. 找到**工具箱**，它应位于设计图面右侧：

    [![](hello-tvos-images/designer03.png "The Toolbox")](hello-tvos-images/designer03.png#lightbox)

    如果在此处找不到它，请浏览到 **"查看 >" > "工具箱"** 查看它。
2. 将**标签**从**工具箱**拖到设计图面上：

    [![](hello-tvos-images/designer04.png "Drag a Label from the Toolbox")](hello-tvos-images/designer04.png#lightbox)
3. 单击**属性板**中的 "**标题**" 属性，将该按钮的标题更改为 "`Hello, tvOS`"，并将 "**字号**" 设置为 "128"：

    [![](hello-tvos-images/designer05.png "Set the title to Hello, tvOS and set the Font Size to 128")](hello-tvos-images/designer05.png#lightbox)
4. 调整标签的大小，使所有单词可见，并将其放置在窗口顶部附近：

    [![](hello-tvos-images/designer06.png "Resize and center the label")](hello-tvos-images/designer06.png#lightbox)
5. 此标签现在需要限制为其位置，使其按预期方式显示。 无论屏幕大小如何。 为此，请单击标签，直到出现*T 形控点*：

    [![](hello-tvos-images/designer07.png "The T-shaped handle")](hello-tvos-images/designer07.png#lightbox)
6. 若要水平约束标签，请选择中心方块并将其拖到垂直虚线上：

    [![](hello-tvos-images/designer08.png "Select the center square")](hello-tvos-images/designer08zoom.png#lightbox)

     标签应变为橙色。
7. 选择标签顶部的 T 控点，并将其拖至窗口上边缘：

    [![](hello-tvos-images/designer09.png "Drag the handle to the top edge of the window")](hello-tvos-images/designer09.png#lightbox)
8. 接下来，依次单击宽度和高度*骨骼控点*，如下所示：

    [![](hello-tvos-images/designer10.png "The width and the height bone handles")](hello-tvos-images/designer10.png#lightbox)

     单击每个*骨骼控点*后，请分别选择 "宽度" 和 "高度" 设置固定尺寸。
9. 完成后，约束应类似于 "属性板" 的 "布局" 选项卡：

    [![](hello-tvos-images/designer11.png "Example Constraints")](hello-tvos-images/designer11.png#lightbox)
10. 将一个**按钮**从**工具箱**拖放到标签下。
11. 单击**属性板**中的 "**标题**" 属性，并将该按钮的标题更改为 `Click Me`：

    [![](hello-tvos-images/designer12.png "Change the buttons title to Click Me")](hello-tvos-images/designer12.png#lightbox)
12. 重复上面的步骤5到步骤8来限制 tvOS 窗口中的按钮。 不过，请将 T 控点拖到该标签的底部，而不是将其拖动到窗口顶部（如步骤 #7）：

    [![](hello-tvos-images/designer14.png "Constrain the button")](hello-tvos-images/designer14.png#lightbox)
13. 将另一个标签拖到按钮下，将其大小调整为与第一个标签相同的宽度，并将其**对齐方式**设置为 "**居中**"：

    [![](hello-tvos-images/designer15.png "Drag another label under the button, size it to be the same width as the first label and set its Alignment to Center")](hello-tvos-images/designer15.png#lightbox)
14. 与第一个标签和按钮相似，将此标签设置为 "居中"，并将其固定到 "位置和大小"：

    [![](hello-tvos-images/designer16.png "Pin the label into location and size")](hello-tvos-images/designer16.png#lightbox)
15. 保存对用户界面所做的更改。

当调整控件大小和移动控件时，应注意到设计器提供了基于[APPLE TV 人体接口准则](https://developer.apple.com/tvos/human-interface-guidelines/)的有用的 snap 提示。 这些指南将帮助你创建高质量的应用程序，这些应用程序对 Apple TV 用户具有熟悉的外观和感觉。

如果你在 "**文档大纲**" 部分中查看，则会看到组成用户界面的元素布局和层次结构的显示方式：

[![](hello-tvos-images/designer17.png "The Document Outline section")](hello-tvos-images/designer17.png#lightbox)

在此处，你可以选择要编辑的项，或拖动以根据需要对 UI 元素重新排序。 例如，如果某个 UI 元素被另一个元素所覆盖，则可以将其拖到列表底部，使其成为窗口中最顶层的项目。

现在，我们已经创建了用户界面，接下来需要公开 UI 项，以便 tvOS 可以在代码中C#访问它们并与其交互。

### <a name="accessing-the-controls-in-the-code-behind"></a>访问代码隐藏中的控件

可通过以下两种主要方法从代码访问 iOS 设计器中添加的控件：

- 为控件创建事件处理程序。
- 为控件提供一个名称，以便以后可以引用它。

当添加其中任何一个时，`ViewController.designer.cs` 中的分部类将会进行更新以反映所做的更改。 这将允许您访问视图控制器中的控件。

### <a name="creating-an-event-handler"></a>创建事件处理程序

在此示例应用程序中，当单击按钮时，我们希望发生_某些事情_，因此需要将事件处理程序添加到按钮上的特定事件。 若要进行此设置，请执行以下操作：

1. 在 Xamarin iOS 设计器中，选择 "视图控制器" 上的 "" 按钮。
2. 在 Properties pad 中，选择 "**事件**" 选项卡：

    [![](hello-tvos-images/event1.png "The Events tab")](hello-tvos-images/event1.png#lightbox)
3. 找到 TouchUpInside 事件，并为其指定一个名为 `Clicked`的事件处理程序：

    [![](hello-tvos-images/event2.png "The TouchUpInside event")](hello-tvos-images/event2.png#lightbox)
4. 按下**enter**后，将打开**ViewController**文件，并在代码中建议你的事件处理程序的位置。 使用键盘上的箭头键来设置位置：

    [![](hello-tvos-images/event3.png "Setting the location")](hello-tvos-images/event3.png#lightbox)
5. 这将创建分部方法，如下所示：

    [![](hello-tvos-images/event4.png "The partial method")](hello-tvos-images/event4.png#lightbox)

现在，我们已准备好开始添加一些代码，以允许该按钮正常工作。

### <a name="naming-a-control"></a>命名控件

单击该按钮时，该标签会根据单击次数进行更新。 为此，我们需要在代码中访问标签。 这是通过为其提供名称来完成的。 请执行以下操作：

1. 打开情节提要，然后选择视图控制器底部的标签。
2. 在 Properties pad 中，选择 "**小组件**" 选项卡：

    [![](hello-tvos-images/name1.png "Select the Widget tab")](hello-tvos-images/name1.png#lightbox)
3. 在 "**标识" > "名称**" 下添加 `ClickedLabel`：

    [![](hello-tvos-images/name2.png "Set ClickedLabel")](hello-tvos-images/name2.png#lightbox)

现在，我们已准备好开始更新标签！

### <a name="how-controls-are-accessed"></a>如何访问控件

如果在**解决方案资源管理器**中选择 `ViewController.designer.cs`，则可以看到 `ClickedLabel` 标签和 `Clicked` 事件处理程序如何映射到中C#的**插座**和**操作**：

[![](hello-tvos-images/accesscontrol.png "Outlets and Actions")](hello-tvos-images/accesscontrol.png#lightbox)

你可能还注意到 `ViewController.designer.cs` 是分部类，因此 Visual Studio for Mac 无需修改将覆盖对类所做的任何更改的 `ViewController.cs`。

通过以这种方式公开 UI 元素，可在视图控制器中访问它们。

通常，您不需要自行打开 `ViewController.designer.cs`，此处只是出于教育目的提供此内容。

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>编写代码

创建用户界面并将其 UI 元素通过**输出口**和**操作**公开给代码后，我们最终可以编写代码来提供程序功能。

在我们的应用程序中，每次单击第一个按钮时，我们都会更新标签以显示按钮的单击次数。 若要实现此目的，我们需要在**Solution Pad**中双击 `ViewController.cs` 文件进行编辑：

[![](hello-tvos-images/code01.png "The Solution Pad")](hello-tvos-images/code01.png#lightbox)

首先，我们需要在 `ViewController` 类中创建一个类级变量，以跟踪发生的点击数。 编辑类定义，使其类似于如下所示：

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        private int numberOfTimesClicked = 0;
        ...
```

接下来，在同一个类（`ViewController`）中，我们需要覆盖**ViewDidLoad**方法并添加一些代码，以便为标签设置初始消息：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

我们需要使用 `ViewDidLoad`，而不是其他方法（如 `Initialize`），因为在操作系统加载并实例化用户界面*后*，将在 `.storyboard` 文件中调用 `ViewDidLoad`。 如果在完全加载并实例化 `.storyboard` 文件之前，我们尝试访问 "标签" 控件，则会出现 `NullReferenceException` 错误，因为尚未创建 "标签" 控件。

接下来，我们需要添加代码以响应用户单击按钮。 将以下内容添加到我们创建的分部类：

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

用户单击按钮时，将调用此代码。

完成所有工作后，便可以生成并测试我们的 tvOS 应用程序了。

## <a name="testing-the-application"></a>测试应用程序

现在可以构建并运行应用程序，确保它按预期运行。 我们可以一步生成并运行所有操作，也可以在不运行它的情况下生成和运行。

每次生成应用程序时，都可以选择所需的生成类型：

- **调试**–使用额外的元数据将调试版本编译到 "" （应用程序）文件中，以便在应用程序运行时对其进行调试。
- **Release** –发布版本还创建 "" 文件，但它不包含调试信息，因此它更小，执行速度更快。  

你可以从 "Visual Studio for Mac" 屏幕左上角的 "**配置选择器**" 中选择生成类型：

[![](hello-tvos-images/run01.png "Select the type of build")](hello-tvos-images/run01.png#lightbox)

### <a name="building-the-application"></a>生成应用程序

在我们的示例中，我们只需要调试版本，因此请确保选择 "**调试**"。 首先，通过按**⌘ B**，或从 "**生成**" 菜单中选择 "**生成**"，来生成应用程序。

如果没有任何错误，您将在 Visual Studio for Mac 的状态栏中看到 "**生成成功**" 消息。 如果出现错误，请查看你的项目，并确保已正确执行这些步骤。 首先确认你的代码（在 Xcode 和 Visual Studio for Mac 中）是否与本教程中的代码匹配。

### <a name="running-the-application"></a>运行应用程序

若要运行应用程序，我们有三个选项：

- 按 **⌘+Enter**。
- 在“运行”菜单中，选择“调试”。
- 单击 Visual Studio for Mac 工具栏中的“播放”按钮（位于“解决方案资源管理器”上方）。

应用程序将生成（如果尚未生成），在调试模式下启动，tvOS 模拟器将启动并显示该应用程序的主界面窗口：

[![示例应用主屏幕](hello-tvos-images/run03.png)](hello-tvos-images/run03.png#lightbox)

从**硬件**菜单中，选择 "**显示 Apple TV 远程**"，以便可以控制模拟器。

[![](hello-tvos-images/run04.png "Select Show Apple TV Remote")](hello-tvos-images/run04.png#lightbox)

使用模拟器的远程，如果单击按钮几次，则应使用以下计数更新标签：

[![](hello-tvos-images/run05.png "The label with updated count")](hello-tvos-images/run05.png#lightbox)

祝贺你！ 这里我们介绍了许多方面的内容，但如果您按照本教程的学习，您现在应该对 tvOS 应用程序的组件以及用于创建它们的工具有深刻的了解。

## <a name="where-to-next"></a>下一步是什么？

由于用户与接口之间的断开连接（不在用户手边的房间内），开发 Apple 电视应用程序带来了几个难题，并限制了应用程序大小和存储的 tvOS。

因此，我们强烈建议你先阅读以下文档，然后再转到 tvOS 应用的设计：

- [TvOS 9 简介](~/ios/tvos/platform/tvos9.md)-本文介绍了 tvOS 9 中所有适用于 Xamarin tvOS 开发人员的新的和修改的 api 和功能。
- [使用导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md)–你的 tvOS 应用程序的用户不会像 iOS 一样直接与它交互，因为在此情况下，它们在设备屏幕上点击图像，而是使用 Siri 远程在房间内间接离开。 本文介绍了焦点的概念以及如何使用它来处理 Xamarin tvOS 应用的用户界面中的导航。
- [Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md)–用户与 Apple TV 和 tvOS 应用交互的主要方式是通过包含的 Siri 远程。 如果你的应用程序是一项游戏，你可以根据需要为你的应用中的 iOS （MFI）蓝牙游戏控制器创建第三方支持。 本文介绍了如何在 tvOS 应用程序中支持新的 Siri 远程和蓝牙游戏控制器。
- [资源和数据存储](~/ios/tvos/app-fundamentals/resources-data-storage.md)-与 iOS 设备不同，新的 Apple TV 并不为 tvOS 应用提供持久的本地存储。 因此，如果 tvOS 应用需要保存信息（如用户首选项），则它必须从 iCloud 存储和检索数据。 本文介绍如何在 tvOS 应用程序中使用资源和永久性数据存储。
- [使用图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)–创建 captivating 图标和图像是为 Apple TV 应用开发沉浸式用户体验的关键部分。 本指南将介绍为你的 tvOS 应用创建和包括必要的图形资产所需的步骤。
- [用户界面](~/ios/tvos/user-interface/index.md)–一般用户体验（UX）范围包括用户界面（UI）控件，使用 Xcode 时，请使用的 INTERFACE BUILDER 和 UX 设计原则。
- [部署和测试](~/ios/tvos/deploy-test/index.md)–此部分涵盖用于测试应用程序以及如何分发应用程序的主题。 此处的主题包括用于调试的工具、部署到测试人员以及如何将应用程序发布到 Apple TV 应用商店。

如果遇到任何使用 tvOS 的问题，请参阅[故障排除](~/ios/tvos/troubleshooting.md)文档，了解有关已知问题和解决方法的列表。

## <a name="summary"></a>总结

本文提供了一个快速入门，通过创建一个简单的 Hello，tvOS 应用程序来开发用于 Visual Studio for Mac tvOS 的应用程序。 其中介绍了 tvOS 设备预配、接口创建、tvOS 的编码和 tvOS 模拟器上的测试的基础知识。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [用 Xamarin 构建 tvOS 应用程序（视频）](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
