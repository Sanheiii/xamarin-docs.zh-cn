---
title: RecyclerView
description: RecyclerView 是用于显示集合的视图组;它旨在为早期视图组（如 ListView 和 GridView）提供更灵活的替换。  本指南说明如何在 Xamarin Android 应用程序中使用和自定义 RecyclerView。
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/03/2018
ms.openlocfilehash: a972f6686103dc9b3a1317d15985cbf3dc78bd82
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457052"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView 是用于显示集合的视图组;它旨在为早期视图组（如 ListView 和 GridView）提供更灵活的替换。 本指南说明如何在 Xamarin Android 应用程序中使用和自定义 RecyclerView。_

## <a name="recyclerview"></a>RecyclerView

许多应用程序都需要显示相同类型的集合 (如) 的消息、联系人、图像或歌曲）;通常，此集合太大而无法放在屏幕上，因此集合显示在一个小窗口中，可以平滑滚动集合中的所有项。
`RecyclerView` 是一个 Android 小组件，用于显示列表或网格中的项集合，使用户能够滚动浏览集合。 下面是一个示例应用程序的屏幕截图，它使用 `RecyclerView` 在垂直滚动列表中显示电子邮件收件箱内容：

[![使用 RecyclerView 列出收件箱消息的示例应用程序](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` 提供两个引人注目的功能：

- 它具有灵活的体系结构，使你能够通过插入首选组件来修改其行为。

- 这种方法对大型集合非常有效，因为它会重用项视图，并要求使用 *视图持有* 者来缓存视图引用。

本指南说明如何 `RecyclerView` 在 Xamarin android 应用程序中使用; 它说明了如何将 `RecyclerView` 包添加到你的 xamarin android 项目，并介绍了如何 `RecyclerView` 在典型的应用程序中使用函数。 提供了真实的代码示例，用于演示如何将集成 `RecyclerView` 到应用程序中，如何实现项-视图单击，以及如何 `RecyclerView` 在其基础数据更改时刷新。 本指南假定你熟悉 Xamarin Android 开发。

### <a name="requirements"></a>要求

尽管 `RecyclerView` 通常与 android 5.0 棒糖形关联，但它提供为支持库 &ndash; `RecyclerView` 适用于面向 API 级别 7 (Android 2.1) 和更高版本的应用。 下面是 `RecyclerView` 在基于 Xamarin 的应用程序中使用所必需的：

- **Xamarin.Android** &ndash; 必须安装和配置 Xamarin xamarin 4.20 或更高版本，并且必须安装 Visual Studio 或 Visual Studio for Mac。

- 应用项目必须包含 **v7. RecyclerView** 包。 有关安装 NuGet 包的详细信息，请参阅 [演练：在项目中包括 NuGet](/visualstudio/mac/nuget-walkthrough)。

### <a name="overview"></a>概述

`RecyclerView` 可被视为 `ListView` `GridView` Android 中的和小组件的替换。 类似于其前置任务， `RecyclerView` 旨在在小窗口中显示大型数据集，但 `RecyclerView` 提供更多布局选项，并且更适合用于显示大集合。 如果你熟悉 `ListView` ，与之间有几个重要的区别 `ListView` `RecyclerView` ：

- `RecyclerView` 使用略微复杂一些：必须编写更多的代码以与一起使用 `RecyclerView` `ListView` 。

- `RecyclerView` 不提供预定义的适配器;必须实现访问数据源的适配器代码。 但是，Android 包含多个预定义的适配器，它们适用于 `ListView` 和 `GridView` 。

- `RecyclerView` 当用户点击某个项时，不提供项单击事件;相反，项单击事件由 helper 类处理。 相反， `ListView` 提供项单击事件。

- `RecyclerView` 通过回收视图和实施视图持有者模式来增强性能，从而消除不必要的布局资源查找。 在中，使用 "查看者" 模式是可选的 `ListView` 。

- `RecyclerView` 基于模块化设计，使其更易于自定义。 例如，你可以插入不同的布局策略，而不会对应用进行重大的代码更改。
    与此相反， `ListView` 在结构中相对单一。

- `RecyclerView` 包括项添加和移除的内置动画。 `ListView` 在应用程序开发人员的部分，动画需要额外的工作。

### <a name="sections"></a>部分

#### <a name="recyclerview-parts-and-functionality"></a>[RecyclerView 部件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

本主题介绍了 `Adapter` 、 `LayoutManager` 和如何 `ViewHolder` 协同工作以支持的帮助器类 `RecyclerView` 。
它提供每个帮助器类的高级概述，并说明如何在应用程序中使用它们。

#### <a name="a-basic-recyclerview-example"></a>[基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

本主题基于 [RecyclerView 部件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) 中提供的信息，提供有关如何 `RecyclerView` 实现各种元素以构建真实的照片浏览应用程序的实际代码示例。

#### <a name="extending-the-recyclerview-example"></a>[扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

本主题将附加代码添加到在 [基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) 中介绍的示例应用程序，以演示如何处理项单击事件，并在 `RecyclerView` 基础数据源发生更改时进行更新。

### <a name="summary"></a>总结

本指南介绍了 Android `RecyclerView` 小组件; 本指南介绍了如何将 `RecyclerView` 支持库添加到 Xamarin Android 项目，如何 `RecyclerView` 回收视图，如何提高工作效率的视图持有者模式，以及如何使各种帮助器类 `RecyclerView` 共同显示集合。 它提供了示例代码来演示如何 `RecyclerView` 将集成到应用程序中，它介绍了如何 `RecyclerView` 通过插入不同布局管理器来定制的布局策略，并介绍了如何处理项单击事件并通知 `RecyclerView` 数据源更改。

有关的详细信息 `RecyclerView` ，请参阅 [RecyclerView 类参考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)。

## <a name="related-links"></a>相关链接

- [RecyclerViewer (示例) ](/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [棒糖形简介](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)