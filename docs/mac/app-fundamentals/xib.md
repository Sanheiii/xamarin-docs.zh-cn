---
title: Xamarin 中的 xib 文件
description: 本文介绍如何使用在 Xcode 的 Interface Builder 中创建的 xib 文件来创建和维护 Xamarin 应用程序的用户界面。
ms.prod: xamarin
ms.assetid: 6AF3D216-448D-4B2D-9026-74E4FFF5923A
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: d70d794ea95c628a2f2f59e94c3c4fe2f458f718
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436106"
---
# <a name="xib-files-in-xamarinmac"></a>Xamarin 中的 xib 文件

_本文介绍如何使用在 Xcode 的 Interface Builder 中创建的 xib 文件来创建和维护 Xamarin 应用程序的用户界面。_

> [!NOTE]
> 为 Xamarin 应用程序创建用户界面的首选方法是使用情节提要。 出于历史原因以及使用较旧的 Xamarin Mac 项目，此文档已保留原样。 有关详细信息，请参阅 [介绍情节提要](~/mac/platform/storyboards/index.md) 文档。

## <a name="overview"></a>概述

在 Xamarin Mac 应用程序中使用 c # 和 .NET 时，可以 *访问在* *Xcode 和* 中工作的开发人员所使用的相同用户界面元素和工具。 由于 Xamarin 与 Xcode 直接集成，因此可以使用 Xcode 的 _Interface Builder_ 来创建和维护用户界面 (或选择直接在 c # 代码) 中创建它们。

MacOS 使用 xib 文件来定义应用程序用户界面的元素 (例如菜单、窗口、视图、标签、文本字段) 在 Xcode 的 Interface Builder 中以图形方式创建和维护。

