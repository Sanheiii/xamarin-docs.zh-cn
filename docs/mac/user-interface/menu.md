---
title: Xamarin 中的菜单
description: 本文介绍如何在 Xamarin. Mac 应用程序中使用菜单。 它介绍了如何在 Xcode 中创建和维护菜单和菜单项，并 Interface Builder 并以编程方式使用它们。
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: c607e4668e4754edaec50a4c8b24776c7bb5844e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430781"
---
# <a name="menus-in-xamarinmac"></a>Xamarin 中的菜单

_本文介绍如何在 Xamarin. Mac 应用程序中使用菜单。它介绍了如何在 Xcode 中创建和维护菜单和菜单项，并 Interface Builder 并以编程方式使用它们。_

在 Xamarin 应用程序中使用 c # 和 .NET 时，可以访问 Cocoa 和 Xcode 中的开发人员所使用的相同的 "" 菜单。 由于 Xamarin 与 Xcode 直接集成，因此可以使用 Xcode 的 Interface Builder 创建和维护菜单栏、菜单和菜单 (项，也可以选择直接在 c # 代码) 中创建它们。

菜单是 Mac 应用程序用户体验的有机组成部分，通常显示在用户界面的各个部分：

- **应用程序的菜单栏** -这是在每个 Mac 应用程序的屏幕顶部显示的主菜单。
- **上下文菜单** -当用户在窗口中右键单击或单击控件时，将显示这些菜单。
- **状态栏** -这是应用程序菜单栏的最右侧的区域，该区域显示在菜单栏) 时钟左侧 (屏幕的顶部，并在将项目添加到左侧时放大到左侧。
- **停靠菜单** -停靠中的每个应用程序的菜单，当用户右键单击或单击该应用程序的图标时，或当用户左键单击并按住鼠标按钮时显示。
- **弹出式按钮和下拉列表** -弹出式按钮显示选定项，并显示用户单击时要从中选择的选项列表。 下拉列表是一种弹出式按钮，通常用于选择特定于当前任务上下文的命令。 两者都可以出现在窗口中的任意位置。

[![示例菜单](menu-images/intro01.png "示例菜单")](menu-images/intro01-large.png#lightbox)

在本文中，我们将介绍在 Xamarin. Mac 应用程序中使用 Cocoa 菜单栏、菜单和菜单项的基本知识。 强烈建议您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 一文，特别是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 及 [输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions) 部分的简介，因为它涵盖了我们将在本文中使用的重要概念和技巧。

你可能想要查看[Xamarin 内部](~/mac/internals/how-it-works.md)示例文档的 "将[c # 类/方法公开到目标 c](~/mac/internals/how-it-works.md) " 部分， `Register` 并说明用于将 `Export` C # 类连接到目标 C 对象和 UI 元素的和特性。

## <a name="the-applications-menu-bar"></a>应用程序的菜单栏 

不同于在 Windows 操作系统上运行的应用程序，每个窗口都可以有自己的菜单栏，在 macOS 上运行的每个应用程序都有一个菜单栏，该菜单栏位于屏幕顶部，用于该应用程序中的每个窗口：

[![菜单栏](menu-images/appmenu01.png "菜单栏")](menu-images/appmenu01-large.png#lightbox)

基于应用程序及其用户界面的当前上下文或状态，在任何给定时刻激活或停用此菜单栏上的项。 例如：如果用户选择一个文本字段，则会启用 " **编辑** " 菜单上的项，例如 " **复制** 和 **剪切**"。

根据 Apple 和默认情况，所有 macOS 应用程序都有一组标准菜单和菜单项显示在应用程序的菜单栏中：

- **Apple 菜单** -此菜单提供对用户可随时使用的系统范围项的访问权限，而不管正在运行的应用程序。 开发人员不能修改这些项。
- **应用菜单** -此菜单显示应用程序的名称（以粗体显示），并帮助用户识别当前正在运行的应用程序。 它包含应用于整个应用程序的项，而不是给定的文档或过程（如退出应用程序）。
- **文件菜单** -用于创建、打开或保存应用程序使用的文档的项。 如果你的应用程序不是基于文档的，则可以重命名或删除此菜单。
- **编辑菜单** -保存用于编辑或修改应用程序用户界面中的元素的命令，如 **剪切**、 **复制**和 **粘贴** 。
- "**格式" 菜单**-如果应用程序适用于文本，此菜单将保留用于调整该文本格式的命令。
- "**视图" 菜单**-保存影响内容显示方式的命令 (在应用程序的用户界面中查看) 。
- **特定于应用程序的菜单** -这些是特定于应用程序的任何菜单 (例如，web 浏览器的书签菜单) 。 它们应该出现在工具栏上的 " **视图** " 和 " **窗口** " 菜单之间。
- "**窗口" 菜单**-包含用于在应用程序中使用 windows 的命令，以及当前打开的窗口的列表。
- "**帮助" 菜单**-如果您的应用程序提供了屏幕帮助，则 "帮助" 菜单应是栏上最右侧的菜单。 

有关应用程序菜单栏和标准菜单和菜单项的详细信息，请参阅 Apple 的用户 [界面指导原则](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)。

### <a name="the-default-application-menu-bar"></a>默认应用程序菜单栏

创建新的 Xamarin Mac 项目时，会自动获取标准的默认应用程序菜单栏，其中包含 macOS 应用程序通常具有的典型项 (如以上) 部分所述。 应用程序的默认菜单栏在 **主** (中定义，并且在 **Solution Pad**中项目下的应用程序 UI) 的其余部分中定义：  

![选择主情节提要](menu-images/appmenu02.png "选择主情节提要")

双击 **Xcode 文件以将其** 打开，以便在的 Interface Builder 中进行编辑，并显示菜单编辑器界面：

[![在 Xcode 中编辑 UI](menu-images/defaultbar01.png "在 Xcode 中编辑 UI")](menu-images/defaultbar01-large.png#lightbox)

从这里，我们可以单击 "**文件**" 菜单中的 "**打开**" 菜单项等项，然后在 "属性"**检查器**中编辑或调整其属性：

[![编辑菜单的属性](menu-images/defaultbar02.png "编辑菜单的属性")](menu-images/defaultbar02-large.png#lightbox)

本文后面会介绍如何添加、编辑和删除菜单和项。 现在，我们只想查看哪些菜单和菜单项是默认可用的，以及如何通过一组预定义的插座和操作自动向代码公开这些菜单项 (。有关详细信息，请参阅我们的 [插座和操作](~/mac/get-started/hello-mac.md#outlets-and-actions) 文档) 。

例如，单击 "**打开**" 菜单项的 "**连接" 检查器**时，可以看到它自动连接到 `openDocument:` 操作： 

[![查看附加的操作](menu-images/defaultbar03.png "查看附加的操作")](menu-images/defaultbar03-large.png#lightbox)

如果在 "**接口" 层次结构**中选择**第一个响应**者，并在 "**连接" 检查器**中向下滚动，则会看到 " `openDocument:` **打开**" 菜单项所附加到的操作的定义，以及针对的应用程序的其他一些默认操作，这些操作不会自动连接到控件)  (：

[![查看所有附加的操作](menu-images/defaultbar04.png "查看所有附加的操作")](menu-images/defaultbar04-large.png#lightbox) 

为什么这很重要？ 下一节将介绍这些自动定义的操作如何与其他 Cocoa 用户界面元素配合使用来自动启用和禁用菜单项，并为项提供内置功能。

稍后我们将使用这些内置操作来启用和禁用代码中的项目，并在选择时提供自己的功能。

<a name="Built-In_Menu_Functionality"></a>

### <a name="built-in-menu-functionality"></a>内置菜单功能

如果在添加任何 UI 项或代码之前运行了新创建的 Xamarin. Mac 应用程序，你会注意到，某些项自动连接并启用了自动内置)  (，如**应用**菜单中的**Quit**项所示：

![已启用的菜单项](menu-images/appmenu03.png "已启用的菜单项")

其他菜单项（例如 **剪切**、 **复制**和 **粘贴** ）不是：

![禁用的菜单项](menu-images/appmenu04.png "禁用的菜单项")

让我们停止应用程序，然后双击**Solution Pad**中的**主情节提要**文件，将其打开，以便在 Xcode 的 Interface Builder 中进行编辑。 接下来，将一个 **文本视图** 从 **库** 中拖到 " **界面编辑器**" 中的窗口视图控制器上：

[![从库中选择文本视图](menu-images/appmenu05.png "从库中选择文本视图")](menu-images/appmenu05-large.png#lightbox)

在 **约束编辑器** 中，让我们将文本视图固定到窗口边缘，并通过单击编辑器顶部的所有四个红色 I-光标并单击 " **添加4个约束** " 按钮，将文本视图固定到窗口边缘并使用窗口进行收缩：

[![编辑约束](menu-images/appmenu06.png "编辑约束")](menu-images/appmenu06-large.png#lightbox)

保存对用户界面设计所做的更改，并切换回 Visual Studio for Mac 以将更改与 Xamarin 项目进行同步。 现在启动该应用程序，在文本视图中键入一些文本，将其选中，然后打开 " **编辑** " 菜单：

![自动启用/禁用菜单项](menu-images/appmenu07.png "自动启用/禁用菜单项")

请注意， **剪切**、 **复制**和 **粘贴** 项如何自动启用并完全正常运行，无需编写单行代码。 

这是怎么回事？ 请记住，内置的预定义操作将连接到默认菜单项 (如上所示) ，属于 macOS 的一部分的大多数 Cocoa 用户界面元素都具有与特定操作的内置挂钩 (例如 `copy:`) 。 因此，在将其添加到窗口、处于活动状态并选定后，会自动启用相应的菜单项或附加到该操作的项。 如果用户选择该菜单项，则将调用并执行内置于 UI 元素中的功能，而无需开发人员干预。

### <a name="enabling-and-disabling-menus-and-items"></a>启用和禁用菜单和项

默认情况下，每次发生用户事件时， `NSMenu` 都会根据应用程序的上下文自动启用和禁用每个可见的菜单和菜单项。 启用/禁用项的方式有三种：

- **自动菜单启用** -如果 `NSMenu` 可以找到相应的对象来响应项所连接的操作，则会启用菜单项。 例如，上面的文本视图与该操作有内置挂钩 `copy:` 。
- **自定义操作和 validateMenuItem：** 对于绑定到 [窗口或视图控制器自定义操作](#Working-with-Custom-Window-Actions)的任何菜单项，可以添加 `validateMenuItem:` 操作并手动启用或禁用菜单项。
- **启用手动菜单** -手动设置 `Enabled` 每个的属性， `NSMenuItem` 以在菜单中单独启用或禁用每个项。

若要选择系统，请设置的 `AutoEnablesItems` 属性 `NSMenu` 。 `true` 自动 (默认行为) 并且 `false` 为手动。 

> [!IMPORTANT]
> 如果选择使用手动启用菜单，则 `NSTextView` 不会自动更新菜单项（甚至是由 AppKit 类控制的菜单项）。 你将负责手动启用和禁用代码中的所有项。

#### <a name="using-validatemenuitem"></a>使用 validateMenuItem

如前所述，对于绑定到 [窗口或视图控制器自定义操作](#Working-with-Custom-Window-Actions)的任何菜单项，可以添加 `validateMenuItem:` 操作并手动启用或禁用菜单项。

在下面的示例中， `Tag` 将使用属性来决定 `validateMenuItem:` 根据中选定文本的状态，操作将启用/禁用的菜单项的类型 `NSTextView` 。 `Tag`已在每个菜单项 Interface Builder 中设置了属性：

![设置 Tag 属性](menu-images/validate01.png "设置 Tag 属性")

并将以下代码添加到视图控制器：

```csharp
[Action("validateMenuItem:")]
public bool ValidateMenuItem (NSMenuItem item) {

    // Take action based on the menu item type
    // (As specified in its Tag)
    switch (item.Tag) {
    case 1:
        // Wrap menu items should only be available if
        // a range of text is selected
        return (TextEditor.SelectedRange.Length > 0);
    case 2:
        // Quote menu items should only be available if
        // a range is NOT selected.
        return (TextEditor.SelectedRange.Length == 0);
    }

    return true;
}
```

如果运行此代码，且未在中选择任何文本 `NSTextView` ，则会禁用两个环绕菜单项 (即使它们连接到视图控制器) 上的操作：

![显示禁用的项](menu-images/validate02.png "显示禁用的项")

如果选中某个文本部分并重新打开该菜单，则两个环绕菜单项将可用：

![显示已启用的项目](menu-images/validate03.png "显示已启用的项目")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>在代码中启用和响应菜单项

如上所述，只需将特定的 Cocoa 用户界面元素添加到 UI 设计 (如文本字段) 中，一些默认菜单项将自动启用并正常工作，而无需编写任何代码。 接下来，让我们看看如何将自己的 c # 代码添加到 Xamarin Mac 项目以启用菜单项，并在用户选择它时提供功能。

例如，假设我们希望用户可以使用 "**文件**" 菜单中的 "**打开**" 项来选择文件夹。 由于我们希望它是应用程序范围的函数，而不局限于给定的窗口或 UI 元素，因此，我们将添加代码来处理应用程序委托。

在 **Solution Pad**中，双击该 `AppDelegate.CS` 文件以将其打开进行编辑：

![选择应用程序委托](menu-images/appmenu08.png "选择应用程序委托")

在方法下面添加以下代码 `DidFinishLaunching` ：

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

现在，让我们运行应用程序，然后打开 " **文件** " 菜单： 

!["文件" 菜单](menu-images/appmenu09.png ""文件" 菜单")

请注意，" **打开** " 菜单项现已启用。 如果选择它，将显示 "打开" 对话框：

![打开的对话框](menu-images/appmenu10.png "打开的对话框")

如果单击 " **打开** " 按钮，将显示警报消息：

![示例对话框消息](menu-images/appmenu11.png "示例对话框消息")

此处的关键行是 `[Export ("openDocument:")]` ，它告诉 `NSMenu` 我们 **AppDelegate** 有一个方法 `void OpenDialog (NSObject sender)` 来响应该 `openDocument:` 操作。 如果你将从上面的内容中记住，默认情况下，" **打开** " 菜单项会自动连接到此操作 Interface Builder：

[![查看附加的操作](menu-images/defaultbar03.png "查看附加的操作")](menu-images/defaultbar03-large.png#lightbox)

接下来，让我们看一下如何在代码中创建自己的菜单、菜单项和操作并对其进行响应。

### <a name="working-with-the-open-recent-menu"></a>使用 "最近打开" 菜单

默认情况下，" **文件** " 菜单包含 " **打开的最近** 使用的项目"，用于跟踪用户在应用中打开的最后几个文件。 如果创建的是 `NSDocument` 基于 Xamarin 的 Xamarin 应用，则会自动为您处理此菜单。 对于任何其他类型的 Xamarin. Mac 应用程序，你将负责手动管理和响应此菜单项。

若要手动处理 " **最近打开** 的菜单" 菜单，您首先需要通知它已使用以下内容打开或保存了一个新文件：

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

即使你的应用程序未使用 `NSDocuments` ，你仍可以 `NSDocumentController` 通过将与文件的位置一起发送到的方法，来使用来维护 " **最近打开** " 菜单 `NSUrl` `NoteNewRecentDocumentURL` `SharedDocumentController` 。

接下来，需要重写 `OpenFile` 应用委托的方法，以打开用户从 " **最近打开** " 菜单中选择的任何文件。 例如：

```csharp
public override bool OpenFile (NSApplication sender, string filename)
{
    // Trap all errors
    try {
        filename = filename.Replace (" ", "%20");
        var url = new NSUrl ("file://"+filename);
        return OpenFile(url);
    } catch {
        return false;
    }
}
```

`true`如果可以打开文件，则返回; 否则返回， `false` 将向用户显示该文件无法打开的内置警告。

由于从 " **打开最近打开** " 菜单返回的文件名和路径可能包含一个空格，因此，需要在创建之前正确地转义此字符， `NSUrl` 否则将会出现错误。 我们通过以下代码来实现此目的：

```csharp
filename = filename.Replace (" ", "%20");
```

最后，我们创建一个 `NSUrl` 指向该文件的，并使用应用程序委托中的 helper 方法打开一个新窗口，并将文件加载到其中：

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

若要将所有内容整合在一起，让我们看一下 **AppDelegate.cs** 文件中的示例实现：

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace MacHyperlink
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }

        public override bool OpenFile (NSApplication sender, string filename)
        {
            // Trap all errors
            try {
                filename = filename.Replace (" ", "%20");
                var url = new NSUrl ("file://"+filename);
                return OpenFile(url);
            } catch {
                return false;
            }
        }
        #endregion

        #region Private Methods
        private bool OpenFile(NSUrl url) {
            var good = false;

            // Trap all errors
            try {
                var path = url.Path;

                // Is the file already open?
                for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
                    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
                    if (content != null && path == content.FilePath) {
                        // Bring window to front
                        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
                        return true;
                    }
                }

                // Get new window
                var storyboard = NSStoryboard.FromName ("Main", null);
                var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

                // Display
                controller.ShowWindow(this);

                // Load the text into the window
                var viewController = controller.Window.ContentViewController as ViewController;
                viewController.Text = File.ReadAllText(path);
                viewController.SetLanguageFromPath(path);
                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                viewController.View.Window.RepresentedUrl = url;

                // Add document to the Open Recent menu
                NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);

                // Make as successful
                good = true;
            } catch {
                // Mark as bad file on error
                good = false;
            }

            // Return results
            return good;
        }
        #endregion

        #region actions
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
                    // Open the document in a new window
                    OpenFile (url);
                }
            }
        }
        #endregion
    }
}
```

根据应用程序的要求，您可能不希望用户在多个窗口中同时打开同一文件。 在示例应用中，如果用户选择一个已打开的文件 (从 " **打开最近打开** " 或 " **打开"。** 菜单项) 中，包含该文件的窗口将置于顶层。

为实现此目的，我们在帮助器方法中使用了以下代码：

```csharp
var path = url.Path;

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

我们设计的 `ViewController` 类在其属性中包含文件的路径 `Path` 。 接下来，我们循环遍历应用程序中所有当前打开的窗口。 如果该文件已在一个窗口中打开，则会使用以下内容将其置于所有其他窗口的前端：

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

如果未找到匹配项，则会打开一个新窗口，其中加载了文件，并在 " **最近打开** " 菜单中记录了该文件：

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);

// Load the text into the window
var viewController = controller.Window.ContentViewController as ViewController;
viewController.Text = File.ReadAllText(path);
viewController.SetLanguageFromPath(path);
viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
viewController.View.Window.RepresentedUrl = url;

// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

<a name="Working-with-Custom-Window-Actions"></a>

### <a name="working-with-custom-window-actions"></a>使用自定义窗口操作

与预先连接到标准菜单项的内置 **第一个响应** 程序操作一样，你可以创建新的自定义操作，并将它们连接到 Interface Builder 中的菜单项。

首先，在应用的一个窗口控制器上定义自定义操作。 例如：

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

接下来，在 **Solution Pad** 中双击应用的情节提要文件，将其打开，以便在 Xcode 的 Interface Builder 中进行编辑。 选择**应用程序场景**下的**第一个响应**程序，然后切换到 "**属性" 检查器**：

![特性检查器](menu-images/action01.png "特性检查器")

单击 **+** " **属性" 检查器** 底部的按钮以添加新的自定义操作：

![添加新操作](menu-images/action02.png "添加新操作")

为其命名，使其名称与你在窗口控制器上创建的自定义操作相同：

![编辑操作名称](menu-images/action03.png "编辑操作名称")

按住 ctrl 的同时单击菜单项并将其拖至**应用程序场景**下的**第一个响应**程序。 从弹出列表中选择刚创建的新操作 (`defineKeyword:` 在此示例中) ：

![附加操作](menu-images/action04.png "附加操作")

保存对情节提要所做的更改并返回到 Visual Studio for Mac 以同步更改。 如果运行应用程序，则将自定义操作连接到的菜单项将自动启用/禁用 (基于打开打开操作的窗口) 并选择菜单项将引发操作：

[![测试新操作](menu-images/action05.png "测试新操作")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus"></a>

### <a name="adding-editing-and-deleting-menus"></a>添加、编辑和删除菜单

如前面部分所示，Xamarin 应用程序附带了一个预设数量的默认菜单和菜单项，特定 UI 控件将自动激活和响应这些菜单项。 我们还了解了如何向应用程序添加代码，这些代码还将启用和响应这些默认项。

在本部分中，我们将介绍如何删除不需要的菜单项，重新组织菜单并添加新菜单、菜单项和操作。

双击**Solution Pad**中的**主情节提要**文件，将其打开进行编辑：

[![在 Xcode 中编辑 UI](menu-images/maint01.png "在 Xcode 中编辑 UI")](menu-images/maint01-large.png#lightbox)

对于我们的特定 Xamarin 应用程序，我们不会使用默认的 " **视图** " 菜单，因此我们要将其删除。 在 " **接口层次结构** " 中，选择作为主菜单栏一部分的 " **视图** " 菜单项：

![选择 "视图" 菜单项](menu-images/maint02.png "选择 "视图" 菜单项")

按 delete 或 backspace 删除菜单。 接下来，我们不会使用 " **格式** " 菜单中的所有项，而是希望在子菜单下移动要使用的项。 在 " **接口层次结构** " 中，选择以下菜单项：

![突出显示多个项](menu-images/maint03.png "突出显示多个项")

将父 **菜单** 下的项从当前所在的子菜单中拖放：

[![将菜单项拖至父菜单](menu-images/maint04.png "将菜单项拖至父菜单")](menu-images/maint04-large.png#lightbox)

菜单现在应如下所示：

[![新位置中的项](menu-images/maint05.png "新位置中的项")](menu-images/maint05-large.png#lightbox)

接下来，将 **文本** 子菜单从 " **格式** " 菜单中拖出，并将其放在 " **格式** " 菜单和 " **窗口** " 菜单之间的主菜单栏上：

[!["文本" 菜单](menu-images/maint06.png ""文本" 菜单")](menu-images/maint06-large.png#lightbox)

让我们返回 " **格式** " 菜单，并删除 **字体** 子菜单项。 接下来，选择 " **格式** " 菜单，然后将其重命名为 "Font"：

[!["字体" 菜单](menu-images/maint07.png ""字体" 菜单")](menu-images/maint07-large.png#lightbox)

接下来，让我们创建一个预定义短语的自定义菜单，该菜单将在选中时自动追加到文本视图中的文本上。 在 " **库" 检查器** 底部的 "搜索" 框中，键入 "菜单"。 这样，就可以更轻松地查找和使用所有菜单 UI 元素：

![库检查器](menu-images/maint08.png "库检查器")

现在，让我们执行以下操作来创建菜单：

1. 将 "**库" 检查器**中的**菜单项**拖到 "**文本**" 和 "**窗口**" 菜单之间的菜单栏上： 

    ![选择库中的新菜单项](menu-images/maint10.png "选择库中的新菜单项")
2. 重命名项 "短语"： 

    [![设置菜单名称](menu-images/maint09.png "设置菜单名称")](menu-images/maint09-large.png#lightbox)
3. 接下来，从**库检查器**中拖出一个**菜单**： 

    ![从库中选择菜单](menu-images/maint11.png "从库中选择菜单")
4. 在刚刚创建的新**菜单项**上放置和**菜单**，并将其名称更改为 "短语"： 

    [![编辑菜单名称](menu-images/maint12.png "编辑菜单名称")](menu-images/maint12-large.png#lightbox)
5. 现在，让我们重命名三个默认 **菜单项** "Address"、"Date" 和 "问候语"： 

    [![短语菜单](menu-images/maint13.png "短语菜单")](menu-images/maint13-large.png#lightbox)
6. 接下来，通过从**库检查器**中拖动**菜单项**并将其调用为 "签名" 来添加第四个**菜单项**： 

    [![编辑菜单项名称](menu-images/maint14.png "编辑菜单项名称")](menu-images/maint14-large.png#lightbox)
7. 保存对菜单栏所做的更改。

现在，让我们创建一组自定义操作，以便将新菜单项公开给 c # 代码。 在 Xcode 中，让我们切换到 **助手** 视图：

[![创建所需的操作](menu-images/maint15.png "创建所需的操作")](menu-images/maint15-large.png#lightbox)

让我们执行以下操作：

1. Control-从 " **地址** " 菜单项拖动到 **AppDelegate** 文件。
2. 切换 **连接** 类型为 " **操作**"： 

    [![选择操作类型](menu-images/maint17.png "选择操作类型")](menu-images/maint17-large.png#lightbox)
3. 输入 **名称** "phraseAddress"，然后按 " **连接** " 按钮以创建新操作： 

    [![配置该操作](menu-images/maint18.png "配置该操作")](menu-images/maint18-large.png#lightbox)
4. 为 " **日期**"、" **问候**" 和 " **签名** " 菜单项重复以上步骤： 

    [![已完成的操作](menu-images/maint19.png "已完成的操作")](menu-images/maint19-large.png#lightbox)
5. 保存对菜单栏所做的更改。

接下来，我们需要为文本视图创建一个插座，以便我们可以从代码调整其内容。 在**助手编辑器**中选择 " **ViewController** " 文件，然后创建一个名为的新插座 `documentText` ：

[![创建输出口](menu-images/maint20.png "创建输出口")](menu-images/maint20-large.png#lightbox)

返回到 Visual Studio for Mac 以同步 Xcode 中的更改。 接下来，编辑 **ViewController.cs** 文件并使其类似于以下内容：

```csharp
using System;

using AppKit;
using Foundation;

namespace MacMenus
{
    public partial class ViewController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public override NSObject RepresentedObject {
            get {
                return base.RepresentedObject;
            }
            set {
                base.RepresentedObject = value;
                // Update the view, if already loaded.
            }
        }

        public string Text {
            get { return documentText.Value; }
            set { documentText.Value = value; }
        } 
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any additional setup after loading the view.
        }

        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            App.textEditor = this;
        }

        public override void ViewWillDisappear ()
        {
            base.ViewDidDisappear ();

            App.textEditor = null;
        }
        #endregion
    }
}
```

这会在类的外部公开文本视图的文本 `ViewController` ，并在窗口获得或失去焦点时通知应用程序委托。 现在，编辑 **AppDelegate.cs** 文件并使其类似于以下内容：

```csharp
using AppKit;
using Foundation;
using System;

namespace MacMenus
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public ViewController textEditor { get; set;} = null;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
        #endregion

        #region Custom actions
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

        partial void phrasesAddress (Foundation.NSObject sender) {

            textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
        }

        partial void phrasesDate (Foundation.NSObject sender) {

            textEditor.Text += DateTime.Now.ToString("D");
        }

        partial void phrasesGreeting (Foundation.NSObject sender) {

            textEditor.Text += "Dear Sirs,\n\n";
        }

        partial void phrasesSignature (Foundation.NSObject sender) {

            textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
        }
        #endregion
    }
}
```

这里，我们已创建 `AppDelegate` 了一个分部类，以便我们可以使用在 Interface Builder 中定义的操作和插座。 我们还将公开 `textEditor` 以跟踪当前处于焦点的窗口。

以下方法用于处理自定义菜单和菜单项：

```csharp
partial void phrasesAddress (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
}

partial void phrasesDate (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += DateTime.Now.ToString("D");
}

partial void phrasesGreeting (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Dear Sirs,\n\n";
}

partial void phrasesSignature (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
}
```

现在，如果我们运行应用程序，" **短语** " 菜单中的所有项都将处于活动状态，并且在选中时会将 "提供短语" 添加到 "文本" 视图中：

![运行的应用的示例](menu-images/maint21.png "运行的应用的示例")

现在，我们已经了解了使用应用程序菜单栏的基本知识，接下来让我们看看如何创建自定义上下文菜单。

### <a name="creating-menus-from-code"></a>从代码创建菜单

除了使用 Xcode 的 Interface Builder 创建菜单和菜单项外，可能还需要在代码中创建、修改或删除菜单、子菜单或菜单项。

在下面的示例中，创建了一个类，用于保存有关将动态创建的菜单项和子菜单的信息：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace AppKit.TextKit.Formatter
{
    public class LanguageFormatCommand : NSObject
    {
        #region Computed Properties
        public string Title { get; set; } = "";
        public string Prefix { get; set; } = "";
        public string Postfix { get; set; } = "";
        public List<LanguageFormatCommand> SubCommands { get; set; } = new List<LanguageFormatCommand>();
        #endregion

        #region Constructors
        public LanguageFormatCommand () {

        }

        public LanguageFormatCommand (string title)
        {
            // Initialize
            this.Title = title;
        }

        public LanguageFormatCommand (string title, string prefix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
        }

        public LanguageFormatCommand (string title, string prefix, string postfix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
            this.Postfix = postfix;
        }
        #endregion
    }
}
```

#### <a name="adding-menus-and-items"></a>添加菜单和项

定义了此类后，以下例程将分析对象的集合， `LanguageFormatCommand` 并通过将新菜单和菜单项追加到现有菜单的底部，来以递归方式生成新菜单和菜单项， (在已传入的 Interface Builder) 中创建：

```csharp
private void AssembleMenu(NSMenu menu, List<LanguageFormatCommand> commands) {
    NSMenuItem menuItem;

    // Add any formatting commands to the Formatting menu
    foreach (LanguageFormatCommand command in commands) {
        // Add separator or item?
        if (command.Title == "") {
            menuItem = NSMenuItem.SeparatorItem;
        } else {
            menuItem = new NSMenuItem (command.Title);

            // Submenu?
            if (command.SubCommands.Count > 0) {
                // Yes, populate submenu
                menuItem.Submenu = new NSMenu (command.Title);
                AssembleMenu (menuItem.Submenu, command.SubCommands);
            } else {
                // No, add normal menu item
                menuItem.Activated += (sender, e) => {
                    // Apply the command on the selected text
                    TextEditor.PerformFormattingCommand (command);
                };
            }
        }
        menu.AddItem (menuItem);
    }
}
``` 

对于 `LanguageFormatCommand` 具有空白属性的任何对象 `Title` ，此例程会创建一个 **分隔符菜单项** ， (菜单部分之间) 窄灰色线条：

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

如果提供了标题，则会创建一个具有该标题的新菜单项：

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

如果 `LanguageFormatCommand` 对象包含子 `LanguageFormatCommand` 对象，则会创建一个子菜单，并 `AssembleMenu` 递归调用方法以生成该菜单：

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

对于没有子菜单的任何新菜单项，将添加代码以处理用户选择的菜单项：

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>测试菜单创建

如果已创建了以下对象集合，则所有上述代码都已准备就绪 `LanguageFormatCommand` ：

```csharp
// Define formatting commands
FormattingCommands.Add(new LanguageFormatCommand("Strong","**","**"));
FormattingCommands.Add(new LanguageFormatCommand("Emphasize","_","_"));
FormattingCommands.Add(new LanguageFormatCommand("Inline Code","`","`"));
FormattingCommands.Add(new LanguageFormatCommand("Code Block","```\n","\n```"));
FormattingCommands.Add(new LanguageFormatCommand("Comment","<!--","-->"));
FormattingCommands.Add (new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Unordered List","* "));
FormattingCommands.Add(new LanguageFormatCommand("Ordered List","1. "));
FormattingCommands.Add(new LanguageFormatCommand("Block Quote","> "));
FormattingCommands.Add (new LanguageFormatCommand ());

var Headings = new LanguageFormatCommand ("Headings");
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 1","# "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 2","## "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 3","### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 4","#### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 5","##### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 6","###### "));
FormattingCommands.Add (Headings);

FormattingCommands.Add(new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Link","[","]()"));
FormattingCommands.Add(new LanguageFormatCommand("Image","![](",")"));
FormattingCommands.Add(new LanguageFormatCommand("Image Link","[![](",")](LinkImageHere)"));
```

并且传递到函数的集合 `AssembleMenu` (将 **格式** 设置菜单设置为基本) 时，将创建以下动态菜单和菜单项：

![正在运行的应用中的新菜单项](menu-images/dynamic01.png "正在运行的应用中的新菜单项")

#### <a name="removing-menus-and-items"></a>删除菜单和项

如果需要从应用程序的用户界面删除任何菜单项或菜单项，可以使用 `RemoveItemAt` 类的方法， `NSMenu` 只需为其提供要删除的项的从零开始的索引。

例如，若要删除以上例程创建的菜单和菜单项，可以使用以下代码：

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

对于上述代码，会在应用中 Xcode 的 Interface Builder 和 aways 中创建前四个菜单项，因此不会动态删除这些项。

<a name="Contextual_Menus"></a>

## <a name="contextual-menus"></a>上下文菜单

当用户在窗口中右键单击或单击控件时，将显示上下文菜单。 默认情况下，macOS 中内置的几个 UI 元素已经附加了上下文菜单 (如) 文本视图。 但有时，我们可能要为已添加到窗口的 UI 元素创建自己的自定义上下文菜单。

让我们在 Xcode 中编辑我们的**主情节提要**文件，并向设计添加**窗口**窗口，将其**类**设置为 "NSPanel"，在 "**身份检查器**" 中，在 "**窗口**" 菜单中添加一个新的 "**助手**" 项，然后使用 "**显示 Segue**" 将其附加到新窗口：

[![设置 segue 类型](menu-images/context01.png "设置 segue 类型")](menu-images/context01-large.png#lightbox)

让我们执行以下操作：

1. 从**库检查器**将**标签**拖到**面板**窗口，并将其文本设置为 "属性"： 

    [![编辑标签的值](menu-images/context03.png "编辑标签的值")](menu-images/context03-large.png#lightbox)
2. 接下来，将 **菜单** 从 **库检查器** 拖到视图层次结构中的视图控制器上，并重命名三个默认菜单项 **文档**、 **文本** 和 **字体**：

    [![必需的菜单项](menu-images/context02.png "必需的菜单项")](menu-images/context02-large.png#lightbox)
3. 现在，控件从 **属性标签** 拖到 **菜单**上：

    [![拖动以创建 segue](menu-images/context04.png "拖动以创建 segue")](menu-images/context04-large.png#lightbox)
4. 在弹出对话框中，选择 " **菜单**： 

    ![设置 segue 类型](menu-images/context05.png "设置 segue 类型")
5. 在 **标识检查器**中，将视图控制器的类设置为 "PanelViewController"： 

    [![设置 segue 类](menu-images/context10.png "设置 segue 类")](menu-images/context10-large.png#lightbox)
6. 切换回 Visual Studio for Mac 进行同步，然后返回到 Interface Builder。
7. 切换到 " **助手编辑器** "，然后选择 " **PanelViewController** " 文件。
8. 为名为的 **文档** 菜单项创建操作 `propertyDocument` ： 

    [![配置该操作](menu-images/context06.png "配置该操作")](menu-images/context06-large.png#lightbox)
9. 为其余菜单项重复创建操作： 

    [![必需的操作](menu-images/context07.png "必需的操作")](menu-images/context07-large.png#lightbox)
10. 最后，为名为的 **属性标签** 创建一个插座 `propertyLabel` ： 

    [![配置插座](menu-images/context08.png "配置插座")](menu-images/context08-large.png#lightbox)
11. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

编辑 **PanelViewController.cs** 文件并添加以下代码：

```csharp
partial void propertyDocument (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Document";
}

partial void propertyFont (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Font";
}

partial void propertyText (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Text";
}
```

现在，如果我们运行应用程序，然后在面板中右键单击属性标签，则会看到自定义上下文菜单。 如果从菜单中选择和项，则标签的值将更改：

![运行的上下文菜单](menu-images/context09.png "运行的上下文菜单")

接下来，让我们看看如何创建状态栏菜单。

## <a name="status-bar-menus"></a>状态栏菜单

状态栏菜单显示状态菜单项的集合，这些项提供与用户的交互或对用户的反馈，如用于反映应用程序状态的菜单或图像。 即使应用程序在后台运行，应用程序的状态栏菜单也处于启用状态和活动状态。 系统范围的状态栏位于应用程序菜单栏的右侧，并且是当前在 macOS 中可用的唯一状态栏。

让我们编辑 **AppDelegate.cs** 文件，并使该 `DidFinishLaunching` 方法如下所示：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Create a status bar menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Text";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Text");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        PhraseAddress(address);
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        PhraseDate(date);
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        PhraseGreeting(greeting);
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        PhraseSignature(signature);
    };
    item.Menu.AddItem (signature);
}
```

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` 向我们提供对系统范围状态栏的访问权限。 `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` 创建新的状态栏项。 在这里，我们创建一个菜单和多个菜单项，并将菜单附加到刚刚创建的状态栏项。 

如果运行该应用程序，将显示新的状态栏项目。 从菜单中选择一项将更改文本视图中的文本： 

![状态栏菜单正在运行](menu-images/statusbar01.png "状态栏菜单正在运行")

接下来，让我们看看如何创建自定义停靠菜单项。

## <a name="custom-dock-menus"></a>自定义停靠菜单

当用户右键单击或在停靠中单击应用程序的图标时，将为您的 Mac 应用程序显示 dock 菜单：

![自定义停靠菜单](menu-images/dock01.png "自定义停靠菜单")

接下来，让我们为应用程序创建自定义停靠菜单：

1. 在 Visual Studio for Mac 中，右键单击应用程序的项目，然后选择 "**添加**" "  >  **新文件 ...** "在 "新建文件" 对话框中，选择 " **Xamarin**  >  "**空接口定义**，使用 "DockMenu" 作为**名称**，然后单击 "**新建**" 按钮创建新的**xib**文件：

    ![添加空界面定义](menu-images/dock02.png "添加空界面定义")
2. 在 **Solution Pad**中，双击 **DockMenu xib** 文件以将其打开，以便在 Xcode 中进行编辑。 创建具有以下项的新 **菜单** ： **地址**、 **日期**、 **问候**和 **签名** 

    [![设置 UI 布局](menu-images/dock03.png "设置 UI 布局")](menu-images/dock03-large.png#lightbox)
3. 接下来，让我们将新的菜单项连接到在上面的 " [添加、编辑和删除菜单](#Adding,_Editing_and_Deleting_Menus) " 部分为自定义菜单创建的现有操作。 切换到**连接检查器**，并选择**接口层次结构**中的**第一个响应方**。 向下滚动并找到 `phraseAddress:` 操作。 将该操作上的圆圈中的线条拖到 **Address** 菜单项：

    [![拖放操作](menu-images/dock04.png "拖放操作")](menu-images/dock04-large.png#lightbox)
4. 对附加到相应操作的所有其他菜单项重复操作： 

    [![必需的操作](menu-images/dock05.png "必需的操作")](menu-images/dock05-large.png#lightbox)
5. 接下来，在**接口层次结构**中选择**应用程序**。 在 " **连接检查器**" 中，将线条从插座上的圆圈拖 `dockMenu` 到刚刚创建的菜单：

    [![将网络向上拖动到插座上](menu-images/dock06.png "将网络向上拖动到插座上")](menu-images/dock06-large.png#lightbox)
6. 保存更改并切换回 Visual Studio for Mac 以便与 Xcode 同步。
7. 双击 **info.plist** 文件以将其打开进行编辑： 

    [![编辑 info.plist 文件](menu-images/dock07.png "编辑 info.plist 文件")](menu-images/dock07-large.png#lightbox)
8. 单击屏幕底部的 " **源** " 选项卡： 

    [![选择源视图](menu-images/dock08.png "选择源视图")](menu-images/dock08-large.png#lightbox)
9. 单击 " **添加新项**"，单击绿色加号按钮，将属性名称设置为 "AppleDockMenu"，将值设置为 "DockMenu" (不带扩展名) 的新 xib 文件的名称： 

    [![添加 DockMenu 项](menu-images/dock09.png "添加 DockMenu 项")](menu-images/dock09-large.png#lightbox)

现在，如果我们运行应用程序，并右键单击其在停靠中的图标，将显示新的菜单项：

![运行的停靠菜单示例](menu-images/dock10.png "运行的停靠菜单示例")

如果从菜单中选择一个自定义项，则会修改文本视图中的文本。

<a name="Pop-up_Menus_and_Pull-Down_Lists"></a>

## <a name="pop-up-button-and-pull-down-lists"></a>弹出式按钮和下拉列表

弹出式按钮显示选定项，并显示用户单击时要从中选择的选项列表。 下拉列表是一种弹出式按钮，通常用于选择特定于当前任务上下文的命令。 两者都可以出现在窗口中的任意位置。

接下来，让我们为应用程序创建一个自定义弹出按钮：

1. 编辑 Xcode 中的**主情节提要**文件，并将**弹出按钮**从**库检查器**拖到我们在[上下文菜单](#Contextual_Menus)部分中创建的**面板**窗口： 

    [![添加弹出按钮](menu-images/popup01.png "添加弹出按钮")](menu-images/popup01-large.png#lightbox)
2. 添加新的菜单项，并将 Popup 中项的标题设置为： **Address**、 **Date**、 **问候语**和 **签名** 

    [![配置菜单项](menu-images/popup02.png "配置菜单项")](menu-images/popup02-large.png#lightbox)
3. 接下来，让我们将新的菜单项连接到在上面的 [添加、编辑和删除菜单](#Adding,_Editing_and_Deleting_Menus) 部分中为自定义菜单创建的现有操作。 切换到**连接检查器**，并选择**接口层次结构**中的**第一个响应方**。 向下滚动并找到 `phraseAddress:` 操作。 将该操作上的圆圈中的线条拖到 **Address** 菜单项： 

    [![拖放操作](menu-images/popup03.png "拖放操作")](menu-images/popup03-large.png#lightbox)
4. 对附加到相应操作的所有其他菜单项重复操作： 

    [![所有必需的操作](menu-images/popup04.png "所有必需的操作")](menu-images/popup04-large.png#lightbox)
5. 保存更改并切换回 Visual Studio for Mac 以便与 Xcode 同步。

现在，如果我们运行应用程序，并从弹出菜单中选择一个项，文本视图中的文本将更改：

![正在运行的弹出窗口的示例](menu-images/popup05.png "正在运行的弹出窗口的示例")

您可以使用与弹出式按钮完全相同的方式来创建和使用下拉列表。 你可以创建自己的自定义操作，就像我们在 [上下文](#Contextual_Menus) 菜单部分中的上下文菜单中那样，可以创建自己的自定义操作。

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin. Mac 应用程序中使用菜单和菜单项。 首先，我们检查过应用程序的菜单栏，然后查看创建上下文菜单，接下来我们检查状态栏菜单和自定义停靠菜单。 最后，我们介绍了弹出式菜单和下拉列表。

## <a name="related-links"></a>相关链接

- [MacMenus (示例) ](/samples/xamarin/mac-samples/macmenus)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [人体学接口准则-菜单](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [应用程序菜单和弹出列表简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)