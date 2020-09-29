---
title: Xamarin 中的 Windows
description: 本文介绍如何在 Xamarin. Mac 应用程序中使用 windows 和面板。 它介绍了如何在 Xcode 和 Interface Builder 中创建窗口和面板，如何从情节提要和 xib 文件加载它们，以及如何以编程方式使用它们。
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 2cacbf5640da60a7d084bf4ab113ef62fad097d8
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436386"
---
# <a name="windows-in-xamarinmac"></a>Xamarin 中的 Windows

_本文介绍如何在 Xamarin. Mac 应用程序中使用 windows 和面板。它介绍了如何在 Xcode 和 Interface Builder 中创建窗口和面板，如何从情节提要和 xib 文件加载它们，以及如何以编程方式使用它们。_

在 Xamarin 应用程序中使用 c # 和 .NET 时，可以访问在*Xcode 和**中工作*的开发人员所使用的相同窗口和面板。 因为 Xamarin 与 Xcode 直接集成，你可以使用 Xcode 的 _Interface Builder_ 创建和维护你的 Windows 和面板 (或（可选）在 c # 代码) 中直接创建它们。

基于其用途，Xamarin 应用程序可以在屏幕上显示一个或多个 Windows，以管理和协调它显示的信息并与之协同工作。 窗口的主要功能是：

1. 提供一个区域，可在其中放置和管理视图和控件。
2. 接受并响应事件，以响应用户与键盘和鼠标的交互。

Windows 可以在无模式状态中使用 (如文本编辑器，该编辑器可同时打开多个文档) 或模式 (如必须先解除的导出对话框，然后应用程序才能继续) 。

面板是一种特殊的窗口 (基类的子类 `NSWindow`) ，通常在应用程序中提供辅助功能，如文本格式检查器和系统颜色选取器等实用工具窗口。

