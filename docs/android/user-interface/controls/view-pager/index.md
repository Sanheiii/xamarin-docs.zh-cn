---
title: ViewPager
description: ViewPager 是一个布局管理器，可用于实现 gestural 导航。 Gestural 导航允许用户向左和向右轻扫以逐步浏览数据页。 本指南说明如何通过 ViewPager 实现 gestural 导航，其中和不包含片段。 还介绍了如何使用 PagerTitleStrip 和 PagerTabStrip 添加页指示器。
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 427ace2043f966b617a258b5f50fa42f943e707e
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457648"
---
# <a name="viewpager"></a>ViewPager

_ViewPager 是一个布局管理器，可用于实现 gestural 导航。Gestural 导航允许用户向左和向右轻扫以逐步浏览数据页。本指南说明如何通过 ViewPager 实现 gestural 导航，其中和不包含片段。还介绍了如何使用 PagerTitleStrip 和 PagerTabStrip 添加页指示器。_

## <a name="overview"></a>概述

应用开发中的一种常见情况是，需要在同级视图之间向用户提供 gestural 导航。 在此方法中，用户可以 swipes 访问 (的内容页，例如，在安装向导或幻灯片放映) 。 可以通过使用 `ViewPager` [Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)中提供的小组件来创建这些轻扫视图。 `ViewPager`是由多个子视图组成的布局小组件，其中的每个子视图都在布局中构成页面： 

[![带有横向刷卡器的 TreePager 应用程序的屏幕截图示例](images/01-intro-sml.png)](images/01-intro.png#lightbox)

通常， `ViewPager` 与 [片段](~/android/platform/fragments/index.md)结合使用; 但是，在某些情况下，你可能希望在 `ViewPager` 不增加的复杂性的情况下使用 `Fragment` 。

`ViewPager` 使用适配器模式向其提供要显示的视图。 此处使用的适配器在概念上类似于 [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)使用的适配器，用于 &ndash; 生成向 `PagerAdapter` `ViewPager` 用户显示的页面。 显示的页 `ViewPager` 可以是 `View` 或 `Fragment` 。 `View`显示时，适配器会将 Android 的 `PagerAdapter` 基类作为子类。 如果 `Fragment` 显示了，则适配器会将 Android 作为子类 `FragmentPagerAdapter` 。 Android 支持库还包括 `FragmentPagerAdapter` (`PagerAdapter`) 子类，以帮助详细了解 `Fragment` 如何将与数据连接。 

本指南演示了这两种方法： 

- 在 [Viewpager 的视图](~/android/user-interface/controls/view-pager/viewpager-and-views.md)中，开发了 [TreePager](/samples/xamarin/monodroid-samples/userinterface-treepager) 应用，以演示如何使用 `ViewPager` 来显示树目录的视图 (落叶和长绿树) 的图像库。 
    `PagerTabStrip`  和 `PagerTitleStrip` 用于显示可帮助进行页面导航的标题。

- 在 [包含片段的 Viewpager](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md)中，开发稍微复杂的 [FlashCardPager](/samples/xamarin/monodroid-samples/userinterface-flashcardpager) 应用程序，以演示如何使用来 `ViewPager` `Fragment` 构建一个应用程序，该应用程序将数学问题呈现为闪存卡并响应用户输入。 

## <a name="requirements"></a>要求

若要 `ViewPager` 在你的应用项目中使用，你必须安装 [Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) 包。 有关安装 NuGet 包的详细信息，请参阅 [演练：在项目中包括 NuGet](/visualstudio/mac/nuget-walkthrough)。 

## <a name="architecture"></a>体系结构

使用以下三个组件实现 gestural 导航 `ViewPager` ：

- ViewPager
- 适配器
- 寻呼指示器

下面总结了其中的每个组件。

### <a name="viewpager"></a>ViewPager

`ViewPager` 是一个布局管理器， `View` 每次显示一个集合。 其工作是检测用户的轻扫手势，并根据需要导航到下一个或上一个视图。 例如，下面的屏幕截图演示了如何 `ViewPager` 在响应用户笔势时从一个图像过渡到下一个图像： 

[![显示视图之间过渡的 TreePager 应用的特写](images/02-transition-sml.png)](images/02-transition.png#lightbox)

### <a name="adapter"></a>适配器

`ViewPager` 从 *适配器*提取其数据。 适配器的作业是创建 `View` 显示的 `ViewPager` ，并根据需要提供它们。 下图说明 &ndash; 了适配器创建和填充的这一概念 `View` ，并将其提供给 `ViewPager` 。 当 `ViewPager` 检测到用户的轻扫手势时，它会要求适配器提供相应的 `View` 来显示： 

[![说明适配器如何将图像和名称连接到 ViewPager 的关系图](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

在此特定示例中，每个 `View` 都是从树映像和树名称构造的，然后将其传递到 `ViewPager` 。 

### <a name="pager-indicator"></a>寻呼指示器

`ViewPager` 可用于显示大型数据集 (例如，图像库可能包含数百个图像) 。 为了帮助用户导航大型数据集， `ViewPager` 通常会附带一个显示字符串的 *页导航指示器* 。 字符串可能是图像标题、标题或只是当前视图在数据集中的位置。 

有两个视图可为你生成此导航信息： `PagerTabStrip` `PagerTitleStrip.` 每个视图都在顶部显示一个字符串 `ViewPager` ，每个视图从的适配器提取其数据， `ViewPager` 以使其始终与当前显示的保持同步 `View` 。 它们之间的区别在于， `PagerTabStrip` 包括 "当前" 字符串的可视指示器，而 `PagerTitleStrip` 不 (如以下屏幕截图所示) ： 

[![具有 PagerTitleStrip 和 PagerTabStrip 的 TreePager 应用程序的屏幕截图](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

本指南演示了如何 immplement `ViewPager` 、适配器和指标应用组件，并将它们集成以支持 gestural 导航。 

## <a name="related-links"></a>相关链接

- [TreePager (示例) ](/samples/xamarin/monodroid-samples/userinterface-treepager)
- [FlashCardPager (示例) ](/samples/xamarin/monodroid-samples/userinterface-flashcardpager)