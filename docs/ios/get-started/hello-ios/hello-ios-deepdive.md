---
title: Hello，iOS - 深入了解
description: 本文档深入介绍了 Hello，iOS 示例应用程序，包括其体系结构、用户界面、内容视图层次结构、测试、部署等。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 61ba3a7e-fe11-4439-8bc8-9809512b8eff
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 4b9043d70bb7460abf62c964da8041f345cd1be6
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435607"
---
# <a name="hello-ios--deep-dive"></a>Hello，iOS - 深入了解

快速入门演练介绍了如何生成和运行基本 Xamarin.iOS 应用程序。 现在可以更深入地了解 iOS 应用程序的工作原理，以便可以生成更复杂的程序。 本指南回顾了“Hello，iOS”演练中的步骤，以便了解 iOS 应用程序开发的基本概念。

本指南可帮助你培养生成单屏幕 iOS 应用程序所需的技能和知识。 完成本指南之后，你应了解 Xamarin.iOS 应用程序的不同部分以及它们如何组合在一起。

::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Visual Studio for Mac 简介

Visual Studio for Mac 是将来自 Visual Studio 和 XCode 的功能结合在一起的免费开放源代码 IDE。 它采用完全集成的可视化设计器、包含重构工具的文本编辑器、程序集浏览器、源代码集成等。 本指南介绍一些基本的 Visual Studio for Mac 功能，但如果初次使用 Visual Studio for Mac，请查看 [Visual Studio for Mac](/visualstudio/mac/) 文档。

Visual Studio for Mac 遵循将代码组织为解决方案和项目的 Visual Studio 做法   。 解决方案是可以容纳一个或多个项目的容器。 项目可以是应用程序（如 iOS 或 Android）、支持库、测试应用程序等。 在 Phoneword 应用中，使用“单视图应用程序”模板添加了新 iPhone 项目  。 初始解决方案如下所示：

![初始解决方案的屏幕截图](hello-ios-deepdive-images/image30.png)

::: zone-end
::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Visual Studio 简介

Visual Studio 是 Microsoft 提供的强大 IDE。 它采用完全集成的可视化设计器、包含重构工具的文本编辑器、程序集浏览器、源代码集成等。 本指南会介绍一些基本 Visual Studio 功能以及 Visual Studio 的 Xamarin 工具。

Visual Studio 将代码组织为解决方案和项目。 解决方案是可以容纳一个或多个项目的容器。 项目可以是应用程序（如 iOS 或 Android）、支持库、测试应用程序等。 在 Phoneword 应用中，使用“单视图应用程序”模板添加了新 iPhone 项目。 初始解决方案如下所示：

![初始解决方案的屏幕截图](hello-ios-deepdive-images/vs-image30.png)

::: zone-end

## <a name="anatomy-of-a-xamarinios-application"></a>Xamarin.iOS 应用剖析

::: zone pivot="macos"

左侧是解决方案板，它包含与解决方案关联的目录结构和所有文件：

![解决方案板，包含与解决方案关联的目录结构和所有文件](hello-ios-deepdive-images/image31.png)

::: zone-end
::: zone pivot="windows"

右侧是解决方案窗格，它包含与解决方案关联的目录结构和所有文件：

![解决方案窗格，包含与解决方案关联的目录结构和所有文件](hello-ios-deepdive-images/vs-image31.png)

::: zone-end

在[了解 iOS](~/ios/get-started/hello-ios/hello-ios-quickstart.md) 演练中，你创建了一个名为 Phoneword 的解决方案，并将一个 iOS 项目 (Phoneword_iOS) 置于其内部 。 项目内的项包括：

