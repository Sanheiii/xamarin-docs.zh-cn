---
title: 在 Xamarin 中使用 tvOS 拆分视图控制器
description: 本文档介绍如何在使用 Xamarin 生成的应用程序中使用 tvOS 拆分视图。 它提供拆分视图控制器的高级概述、如何将它们与情节提要一起使用、如何访问大纲视图和详细视图以及显示和隐藏母版视图。
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 9cd19cfccac03ea1edb59c0422f197e33873abc6
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436892"
---
# <a name="working-with-tvos-split-view-controllers-in-xamarin"></a>在 Xamarin 中使用 tvOS 拆分视图控制器

拆分视图控制器在屏幕上并行呈现和管理主视图控制器和详细视图控制器。 拆分视图控制器用于在大纲视图中提供持久的可获得焦点的内容， (详细信息视图中左侧) 和相关详细信息的较小部分 (右侧) 中较大的部分。

[![示例拆分视图](split-views-images/intro01.png)](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers"></a>

## <a name="about-split-view-controllers"></a>关于拆分视图控制器

如上所述，拆分视图控制器管理并列显示的主视图控制器和详细视图控制器，主节点是左侧较小的视图，右侧的详细信息。 

此外，还可以根据需要隐藏或显示母版视图控制器： 

[![隐藏的母版视图控制器](split-views-images/intro02.png)](split-views-images/intro02.png#lightbox)

拆分视图控制器通常用于显示可筛选内容的列表，其中的类别包含在主视图中，而筛选后的结果在详细信息视图中。 这通常显示为左侧的表视图，以及右侧的 [集合视图](~/ios/tvos/user-interface/collection-views.md) 。

设计需要拆分视图控制器的用户界面时，Apple 建议使用不更改 (仅更改内容的主和详细视图控制器，而不会更改结构) 。 如果确实需要交换视图控制器，最好使用导航控制器作为视图控制器的基础，该控制器需要 (Master 或 Detail) 更改。

Apple 对于使用拆分视图控制器有以下建议：

- **使用正确的拆分百分比** -默认情况下，拆分视图控制器使用全屏视图控制器的第三个屏幕，并为详细信息视图控制器使用三分之二。 您也可以使用50/50 分割。 选择正确的百分比，使内容在屏幕上显示均衡。
- **保持主选择** -当 "详细信息视图" 中的内容可以更改时，它将响应用户在母版视图中的选择，应修复母版视图内容。 此外，您应该清楚地在母版视图中显示当前选定的项。
- **使用单个统一标题** -通常情况下，你需要在详细信息视图中使用单个居中的标题，而不是在详细信息视图和母版视图中使用标题。

<a name="Split-View-Controllers-and-Storyboards"></a>

## <a name="split-view-controllers-and-storyboards"></a>拆分视图控制器和情节提要

若要在 tvOS 应用中使用拆分视图控制器，最简单的方法是使用 iOS 设计器将它们添加到应用的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 **Solution Pad**中，双击该文件， `Main.storyboard` 然后将其打开进行编辑。
1. 从 "工具箱" 中将 **"** **拆分视图" 控制器**拖放到视图上： 

    [![拆分视图控制器](split-views-images/activity01.png)](split-views-images/activity01.png#lightbox)
1. 默认情况下，iOS 设计器将在主视图中安装一个导航控制器和一个视图控制器。 如果这不能满足应用程序的要求，只需将其删除即可。
1. 如果删除了默认的母版视图，请将新的视图控制器拖动到设计图面上： 

    [![视图控制器](split-views-images/activity02.png)](split-views-images/activity02.png#lightbox)
1. 按住 ctrl 并从拆分视图控制器拖动到新的主视图控制器。 
1. 从**弹出菜单**中选择 " **Master** "： 

    [![从弹出菜单中选择 "主"](split-views-images/activity03.png)](split-views-images/activity03.png#lightbox)
1. 设计大纲和详细信息视图的内容： 

    [![示例布局](split-views-images/activity04.png)](split-views-images/activity04.png#lightbox)
1. 在**Properties Pad**的 "**小组件" 选项卡**中指定**名称**，以使用 c # 代码中的 UI 控件。
1. 保存更改并返回到 Visual Studio for Mac。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 **解决方案资源管理器**中，双击该文件， `Main.storyboard` 然后将其打开进行编辑。
1. 从 "工具箱" 中将 **"** **拆分视图" 控制器**拖放到视图上： 

    [![拆分视图控制器](split-views-images/activity01-vs.png)](split-views-images/activity01-vs.png#lightbox)
1. 默认情况下，iOS 设计器将在主视图中添加导航控制器和视图控制器。 如果这不能满足应用程序的要求，只需将其删除即可。
1. 如果删除了默认的母版视图，请将新的视图控制器拖动到设计图面上： 

    [![视图控制器](split-views-images/activity02-vs.png)](split-views-images/activity02-vs.png#lightbox)
1. 按住 ctrl 并从拆分视图控制器拖动到新的主视图控制器。 
1. 从**弹出菜单**中选择 " **Master** "： 

    [![从弹出菜单中选择 "主"](split-views-images/activity03-vs.png)](split-views-images/activity03-vs.png#lightbox)
1. 设计大纲和详细信息视图的内容： 

    [![内容布局](split-views-images/activity04.png)](split-views-images/activity04.png#lightbox)
1. 在 "**属性资源管理器**" 的 "**小组件" 选项卡**中分配**名称**，以使用 c # 代码中的 UI 控件。
1. 保存所做更改。

-----

有关使用情节提要的详细信息，请参阅 [tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Working-with-Split-View-Controllers"></a>

## <a name="working-with-split-view-controllers"></a>使用拆分视图控制器

如上所述，拆分视图控制器通常用于向用户显示已筛选内容的情况。 主类别显示在母版视图的左侧，并根据用户的选择在详细信息视图中显示筛选结果。

<a name="Accessing-Master-and-Detail"></a>

### <a name="accessing-master-and-detail"></a>访问 Master 和 Detail

如果需要以编程方式访问主视图控制器和详细视图控制器，请使用 `ViewControllers` 拆分视图控制器的属性。 例如：

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

它以数组形式显示，其中第一个元素 (0) 在主视图控制器中，第二个元素 (1) 为详细信息。

<a name="Accessing-Detail-from-Master"></a>

### <a name="accessing-detail-from-master"></a>从 Master 访问详细信息

由于你通常会在详细信息视图中基于用户在主节点中所做的选择来显示详细信息，因此你需要通过某种方式从 Master 访问详细信息。

要执行此操作，最简单的方法是在主视图控制器类上公开一个属性，例如：

```csharp
public DetailViewController DetailController { get; set;}
```

在拆分视图控制器中，重写 `ViewDidLoad` 方法，并将两个视图绑定在一起。 例如：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Gain access to master and detail view controllers
    var masterController = ViewControllers [0] as MasterViewController;
    var detailController = ViewControllers [1] as DetailViewController;

    // Wire-up views
    masterController.SplitViewController = this;
    masterController.DetailController = detailController;
    detailController.SplitViewController = this;
}
```

你可以公开详细信息视图控制器上的属性和方法，Master 可以使用该控制器根据需要提供新数据。

<a name="Showing-and-Hiding-Master"></a>

### <a name="showing-and-hiding-master"></a>显示和隐藏 Master

或者，您可以使用拆分视图控制器的属性来显示和隐藏母版视图控制器 `PreferredDisplayMode` 。 例如：

```csharp
// Show hide split view
if (SplitViewController.DisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden) {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible;
} else {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.PrimaryHidden;
}
```

`UISplitViewControllerDisplayMode`枚举定义如何将主视图控制器呈现为以下内容之一：

- **自动** -tvOS 将控制母版和详细信息视图的表示形式。
- **PrimaryHidden** -隐藏母版视图控制器。
- **AllVisible** -并排显示主节点和详细信息视图控制器。 这是正常的默认演示。
- **PrimaryOverlay** -详细信息视图控制器在下扩展，并由主节点覆盖。

若要获取当前显示状态，请使用 `DisplayMode` 拆分视图控制器的属性。

<a name="Summary"></a>

## <a name="summary"></a>总结

本文介绍了如何在 tvOS 应用内设计和使用拆分视图控制器。

## <a name="related-links"></a>相关链接

- [tvOS 示例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人体学接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)