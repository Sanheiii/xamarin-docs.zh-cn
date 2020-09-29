---
title: Xamarin 中的工具栏
description: 本文介绍如何在 Xamarin. Mac 应用程序中使用工具栏。 它介绍了如何在 Xcode 和 Interface Builder 中创建和维护工具栏，如何将它们公开给代码，以及如何以编程方式使用它们。
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 5a6c7a013a72d2ad0e80e305b8c4c550011a1737
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430705"
---
# <a name="toolbars-in-xamarinmac"></a>Xamarin 中的工具栏

_本文介绍如何在 Xamarin. Mac 应用程序中使用工具栏。它介绍了如何在 Xcode 和 Interface Builder 中创建和维护工具栏，如何将它们公开给代码，以及如何以编程方式使用它们。_

使用 Visual Studio for Mac 的 Xamarin 开发人员可以访问对使用 Xcode 的 macOS 开发人员（包括 toolbar 控件）可用的相同 UI 控件。 由于 Xamarin 与 Xcode 直接集成，因此可以使用 Xcode 的 Interface Builder 来创建和维护工具栏项。 还可在 c # 中创建这些工具栏项。

MacOS 中的工具栏将添加到窗口的顶部，并提供对与功能相关的命令的简单访问。 工具栏可以隐藏、显示或由应用程序的用户自定义，并且可以通过各种方式显示工具栏项。

本文介绍了如何在 Xamarin. Mac 应用程序中使用工具栏和工具栏项。 

继续之前，请通读 [Hello，Mac](~/mac/get-started/hello-mac.md) 一文，特别是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 及 [输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions) 部分的简介，因为它介绍了将在本指南中使用的主要概念和技术。

另请参阅 " [Xamarin 内部](~/mac/internals/how-it-works.md)版" 文档中的 "[向目标位置公开 c # 类/方法](~/mac/internals/how-it-works.md)" 部分。 它说明了 `Register` `Export` 用于将 c # 类连接到目标 C 类的和特性。

## <a name="introduction-to-toolbars"></a>工具栏简介

MacOS 应用程序中的任何窗口都可以包含工具栏：

![带有工具栏的示例窗口](toolbar-images/info01.png "带有工具栏的示例窗口")

使用工具栏，您的应用程序的用户可以快速访问重要或常用的功能。 例如，文档编辑应用程序可能会提供用于设置文本颜色、更改字体或打印当前文档的工具栏项。

工具栏可以通过以下三种方式显示项：

1. **图标和文本** 

     ![带有图标和文本的工具栏](toolbar-images/info02.png "带有图标和文本的工具栏")

2. **仅图标** 

     ![仅图标工具栏](toolbar-images/info03.png "仅图标工具栏")

3. **仅文本** 

     ![纯文本工具栏](toolbar-images/info04.png "纯文本工具栏")

右键单击工具栏，然后从上下文菜单中选择 "显示模式"，在这两种模式之间切换：

![工具栏的上下文菜单](toolbar-images/info05.png "工具栏的上下文菜单")

使用同一个菜单以更小的大小显示工具栏：

![带有小图标的工具栏](toolbar-images/info06.png "带有小图标的工具栏")

菜单还允许自定义工具栏：

![用于自定义工具栏的对话框](toolbar-images/info07.png "用于自定义工具栏的对话框")

在 Xcode 的 Interface Builder 中设置工具栏时，开发人员可以提供不属于其默认配置的其他工具栏项。 然后，应用程序的用户可以自定义工具栏，根据需要添加和删除这些预定义项。 当然，工具栏可以重置为其默认配置。

工具栏会自动连接到 " **视图** " 菜单，该菜单允许用户隐藏、显示和自定义该菜单：

!["视图" 菜单中与工具栏相关的项](toolbar-images/info08.png ""视图" 菜单中与工具栏相关的项")

有关更多详细信息，请参阅 [内置菜单功能](~/mac/user-interface/menu.md) 文档。

此外，如果在 Interface Builder 中正确配置了工具栏，应用程序将自动在应用程序的多个启动范围内保持工具栏自定义。

本指南的后续部分介绍如何使用 Xcode 的 Interface Builder 创建和维护工具栏，以及如何在代码中使用它们。

## <a name="setting-a-custom-main-window-controller"></a>设置自定义主窗口控制器

若要通过插座和操作向 c # 代码公开 UI 元素，Xamarin 应用程序必须使用自定义窗口控制器：