- **引用** - 包含生成和运行应用程序所需的程序集。 展开该目录可查看对 .NET 程序集（如 [System](/dotnet/api/system)、System.Core 和 [System.Xml](/dotnet/api/system.xml)）的引用以及对 Xamarin.iOS 程序集的引用。
- **包** - 包目录包含现成的 NuGet 包。
- **资源** - 资源文件夹存储其他媒体。
- **Main.cs** – 此项包含应用程序的主入口点。 若要启动应用程序，应传入主应用程序类 `AppDelegate` 的名称。
- **AppDelegate.cs** – 此文件包含主应用程序类，负责创建窗口、生成用户界面以及侦听来自操作系统的事件。
- **Main.storyboard** - 情节提要包含应用程序用户界面的可视化设计。 情节提要文件在一个名为 iOS 设计器的图形编辑器中打开。
- **ViewController.cs** – 视图控制器为用户看到和触摸的屏幕（视图）提供支持。 视图控制器负责处理用户与视图之间的交互。
- **ViewController.designer.cs** - `designer.cs` 是自动生成的文件，在视图中的控件与这些控件在视图控制器中的代码表示形式之间充当粘合剂。 由于这是内部管道文件时，因此 IDE 会覆盖任何手动更改，在大多数情况下可以忽略此文件。 有关可视化设计器与支持代码之间的关系的详细信息，请参阅  [iOS 设计器简介](~/ios/user-interface/designer/introduction.md)指南。
- **Info.plist** - Info.plist 是设置应用程序属性（如应用程序名称、图标、启动图像等）的位置。 这是一个功能强大的文件，在[使用属性列表](~/ios/app-fundamentals/property-lists.md)指南中对它进行了全面介绍。
- **Entitlements.plist** - 权利属性列表使我们可以指定应用程序功能（也称为应用商店技术），如 iCloud、PassKit 等。 [使用属性列表](~/ios/app-fundamentals/property-lists.md)指南中提供了有关 Entitlements.plist 的详细信息。 有关权利的一般简介，请参阅 [设备预配](~/ios/get-started/installation/device-provisioning/index.md)指南。

## <a name="architecture-and-app-fundamentals"></a>体系结构和应用基础知识

需要首先完成两个工作，然后 iOS 应用程序才能加载用户界面。 首先，应用程序需要定义一个入口点 – 在应用程序的进程加载到内存中时运行的第一段代码。 其次，需要定义一个类来处理应用程序范围事件以及与操作系统进行交互。

此部分会研究下图中所示的关系：

