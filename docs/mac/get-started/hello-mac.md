---
title: Hello，Mac - 演练
description: 本文档演示如何创建 Xamarin.Mac 应用，并介绍了 Visual Studio for Mac、Xcode 和 Interface Builder。 其中介绍了如何通过输出口和操作向代码公开 UI 控件，并说明了如何生成、运行和测试 Xamarin.Mac 应用程序。
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 37D0E9E6-979B-7069-B3BE-C5F0AF99BA72
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 09/02/2018
ms.openlocfilehash: 49a4597aec37c1a9be550f62f4f97dd8fe7def08
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430619"
---
# <a name="hello-mac-walkthrough"></a>Hello, Mac – 演练

通过 Xamarin.Mac，可使用以 Objective-C  或 Swift  进行开发时所用的 macOS API 来以 C# 和 .NET 开发完全本机 Mac 应用。 由于 Xamarin.Mac 与 Xcode 直接集成，开发人员可以使用 Xcode 的 _Interface Builder_ 创建应用的用户界面（或选择通过 C# 代码直接创建）。

此外，由于 Xamarin.Mac 应用程序通过 C# 和 .NET 编写而成，因而可与 Xamarin.iOS 和 Xamarin.Android 移动应用共享代码；同时在每个平台上实现本机体验。

本文通过生成一个对按钮点击次数进行计数的简单 Hello, Mac 应用，介绍了通过 Xamarin.Mac、Visual Studio for Mac 以及 Xcode 的 Interface Builder 创建 Mac 应用需要用到的主要概念  ：