[![编辑 Xcode 中的窗口](window-images/intro01.png)](window-images/intro01.png#lightbox)

在本文中，我们将介绍在 Xamarin. Mac 应用程序中使用 Windows 和面板的基本知识。 强烈建议您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 一文，特别是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 及 [输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions) 部分的简介，因为它涵盖了我们将在本文中使用的重要概念和技巧。

你可能想要查看[Xamarin 内部](~/mac/internals/how-it-works.md)示例文档的 "将[c # 类/方法公开到目标 c](~/mac/internals/how-it-works.md) " 部分， `Register` 并说明用于将 `Export` C # 类连接到目标 C 对象和 UI 元素的和命令。

## <a name="introduction-to-windows"></a>Windows 简介

如上所述，窗口提供了一个区域，可在其中放置和管理视图和控件，并根据用户交互 (通过键盘或鼠标) 来响应事件。

根据 Apple，macOS 应用中有五种主要类型的 Windows：

- **文档** 窗口-一个文档窗口，包含基于文件的用户数据，如电子表格或文本文档。
- **应用窗口** -应用窗口是不是基于文档的应用程序的主窗口， (如 Mac) 上的日历应用。
- **面板** -一个面板浮动在其他窗口上，并提供用户在打开文档时可以使用的工具或控件。 在某些情况下，面板可以为半透明 (例如，使用大型图形) 时。
- **对话框** -显示响应用户操作的对话框，通常提供用户完成操作的方法。 对话框需要用户的响应才能关闭。  (参阅使用 [对话](~/mac/user-interface/dialog.md)) 
- "**警报**"-"警报" 是一种特殊类型的对话框，在出现严重问题时出现 (例如错误) 或警告 (如准备删除文件) 。 由于警报是一个对话框，因此它还需要用户响应才能关闭。  (参阅使用 [警报](~/mac/user-interface/alert.md)) 

有关详细信息，请参阅 Apple 的[macOS 设计主题](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)的[About Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1)部分。

### <a name="main-key-and-inactive-windows"></a>Main、key 和非活动窗口

Xamarin 应用程序中的 Windows 根据用户当前与之交互的方式，其外观和行为可能有所不同。 当前关注用户关注的最重要的文档或应用窗口称为 _主窗口_。 在大多数情况下，此窗口还将是 (当前正在接受用户输入) 的窗口的 _键窗口_ 。 但这并不总是这样，例如，颜色选取器可以打开，而是用户与之交互的键窗口，以更改文档窗口中项的状态， (这仍是主窗口) 。

主窗口和键 Windows (如果它们分别是处于活动状态的) ，则 _非活动窗口_ 处于打开状态，但不在前台。 例如，文本编辑器应用程序一次可以打开多个文档，只是主窗口处于活动状态，所有其他窗口都处于非活动状态。 

有关详细信息，请参阅 Apple 的[macOS 设计主题](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)的[About Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1)部分。

### <a name="naming-windows"></a>命名窗口

窗口可以显示标题栏，在显示标题时，通常是应用程序的名称、正在处理的文档的名称或窗口 (如检查器) 。 某些应用程序不会显示标题栏，因为这些应用程序可以看到并不能使用文档。

Apple 建议遵循以下准则：

- 使用应用程序名称作为主文档窗口的标题。 
- 命名新的文档窗口 `untitled` 。 对于第一个新文档，不要向标题附加数字 (如 `untitled 1`) 。 如果用户在保存之前创建了另一个新文档并将其标题为标题，则调用该窗口 `untitled 2` ， `untitled 3` 等。

有关详细信息，请参阅 Apple 的[macOS 设计主题](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)的[命名窗口](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1)部分。

### <a name="full-screen-windows"></a>全屏窗口

在 macOS 中，应用程序窗口可以全屏显示所有内容，包括应用程序菜单栏 (可以通过将光标移动到屏幕顶部) 来提供干扰，使其与内容进行自由交互。

Apple 建议以下准则：

- 确定窗口全屏显示是否有意义。 提供简单交互的应用程序 (如计算器) 不应提供全屏模式。
- 如果全屏任务需要工具栏，则显示它。 通常，在全屏模式下，工具栏处于隐藏状态。
- 全屏窗口应具有用户完成任务所需的所有功能。
- 如果可能，请避免在用户处于全屏窗口时进行 Finder 交互。
- 利用增加的屏幕空间，而无需将焦点移出主任务。

有关详细信息，请参阅 Apple 的[macOS 设计主题](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)的[全屏窗口](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1)部分。

### <a name="panels"></a>面板

面板是一个辅助窗口，其中包含的控件和选项会影响活动文档或选择 (如系统颜色选取器) ：

[![颜色面板](window-images/panel01.png)](window-images/panel01.png#lightbox)

面板可以是 _特定于应用程序_ ，也可以是 _系统_范围。 应用特定的面板在应用程序的文档窗口的顶部浮动，并在应用程序处于后台时消失。 系统范围面板 (如 **字体** 面板) ，无论应用程序是在所有打开的窗口顶部浮动。 

Apple 建议以下准则：

- 通常，使用标准面板，只应慎用和图形密集型任务使用透明面板。
- 请考虑使用面板来使用户能够轻松访问重要控件或直接影响其任务的信息。
- 根据需要隐藏和显示面板。
- 面板应始终包含标题栏。
- 面板不应包含活动的 "最小化" 按钮。

#### <a name="inspectors"></a>检查器

大多数新式 macOS 应用程序都提供辅助控件和选项，这些控件和选项会影响作为主 (窗口的一部分的 _检查_ 器（如) 下面所示的 **页面** 应用程序），而不是使用面板窗口：

[![示例检查器](window-images/panel02.png)](window-images/panel02.png#lightbox)

有关详细信息，请参阅 Apple 的[macOS 设计主题](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)的[面板](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1)部分和[MacInspector](/samples/xamarin/mac-samples/macinspector)示例应用程序，以获取 Xamarin 应用程序中**检查器接口**的完整实现。

## <a name="creating-and-maintaining-windows-in-xcode"></a>在 Xcode 中创建和维护窗口

创建新的 Xamarin Cocoa 应用程序时，默认情况下会获得一个标准空白窗口。 此窗口在 `.storyboard` 项目中自动包含的文件中定义。 若要编辑 windows 设计，请在 **解决方案资源管理器**中双击该 `Main.storyboard` 文件：

[![选择主情节提要](window-images/edit01.png)](window-images/edit01.png#lightbox)

这将在 Xcode 的 Interface Builder 中打开窗口设计：

[![在 Xcode 中编辑 UI](window-images/edit02.png)](window-images/edit02.png#lightbox)

在 " **属性检查器**" 中，有几个属性可用于定义和控制窗口：

- **标题** -这是将在窗口标题栏中显示的文本。
- **自动** 保存-这是在窗口位置和设置自动保存时用于 ID 窗口的 _键_ 。
- **标题栏** -窗口是否显示标题栏。
- **统一标题和工具栏** -如果窗口中包含工具栏，则应将其作为标题栏的一部分。
- **完全大小的内容视图** -允许在标题栏下显示窗口的内容区域。
- **Shadow** -窗口具有阴影。
- 带有**纹理**纹理的窗口可以使用 (如活力) 的效果，并可通过在其主体上的任意位置拖动来四处移动。
- **关闭** -窗口具有 "关闭" 按钮。
- **最小化** -窗口具有 "最小化" 按钮。
- **调整大小** -窗口具有大小调整控件。
- **工具栏按钮** -窗口具有 "隐藏/显示" 工具栏按钮。
- 可**还原**-窗口的位置和设置自动保存和还原。
- **启动时可见** -加载文件时自动显示的窗口 `.xib` 。
- **停用时隐藏** -应用程序进入后台时隐藏窗口。
- **关闭时释放** -当关闭窗口时，将从内存中清除窗口。
- **始终显示工具提示-始终** 显示工具提示。
- 重新**计算视图循环**-在绘制窗口之前，将重新计算视图顺序。
- **空格**、 **Exposé** 和 **循环** -All 定义窗口在这些 macOS 环境中的行为方式。 
- **全屏** -确定此窗口是否可以进入全屏模式。 
- **动画** -控制可用于窗口的动画类型。
- **外观** -控制窗口的外观。 目前只有一种外观：浅绿色。

有关更多详细信息，请参阅 Apple [简介 Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) 和 [NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow) 文档。

### <a name="setting-the-default-size-and-location"></a>设置默认大小和位置

若要设置窗口的初始位置并控制其大小，请切换到 **大小检查器**：

[![默认大小和位置](window-images/edit07.png)](window-images/edit07.png#lightbox)

在这里，你可以设置窗口的初始大小，为其指定最小和最大大小，设置屏幕上的初始位置并控制窗口周围的边框。

### <a name="setting-a-custom-main-window-controller"></a>设置自定义主窗口控制器

为了能够创建用于向 c # 代码公开 UI 元素的插座和操作，Xamarin 应用程序需要使用自定义的窗口控制器。

执行以下操作：

1. 在 Xcode 的 Interface Builder 中打开应用的情节提要。
2. `NSWindowController`在 Design Surface 中选择。
3. 切换到 **标识检查器** 视图，并输入 `WindowController` 作为 **类名**： 

    [![设置类名](window-images/windowcontroller01.png)](window-images/windowcontroller01.png#lightbox)
4. 保存更改并返回到要同步的 Visual Studio for Mac。
5. `WindowController.cs`文件将添加到 Visual Studio for Mac 中的**解决方案资源管理器**中的项目： 

    [![选择 windows 控制器](window-images/windowcontroller02.png)](window-images/windowcontroller02.png#lightbox)
6. 重新打开 Xcode 的 Interface Builder 中的情节提要。
7. `WindowController.h`文件将可供使用： 

    [![编辑 WindowController 文件](window-images/windowcontroller03.png)](window-images/windowcontroller03.png#lightbox)

### <a name="adding-ui-elements"></a>添加 UI 元素

若要定义窗口的内容，请将 "库" **检查器** 中的控件拖到 " **界面编辑器**"。 有关使用 Interface Builder 创建和启用控件的详细信息，请参阅 [Xcode 和 Interface Builder 文档简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 。

例如，让我们将工具栏从 **库检查器** 拖到 " **界面编辑器**" 中的窗口上：

[![从库中选择工具栏](window-images/edit03.png)](window-images/edit03.png#lightbox)

接下来，在 **文本视图** 中拖动并调整其大小，以填充工具栏下面的区域：

[![添加文本视图](window-images/edit04.png)](window-images/edit04.png#lightbox)

由于我们希望 **文本视图** 随着窗口大小的变化而缩小和增长，接下来，我们将切换到 " **约束编辑器** " 并添加以下约束：

[![编辑约束](window-images/edit05.png)](window-images/edit05.png#lightbox)

通过单击编辑器顶部的四个 **红色 I-光标** ，并单击 " **添加4个约束**"，我们会告诉文本视图在窗口调整大小时，在水平和垂直方向上停留于给定的 X，Y 坐标。

最后，使用**输出口**向代码公开**文本视图** (确保选择 `ViewController.h` 文件) ：

[![配置插座](window-images/edit06.png)](window-images/edit06.png#lightbox)

保存更改并切换回 Visual Studio for Mac 以便与 Xcode 同步。

有关使用 **输出口** 和 **操作**的详细信息，请参阅我们的 [插座和操作](~/mac/get-started/hello-mac.md#outlets-and-actions) 文档。

### <a name="standard-window-workflow"></a>标准窗口工作流

对于你在 Xamarin Mac 应用程序中创建和使用的任何窗口，该过程基本上与我们刚刚执行的操作相同：

1. 对于不是自动添加到项目的默认窗口，请将新的窗口定义添加到项目。 下面将详细讨论这一内容。
1. 双击该 `Main.storyboard` 文件以打开窗口设计，以便在 Xcode 的 Interface Builder 中进行编辑。
1. 将新窗口拖入用户界面的设计，并使用_segue_ (将窗口挂钩到主窗口。有关详细信息，请参阅我们使用[情节提要](~/mac/platform/storyboards/indepth.md)文档) 的[segue](~/mac/platform/storyboards/indepth.md#Segues)部分。
1. 在 **属性检查器** 和 **大小检查器**中设置任何所需的窗口属性。
1. 拖动生成接口所需的控件，并在 **属性检查器**中对其进行配置。
1. 使用 **大小检查器** 处理 UI 元素的大小调整。
1. 通过 **插座** 和 **操作**向 c # 代码公开窗口的 UI 元素。
1. 保存更改并切换回 Visual Studio for Mac 以便与 Xcode 同步。

现在我们已经创建了一个基本窗口，接下来我们将介绍 Xamarin Mac 应用程序在使用 windows 时所做的典型过程。 

## <a name="displaying-the-default-window"></a>显示默认窗口

默认情况下，新的 Xamarin Mac 应用程序在启动时将自动显示在该文件中定义的窗口 `MainWindow.xib` ：

[![运行的示例窗口](window-images/display01.png)](window-images/display01.png#lightbox)

由于我们修改了上述窗口的设计，因此它现在包含默认的工具栏和 **文本视图** 控件。 文件中的以下部分 `Info.plist` 负责显示此窗口：

[![编辑信息。 info.plist](window-images/display00.png)](window-images/display00.png#lightbox)

" **主接口** " 下拉列表用于选择在此情况下) 使用的情节提要，将用作主要应用 UI (`Main.storyboard` 。

视图控制器会自动添加到项目中，以控制在 (显示的主窗口及其主视图) 。 它在文件中定义 `ViewController.cs` ，并附加到 "**标识检查器**" 下 Interface Builder 中的**文件所有者**：

[![设置文件的所有者](window-images/display02.png)](window-images/display02.png#lightbox)

对于我们的窗口，我们希望它在 `untitled` 第一次打开时具有标题，因此让我们重写 `ViewWillAppear` 中的方法，如下所示 `ViewController.cs` ：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
```    

> [!NOTE]
> 窗口的 `Title` 属性是在方法中设置的，而不是在方法中设置的 `ViewWillAppear` `ViewDidLoad` ，因为虽然视图可能会加载到内存中，但尚未完全实例化。 访问 `Title` 方法中的属性 `ViewDidLoad` 将会出现 `null` 异常，因为尚未构造该窗口并将其连接到属性。

## <a name="programmatically-closing-a-window"></a>以编程方式关闭窗口

有时，您可能希望以编程方式关闭 Xamarin Mac 应用程序中的窗口，而不是让用户单击窗口的 " **关闭** " 按钮或使用菜单项。 macOS 提供了两种不同的方法来关闭 `NSWindow` 编程： `PerformClose` 和 `Close` 。

### <a name="performclose"></a>PerformClose

调用的 `PerformClose` 方法 `NSWindow` 模拟用户单击窗口的 " **关闭** " 按钮，方法是立即突出显示该按钮，然后关闭窗口。

如果应用程序实现了 `NSWindow` 的 `WillClose` 事件，它将在窗口关闭前引发。 如果事件返回 `false` ，则窗口不会关闭。 如果该窗口没有 " **关闭** " 按钮或由于任何原因而无法关闭，则操作系统将发出警报声音。

例如：

```csharp
MyWindow.PerformClose(this);
```

将尝试关闭该 `MyWindow` `NSWindow` 实例。 如果成功，则将关闭该窗口，否则将发出警报声音，并保持打开状态。

### <a name="close"></a>关闭

调用的 `Close` 方法不 `NSWindow` 会模拟用户单击窗口的 " **关闭** " 按钮，只需暂时突出显示该按钮，就会关闭该窗口。

窗口无需关闭即可关闭，并且 `NSWindowWillCloseNotification` 通知将发送到处于关闭状态的默认通知中心。

`Close`方法不同于方法中的两个重要方式 `PerformClose` ：

1. 它不会尝试引发 `WillClose` 事件。
2. 它不会通过暂时突出显示按钮来模拟用户单击 " **关闭** " 按钮。

例如：

```csharp
MyWindow.Close();
```

将关闭 `MyWindow` `NSWindow` 实例。

## <a name="modified-windows-content"></a>修改的 windows 内容

在 macOS 中，Apple 提供了一种方法，通知用户 () 的窗口内容已 `NSWindow` 被用户修改并需要保存。 如果该窗口包含修改后的内容，则它的 **关闭** 小组件中将显示一个小的黑点：

[![具有已修改标记的窗口](window-images/close01.png)](window-images/close01.png#lightbox)

如果用户在存在对窗口内容的未保存更改的情况下尝试关闭窗口或退出 Mac 应用，则应显示一个 [对话框](~/mac/user-interface/dialog.md) 或 [模式表](~/mac/user-interface/dialog.md) ，并允许用户首先保存其更改：

[![关闭窗口时显示的保存工作表](window-images/close02.png)](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>将窗口标记为已修改

若要将窗口标记为具有修改的内容，请使用以下代码：

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

保存更改后，请使用以下内容清除修改后的标志：

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>在关闭窗口前保存更改

若要查看用户是否关闭窗口并允许他们事先保存修改后的内容，你将需要创建的子类 `NSWindowDelegate` 并重写其 `WindowShouldClose` 方法。 例如：

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class EditorWindowDelegate : NSWindowDelegate
    {
        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public EditorWindowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            // is the window dirty?
            if (Window.DocumentEdited) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Critical,
                    InformativeText = "Save changes to document before closing window?",
                    MessageText = "Save Document",
                };
                alert.AddButton ("Save");
                alert.AddButton ("Lose Changes");
                alert.AddButton ("Cancel");
                var result = alert.RunSheetModal (Window);

                // Take action based on result
                switch (result) {
                case 1000:
                    // Grab controller
                    var viewController = Window.ContentViewController as ViewController;

                    // Already saved?
                    if (Window.RepresentedUrl != null) {
                        var path = Window.RepresentedUrl.Path;

                        // Save changes to file
                        File.WriteAllText (path, viewController.Text);
                        return true;
                    } else {
                        var dlg = new NSSavePanel ();
                        dlg.Title = "Save Document";
                        dlg.BeginSheet (Window, (rslt) => {
                            // File selected?
                            if (rslt == 1) {
                                var path = dlg.Url.Path;
                                File.WriteAllText (path, viewController.Text);
                                Window.DocumentEdited = false;
                                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                                viewController.View.Window.RepresentedUrl = dlg.Url;
                                Window.Close();
                            }
                        });
                        return true;
                    }
                    return false;
                case 1001:
                    // Lose Changes
                    return true;
                case 1002:
                    // Cancel
                    return false;
                }
            }

            return true;
        }
        #endregion
    }
}
```

使用以下代码将此委托的实例附加到窗口中：

```csharp
// Set delegate
Window.Delegate = new EditorWindowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>在关闭应用之前保存更改

最后，你的 Xamarin Mac 应用应检查它的任何窗口是否包含修改的内容，并允许用户在退出前保存更改。 为此，请编辑 `AppDelegate.cs` 文件，重写 `ApplicationShouldTerminate` 方法，使其看起来如下所示：

```csharp
public override NSApplicationTerminateReply ApplicationShouldTerminate (NSApplication sender)
{
    // See if any window needs to be saved first
    foreach (NSWindow window in NSApplication.SharedApplication.Windows) {
        if (window.Delegate != null && !window.Delegate.WindowShouldClose (this)) {
            // Did the window terminate the close?
            return NSApplicationTerminateReply.Cancel;
        }
    }

    // Allow normal termination
    return NSApplicationTerminateReply.Now;
}
```

## <a name="working-with-multiple-windows"></a>使用多个窗口

大多数基于文档的 Mac 应用程序可以同时编辑多个文档。 例如，文本编辑器可以同时打开多个文本文件，以便进行编辑。 默认情况下，新的 Xamarin Mac 应用程序有一个 " **文件** " 菜单，其中的 **新** 项自动连接到 `newDocument:` **操作**。

下面的代码将激活此新项，并允许用户打开主窗口的多个副本以便一次编辑多个文档。

编辑 `AppDelegate.cs` 文件并添加以下计算属性：

```csharp
public int UntitledWindowCount { get; set;} =1;
```

使用此跟踪未保存文件的数量，以便我们可以向用户提供反馈，如上文所述)  (。

接下来，添加以下方法：

```csharp
[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

此代码会创建新版本的窗口控制器，加载新窗口，使其成为主窗口和键窗口，并设置标题。 现在，如果我们运行应用程序，并从 "**文件**" 菜单中选择 "**新建**"，则将打开并显示一个新的编辑器窗口：

[![添加了新的无标题窗口](window-images/display04.png)](window-images/display04.png#lightbox)

如果打开 **windows** 菜单，你会看到应用程序自动跟踪并处理打开的窗口：

[![Windows 菜单](window-images/display05.png)](window-images/display05.png#lightbox)

有关使用 Xamarin 应用程序中的菜单的详细信息，请参阅使用 [菜单](~/mac/user-interface/menu.md) 文档。

### <a name="getting-the-currently-active-window"></a>获取当前处于活动状态的窗口

在可打开多个 windows (文档) 的 Xamarin 应用程序中，有时需要获取当前的最顶层窗口 (键窗口) 。 以下代码将返回密钥窗口：

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

它可以在需要访问当前的键窗口的任何类或方法中调用。 如果当前未打开任何窗口，它将返回 `null` 。

### <a name="accessing-all-app-windows"></a>访问所有应用窗口

有时您可能需要访问您的 Xamarin 应用程序当前已经打开的所有窗口。 例如，若要查看用户要打开的文件是否已在退出窗口中打开。

`NSApplication.SharedApplication`维护一个 `Windows` 属性，该属性包含应用中所有打开的窗口的数组。 可以循环访问此数组，以访问应用的所有当前窗口。 例如：

```csharp
// Is the file already open?
for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
    if (content != null && path == content.FilePath) {
        // Bring window to front
        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
        return true;
    }
}
```

在示例代码中，我们将每个返回的窗口转换为 `ViewController` 应用程序中的自定义类，并针对用户要打开的文件的路径测试自定义属性的值 `Path` 。 如果文件已打开，则会将该窗口置于前台。

## <a name="adjusting-the-window-size-in-code"></a>调整代码中的窗口大小

有时，应用程序需要在代码中调整窗口的大小。 若要调整窗口的大小并重新定位，请调整其 `Frame` 属性。 调整窗口大小时，通常还需要调整其原点，以使窗口位于同一位置，因为 macOS 的坐标系统。

不同于 iOS，其中左上角表示 (0，0) ，macOS 使用屏幕左下角表示 (0，0) 的数学坐标系统。 在 iOS 中，坐标会随着向下移动向右移动而增加。 在 macOS 中，坐标向右增加了值。 

下面的示例代码调整窗口的大小：

```csharp
nfloat y = 0;

// Calculate new origin
y = Frame.Y - (768 - Frame.Height);

// Resize and position window
CGRect frame = new CGRect (Frame.X, y, 1024, 768);
SetFrame (frame, true);
```

> [!IMPORTANT]
> 当你在代码中调整 windows 大小和位置时，需要确保你遵守 Interface Builder 中设置的最小和最大大小。 这不会自动生效，你将能够使窗口放大或小于这些限制。

## <a name="monitoring-window-size-changes"></a>监视窗口大小更改

有时，你可能需要监视 Xamarin 应用内窗口大小的更改。 例如，重绘内容以适应新的大小。

若要监视大小更改，请首先确保已为 Xcode 的 Interface Builder 中的窗口控制器分配了自定义类。 例如， `MasterWindowController` 在以下内容中：

[![标识检查器](window-images/resize01.png)](window-images/resize01.png#lightbox)

接下来，编辑自定义窗口控制器类，并监视 `DidResize` 控制器窗口上的事件，以获得实时大小更改的通知。 例如：

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

或者，您可以使用 `DidEndLiveResize` 事件，仅在用户完成更改窗口大小时通知您。 例如：

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

        Window.DidEndLiveResize += (sender, e) => {
        // Do something after the user's finished resizing
        // the window
    };
}
```

## <a name="setting-a-windows-title-and-represented-file"></a>设置窗口标题和表示的文件

当使用表示文档的窗口时， `NSWindow` 具有 `DocumentEdited` 属性，如果设置为在 `true` "关闭" 按钮中显示一个小点，则会为用户提供一个指示文件已修改并且应在关闭前保存的指示。

编辑 `ViewController.cs` 文件并进行以下更改：

```csharp
public bool DocumentEdited {
    get { return View.Window.DocumentEdited; }
    set { View.Window.DocumentEdited = value; }
}
...

public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";

    View.Window.WillClose += (sender, e) => {
        // is the window dirty?
        if (DocumentEdited) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to give the user the ability to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    };
}

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Show when the document is edited
    DocumentEditor.TextDidChange += (sender, e) => {
        // Mark the document as dirty
        DocumentEdited = true;
    };

    // Overriding this delegate is required to monitor the TextDidChange event
    DocumentEditor.ShouldChangeTextInRanges += (NSTextView view, NSValue[] values, string[] replacements) => {
        return true;
    };

}
```

我们还会监视 `WillClose` 窗口中的事件，并检查属性的状态 `DocumentEdited` 。 如果需要， `true` 我们需要让用户能够保存对文件所做的更改。 如果我们运行应用程序并输入一些文本，将显示点：

[![已更改的窗口](window-images/file01.png)](window-images/file01.png#lightbox)

如果尝试关闭该窗口，会收到警报：

[![显示 "保存" 对话框](window-images/file02.png)](window-images/file02.png#lightbox)

如果要从文件加载文档，请使用方法将窗口的标题设置为该文件的名称， `window.SetTitleWithRepresentedFilename (Path.GetFileName(path));` (给定该方法 `path` 表示) 打开的文件。 此外，您还可以使用方法设置文件的 URL `window.RepresentedUrl = url;` 。

如果 URL 指向 OS 已知的文件类型，它的图标将显示在标题栏中。 如果用户右键单击该图标，将显示该文件的路径。

编辑 `AppDelegate.cs` 文件并添加以下方法：

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = true;
    dlg.CanChooseDirectories = false;

    if (dlg.RunModal () == 1) {
        // Nab the first file
        var url = dlg.Urls [0];

        if (url != null) {
            var path = url.Path;

            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow(this);

            // Load the text into the window
            var viewController = controller.Window.ContentViewController as ViewController;
            viewController.Text = File.ReadAllText(path);
                    viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
            viewController.View.Window.RepresentedUrl = url;

        }
    }
}
```

现在，如果我们运行应用程序，请从 "**文件**" 菜单中选择 "**打开 ...** "，从 "**打开**" 对话框中选择一个文本文件并将其打开：

[!["打开" 对话框](window-images/file03.png)](window-images/file03.png#lightbox)

将显示该文件，并将标题设置为该文件的图标：

[![已加载文件的内容](window-images/file04.png)](window-images/file04.png#lightbox)

## <a name="adding-a-new-window-to-a-project"></a>向项目添加新窗口

除了主文档窗口以外，Xamarin 应用程序可能还需要向用户显示其他类型的 windows，如首选项或检查器面板。

若要添加新窗口，请执行以下操作：

1. 在 **解决方案资源管理器**中，双击该 `Main.storyboard` 文件以将其打开，以便在 Xcode 的 Interface Builder 中进行编辑。
2. 将新的 **窗口控制器** 从 **库** 中拖放到 **Design Surface**：

    [![选择库中的新窗口控制器](window-images/new01.png)](window-images/new01.png#lightbox)
3. 在 **标识检查器**中， `PreferencesWindow` 为 **情节提要 ID**输入： 

    [![设置情节提要 ID](window-images/new02.png)](window-images/new02.png#lightbox)
4. 设计接口： 

    [![设计 UI](window-images/new03.png)](window-images/new03.png#lightbox)
5. 打开应用菜单 (`MacWindows`) ，选择 " **首选项 ...**"，然后单击并拖动到新窗口： 

    [![创建 segue](window-images/new05.png)](window-images/new05.png#lightbox)
6. 从弹出菜单中选择 " **显示** "。
7. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

如果运行代码并从 "**应用程序" 菜单**中选择 "**首选项 ...** "，则将显示该窗口：

[![示例首选项菜单](window-images/new04.png)](window-images/new04.png#lightbox)

## <a name="working-with-panels"></a>使用面板

如本文开头所述，面板浮动在其他窗口上，并提供用户在打开文档时可以使用的工具或控件。 

就像你在 Xamarin 应用程序中创建并使用的任何其他类型的窗口一样，此过程基本上是相同的：

1. 向项目中添加新的窗口定义。
2. 双击该 `.xib` 文件以打开窗口设计，以便在 Xcode 的 Interface Builder 中进行编辑。
3. 在 **属性检查器** 和 **大小检查器**中设置任何所需的窗口属性。
4. 拖动生成接口所需的控件，并在 **属性检查器**中对其进行配置。
5. 使用 **大小检查器** 处理 UI 元素的大小调整。
6. 通过 **插座** 和 **操作**向 c # 代码公开窗口的 UI 元素。
7. 保存更改并切换回 Visual Studio for Mac 以便与 Xcode 同步。

在 " **属性检查器**" 中，您具有特定于面板的以下选项：

[![特性检查器](window-images/panel03.png)](window-images/panel03.png#lightbox)

- **样式** -允许你调整面板的样式：常规面板 (看起来像标准窗口) ，实用工具面板 (具有更小的标题栏) ，HUD 面板 (是透明的，并且标题栏是背景) 的一部分。
- **非激活** -在面板变成键窗口时确定。
- **文档模式** -如果文档模式，面板将只浮动在应用程序窗口的上方，否则它将浮动在上方。

若要添加新面板，请执行以下操作：

1. 在**解决方案资源管理器**中，右键单击项目，然后选择 "**添加**" "  >  **新文件 ...**"。
2. 在 "新建文件" 对话框中， **Xamarin.Mac**选择  >  **包含控制器的 Xamarin Cocoa 窗口**：

    [![添加新的窗口控制器](window-images/panels00.png)](window-images/panels00.png#lightbox)

3. 对“名称”输入 `DocumentPanel`，然后单击“新建”按钮。
4. 双击该 `DocumentPanel.xib` 文件以将其打开，以便在 Interface Builder 中进行编辑： 

    [![编辑面板](window-images/new02.png)](window-images/new02.png#lightbox)

5. 删除现有窗口，并在 "**界面编辑器**" 中的 "**库" 检查器**中拖动面板： 

    [![删除现有窗口](window-images/panels01.png)](window-images/panels01.png#lightbox)

6. 将面板挂钩到**文件的所有者**  -  **窗口**  -  **插座**： 

    [![拖放面板](window-images/panels02.png)](window-images/panels02.png#lightbox)

7. 切换到 **标识检查器** ，将面板的类设置为 `DocumentPanel` ： 

    [![设置面板的类](window-images/panels03.png)](window-images/panels03.png#lightbox)

8. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。
9. 编辑 `DocumentPanel.cs` 文件，并将类定义更改为以下内容： 

    `public partial class DocumentPanel : NSPanel`

10. 保存对文件所做的更改。

编辑 `AppDelegate.cs` 文件并使方法如下所 `DidFinishLaunching` 示：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

如果运行应用程序，将显示 "面板"：

[![正在运行的应用程序中的面板](window-images/panels04.png)](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> Apple 已弃用面板窗口，应将其替换为 **检查器接口**。 有关在 Xamarin 应用程序中创建 **检查器** 的完整示例，请参阅我们的 [MacInspector](/samples/xamarin/mac-samples/macinspector) 示例应用。

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin. Mac 应用程序中使用窗口和面板。 我们看到了不同的窗口和面板的类型和用法，如何在 Xcode 的 Interface Builder 中创建和维护窗口和面板，以及如何在 c # 代码中使用窗口和面板。

## <a name="related-links"></a>相关链接

- [MacWindows (示例) ](/samples/xamarin/mac-samples/macwindows)
- [MacInspector (示例) ](/samples/xamarin/mac-samples/macinspector)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用菜单](~/mac/user-interface/menu.md)
- [ (Apple) 的 macOS 设计主题 ](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)
- [ (Apple) 的窗口、面板和屏幕 ](https://developer.apple.com/documentation/appkit/windows_panels_and_screens)