[![正在运行的应用的示例](xib-images/intro01.png "正在运行的应用的示例")](xib-images/intro01-large.png#lightbox)

在本文中，我们将介绍在 Xamarin. Mac 应用程序中使用 xib 文件的基础知识。 强烈建议你先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 文章，因为它涵盖了我们将在本文中使用的重要概念和技巧。

您可能想要了解如何向[Xamarin 内部](~/mac/internals/how-it-works.md)对象[公开 c # 类/方法](~/mac/internals/how-it-works.md)， `Register` 并说明用于将 `Export` c # 类与目标 c 对象和 UI 元素连接起来的和特性。

## <a name="introduction-to-xcode-and-interface-builder"></a>Xcode 和 Interface Builder 简介

作为 Xcode 的一部分，Apple 已创建名为 Interface Builder 的工具，它允许你在设计器中直观地创建用户界面。 Xamarin 将流畅地与 Interface Builder 集成在一起，使你能够通过目标为 C 的用户所用的工具创建 UI。

### <a name="components-of-xcode"></a>Xcode 组件

在 Visual Studio for Mac 中打开 Xcode 中的 xib 文件时，该文件将在左侧的 " **项目导航器** "、中间的 " **界面层次结构** " 和 " **界面编辑器** " 中打开，右侧有一个 " **属性 & 实用工具** " 部分：

[![Xcode UI 的组件](xib-images/xcode03.png "Xcode UI 的组件")](xib-images/xcode03-large.png#lightbox)

让我们看一下其中每个 Xcode 部分的作用，以及如何使用它们来创建 Xamarin 应用程序的接口。

#### <a name="project-navigation"></a>项目导航

在 Xcode 中打开 xib 文件以进行编辑时，Visual Studio for Mac 会在后台创建一个 Xcode 项目文件，以便在其自身与 Xcode 之间传输更改。 稍后，当你从 Xcode 切换回 Visual Studio for Mac 时，对此项目所做的任何更改都将通过 Visual Studio for Mac 与 Xamarin 项目进行同步。

" **项目导航** " 部分用于在组成此 _填充码_ Xcode 项目的所有文件之间导航。 通常，你只对此列表中的 xib 文件感兴趣，如 **MainMenu. xib** 和 **mainwindow.xaml**。

#### <a name="interface-hierarchy"></a>接口层次结构

" **接口层次结构** " 部分可让你轻松访问用户界面的多个关键属性，如其 **占位符** 和主 **窗口**。 你还可以使用此部分访问组成用户界面的各个元素 (视图) ，并通过在层次结构内拖动这些元素来调整其嵌套方式。

#### <a name="interface-editor"></a>界面编辑器

" **界面编辑器** " 部分提供了图形化用户界面的图面。 你将从 "**属性" & "实用程序**" 部分的 "**库**" 部分中拖动元素，以创建你的设计。 将 (视图中的用户界面元素添加) 到设计图面时，它们将按照它们在 "**界面编辑器**" 中出现的顺序添加到 "**接口层次结构**" 部分。

#### <a name="properties--utilities"></a>& 实用工具的属性

" **属性 & 实用程序** " 部分分为两个主要部分：我们将使用的 **属性** (也称为 "检查器) 和 **库**"：

![属性检查器](xib-images/xcode04.png "属性检查器")

本部分最初是空的，但是，如果您在 " **界面编辑器** " 或 " **界面层次结构**" 中选择元素，则 " **属性** " 部分将填充有关您可以调整的给定元素和属性的信息。

“属性”**** 部分内具有 8 个不同的检查器选项卡**，如下所示：

[![所有检查器的概述](xib-images/xcode05.png "所有检查器的概述")](xib-images/xcode05-large.png#lightbox)

由左至右，这些选项卡依次为：

- **文件检查器** – 文件检查器显示文件信息，例如正在编辑的 Xib 文件的文件名称和位置。
- **快速帮助** – 快速帮助选项卡基于 Xcode 中所选内容提供相应背景帮助。
- **标识检查器** – 标识检查器提供有关所选控件/视图的信息。
- **属性检查器** –属性检查器允许您自定义所选控件/视图的各种属性。
- **大小检查器** –大小检查器允许您控制所选控件/视图的大小和大小调整行为。
- **连接检查器** – "连接" 检查器显示选定控件的插座和操作连接。 我们只需片刻就能查看输出口和操作。
- **绑定检查器** –绑定检查器允许你配置控件，使其值自动绑定到数据模型。
- **查看效果检查器** –查看效果检查器允许您指定控件上的效果，例如动画。

在 " **库** " 部分中，可以找到要置于设计器中的控件和对象，以图形方式生成用户界面：

![库检查器的示例](xib-images/xcode06.png "库检查器的示例")

现在你熟悉了 Xcode IDE 和 Interface Builder，接下来让我们看一下如何使用它来创建用户界面。

## <a name="creating-and-maintaining-windows-in-xcode"></a>在 Xcode 中创建和维护窗口

用于创建 Xamarin 应用程序的用户界面的首选方法是使用情节提要 (请参阅我们的 [情节提要文档简介](~/mac/platform/storyboards/index.md)) 并因此，在 Xamarin 中启动的任何新项目都将默认使用情节提要。

若要切换到使用基于 xib 的 UI，请执行以下操作：

1. 打开 Visual Studio for Mac，然后启动新的 Xamarin Mac 项目。
2. 在**Solution Pad**中，右键单击项目，然后选择 "**添加**  >  **新文件 ...** "
3. 选择**Mac**  >  **Windows 控制器**：

    ![添加新的窗口控制器](xib-images/setup00.png "添加新的窗口控制器")
4. 输入 `MainWindow` 作为名称，然后单击 " **新建** " 按钮：

    ![添加新的主窗口](xib-images/setup01.png "添加新的主窗口")
5. 再次右键单击该项目，然后选择 "**添加**  >  **新文件 ...** "
6. 选择**Mac**  >  **主菜单**：

    ![添加新的主菜单](xib-images/setup02.png "添加新的主菜单")
7. 将名称保留为 `MainMenu` ，然后单击 " **新建** " 按钮。
8. 在 **Solution Pad** 选择 " **主" storyboard** 文件，右键单击并选择 " **删除**"：

    ![选择主情节提要](xib-images/setup03.png "选择主情节提要")
9. 在 "删除" 对话框中，单击 " **删除** " 按钮：

    ![确认删除](xib-images/setup04.png "确认删除")
10. 在 **Solution Pad**中，双击 **info.plist** 文件以将其打开以进行编辑。
11. `MainMenu`从**主界面**下拉列表中选择：

    [![设置主菜单](xib-images/setup05.png "设置主菜单")](xib-images/setup05-large.png#lightbox)
12. 在 **Solution Pad**中，双击 **MainMenu xib** 文件以将其打开，以便在 Xcode 的 Interface Builder 中进行编辑。
13. 在 " **库检查器**" 中，在 `object` 搜索字段中键入，然后将新的 **对象** 拖到设计图面上：

    [![编辑主菜单](xib-images/setup06.png "编辑主菜单")](xib-images/setup06-large.png#lightbox)
14. 在 **标识检查器**中， `AppDelegate` 为 **类**输入：

    [![选择应用程序委托](xib-images/setup07.png "选择应用程序委托")](xib-images/setup07-large.png#lightbox)
15. 从**接口层次结构**中选择 "**文件所有者**"，切换到 "**连接检查器**"，然后将行从委托拖动到 `AppDelegate` 刚刚添加到项目中的**对象**：

    [![连接应用程序委托](xib-images/setup08.png "连接应用程序委托")](xib-images/setup08-large.png#lightbox)
16. 保存更改并返回到 Visual Studio for Mac。

完成所有这些更改后，编辑 **AppDelegate.cs** 文件并使其类似于以下内容：

```csharp
using AppKit;
using Foundation;

namespace MacXib
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        public MainWindowController mainWindowController { get; set; }

        public AppDelegate ()
        {
        }

        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
            mainWindowController = new MainWindowController ();
            mainWindowController.Window.MakeKeyAndOrderFront (this);
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
    }
}
```

现在，在添加窗口控制器时，将在项目中自动包含的 **xib** 文件中定义应用的主窗口。 若要编辑 windows 设计，请在 **Solution Pad**中，双击 " **mainwindow.xaml" xib** 文件：

![选择 Mainwindow.xaml. xib 文件](xib-images/edit01.png "选择 Mainwindow.xaml. xib 文件")

这将在 Xcode 的 Interface Builder 中打开窗口设计：

[![编辑 Mainwindow.xaml. xib](xib-images/edit02.png "编辑 Mainwindow.xaml. xib")](xib-images/edit02-large.png#lightbox)

### <a name="standard-window-workflow"></a>标准窗口工作流

对于你在 Xamarin Mac 应用程序中创建和使用的任何窗口，该过程基本上是相同的：

1. 对于不是自动添加到项目的默认窗口，请将新的窗口定义添加到项目。
2. 双击 "xib" 文件，打开 "窗口设计" 以便在 Xcode 的 Interface Builder 中进行编辑。
3. 在 **属性检查器** 和 **大小检查器**中设置任何所需的窗口属性。
4. 拖动生成接口所需的控件，并在 **属性检查器**中对其进行配置。
5. 使用 **大小检查器** 处理 UI 元素的大小调整。
6. 通过插座和操作向 c # 代码公开窗口的 UI 元素。
7. 保存更改并切换回 Visual Studio for Mac 以便与 Xcode 同步。

### <a name="designing-a-window-layout"></a>设计窗口布局

对于添加的每个元素，在接口生成器中布局用户界面的过程基本上是相同的：

1. 在 **库检查器** 中找到所需的控件并将其拖放到 " **界面编辑器** " 中并将其定位。
2. 在 **属性检查器**中设置任何所需的窗口属性。
3. 使用 **大小检查器** 处理 UI 元素的大小调整。
4. 如果使用的是自定义类，请在 **标识检查器**中进行设置。
5. 通过插座和操作向 c # 代码公开 UI 元素。
6. 保存更改并切换回 Visual Studio for Mac 以便与 Xcode 同步。

例如：

1. 在 Xcode 中，从“库”部分拖动“Push Button”：

    [![从库中选择一个按钮](xib-images/xcode07.png "从库中选择一个按钮")](xib-images/xcode07-large.png#lightbox)
2. 将此按钮拖放到 "**界面编辑器**" 中的**窗口**上：

    [![向窗口添加按钮](xib-images/xcode08.png "向窗口添加按钮")](xib-images/xcode08-large.png#lightbox)
3. 单击“属性检查器”**** 中的 **Title** 属性，将此按钮的标题更改为 `Click Me`：

    ![设置按钮特性](xib-images/xcode09.png "设置按钮特性")
4. 从“库”部分拖动“标签”：

    [![选择库中的标签](xib-images/xcode10.png "选择库中的标签")](xib-images/xcode10-large.png#lightbox)
5. 将此标签拖放到“界面编辑器”中此按钮旁的“窗口”中：

    [![向窗口添加标签](xib-images/xcode11.png "向窗口添加标签")](xib-images/xcode11-large.png#lightbox)
6. 按住此标签上的右控点，将其拖动至靠近窗口边缘的位置：

    [![调整标签的大小](xib-images/xcode12.png "调整标签的大小")](xib-images/xcode12-large.png#lightbox)
7. 在 " **界面编辑器**" 中仍选中标签后，切换到 **大小检查器**：

    ![选择大小检查器](xib-images/xcode13.png "选择大小检查器")
8. 在 " **自动调整" 框** 中，单击右侧的 **暗红色方括号** 和中心的 " **暗红色水平" 箭头** ：

    ![编辑自动调整属性](xib-images/xcode14.png "编辑自动调整属性")
9. 这可确保在运行的应用程序中调整窗口大小时，标签将拉伸以增大和缩小。 **红色方括号**和 "**自动调整" 框**的顶部和左侧告诉标签停留在其给定的 X 和 Y 位置。
10. 保存对用户界面所做的更改

当调整控件大小和移动控件时，应注意到 Interface Builder 提供基于 [OS X 人体接口准则](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的有用的 snap 提示。 这些指南将帮助你创建高质量的应用程序，这些应用程序对 Mac 用户具有熟悉的外观和感觉。

如果你在 " **界面层次结构** " 部分中查看，则会看到组成用户界面的元素布局和层次结构的显示方式：

![在“界面层次结构”中选择元素](xib-images/xcode15.png "在“界面层次结构”中选择元素")

在此处，你可以选择要编辑的项，或拖动以根据需要对 UI 元素重新排序。 例如，如果某个 UI 元素被另一个元素所覆盖，则可以将其拖到列表底部，使其成为窗口中最顶层的项目。

有关在 Xamarin Mac 应用程序中使用 Windows 的详细信息，请参阅我们的 [windows](~/mac/user-interface/window.md) 文档。

## <a name="exposing-ui-elements-to-c-code"></a>向 c # 代码公开 UI 元素

在 Interface Builder 中完成用户界面的外观布局后，您将需要公开 UI 的元素，以便可以从 c # 代码进行访问。 为此，你将使用操作和插座。

### <a name="setting-a-custom-main-window-controller"></a>设置自定义主窗口控制器

为了能够创建用于向 c # 代码公开 UI 元素的插座和操作，Xamarin 应用程序需要使用自定义的窗口控制器。

执行以下操作：

1. 在 Xcode 的 Interface Builder 中打开应用的情节提要。
2. `NSWindowController`在 Design Surface 中选择。
3. 切换到 **标识检查器** 视图，并输入 `WindowController` 作为 **类名**：

    [![编辑类名称](xib-images/windowcontroller01.png "编辑类名称")](xib-images/windowcontroller01-large.png#lightbox)
4. 保存更改并返回到要同步的 Visual Studio for Mac。
5. **WindowController.cs**文件将添加到 Visual Studio for Mac 中的**Solution Pad**中的项目：

    ![Visual Studio for Mac 中的新类名称](xib-images/windowcontroller02.png "Visual Studio for Mac 中的新类名称")
6. 重新打开 Xcode 的 Interface Builder 中的情节提要。
7. **WindowController**文件将可供使用：

    [![Xcode 中匹配的 .h 文件](xib-images/windowcontroller03.png "Xcode 中匹配的 .h 文件")](xib-images/windowcontroller03-large.png#lightbox)

### <a name="outlets-and-actions"></a>插座和操作

什么是插座和操作？ 在传统的 .NET 用户界面编程中，用户界面中的控件在添加时会自动作为属性公开。 然而在 Mac 中情况则不同，如果仅将控件添加到视图，代码不可对其进行访问。 开发人员必须对代码显式公开 UI。 为此，Apple 提供了两个选项：

- **输出口** – 输出口类似于属性。 如果将控件绑定到插座，则会通过属性向代码公开此控件，因此可以执行诸如附加事件处理程序、调用其上的方法等操作。
- **操作** - 操作类似于 WPF 中的命令模式。 例如，在对控件执行操作时，如果单击按钮，则控件将自动调用代码中的方法。 操作功能强大且方便，因为可以将多个控件连接到相同操作。

在 Xcode 中，通过 *控件-拖动*直接在代码中添加输出口和操作。 更具体地说，这意味着若要创建输出口或操作，请选择想要添加插座或操作的控制元素，按住键盘上的 " **控制** " 按钮，然后将该控件直接拖到代码中。

对于 Xamarin 开发人员而言，这意味着您将拖入与要创建输出口或操作的 c # 文件相对应的目标 C 存根文件。 Visual Studio for Mac 创建一个名为 **mainwindow.xaml** 的文件作为其生成的填充码 Xcode 项目的一部分，以便使用 Interface Builder：

[![Xcode 中的 .h 文件的示例](xib-images/xcode16.png "Xcode 中的 .h 文件的示例")](xib-images/xcode16-large.png#lightbox)

此存根 .h 文件会在创建新的时反映自动添加到 Xamarin Mac 项目的 **MainWindow.designer.cs** `NSWindow` 。 此文件将用于同步 Interface Builder 所做的更改，我们将在其中创建输出口和操作，以便向 c # 代码公开 UI 元素。

#### <a name="adding-an-outlet"></a>添加插座

首先，让我们看一下如何创建插座来向 c # 代码公开 UI 元素。

请执行以下操作：

1. 在 Xcode 中屏幕右上角，单击“双圆”按钮，打开“助手编辑器”：

    [![选择助手编辑器](xib-images/outlet01.png "选择助手编辑器")](xib-images/outlet01-large.png#lightbox)
2. 此时 Xcode 会切换为拆分视图模式，“界面编辑器”位于一边，“代码编辑器”位于另一边。
3. 请注意，Xcode 已在**代码编辑器**中自动选取**MainWindowController**文件，这是不正确的。 如果你记得讨论了上述哪些插座和操作，则需要选择 **mainwindow.xaml** 。
4. 在 **代码编辑器** 的顶部，单击 "自动" **链接** ，然后选择 " **mainwindow.xaml** " 文件：

    [![选择正确的 .h 文件](xib-images/outlet02.png "选择正确的 .h 文件")](xib-images/outlet02-large.png#lightbox)
5. 此时 Xcode 应选择了正确的文件：

    [![选择了正确的文件](xib-images/outlet03.png "选择了正确的文件")](xib-images/outlet03-large.png#lightbox)
6. **最后一步非常重要！** 如果未选择正确的文件，则无法创建输出口和操作，也无法在 c # 中将其公开给错误的类！
7. 在 " **界面编辑器**" 中，按住键盘上的 **ctrl** 键，然后在代码编辑器中单击-将上面创建的标签拖到 `@interface MainWindow : NSWindow { }` 代码下面：

    [![拖动以创建新插座](xib-images/outlet04.png "拖动以创建新插座")](xib-images/outlet04-large.png#lightbox)
8. 会显示一个对话框。 将 **连接** 设置为 "输出口"，并输入 `ClickedLabel` 作为 **名称**：

    [![设置插座属性](xib-images/outlet05.png "设置插座属性")](xib-images/outlet05-large.png#lightbox)
9. 单击 " **连接** " 按钮以创建插座：

    ![已完成的输出口](xib-images/outlet06.png "已完成的输出口")
10. 保存对文件所做的更改。

#### <a name="adding-an-action"></a>添加操作

接下来，让我们看看如何创建一个操作来向 c # 代码公开用户与 UI 元素的交互。

执行以下操作：

1. 确保仍处于 **助手编辑器** 中，并且 **Mainwindow.xaml** 文件在 **代码编辑器**中可见。
2. 在 " **界面编辑器**" 中，按住键盘上的 **ctrl** 键，然后单击并将上面创建的按钮拖到代码编辑器上代码下面 `@property (assign) IBOutlet NSTextField *ClickedLabel;` ：

    [![拖动以创建操作](xib-images/action01.png "拖动以创建“操作”")](xib-images/action01-large.png#lightbox)
3. 将 **连接** 类型更改为 "操作"：

    [![选择操作类型](xib-images/action02.png "选择操作类型")](xib-images/action02-large.png#lightbox)
4. 输入 `ClickedButton` 作为**名称**：

    [![配置操作](xib-images/action03.png "配置操作")](xib-images/action03-large.png#lightbox)
5. 单击 " **连接** " 按钮以创建操作：

    ![已完成的操作](xib-images/action04.png "已完成的操作")
6. 保存对文件所做的更改。

在用户界面上连线并向 c # 代码公开后，切换回 Visual Studio for Mac，并让它同步 Xcode 和 Interface Builder 中的更改。

### <a name="writing-the-code"></a>编写代码

创建用户界面并将其 UI 元素通过输出口和操作公开给代码后，你就可以编写代码来使程序保持生活。 例如，通过在**Solution Pad**中双击来打开**MainWindow.cs**文件进行编辑：

[![MainWindow.cs 文件](xib-images/code01.png "MainWindow.cs 文件")](xib-images/code01-large.png#lightbox)

并将以下代码添加到 `MainWindow` 类，以使用上面创建的示例插座：

```csharp
private int numberOfTimesClicked = 0;
...

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Set the initial value for the label
    ClickedLabel.StringValue = "Button has not been clicked yet.";
}
```

请注意，在 `NSLabel` c # 中，通过在 Xcode 中创建其插座时在 Xcode 中分配的直接名称进行访问，在这种情况下，它称为 `ClickedLabel` 。 你可以像访问任何常规 c # 类一样，访问公开对象的任何方法或属性。

> [!IMPORTANT]
> 需要使用 `AwakeFromNib` ，而不是使用其他方法（如 `Initialize` ），因为在 `AwakeFromNib` 操作系统加载并从 xib 文件实例化用户界面 _后_ 调用。 如果尝试在完全加载并实例化 xib 文件之前访问 "标签" 控件，则会出现错误，因为尚未 `NullReferenceException` 创建 "标签" 控件。

接下来，将下面的分部类添加到 `MainWindow` 类：

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

此代码附加到你在 Xcode 和 Interface Builder 中创建的操作，并且在用户单击该按钮时将调用。

某些 UI 元素自动具有内置操作，例如，默认菜单栏中的项（如 " **打开 ...** " 菜单项） (`openDocument:`) 。 在 **Solution Pad**中，双击 **AppDelegate.cs** 文件以将其打开以进行编辑，并在方法下面添加以下代码 `DidFinishLaunching` ：

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = false;
    dlg.CanChooseDirectories = true;

    if (dlg.RunModal () == 1) {
        var alert = new NSAlert () {
            AlertStyle = NSAlertStyle.Informational,
            InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
            MessageText = "Folder Selected"
        };
        alert.RunModal ();
    }
}
```

此处的关键行是 `[Export ("openDocument:")]` ，它表示 `NSMenu` **AppDelegate** 具有响应操作的方法 `void OpenDialog (NSObject sender)` `openDocument:` 。

有关使用菜单的详细信息，请参阅我们的 [菜单](~/mac/user-interface/menu.md) 文档。

## <a name="synchronizing-changes-with-xcode"></a>与 Xcode 同步更改

切换回 Xcode 的 Visual Studio for Mac 时，在 Xcode 中所做的任何更改都将自动与 Xamarin 项目同步。

如果在**Solution Pad**中选择 " **MainWindow.designer.cs** "，则可以在 c # 代码中查看我们的插座和操作如何连接：

[![与 Xcode 同步更改](xib-images/sync01.png "与 Xcode 同步更改")](xib-images/sync01-large.png#lightbox)

请注意 **MainWindow.designer.cs** 文件中的两个定义：

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

在 Xcode 中的 **mainwindow.xaml** 文件中，与定义对齐：

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

如您所见，Visual Studio for Mac 侦听对 .h 文件的更改，然后在相应的 **designer.cs** 文件中自动同步这些更改，以将这些更改公开给您的应用程序。 你还可能会注意到， **MainWindow.designer.cs** 是一个分部类，因此 Visual Studio for Mac 不必修改 **MainWindow.cs** ，这将覆盖对类所做的任何更改。

您通常不需要亲自打开 **MainWindow.designer.cs** ，这里只是为了教育目的。

> [!IMPORTANT]
> 在大多数情况下，Visual Studio for Mac 会自动查看在 Xcode 中所做的任何更改，并将其同步到 Xamarin Mac 项目。 如果同步不自动进行，请切换回 Xcode，然后再次切换到 Visual Studio for Mac。 这通常会开始同步周期。

## <a name="adding-a-new-window-to-a-project"></a>向项目添加新窗口

除了主文档窗口以外，Xamarin 应用程序可能还需要向用户显示其他类型的 windows，如首选项或检查器面板。 向项目添加新窗口时，应始终使用 **带有 Controller 选项的 Cocoa 窗口** ，因为这样可以更轻松地从 xib 文件加载窗口。

若要添加新窗口，请执行以下操作：

1. 在**Solution Pad**中，右键单击项目，然后选择 "**添加**  >  **新文件 ...**"
2. 在 "新建文件" 对话框中， **Xamarin.Mac**选择  >  **包含控制器的 Xamarin Cocoa 窗口**：

    ![添加新的窗口控制器](xib-images/new01.png "添加新的窗口控制器")
3. 对“名称”输入 `PreferencesWindow`，然后单击“新建”按钮。
4. 双击 "PreferencesWindow" **xib** 文件，将其打开，以便在 Interface Builder 中进行编辑：

    [![编辑 Xcode 中的窗口](xib-images/new02.png "编辑 Xcode 中的窗口")](xib-images/new02-large.png#lightbox)
5. 设计接口：

    [![设计 windows 布局](xib-images/new03.png "设计 windows 布局")](xib-images/new03-large.png#lightbox)
6. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

将以下代码添加到 **AppDelegate.cs** 以显示新窗口：

```csharp
[Export("applicationPreferences:")]
void ShowPreferences (NSObject sender)
{
    var preferences = new PreferencesWindowController ();
    preferences.Window.MakeKeyAndOrderFront (this);
}
```

`var preferences = new PreferencesWindowController ();`该行创建一个窗口控制器的新实例，该实例从 xib 文件加载窗口并将其增加。 `preferences.Window.MakeKeyAndOrderFront (this);`该行向用户显示新窗口。

如果运行代码并从 "**应用程序" 菜单**中选择 "**首选项 ...** "，则将显示该窗口：

![运行示例应用](xib-images/new04.png "运行示例应用")

有关在 Xamarin Mac 应用程序中使用 Windows 的详细信息，请参阅我们的 [windows](~/mac/user-interface/window.md) 文档。

## <a name="adding-a-new-view-to-a-project"></a>向项目添加新视图

有时可以更轻松地将窗口设计分解为多个更易于管理的 xib 文件。 例如，当在 " **首选项" 窗口** 中选择工具栏项时，或在响应 **源列表** 选择时换出内容时，就像转换主窗口的内容一样。

将新视图添加到项目时，应始终使用 **带有 Controller 选项的 Cocoa 视图** ，因为这样可以更轻松地从 xib 文件加载视图。

若要添加新视图，请执行以下操作：

1. 在**Solution Pad**中，右键单击项目，然后选择 "**添加**  >  **新文件 ...**"
2. 在 "新建文件" 对话框中， **Xamarin.Mac**选择  >  **包含控制器的 Xamarin Cocoa 视图**：

    ![添加新视图](xib-images/view01.png "添加新视图")
3. 对“名称”输入 `SubviewTable`，然后单击“新建”按钮。
4. 双击 "SubviewTable" **xib** 文件以将其打开，以便在 Interface Builder 中进行编辑并设计用户界面：

    [![在 Xcode 中设计新视图](xib-images/view02.png "在 Xcode 中设计新视图")](xib-images/view02-large.png#lightbox)
5. 连接任何所需的操作和插座。
6. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

接下来，编辑 **SubviewTable.cs** ，并将以下代码添加到 **AwakeFromNib** 文件，以便在加载新视图时填充新视图：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create the Product Table Data Source and populate it
    var DataSource = new ProductTableDataSource ();
    DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));
    DataSource.Sort ("Title", true);

    // Populate the Product Table
    ProductTable.DataSource = DataSource;
    ProductTable.Delegate = new ProductTableDelegate (DataSource);

    // Auto select the first row
    ProductTable.SelectRow (0, false);
}
```

向项目添加枚举，以跟踪当前正在显示的视图。 例如， **SubviewType.cs**：

```csharp
public enum SubviewType
{
    None,
    TableView,
    OutlineView,
    ImageView
}
```

编辑将使用该视图并显示该视图的窗口的 xib 文件。 添加一个 **自定义视图** ，它将在通过 c # 代码将其加载到内存中后充当视图的容器，并将其公开到名为的插座 `ViewContainer` ：

[![创建所需的插座](xib-images/view03.png "创建所需的插座")](xib-images/view03-large.png#lightbox)

保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

接下来，编辑将显示新视图的窗口的 .cs 文件 (例如 **MainWindow.cs**) 并添加以下代码：

```csharp
private SubviewType ViewType = SubviewType.None;
private NSViewController SubviewController = null;
private NSView Subview = null;
...

private void DisplaySubview(NSViewController controller, SubviewType type) {

    // Is this view already displayed?
    if (ViewType == type) return;

    // Is there a view already being displayed?
    if (Subview != null) {
        // Yes, remove it from the view
        Subview.RemoveFromSuperview ();

        // Release memory
        Subview = null;
        SubviewController = null;
    }

    // Save values
    ViewType = type;
    SubviewController = controller;
    Subview = controller.View;

    // Define frame and display
    Subview.Frame = new CGRect (0, 0, ViewContainer.Frame.Width, ViewContainer.Frame.Height);
    ViewContainer.AddSubview (Subview);
}
```

如果需要显示从窗口容器中的 xib 文件加载的新视图 () 上添加的 **自定义视图** ，则此代码将处理删除任何现有视图，并将其替换为新视图。 它看起来看您已经看到了一个视图，如果它从屏幕中删除了它。 接下来，它会将已传入 (的视图从视图控制器) 调整大小以适合内容区域，并将其添加到内容中以供显示。

若要显示新视图，请使用以下代码：

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

这将为要显示的新视图创建一个视图控制器的新实例，将其类型 (设置为添加到项目) 的枚举指定，并使用 `DisplaySubview` 添加到窗口的类的方法来实际显示视图。 例如：

[![运行示例应用](xib-images/view04.png "运行示例应用")](xib-images/view04-large.png#lightbox)

有关在 Xamarin 应用程序中使用 Windows 的详细信息，请参阅我们的 [窗口](~/mac/user-interface/window.md) 和 [对话框](~/mac/user-interface/dialog.md) 文档。

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin. Mac 应用程序中使用 xib 文件。 我们看到了不同的类型和使用 xib 文件来创建应用程序的用户界面，如何在 Xcode 的 Interface Builder 中创建和维护 xib 文件，以及如何在 c # 代码中使用 xib 文件。

## <a name="related-links"></a>相关链接

- [MacImages（示例）](/samples/xamarin/mac-samples/macimages)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [菜单](~/mac/user-interface/menu.md)
- [对话框](~/mac/user-interface/dialog.md)
- [使用图像](~/mac/app-fundamentals/image.md)
- [macOS 人机界面指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)