1. 在 Xcode 的 Interface Builder 中打开应用的情节提要。
2. 在设计图面上选择窗口控制器。
3. 切换到 **标识检查器** ，并输入 "WindowController" 作为 **类名**： 

    [![设置窗口控制器的自定义类名称](toolbar-images/windowcontroller01.png "设置窗口控制器的自定义类名称")](toolbar-images/windowcontroller01-large.png#lightbox) 

4. 保存更改并返回到要同步的 Visual Studio for Mac。
5. **WindowController.cs**文件将添加到 Visual Studio for Mac 中的**Solution Pad**中的项目： 

    ![在 Solution Pad 中选择 "WindowController.cs"](toolbar-images/windowcontroller02.png "在 Solution Pad 中选择 "WindowController.cs"")

6. 重新打开 Xcode 的 Interface Builder 中的情节提要。
7. **WindowController**文件将可供使用： 

    [![WindowController 文件](toolbar-images/windowcontroller03.png "WindowController 文件")](toolbar-images/windowcontroller03-large.png#lightbox)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>在 Xcode 中创建和维护工具栏

工具栏是通过 Xcode 的 Interface Builder 创建和维护的。 若要向应用程序添加工具栏，请在此示例中编辑应用程序的主 **情节提要 (** 在此示例中，请在 **Solution Pad**中双击它) ：

![在 Solution Pad 中打开](toolbar-images/edit01.png "在 Solution Pad 中打开")

在 **库检查器**的 " **搜索" 框** 中输入 "工具"，使其更易于查看所有可用的工具栏项：

![进行筛选以显示工具栏项的库检查器](toolbar-images/edit02.png "进行筛选以显示工具栏项的库检查器")

将工具栏拖动到 " **界面编辑器**" 中的窗口。 选择工具栏后，通过在 " **属性" 检查器**中设置属性来配置其行为：

![工具栏的特性检查器](toolbar-images/edit04.png "工具栏的特性检查器")

以下属性可用：

1. **显示** -控制工具栏是显示图标、文本还是同时显示二者
2. **启动时可见** -如果选择了此选项，则默认情况下该工具栏可见。
3. **自定义** -如果选择了此选项，用户可以编辑和自定义工具栏。
4. **分隔符** -如果选中，则将工具栏从窗口的内容中分离出来。
5. **大小** -设置工具栏的大小
6. **自动保存** -如果选择此选项，应用程序将在应用程序启动时保持用户的工具栏配置更改。

选择 " **自动保存** " 选项，将所有其他属性保留为其默认设置。 

打开 **接口层次结构**中的工具栏后，通过选择工具栏项打开 "自定义" 对话框：

![自定义工具栏](toolbar-images/edit05.png "自定义工具栏")

使用此对话框可以设置已属于工具栏的项的属性、设计应用程序的默认工具栏以及提供额外的工具栏项以便用户在自定义工具栏时选择。 若要将项添加到工具栏，请从 **库检查器**中拖动它们：

![库检查器](toolbar-images/edit06.png "库检查器")

可以添加以下工具栏项：

- **图像工具栏项** -带有自定义图像的工具栏项作为图标。
- **灵活空间工具栏项** -用于对齐后续工具栏项的灵活空间。 例如，一个或多个 toolbar 项后跟一个灵活的空间工具栏项，另一个工具栏项会将最后一项固定到工具栏的右侧。
- **空间工具栏项** -工具栏上各项之间的固定空间
- **分隔符工具栏项** -两个或多个工具栏项之间的可见分隔符，用于分组
- **自定义工具栏项** -允许用户自定义工具栏
- **打印工具栏项** -允许用户打印打开的文档
- **显示颜色工具栏项** -显示标准系统颜色选取器： 

     ![系统颜色选取器](toolbar-images/edit07.png "系统颜色选取器")

- **显示字体工具栏项** -显示标准系统字体对话框： 

     ![字体选择器](toolbar-images/edit08.png "字体选择器")

> [!IMPORTANT]
> 正如稍后会发现，许多标准 Cocoa UI 控件（如搜索字段、分段控件和水平滑块）也可以添加到工具栏中。

### <a name="adding-an-item-to-a-toolbar"></a>向工具栏添加项

若要将某项添加到工具栏，请选择 " **接口" 层次结构** 中的工具栏，然后单击其中一个项，这将显示 "自定义" 对话框。 接下来，将新项从 **库检查器** 拖到 " **允许的工具栏项** " 区域：

![工具栏自定义对话框中允许的工具栏项](toolbar-images/add01.png "工具栏自定义对话框中允许的工具栏项")

若要确保新项是默认工具栏的一部分，请将其拖到 **默认工具栏项** 区域： 

![通过拖动对工具栏项进行重新排序](toolbar-images/add02.png "通过拖动对工具栏项进行重新排序")

若要重新排列默认的工具栏项，请将其向左或向右拖动。

接下来，使用 " **属性检查器** " 设置项的默认属性：

![使用特性检查器自定义工具栏项](toolbar-images/add03.png "使用特性检查器自定义工具栏项")

以下属性可用：

- **图像名称** -用作项的图标的图像
- **标签** -要在工具栏中为该项显示的文本
- **调色板标签** -为 " **允许的工具栏项** " 区域中的项显示的文本
- **标记** -可选的唯一标识符，有助于标识代码中的项。
- **标识符** -定义工具栏项类型。 自定义值可用于在代码中选择工具栏项。
- 可**选择**-如果已选中，则项的作用类似于 "开/关" 按钮。

> [!IMPORTANT]
> 将项目添加到 " **允许的工具栏项** " 区域，而不是 "默认" 工具栏，为用户提供自定义选项。 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>向工具栏添加其他 UI 控件

还可以将多个 Cocoa UI 元素（如搜索字段和分段控件）添加到工具栏中。

若要尝试此功能，请在 **接口层次结构** 中打开工具栏，然后选择工具栏项以打开 "自定义" 对话框。 将 " **搜索" 字段** 从 **库检查器** 拖到 " **允许的工具栏项** " 区域：

![使用工具栏自定义对话框](toolbar-images/add05.png "使用工具栏自定义对话框")

从这里，使用 Interface Builder 配置搜索字段，并通过操作或插座将其公开给代码。

## <a name="built-in-toolbar-item-support"></a>内置工具栏项支持

默认情况下，多个 Cocoa UI 元素与标准工具栏项交互。 例如，将一个 **文本视图** 拖动到应用程序的窗口上，并将其定位到填充内容区域：

[![向应用程序添加文本视图](toolbar-images/edit09.png "向应用程序添加文本视图")](toolbar-images/edit09-large.png#lightbox)

保存文档，返回到 Visual Studio for Mac 以与 Xcode 同步，运行应用程序，输入一些文本，选择它，然后单击 " **颜色** " 工具栏项。 请注意，文本视图自动使用颜色选取器：

![带有文本视图和颜色选取器的内置工具栏功能](toolbar-images/edit10.png "带有文本视图和颜色选取器的内置工具栏功能")

## <a name="using-images-with-toolbar-items"></a>使用带有工具栏项的图像

使用 **图像工具栏项**，添加到 **Resources** 文件夹的任何位图图像 (并给定 **包资源**) 的生成操作，可以在工具栏上显示为图标：

1. 在 Visual Studio for Mac 的 " **Solution Pad**中，右键单击" **Resources** "文件夹，然后选择"**添加**  >  **文件**"。
2. 在 " **添加文件** " 对话框中，导航到所需的映像，将其选中，然后单击 " **打开** " 按钮： 

    [![选择要添加的映像](toolbar-images/edit11.png "选择要添加的映像")](toolbar-images/edit11-large.png#lightbox)

3. 选择 " **复制**"，选中 " **对所有选定的文件使用相同的操作**"，然后单击 **"确定"**：

    ![为已添加的映像选择复制操作](toolbar-images/edit12.png "为已添加的映像选择复制操作")

4. 在 **Solution Pad**中，双击 **Xib** 以在 Xcode 中打开它。

5. 在 **接口层次结构** 中选择工具栏，并单击其中一个项以打开 "自定义" 对话框。

6. 将 " **图像" 工具栏项** 从 **库检查器** 拖到工具栏的 " **允许的工具栏项** " 区域： 

    ![添加到 "允许的工具栏项" 区域的图像工具栏项](toolbar-images/edit14.png "添加到 "允许的工具栏项" 区域的图像工具栏项")

7. 在 " **属性检查器**" 中，选择刚添加到 Visual Studio for Mac 中的映像： 

    ![为工具栏项设置自定义图像](toolbar-images/edit15.png "为工具栏项设置自定义图像")

8. 将 **标签** 设置为 "垃圾桶"，将 **调色板标签** 设置为 "擦除文档"： 

    ![设置工具栏项标签和调色板标签](toolbar-images/edit16.png "设置工具栏项标签和调色板标签")

9. 将 " **分隔符工具栏" 项** 从 **库检查器** 拖到工具栏的 " **允许的工具栏项** " 区域： 

    [![添加到 "允许的工具栏项" 区域的分隔符工具栏项](toolbar-images/edit17.png "添加到 "允许的工具栏项" 区域的分隔符工具栏项")](toolbar-images/edit17-large.png#lightbox)

10. 将分隔符项和 "垃圾桶" 项拖到 **默认工具栏项** 区域，并按以下方式设置工具栏项的顺序： (颜色、字体、分隔符、废纸篓、灵活空间、打印) ： 

    ![默认工具栏项](toolbar-images/edit18.png "默认工具栏项")

11. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

运行应用程序以验证默认情况下是否显示新工具栏：

![带有自定义默认项的工具栏](toolbar-images/edit19.png "带有自定义默认项的工具栏")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>通过插座和操作公开工具栏项

若要在代码中访问工具栏或工具栏项，则必须将其附加到插座或操作：

1. 在 **Solution Pad**中， **双击 "Xcode" 将其** 打开。
2. 确保已将自定义类 "WindowController" 分配给 **标识检查器**中的主窗口控制器：

    [![使用标识检查器设置窗口控制器的自定义类](toolbar-images/edit20a.png "使用标识检查器设置窗口控制器的自定义类")](toolbar-images/edit20a-large.png#lightbox)

3. 接下来，选择 **接口层次结构**中的工具栏项： 

    ![在接口层次结构中选择工具栏项](toolbar-images/edit20.png "在接口层次结构中选择工具栏项")  

4. 打开 " **助手" 视图**，选择 " **WindowController** " 文件，然后从 "工具栏" 项拖到 " **WindowController** " 文件。
5. 将 " **连接** 类型" 设置为 " **操作**"，为 " **名称**" 输入 "TrashDocument"，并单击 " **连接** " 按钮： 

    [![为工具栏项配置操作](toolbar-images/edit23.png "为工具栏项配置操作")](toolbar-images/edit23-large.png#lightbox)

6. 在**ViewController**文件中将**文本视图**公开为名为 "documentEditor" 的插座： 

    [![为文本视图配置插座](toolbar-images/edit24.png "为文本视图配置插座")](toolbar-images/edit24-large.png#lightbox)

7. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

在 Visual Studio for Mac 中，编辑 **ViewController.cs** 文件并添加以下代码：

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

接下来，编辑 **WindowController.cs** 文件，并将以下代码添加到类的底部 `WindowController` ：

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

运行应用程序时， **垃圾桶** 工具栏项将处于活动状态：

![具有活动垃圾桶项的工具栏](toolbar-images/edit25.png "具有活动垃圾桶项的工具栏")

请注意， **垃圾桶** 工具栏项现在可以用于删除文本。

## <a name="disabling-toolbar-items"></a>禁用工具栏项

若要禁用工具栏上的项，请创建一个自定义 `NSToolbarItem` 类，并重写 `Validate` 方法。 然后，在 Interface Builder 中，将自定义类型分配给要启用/禁用的项。

若要创建自定义 `NSToolbarItem` 类，请右键单击该项目，然后选择 "**添加**  >  **新文件 ...**"。选择 "**常规**  >  **空类**"，输入 "ActivatableItem" 作为**名称**，然后单击 "**新建**" 按钮： 

![在 Visual Studio for Mac 中添加空类](toolbar-images/custom01.png "在 Visual Studio for Mac 中添加空类")

接下来，编辑 **ActivatableItem.cs** 文件，如下所示：

```csharp
using System;

using Foundation;
using AppKit;

namespace MacToolbar
{
    [Register("ActivatableItem")]
    public class ActivatableItem : NSToolbarItem
    {
        public bool Active { get; set;} = true;

        public ActivatableItem ()
        {
        }

        public ActivatableItem (IntPtr handle) : base (handle)
        {
        }

        public ActivatableItem (NSObjectFlag  t) : base (t)
        {
        }

        public ActivatableItem (string title) : base (title)
        {
        }

        public override void Validate ()
        {
            base.Validate ();
            Enabled = Active;
        }
    }
}
```

双击 **"Xcode" 将其** 打开。 选择上面创建的 **垃圾桶** 工具栏项，并将其类更改为 " **标识检查器**" 中的 "ActivatableItem"：

![为工具栏项设置自定义类](toolbar-images/custom02.png "为工具栏项设置自定义类")

`trashItem`为**垃圾桶**工具栏项创建一个名为的插座。 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。 最后，打开 **MainWindow.cs** 并将 `AwakeFromNib` 方法更新为，如下所示：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

运行应用程序，并注意在工具栏中现在已禁用 **垃圾桶** ：

![包含不活动垃圾箱项的工具栏](toolbar-images/custom03.png "包含不活动垃圾箱项的工具栏")

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin. Mac 应用程序中使用工具栏和工具栏项。 它介绍了如何在 Xcode 的 Interface Builder 中创建和维护工具栏，某些 UI 控件如何自动使用工具栏项、如何使用 c # 代码中的工具栏以及如何启用和禁用工具栏项。

## <a name="related-links"></a>相关链接

- [MacToolbar (示例) ](/samples/xamarin/mac-samples/mactoolbar)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [用于工具栏的用户界面指南](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [工具栏简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)