[![正在运行的了解 Mac 应用示例](hello-mac-images/run02-sml.png)](hello-mac-images/run02.png#lightbox)

包含以下概念：

- **Visual Studio for Mac** – 介绍 Visual Studio for Mac，以及如何使用 Visual Studio for Mac 创建 Xamarin.Mac 应用程序。
- **Xamarin.Mac 应用程序剖析** – Xamarin.Mac 应用程序组成部分。
- **Xcode 的 Interface Builder** - 如何使用 Xcode 的 Interface Builder 定义应用的用户界面。
- **输出口和操作** - 如何使用输出口和操作在用户界面中连接控件。
- **部署/测试** – 如何运行和测试 Xamarin.Mac 应用。

## <a name="requirements"></a>要求

Xamarin.Mac 应用程序开发需要以下条件：

- 运行 macOS High Sierra (10.13) 或更高版本的 Mac 计算机。
- [Xcode 10 或更高版本](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)。
- 最新版本的 [Xamarin.Mac 和 Visual Studio for Mac](/visualstudio/mac/installation/)。

若要运行使用 Xamarin.Mac 构建的应用程序，需要：

- 运行 macOS 10.7 或更高版本的 Mac 计算机。

> [!WARNING]
> 即将发布的 Xamarin.Mac 4.8 版本仅支持 macOS 10.9 或更高版本。
> 早期版本的 Xamarin.Mac 支持 macOS 10.7 或更高版本，但这些较旧的 macOS 版本缺少足够的 TLS 基础结构，无法支持 TLS 1.2。 若要面向 macOS 10.7 或 macOS 10.8，请使用 Xamarin.Mac 4.6 或更早版本。

## <a name="starting-a-new-xamarinmac-app-in-visual-studio-for-mac"></a>在 Visual Studio for Mac 中启动新的 Xamarin.Mac 应用

如上所述，本指南将介绍如何创建名称为 `Hello_Mac` 的 Mac 应用，该应用会向主窗口添加一个按钮和标签。 点击此按钮后，标签会显示已点击次数。

若要开始，请执行以下步骤：

1. 启动 Visual Studio for Mac：

    [![主要的 Visual Studio for Mac 接口](hello-mac-images/setup01-sml.png)](hello-mac-images/setup01.png#lightbox)

2. 单击“新建项目...”按钮以打开“新建项目”对话框，然后依次选择“Mac” > “应用” > “Cocoa 应用”，单击“下一步”按钮：

    [![选择 Cocoa 应用](hello-mac-images/setup02-sml.png)](hello-mac-images/setup02.png#lightbox)

3. 在“应用名称”中输入 `Hello_Mac`，其他所有项保持默认值。 单击“下一步”：

    [![设置应用名称](hello-mac-images/setup03-sml.png)](hello-mac-images/setup03.png#lightbox)

4. 确认该新项目在计算机中的位置：

    [![验证新解决方案详细信息](hello-mac-images/setup04-sml.png)](hello-mac-images/setup04.png#lightbox)

5. 单击“创建”按钮。

Visual Studio for Mac 会创建新的 Xamarin.Mac 应用，并显示添加到此应用解决方案的默认文件：

[![新的解决方案默认视图](hello-mac-images/project01-sml.png)](hello-mac-images/project01.png#lightbox)

Visual Studio for Mac 使用与Visual Studio 2019 相同的解决方案和项目结构 。 解决方案是可包含一个或多个项目的容器；项目可包含应用程序、支持库和测试应用程序等。“文件 > 新建项目”模板会自动创建一个解决方案和应用程序项目。

## <a name="anatomy-of-a-xamarinmac-application"></a>Xamarin.Mac 应用程序剖析

Xamarin.Mac 应用程序编程与使用 Xamarin.iOS 编程非常相似。 iOS 使用 Mac 所用的 CocoaTouch 框架（Cocoa 的精简版本）。

项目中所含文件如下：

- Main.cs 包含应用的主入口点。 启动应用时，`Main` 类会包含运行的首个方法。
- AppDelegate.cs 包含的 `AppDelegate` 类负责侦听操作系统中的事件。
- Info.plist 包含应用属性，例如应用程序名称和图标等属性。
- Entitlements.plist 包含应用的授权，并允许访问 Sandboxing 和 iCloud 支持等。
- Main.storyboard 定义应用的用户界面（窗口和菜单），通过 Segues 设计窗口间的互连。 Storyboard 是包含视图（用户界面元素）定义的 XML 文件。 此文件可在 Xcode 内由 Interface Builder 创建和维护。
- ViewController.cs 是主窗口的控制器。 控制器会在另一文章中进行详细介绍，但现在，可将控制器理解为任何特定视图的主引擎。
- ViewController.designer.cs 包含有助于与主屏幕用户界面进行集成的联结代码。

以下部分将简要介绍这些文件。 之后会进行详细介绍，但现在了解相关基础知识会很有帮助。

### <a name="maincs"></a>Main.cs

**Main.cs** 文件非常简单。 此文件包含静态 `Main` 方法，用于创建新 Xamarin.Mac 应用实例和传递处理 OS 事件的类的名称，在本示例中为 `AppDelegate` 类：

```csharp
using System;
using System.Drawing;
using Foundation;
using AppKit;
using ObjCRuntime;

namespace Hello_Mac
{
    class MainClass
    {
        static void Main (string[] args)
        {
            NSApplication.Init ();
            NSApplication.Main (args);
        }
    }
}
```

### <a name="appdelegatecs"></a>AppDelegate.cs

`AppDelegate.cs` 文件包含负责创建窗口和侦听 OS 事件的 `AppDelegate` 类：

```csharp
using AppKit;
using Foundation;

namespace Hello_Mac
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        public AppDelegate ()
        {
        }

        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
    }
}
```

如果开发人员之前未生成过 iOS 应用，则可能对此代码并不熟悉，但其实此代码非常简单。

应用实例化后，`DidFinishLaunching` 方法会运行，并实际上负责创建应用的窗口以及启动在其中显示视图的过程。

用户或系统实例化应用关闭时会调用 `WillTerminate` 方法。 开发人员可使用此方法在应用退出前最终确定应用（例如保存用户参数设置或窗口大小和位置）。

### <a name="viewcontrollercs"></a>ViewController.cs

Cocoa（以及其派生产品 CocoaTouch）使用“模型视图控制器” (MVC) 模式。 `ViewController` 声明表示控制实际应用窗口的对象。 通常，对于每个创建的窗口（以及窗口内许多其他项），都存在一个负责窗口生命周期的控制器，例如显示窗口、向窗口添加新视图（控件）等。

`ViewController` 类是主窗口的控制器。 此控制器负责主窗口的生命周期。 之后会详细进行介绍，现在快速了解即可：

```csharp
using System;

using AppKit;
using Foundation;

namespace Hello_Mac
{
    public partial class ViewController : NSViewController
    {
        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any additional setup after loading the view.
        }

        public override NSObject RepresentedObject {
            get {
                return base.RepresentedObject;
            }
            set {
                base.RepresentedObject = value;
                // Update the view, if already loaded.
            }
        }
    }
}
```

### <a name="viewcontrollerdesignercs"></a>ViewController.Designer.cs

主窗口类的设计器文件最初为空，但通过 Xcode Interface Builder 创建用户界面时会由 Visual Studio for Mac 自动进行填充：

```csharp
// WARNING
//
// This file has been generated automatically by Visual Studio for Mac to store outlets and
// actions made in the UI designer. If it is removed, they will be lost.
// Manual changes to this file may not be handled correctly.
//
using Foundation;

namespace Hello_Mac
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

设计器文件不应直接编辑，因为设计器文件会由 Visual Studio for Mac 自动管理，以提供 pluming 代码，通过该代码可访问已添加到应用中任意窗口或视图的控件。

创建 Xamarin.Mac 应用项目以及基本了解其组件后，可切换到 Xcode，使用 Interface Builder 创建用户界面。

### <a name="infoplist"></a>Info.plist

`Info.plist` 文件包含 Xamarin.Mac 应用的相关信息，例如其“名称”和“捆绑标识符”：

[![Visual Studio for Mac plist 编辑器](hello-mac-images/infoplist01.png)](hello-mac-images/infoplist01.png#lightbox)

它还可定义用于在“主界面”下拉菜单下显示 Xamarin.Mac 应用的用户界面的 _Storyboard_。 在上面的示例中，下拉菜单中的 `Main` 关联到“解决方案资源管理器”中项目源树的 `Main.storyboard`。 它还定义应用图标，具体是通过指定包含图标的资产目录（在此示例中为“应用图标”）。

### <a name="entitlementsplist"></a>Entitlements.plist

应用的 `Entitlements.plist` 文件控制着 Xamarin.Mac 应用具有的权利，例如 **Sandboxing** 和 **iCloud**：

[![Visual Studio for Mac 权利编辑器](hello-mac-images/entitlements01.png)](hello-mac-images/entitlements01.png#lightbox)

对于 Hello World 示例，无需权利。 下一部分介绍如何使用 Xcode 的 Interface Builder 编辑 Main.storyboard 文件和定义 Xamarin.Mac 应用的 UI。

## <a name="introduction-to-xcode-and-interface-builder"></a>Xcode 和 Interface Builder 简介

作为 Xcode 的一部分，Apple 已创建名为 Interface Builder 的工具，开发人员通过该工具可在设计器中直观地创建用户界面。 Xamarin.Mac 与 Interface Builder 集成非常流畅，可通过与 Objective-C 用户相同的工具创建 UI。

若要开始，请双击“解决方案资源管理器”中的 `Main.storyboard` 文件将其打开，以便在 Xcode 和 Interface Builder 中进行编辑：

[![解决方案资源管理器中的 Main.storyboard 文件](hello-mac-images/xcode01.png)](hello-mac-images/xcode01.png#lightbox)

这会启动 Xcode，其如以下屏幕截图所示：

[![默认的 XCode Interface Builder 视图](hello-mac-images/xcode02.png)](hello-mac-images/xcode02.png#lightbox)

开始设计界面前，请快速了解 Xcode 概述，熟悉将会使用的主要功能。

> [!NOTE]
> 开发人员无需使用 Xcode 和 Interface Builder 来创建 Xamarin.Mac 应用的用户界面，可直接通过 C# 代码创建 UI，但这超出了本文的讨论范围。 为简单起见，本教程其余部分会使用 Interface Builder 来创建用户界面。

### <a name="components-of-xcode"></a>Xcode 组件

从 Visual Studio for Mac 的 Xcode 中打开 .storyboard 文件时，“项目导航器”位于左侧，“界面层次结构”和“界面编辑器”位于中间，“属性和实用程序”部分位于右侧：

[![XCode 中 Interface Builder 的各个部分](hello-mac-images/xcode03.png)](hello-mac-images/xcode03.png#lightbox)

后续部分介绍其中每个 Xcode 功能的作用以及如何使用这些功能创建 Xamarin.Mac 应用的界面。

### <a name="project-navigation"></a>项目导航

打开 .storyboard 文件在 Xcode 中进行编辑时，Visual Studio for Mac 会在后台创建 Xcode 项目文件，以便在 Visual Studio for Mac 和 Xcode 之间传递更改。 之后，开发人员从 Xcode 切换回 Visual Studio for Mac 时，对此项目所作的任何更改都将通过 Visual Studio for Mac 与 Xamarin.Mac 项目同步。

通过“项目导航”部分，开发人员可在组成此填充码 Xcode 项目的所有文件之间导航。 通常，开发人员只会对此列表中的 `.storyboard` 文件感兴趣，例如 `Main.storyboard`。

### <a name="interface-hierarchy"></a>界面层次结构

通过“界面层次结构”部分，开发人员可轻松访问用户界面的多个关键属性，例如占位符和主窗口  。 可使用此部分访问组成用户界面的各个元素（视图），并通过在层次结构内拖动这些元素来调整其嵌套方式。

### <a name="interface-editor"></a>界面编辑器

“界面编辑器”部分提供了用户界面图形化布局的设计面。从“属性和实用程序”部分的“库”中拖动元素来创建设计。 用户界面元素（视图）添加到设计图面后，会添加到“界面层次结构”部分，从而使其显示于“界面编辑器”中。

### <a name="properties--utilities"></a>属性和实用程序

“属性和实用程序”部分分为两个主要部分，即“属性”（又名“检查器”）和“库”：

[![属性检查器](hello-mac-images/xcode04.png)](hello-mac-images/xcode04.png#lightbox)

本部分起初基本为空，但开发人员在“用户编辑器”或“界面层次结构”中选择元素后，“属性”部分内会填充有开发人员可调整的特定元素和属性的相关信息。

“属性”部分内具有 8 个不同的检查器选项卡，如下所示：

[![所有检查器的概述](hello-mac-images/xcode05.png)](hello-mac-images/xcode05.png#lightbox)

### <a name="properties--utility-types"></a>属性和实用程序类型

由左至右，这些选项卡依次为：

- **文件检查器** – 文件检查器显示文件信息，例如正在编辑的 Xib 文件的文件名称和位置。
- **快速帮助** – 快速帮助选项卡基于 Xcode 中所选内容提供相应背景帮助。
- **标识检查器** – 标识检查器提供有关所选控件/视图的信息。
- **属性检查器** – 通过属性检查器，开发人员可自定义所选控件/视图的各种属性。
- **大小检查器** – 通过大小检查器，开发人员可控制所选控件/视图的大小和调整大小。
- **连接检查器** – 连接检查器显示所选控件的“输出口”和“操作”连接。 后文将详细介绍输出口和操作。
- **绑定检查器** – 通过绑定检查器，开发人员可配置控件，以使其值自动绑定到数据模型。
- **视图效果检查器** – 通过视图效果检查器，开发人员可对控件指定效果，例如动画。

使用“库”部分查找要放入设计器的控件和对象，从而以图形方式生成用户界面：

[![XCode 库检查器](hello-mac-images/xcode06.png)](hello-mac-images/xcode06.png#lightbox)

## <a name="creating-the-interface"></a>创建界面

了解 Xcode IDE 和 Interface Builder 的基础知识后，开发人员便可创建主视图的用户界面。

请按照这些步骤使用 Interface Builder：

1. 在 Xcode 中，从“库”部分拖动“Push Button”：

    [![从“库检查器”中选择“NSButton”](hello-mac-images/xcode07.png)](hello-mac-images/xcode07.png#lightbox)

2. 将此按钮拖放到“界面编辑器”中的“视图”（在“窗口控制器”下方）：

    [![将按钮添加到界面设计](hello-mac-images/xcode08.png)](hello-mac-images/xcode08.png#lightbox)

3. 单击“属性检查器”中的 Title 属性，将此按钮的标题更改为“Click Me”：

    [![设置按钮的属性](hello-mac-images/xcode09.png)](hello-mac-images/xcode09.png#lightbox)

4. 从“库”部分拖动“标签”：

    [![从“库检查器”中选择“标签”](hello-mac-images/xcode10.png)](hello-mac-images/xcode10.png#lightbox)

5. 将此标签拖放到“界面编辑器”中此按钮旁的“窗口”中：

    [![将标签添加到界面设计](hello-mac-images/xcode11.png)](hello-mac-images/xcode11.png#lightbox)

6. 按住此标签上的右控点，将其拖动至靠近窗口边缘的位置：

    [![调整标签的大小](hello-mac-images/xcode12.png)](hello-mac-images/xcode12.png#lightbox)

7. 选择刚才在“界面编辑器”中添加的按钮，然后单击窗口底部的“约束编辑器”图标：

    [![将约束添加到按钮](hello-mac-images/xcode13.png)](hello-mac-images/xcode13.png#lightbox)

8. 在编辑器顶部，单击顶部左侧的**红色 I 型光标**。 这样，调整窗口大小后，此按钮会保持处于屏幕左上角的相同位置。

9. 接下来，选中“高度”和“宽度”框，并使用默认大小。 这样，窗口大小调整后，此按钮会保持相同大小。

10. 单击“添加 4 个约束”按钮添加约束，然后关闭编辑器。

11. 选择此标签，然后再次单击“约束编辑器”图标：

    [![将约束添加到标签](hello-mac-images/xcode14.png)](hello-mac-images/xcode14.png#lightbox)

12. 通过单击“约束编辑器”顶部、右侧和左侧的**红色 I 型光标**，指示此标签处于其特定的 X 和 Y 位置，以及在运行的应用程序中窗口大小调整时进行扩展和收缩。

13. 再次选中“高度”框并使用默认大小，然后单击“添加 4 个约束”按钮添加约束，然后关闭编辑器。

14. 保存用户界面更改。

调整控件大小和移动控件时，请注意 Interface Builder 会根据 [macOS 人机界面指南](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)提供有用的快捷提示。 这些指南有助于开发人员创建出外观和风格为 Mac 用户所熟悉的优质应用。

在“界面层次结构”部分中可查看组成用户界面的元素布局和层次结构的显示方式：

[![在“界面层次结构”中选择元素](hello-mac-images/xcode15.png)](hello-mac-images/xcode15.png#lightbox)

如果需要，开发人员可从此处选择要进行编辑的项，或通过拖动对 UI 元素进行重新排序。 例如，如果某 UI 元素被另一元素覆盖，开发人员可将其拖动到列表底部，使其成为窗口上最顶层的项。

用户界面创建后，开发人员需要公开 UI 项，以便 Xamarin.Mac 可进行访问和在 C# 代码中与之交互。 接下来的“输出口和操作”部分中介绍了其执行方法。

### <a name="outlets-and-actions"></a>输出口和操作

**输出口**和**操作**是什么？ 在传统的 .NET 用户界面编程中，用户界面中的控件在添加时会自动作为属性公开。 然而在 Mac 中情况则不同，如果仅将控件添加到视图，代码不可对其进行访问。 开发人员必须对代码显式公开 UI。 为此，Apple 提供了两个选项：

- **输出口** – 输出口类似于属性。 如果开发人员将控件连接到输出口，则控件会通过属性对代码公开，从而使控件可执行诸如附加事件处理程序和调用方法等操作。
- **操作** - 操作类似于 WPF 中的命令模式。 例如，对控件执行操作时，比如单击按钮，则控件会自动在代码中调用方法。 操作功能强大且非常方便，因为开发人员可对同一操作连接多个控件。

在 Xcode 中，会通过拖动控件直接将**输出口**和**操作**添加到代码。 更具体地说，这意味着若要创建“输出口”或“操作”，开发人员需选择要添加“输出口”和“操作”的控件元素，按住键盘上的 Ctrl 键，然后将此控件直接拖到代码中    。

对于 Xamarin.Mac 开发人员，这意味着开发人员应拖动到与要创建**输出口**或**操作**的 C# 文件所对应的 Objective-C 存根文件中。 Visual Studio for Mac 创建一个名为 `ViewController.h` 的文件作为其生成的填充码 Xcode 项目的一部分，以便使用 Interface Builder：

[![在 Xcode 中查看源](hello-mac-images/xcode16-sml.png)](hello-mac-images/xcode16.png#lightbox)

此存根 `.h` 文件反映了创建新的 `NSWindow` 时自动添加到 Xamarin.Mac 项目的 `ViewController.designer.cs`。 此文件用于同步对 Interface Builder 所作的更改，且会在此文件中创建**输出口**和**操作**，从而使 UI 元素对 C# 代码公开。

#### <a name="adding-an-outlet"></a>添加输出口

基本了解**输出口**和**操作**后，请创建**输出口**将创建的标签对 C# 代码公开。

请执行以下操作：

1. 在 Xcode 中屏幕右上角，单击“双圆”按钮，打开“助手编辑器”：

    [![显示助手编辑器](hello-mac-images/outlet01.png)](hello-mac-images/outlet01.png#lightbox)

2. 此时 Xcode 会切换为拆分视图模式，“界面编辑器”位于一边，“代码编辑器”位于另一边。

3. 请注意，Xcode 已自动在“代码编辑器”中选取 **ViewController.m** 文件，这是不正确的。 通过以上关于什么是**输出口**和**操作**的讨论可知，开发人员需要选中 **ViewController.h**。

4. 在“代码编辑器”顶部，单击“自动链接”并选择 `ViewController.h` 文件：

    [![选择正确的文件](hello-mac-images/outlet02.png)](hello-mac-images/outlet02.png#lightbox)

5. 此时 Xcode 应选择了正确的文件：

    [![查看 ViewController.h 文件](hello-mac-images/outlet03.png)](hello-mac-images/outlet03.png#lightbox)

6. 最后一个步骤至关重要！：如果未选中正确的文件，则无法创建输出口和操作，或者会公开给 C# 中错误的类！

7. 在“界面编辑器”中，按住键盘上的 **Ctrl** 键，单击并将前面创建的标签拖动到代码编辑器中（`@interface ViewController : NSViewController {}` 代码下方）：

    [![拖动以创建输出口](hello-mac-images/outlet04.png)](hello-mac-images/outlet04.png#lightbox)

8. 会显示一个对话框。 使“连接”保持设置为“输出口”，然后在“名称”中输入 `ClickedLabel`：

    [![定义输出口](hello-mac-images/outlet05.png)](hello-mac-images/outlet05.png#lightbox)

9. 单击“连接”按钮，创建**输出口**：

    [![查看最终输出口](hello-mac-images/outlet06.png)](hello-mac-images/outlet06.png#lightbox)

10. 保存对文件所做的更改。

#### <a name="adding-an-action"></a>添加操作

接下来，对 C# 代码公开按钮。 与上面的标签类似，开发人员可将按钮连接到**输出口**。 由于只希望对点击的按钮响应，因此改用**操作**。

请执行以下操作：

1. 请确保 Xcode 仍位于“助手编辑器”中，且 **ViewController.h** 文件在“代码编辑器”中可见。

2. 在“界面编辑器”中，按住键盘上的 **Ctrl** 键，单击并将前面创建的按钮拖动到代码编辑器中（`@property (assign) IBOutlet NSTextField *ClickedLabel;` 代码下方）：

    [![拖动以创建操作](hello-mac-images/action01.png)](hello-mac-images/action01.png#lightbox)

3. 将“连接”类型更改为“操作”：

    [![定义操作](hello-mac-images/action02.png)](hello-mac-images/action02.png#lightbox)

4. 输入 `ClickedButton` 作为**名称**：

    [![为新操作命名](hello-mac-images/action03.png)](hello-mac-images/action03.png#lightbox)

5. 单击“连接”按钮，创建**操作**：

    [![查看最终操作](hello-mac-images/action04.png)](hello-mac-images/action04.png#lightbox)

6. 保存对文件所做的更改。

用户界面连接和公开给 C# 代码后，请切换回 Visual Studio for Mac，使其同步在 Xcode 和 Interface Builder 中所作的更改。

> [!NOTE]
> 对第一个应用创建用户界面、输出口和操作可能花费了较长的时间，且工作看起来较大，但其实大部分时间是用于介绍许多新概念和新功能 。 使用 Interface Builder 练习一段时间后，一两分钟内便可创建此界面及其所有的输出口和操作 。

### <a name="synchronizing-changes-with-xcode"></a>与 Xcode 同步更改

开发人员从 Xcode 切换回 Visual Studio for Mac 时，在 Xcode 中所作的任何更改将会自动与 Xamarin.Mac 项目同步。

在“解决方案资源管理器”中选择 ViewController.designer.cs，查看 C# 代码中如何连接输出口和操作   ：

[![与 Xcode 同步更改](hello-mac-images/sync01-sml.png)](hello-mac-images/sync01.png#lightbox)

注意 **ViewController.designer.cs** 文件中的这两个定义：

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

与 Xcode 的 `ViewController.h` 文件中的定义对齐：

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

Visual Studio for Mac 会侦听对 **.h** 文件的更改，然后在相应的 **.designer.cs** 文件中自动同步这些更改，以将其公开给应用。 请注意，**ViewController.designer.cs** 是一个分部类，因此 Visual Studio for Mac 不必修改 **ViewController.cs**，它会覆盖开发人员对此类所作的任何更改。

正常情况下，开发人员不需要打开 **ViewController.designer.cs**，此处提供仅为了介绍方便。

> [!NOTE]
> 大多数情况下，Visual Studio for Mac 会自动发现 Xcode 中所作的任何更改，并将其同步到 Xamarin.Mac 项目。 如果同步不自动进行，请切换回 Xcode，然后再次切换到 Visual Studio for Mac。 这通常会开始同步周期。

## <a name="writing-the-code"></a>编写代码

创建用户界面并将其 UI 元素通过输出口和操作公开给代码后，便可开始编写代码以最终完成程序 。

对于此示例应用，每次单击第一个按钮时，会更新标签，显示该按钮的已点击次数。 为完成此操作，请通过在“解决方案资源管理器”中双击 `ViewController.cs` 文件以将它打开进行编辑：

[![在 Visual Studio for Mac 中查看 ViewController.cs 文件](hello-mac-images/code01-sml.png)](hello-mac-images/code01.png#lightbox)

首先，在 `ViewController` 类中创建类级别变量以跟踪已发生的点击数。 编辑类定义，使其类似于如下所示：

```csharp
namespace Hello_Mac
{
    public partial class ViewController : NSViewController
    {
        private int numberOfTimesClicked = 0;
        ...
```

接下来，在同一类 (`ViewController`) 中，替代 `ViewDidLoad` 方法和某些代码，从而设置标签的初始消息：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.StringValue = "Button has not been clicked yet.";
}
```

请使用 `ViewDidLoad`，不要使用 `Initialize` 等其他方法，因为 OS 从 .storyboard 加载和实例化用户界面后会调用 `ViewDidLoad`。 如果开发人员在 .storyboard 文件完全加载和实例化之前尝试访问标签控件，则会出现 `NullReferenceException` 错误，因为此时标签控件尚不存在。

下一步，添加代码以对用户点击按钮作出响应。 将下面的分部方法添加到 `ViewController` 类：

```csharp
partial void ClickedButton (Foundation.NSObject sender) {
    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

此代码会附加到在 Xcode 和 Interface Builder 中创建的**操作**，且每次用户点击按钮时都会调用此代码。

## <a name="testing-the-application"></a>测试应用程序

现在即可生成并运行应用，确保其可按预期运行。 开发人员可在一个步骤中同时生成和运行应用，或者只生成应用而不运行。

生成应用时，开发人员可选择所需的版本类型：

- **调试** – 调试版本会编译到 **.app**（应用程序）文件，其中包含的一些额外元数据可使开发人员在应用运行时进行相关调试。
- **发布** – 发布版本也会创建 **.app** 文件，但其中不包含调试调试信息，因此其大小更小，执行速度更快。

开发人员可以从 Visual Studio for Mac 屏幕左上角的“配置选择器”中选择生成的类型：

[![选择调试版本](hello-mac-images/run01-sml.png)](hello-mac-images/run01.png#lightbox)

## <a name="building-the-application"></a>生成应用程序

本示例中，只需调试版本，因此请确保选择“调试”。 首先，通过按 **⌘B** 或在“生成”菜单中选择“生成所有”，生成应用。

如果没有任何错误，则 Visual Studio for Mac 状态栏中会显示“生成成功”消息。 如果出现错误，请检查项目，并确定是否正确执行了上述步骤。 请首先确认代码（Xcode 和 Visual Studio for Mac 中的代码）与本教程中的代码匹配。

## <a name="running-the-application"></a>运行应用程序

可通过三种方式运行应用：

- 按 **⌘+Enter**。
- 在“运行”菜单中，选择“调试”。
- 单击 Visual Studio for Mac 工具栏中的“播放”按钮（位于“解决方案资源管理器”上方）。

应用会生成（如果尚未生成），以调试模式启动并显示主界面窗口：

[![运行应用程序](hello-mac-images/run02-sml.png)](hello-mac-images/run02.png#lightbox)

如果多次点击按钮，则标签会更新计数：

[![显示单击按钮的结果](hello-mac-images/run03-sml.png)](hello-mac-images/run03.png#lightbox)

## <a name="where-to-next"></a>下一步

了解 Xamarin.Mac 应用程序基础知识后，请阅读以下文档，进行深入了解：

- [Storyboard 简介](~/mac/platform/storyboards/index.md) - 此文提供有关 Xamarin.Mac 应用中的 Storyboard 的简介。 其中介绍了如何使用 Storyboard 和 Xcode 的 Interface Builder 创建和维护应用 UI。
- [窗口](~/mac/user-interface/window.md) - 本文介绍 Xamarin.Mac 应用程序中窗口和窗格的相关内容。 其中包括在 Xcode 和 Interface Builder 中创建和维护窗口与窗格、从 .xib 文件加载窗口和窗格、使用窗口以及响应 C# 代码中的窗口。
- [对话框](~/mac/user-interface/dialog.md) - 本文介绍如何在 Xamarin.Mac 应用程序中使用对话框和模式窗口。 其中包含如何在 Xcode 和 Interface Builder 中创建和维护模式窗口、使用标准对话框、显示以及响应 C# 代码中的窗口。
- [警告](~/mac/user-interface/alert.md) - 本文介绍 Xamarin.Mac 应用程序中警告的相关内容。 其中包括通过 C# 代码创建和显示警告以及响应警告。
- [菜单](~/mac/user-interface/menu.md) - 从屏幕顶部的应用程序主菜单到可在窗口中任意位置出现的弹出菜单和上下文菜单，Mac 应用程序用户界面中的各个部分都会使用到菜单。 菜单是构成 Mac 应用程序用户体验的一个组成部分。 本文介绍如何在 Xamarin.Mac 应用程序中使用 Cocoa 菜单。
- [工具栏](~/mac/user-interface/toolbar.md) - 本文介绍 Xamarin.Mac 应用程序中工具栏的相关内容。 其中包含 如何在 Xcode 和 Interface Builder 中创建和维护工具栏、如何使用输出口和操作对代码公开工具栏项、如何启用和禁用工具栏项以及响应 C# 代码中的工具栏项。
- [表视图](~/mac/user-interface/table-view.md) - 本文介绍如何使用 Xamarin.Mac 应用程序中表视图。 其中包含在如何 Xcode 和 Interface Builder 中创建和维护表视图、如何使用输出口和操作对代码公开表视图项、如何填充表项以及响应 C# 代码中的表视图项。
- [大纲视图](~/mac/user-interface/outline-view.md) - 本文介绍如何在 Xamarin.Mac 应用程序中使用大纲视图。 其中包含如何在 Xcode 和 Interface Builder 中创建和维护大纲视图、如何使用输出口和操作对代码公开大纲视图项、填充大纲项以及响应 C# 代码中的大纲视图项。
- [源列表](~/mac/user-interface/source-list.md) - 本文介绍如何 Xamarin.Mac 应用程序中使用源列表。 其中包含如何在 Xcode 和 Interface Builder 中创建和维护源列表、如何使用输出口和操作对代码公开源列表项、如何填充源列表项以及响应 C# 代码中的源列表项。
- [集合视图](~/mac/user-interface/collection-view.md) - 本文介绍如何在 Xamarin.Mac 应用程序中使用集合视图。 其中包含如何在 Xcode 和 Interface Builder 中创建和维护集合视图、如何使用操作对代码公开集合视图元素、如何填充集合视图以及响应 C# 代码中的集合视图。
- [图像](~/mac/app-fundamentals/image.md) - 本文介绍如何在 Xamarin.Mac 应用中使用图像和图标。 其中包括如何创建和维护用于创建应用图标的图像以及如何在 C# 代码和 Xcode 的 Interface Builder 中使用图像。

[Mac 示例库](/samples/browse/?products=xamarin&term=Xamarin.Mac)包含即时可用的代码示例，以便于学习 Xamarin.Mac。

[SourceWriter 示例应用](/samples/xamarin/mac-samples/sourcewriter)是一个完整的 Xamarin.Mac 应用示例（其中包含用户在典型 Mac 应用程序中可能希望找到的许多功能）。 SourceWriter 是一个非常简单的源代码编辑器，提供代码补全和简单语法突出显示支持。

SourceWriter 代码已经完全注释，且在可用时，提供了相关链接，链接涵盖了从关键技术或方法到 Xamarin.Mac 文档中的相关信息。

## <a name="summary"></a>总结

本文介绍标准 Xamarin.Mac 应用的基础知识。 其中包括如何在 Visual Studio for Mac 中创建新应用，如何在 Xcode 和 Interface Builder 中设计用户界面，如何使用输出口和操作对 C# 代码公开 UI 元素，如何添加代码处理 UI 元素，以及最终生成和测试 Xamarin.Mac 应用。

## <a name="related-links"></a>相关链接

- [Hello，Mac（示例）](/samples/xamarin/mac-samples/hello-mac)
- [macOS 人机界面指南](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)