[![此图说明体系结构和应用基础知识的关系](hello-ios-deepdive-images/image32.png)](hello-ios-deepdive-images/image32.png#lightbox)

### <a name="main-method"></a>Main 方法

iOS 应用程序的主入口点是 `Application` 类。 `Application` 类在 Main.cs 文件中定义，其中包含静态 `Main` 方法。 该方法会创建新 Xamarin.iOS 应用程序实例并传递将处理 OS 事件的“应用程序委托”类的名称。 静态 `Main` 方法的模板代码如下所示：

```csharp
using System;
using UIKit;

namespace Phoneword_iOS
{
    public class Application
    {
        static void Main (string[] args)
        {
            UIApplication.Main (args, null, "AppDelegate");
        }
    }
}
```

### <a name="application-delegate"></a>应用委托

在 iOS 中，“应用程序委托”类会处理系统事件；此类处于 AppDelegate.cs 中。 `AppDelegate` 类管理应用程序窗口。 窗口是充当用户界面容器的 `UIWindow` 类的单个实例。 默认情况下，应用程序只获取一个将其内容加载到其中的窗口，该窗口会附加到提供与物理设备屏幕尺寸匹配的边框的屏幕（单个 `UIScreen` 实例）。

AppDelegate 组件还负责订阅有关重要应用程序事件（如应用完成启动时或内存不足时）的系统更新。

下面提供了 AppDelegate 的模板代码：

```csharp
using System;
using Foundation;
using UIKit;

namespace Phoneword_iOS
{

    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        public override UIWindow Window {
            get;
            set;
        }

        ...
    }
}
```

一旦应用程序定义了其窗口，便可以开始加载用户界面。 下一部分探讨 UI 创建。

## <a name="user-interface"></a>用户界面

iOS 应用的用户界面类似于店面 - 应用程序通常只获取一个窗口，但是它可以使用所需数量的对象填满窗口，并且可以根据应用要显示的内容更改对象和排列方式。 此情形中的对象（用户看到的内容）称为视图。 为了在应用程序中构建单个屏幕，视图会在“内容视图层次结构”中互相堆叠，该层次结构由单个视图控制器进行管理。 具有多个屏幕的应用程序具有多个内容视图层次结构（各自具有自己的视图控制器），应用程序会将视图置于窗口中，以基于用户所处的屏幕创建不同的内容视图层次结构。

此部分会通过介绍视图、内容视图层次结构和 iOS 设计器来深入探讨用户界面。

### <a name="ios-designer-and-storyboards"></a>iOS Designer 和情节提要

iOS 设计器是一种用于在 Xamarin 中构建用户界面的可视化工具。 可以通过双击任何情节提要 (.storyboard) 文件（这会打开类似于下面屏幕截图的视图）来启动该设计器：

::: zone pivot="macos"

![iOS 设计器接口](hello-ios-deepdive-images/image33.png)

Storyboard 是包含应用屏幕的可视化设计以及屏幕切换效果和关系的文件。 情节提要中的应用程序屏幕表示形式称为_场景_。 每个场景都表示一个视图控制器以及它管理的视图堆栈（内容视图层次结构）。 通过模板新建“单一视图应用程序”项目后，Visual Studio for Mac 会自动生成一个名为 `Main.storyboard` 的 Storyboard 文件，并使用一个场景进行填充，如下面的屏幕截图所示：

![Visual Studio for Mac 自动生成名为 Main.storyboard 的情节提要文件，并使用单个场景对其进行填充](hello-ios-deepdive-images/image34.png)

可以选择 Storyboard 屏幕底部的黑色条，以便选择场景的视图控制器。 视图控制器是 `UIViewController` 类的实例，该类包含内容视图层次结构的支持代码。 可以在“Properties Pad”中查看和设置此视图控制器的属性，如下面的屏幕截图所示：

![“属性”窗格](hello-ios-deepdive-images/image35.png)

::: zone-end
::: zone pivot="windows"

![iOS 设计器接口](hello-ios-deepdive-images/vs-image33.png)

Storyboard 是包含应用屏幕的可视化设计以及屏幕切换效果和关系的文件。 情节提要中的应用程序屏幕表示形式称为_场景_。 每个场景都表示一个视图控制器以及它管理的视图堆栈（内容视图层次结构）。 通过模板新建“单视图应用”项目后，Visual Studio 会自动生成名为 `Main.storyboard` 的 Storyboard 文件，并使用一个场景进行填充，如下面的屏幕截图所示：

![Visual Studio 自动生成名为 Main.storyboard 的情节提要文件，并使用单个场景对其进行填充](hello-ios-deepdive-images/vs-image34.png)

可以选择 Storyboard 屏幕底部栏，以便选择场景的视图控制器。 视图控制器是 `UIViewController` 类的实例，该类包含内容视图层次结构的支持代码。 可以在“属性窗格”中查看和设置此视图控制器的属性，如下面的屏幕截图所示：

![“属性”窗格](hello-ios-deepdive-images/vs-image35.png)

::: zone-end

可以单击场景的白色部分，选择视图。 视图是 `UIView` 类的实例，该类定义屏幕的某个区域，并为使用该区域中的内容提供接口。 默认视图是填充整个设备屏幕的单个“根视图”。

场景左侧是一个具有标志图标的灰色箭头，如下面的屏幕截图所示：

 [![包含标记图标的灰色箭头](hello-ios-deepdive-images/image37.png)](hello-ios-deepdive-images/image37.png#lightbox)

该灰色箭头表示情节提要转换，称为 Segue（发音为“seg-way”）。 由于此 Segue 没有源，因此它称为无源 Segue。 无源 Segue 指向其视图在应用程序启动时加载到应用程序窗口的第一个场景。 场景及其中的视图会是用户在应用加载时看到的第一个内容。

构建用户界面时，可以将其他视图从“工具箱”拖动到设计图面上的主视图，如下面的屏幕截图所示：

::: zone pivot="macos"

![可将其他视图从工具箱拖动到设计曲面上的主视图中](hello-ios-deepdive-images/image38.png)

::: zone-end
::: zone pivot="windows"

![可将其他视图从工具箱拖动到设计曲面上的主视图中](hello-ios-deepdive-images/vs-image38.png)

::: zone-end

这些其他视图称为子视图。 根视图连同子视图都是“内容视图层次结构”（由 `ViewController` 管理）的一部分。 可以通过在“文档大纲”板中进行检查来查看场景中所有元素的大纲：

::: zone pivot="macos"

![“文档大纲”板](hello-ios-deepdive-images/image39.png)

::: zone-end
::: zone pivot="windows"

![“文档大纲”板](hello-ios-deepdive-images/vs-image39.png)

::: zone-end

下图中突出显示了子视图：

::: zone pivot="macos"

![图中突出显示了子视图](hello-ios-deepdive-images/image40.png)

::: zone-end
::: zone pivot="windows"

![图中突出显示了子视图](hello-ios-deepdive-images/vs-image40.png)

::: zone-end

下一部分会分解此场景表示的内容视图层次结构。

## <a name="content-view-hierarchy"></a>内容视图层次结构

“内容视图层次结构”是由单个视图控制器管理的视图和子视图的堆栈，如下图所示：

 [![内容视图层次结构](hello-ios-deepdive-images/image41.png)](hello-ios-deepdive-images/image41.png#lightbox)

我们可以通过在“属性板”的“视图”部分中暂时将根视图的背景色更改为黄色，使 `ViewController` 的内容视图层次结构可以更方便地进行查看，如下面的屏幕截图所示：

::: zone pivot="macos"

![在 Properties Pad 的“视图”部分，将根视图的背景色更改为黄色](hello-ios-deepdive-images/image42.png)

::: zone-end
::: zone pivot="windows"

![在 Properties Pad 的“视图”部分，将根视图的背景色更改为黄色](hello-ios-deepdive-images/vs-image42.png)

::: zone-end

下图显示了窗口、视图、子视图与视图控制器之间的关系，它们向设备屏幕提供了用户界面：

[![窗口、视图、子视图和视图控制器之间的关系](hello-ios-deepdive-images/image43.png)](hello-ios-deepdive-images/image43.png#lightbox)

在下一部分中，讨论如何在代码中使用视图，并学习使用视图控制器和视图生命周期为用户交互进行编程。

## <a name="view-controllers-and-the-view-lifecycle"></a>视图控制器和视图生命周期

每个内容视图层次结构都有一个对应的视图控制器以便为用户交互提供支持。 视图控制器的作用是管理内容视图层次结构中的视图。 视图控制器不是内容视图层次结构的一部分，它不是界面中的元素。 相反，它提供的代码可为用户与屏幕上的对象进行交互提供支持。

### <a name="view-controllers-and-storyboards"></a>视图控制器和情节提要

::: zone pivot="macos"

视图控制器在 Storyboard 中的场景底部以条状呈现。 选择视图控制器可以在“Properties Pad”中查看其属性：

![选择视图控制器可以在“属性”窗格中查看其属性](hello-ios-deepdive-images/image44.png)

可以在“Properties Pad”的“标识”部分中编辑“类”属性，设置此场景表示的内容视图层次结构的自定义视图控制器类  。 例如，我们的“Phoneword”应用将 `ViewController` 设置为第一屏的视图控制器，如下面的屏幕截图所示：

![Phoneword 应用程序将 ViewController 设置为视图控制器](hello-ios-deepdive-images/image45new.png)

::: zone-end
::: zone pivot="windows"

视图控制器在 Storyboard 中的场景底部以条状呈现。 选择视图控制器可以在“属性”窗格中查看其属性：

![选择视图控制器可以在“属性”窗格中查看其属性](hello-ios-deepdive-images/vs-image44.png)

可以在“属性窗格”的“标识”部分中编辑“类”属性，设置此场景表示的内容视图层次结构的自定义视图控制器类  。 例如，我们的“Phoneword”应用将 `ViewController` 设置为第一屏的视图控制器，如下面的屏幕截图所示：

![Phoneword 应用程序将 ViewController 设置为视图控制器](hello-ios-deepdive-images/vs-image45.png)

::: zone-end

这会将视图控制器的情节提要表示链接到 `ViewController` C# 类。 打开 `ViewController.cs` 文件，请注意视图控制器是 `UIViewController` 的“子类”，如下面的代码所示：

```csharp
public partial class ViewController : UIViewController
{
    public ViewController (IntPtr handle) : base (handle)
    {

    }
}
```

`ViewController` 现在会推动情节提要中与此视图控制器关联的内容视图层次结构的交互。 接下来，你会通过引入一个称为视图生命周期的进程，来了解视图控制器在管理视图方面的角色。

> [!NOTE]
> 对于不需要用户交互的仅可视化屏幕，可以在“Properties Pad”中将“Class”属性留空。 这会将视图控制器的支持类设置为 `UIViewController` 的默认实现（这适合于不打算添加自定义代码的情况）。

### <a name="view-lifecycle"></a>视图生命周期

视图控制器负责对窗口加载和卸载内容视图层次结构。 内容视图层次结构中的视图发生了重要情况时，操作系统会通过视图生命周期中的事件通知视图控制器。 通过替代视图生命周期中的方法，可以与屏幕上的对象进行交互，并创建响应迅速的动态用户界面。

以下这些是基本生命周期方法及其功能：

- **ViewDidLoad** - 在视图控制器首次将其内容视图层次结构加载到内存中时调用一次。 这是进行初始设置的好时机，因为它是子视图首次在代码中可用的时间。
- **ViewWillAppear** - 每当视图控制器的视图将要添加到内容视图层次结构并出现在屏幕上时进行调用。
- **ViewWillDisappear** - 每当视图控制器的视图将要从内容视图层次结构中删除并从屏幕上消失时进行调用。 此生命周期事件用于清理和保存状态。
- **ViewDidAppear** 和 **ViewDidDisappear** - 分别在对内容视图层次结构添加或删除视图时进行调用。

自定义代码添加到生命周期的任何阶段时，生命周期方法的“基实现”都必须进行“替代” 。 这通过利用现有生命周期方法（已附加了一些代码）并使用其他代码扩展它来实现。 基实现从方法内部进行调用，以确保原始代码在新代码之前运行。 下一部分中对此演示了一个示例。

有关使用视图控制器的详细信息，请参阅 Apple 的[适用于 iOS 的视图控制器编程指南](https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/index.html#//apple_ref/doc/uid/TP40007457-CH2-SW1)和 [UIViewController 参考](https://developer.apple.com/documentation/uikit/uiviewcontroller?language=objc)。

### <a name="responding-to-user-interaction"></a>响应用户交互

视图控制器最重要的作用是响应用户交互（如按下按钮、导航等）。 处理用户交互的最简单方法是关联控件以侦听用户输入并附加事件处理程序以响应输入。 例如，按钮可以进行关联，以响应触控事件，如 Phoneword 应用所示。

接下来探讨其工作原理。
在 `Phoneword_iOS` 项目中，向内容视图层次结构添加一个名为 `TranslateButton` 的按钮：

[![名为 TranslateButton 的按钮添加到内容视图层次结构](hello-ios-deepdive-images/image1.png)](hello-ios-deepdive-images/image1.png#lightbox)

在“属性板”中将“名称”分配给“按钮”控件时，iOS 设计器会自动将它映射到 **ViewController.designer.cs** 中的控件，从而使 `TranslateButton` 在 `ViewController` 类中可用。 控件会首先在视图生命周期的 `ViewDidLoad` 阶段中可用，因此此生命周期方法用于响应用户的触摸：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // wire up TranslateButton here
}
```

Phoneword 应用使用一个名为 `TouchUpInside` 的触摸事件来侦听用户的触摸。 `TouchUpInside` 会在控件边界内侦听跟在触摸下压（手指触摸屏幕）后面的触摸提起事件（手指提起离开屏幕）。 `TouchUpInside` 的相反事件是 `TouchDown` 事件，它在用户按下控件时触发。 `TouchDown` 事件会捕获大量干扰信息，并且没有为用户提供通过滑动手指离开控件来取消触摸的选项。 `TouchUpInside` 是响应**按钮**触摸的最常用方法，可创建用户在按下按钮时所期望的体验。 在 Apple 的 [iOS 人机界面指南](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/MobileHIG/index.html)中提供了有关此方面的详细信息。

应用使用了 lambda 来处理 `TouchUpInside` 事件，不过也可以使用委托或命名事件处理程序。 最终按钮代码类似于下面这样：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    string translatedNumber = "";

    TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
      translatedNumber = Core.PhonewordTranslator.ToNumber(PhoneNumberText.Text);
      PhoneNumberText.ResignFirstResponder ();

      if (translatedNumber == "") {
        CallButton.SetTitle ("Call", UIControlState.Normal);
        CallButton.Enabled = false;
      } else {
        CallButton.SetTitle ("Call " + translatedNumber, UIControlState.Normal);
        CallButton.Enabled = true;
      }
  };
}
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Phoneword 中引入的其他概念

Phoneword 应用程序引入了多个本指南中未提及的概念。 这些概念包括：

- **更改按钮文本** – Phoneword 应用演示了如何通过对**按钮**调用 `SetTitle` 并传入新文本和**按钮**的_控件状态_，来更改**按钮**的文本。 例如，下面的代码将 CallButton 的文本更改为“Call”：

    ```csharp
    CallButton.SetTitle ("Call", UIControlState.Normal);
    ```

- **启用和禁用按钮** – **按钮**可以处于 `Enabled` 或 `Disabled` 状态。 禁用的**按钮**不会响应用户输入。 例如，以下代码禁用 `CallButton`：

    ```csharp
    CallButton.Enabled = false;
    ```

    有关按钮的详细信息，请参阅[按钮](~/ios/user-interface/controls/buttons.md)指南。

- **消除键盘** – 当用户点击文本字段中，iOS 会显示键盘以便用户可以输入内容。 遗憾的是，没有内置功能可消除键盘。 当用户按下 `TranslateButton` 时，以下代码将添加到 `TranslateButton` 以消除键盘：

    ```csharp
    PhoneNumberText.ResignFirstResponder ();
    ```

    有关消除键盘的另一个示例，请参阅[消除键盘](https://github.com/xamarin/recipes/tree/master/Recipes/ios/input/keyboards/dismiss_the_keyboard)方案。

- **使用 URL 发起电话呼叫** – 在 Phoneword 应用中，使用 Apple URL 方案启动系统电话应用。 自定义 URL 方案由“tel:”前缀和转换后的电话号码组成，如下面的代码所示：

    ```csharp
    var url = new NSUrl ("tel:" + translatedNumber);
    if (!UIApplication.SharedApplication.OpenUrl (url))
    {
        // show alert Controller
    }
    ```

- **显示警报** – 当用户尝试在不支持呼叫的设备（例如模拟器或 iPod Touch）上发起电话呼叫时，会显示警报对话框，以便让用户知道无法发起电话呼叫。 下面的代码创建并填充警报控制器：

    ```csharp
    if (!UIApplication.SharedApplication.OpenUrl (url)) {
                    var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                    alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                    PresentViewController (alert, true, null);
                }
    ```

    有关 iOS 警报视图的详细信息，请参阅[警报控制器方法](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)。

## <a name="testing-deployment-and-finishing-touches"></a>测试、部署和完成收尾工作

Visual Studio for Mac 和 Visual Studio 均提供许多用于测试和部署应用程序的选项。 此部分介绍调试选项，演示如何在设备上测试应用程序，以及介绍用于创建自定义应用图标和启动图像的工具。

### <a name="debugging-tools"></a>调试工具

有时，应用程序代码中的问题难以进行诊断。 为了帮助诊断复杂的代码问题，可以[设置断点](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)、[逐行执行代码](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code)或[将信息输出到日志窗口](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window)。

### <a name="deploy-to-a-device"></a>部署到设备

iOS 模拟器是测试应用程序的快速方法。 该模拟器具有一些针对测试的有用优化，包括模拟位置、[模拟移动](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/test_location_changes_in_simulator)等。 但是，用户不会在模拟器中使用最终应用。 所有应用程序都应该及早并且经常在实际设备上进行测试。

设备会花费时间进行设置并需要 Apple 开发人员帐户。 [设备预配](~/ios/get-started/installation/device-provisioning/index.md)指南提供了有关使设备准备好进行开发的详尽说明。

> [!NOTE]
> 目前，Apple 要求需拥有开发证书或签名标识才能为物理设备或模拟器生成代码。 请按照[设备预配指南](~/ios/get-started/installation/device-provisioning/manual-provisioning.md)中的步骤执行此设置。

设置了设备之后，便可以通过将它插入，在生成工具栏中将目标更改为 iOS 设备，以及按“启动”（“播放”）来部署到它，如下面的屏幕截图所示：

::: zone pivot="macos"

![按“开始/播放”](hello-ios-deepdive-images/image46new.png)

::: zone-end
::: zone pivot="windows"

![按“开始/播放”](hello-ios-deepdive-images/vs-image46.png)

::: zone-end

应用会部署到 iOS 设备：

[![应用将部署到 iOS 设备并运行](hello-ios-deepdive-images/image1.png)](hello-ios-deepdive-images/image1.png#lightbox)

### <a name="generate-custom-icons-and-launch-images"></a>生成自定义图标和启动图像

并非每个人都具有可用于创建应用为脱颖而出而需要的自定义图标和启动图像的设计器。下面是几种生成自定义应用图像的备选方法：

::: zone pivot="macos"

- [**Pixelmator**](https://www.pixelmator.com/) – 适用于 Mac 的通用图像编辑应用（成本大约为 30 美元）。
- [**Fiverr**](https://www.fiverr.com/) – 从各种设计器中进行选择以便为你创建图标集（从 5 美元开始）。 可以漫无目标，不过如果需要动态设计的图标，则是一个好资源

::: zone-end
::: zone pivot="windows"

- Visual Studio – 可以用于直接在 IDE 中为应用创建简单图标集。
- [**Fiverr**](https://www.fiverr.com/) – 从各种设计器中进行选择以便为你创建图标集（从 5 美元开始）。 可以漫无目标，不过如果需要动态设计的图标，则是一个好资源

::: zone-end

有关图标和启动图像大小和要求的详细信息，请参阅[使用图像指南](~/ios/app-fundamentals/images-icons/index.md)。

## <a name="summary"></a>总结

祝贺你！ 你现在已充分了解了 Xamarin.iOS 应用程序的组件以及用于创建它们的工具。
在[入门系列中的下一个教程](~/ios/get-started/hello-ios-multiscreen/index.md)中，你会扩展应用程序以处理多个屏幕。 在此过程中，你会实现导航控制器，了解情节提要 Segue，以及在扩展应用程序以处理多个屏幕时引入模型、视图、控制器 (MVC) 模式。

## <a name="related-links"></a>相关链接

- [Hello，iOS（示例）](/samples/xamarin/ios-samples/hello-ios)
- [iOS 人机界面指南](https://developer.apple.com/design/human-interface-guidelines/ios/overview/themes/)
- [iOS 预配门户](https://developer.apple.com/account/#/overview)