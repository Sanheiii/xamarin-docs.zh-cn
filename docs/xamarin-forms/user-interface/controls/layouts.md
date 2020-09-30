---
title: Xamarin.Forms 布局
description: Xamarin.Forms 布局用于将用户界面控件组合到可视结构中。 本文列出了中包含的布局 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f749bda3f0fb10edf8c662ef08c22164087dee47
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563739"
---
# <a name="no-locxamarinforms-layouts"></a>Xamarin.Forms 布局

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Xamarin.Forms 布局用于将用户界面控件组合到可视结构中。_

[`Layout`](xref:Xamarin.Forms.Layout)和 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 中的类 Xamarin.Forms 是视图的专用化子类型，用作视图和其他布局的容器。 `Layout`类本身派生自 [`View`](views.md) 。 `Layout`派生通常包含用于在应用程序中设置子元素的位置和大小的逻辑 Xamarin.Forms 。

[![：： no (Xamarin) ：：： Layout 类型](layouts-images/layouts-sml.png "：： no (Xamarin) ：：： Layout 类型")](layouts-images/layouts.png#lightbox "：： no (Xamarin) ：：： Layout 类型")

派生自的类 `Layout` 可以分为两个类别：

## <a name="layouts-with-single-content"></a>具有单一内容的布局

这些类派生自 [`Layout`](xref:Xamarin.Forms.Layout) ，其中定义 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 和 [`IsClippedToBounds`](xref:Xamarin.Forms.Layout.IsClippedToBounds) 属性：

| 类型 | 说明 | 外观 |
| --- | --- | --- |
| `ContentView` | [`ContentView`](xref:Xamarin.Forms.ContentView) 包含用属性设置的单个子级 [`Content`](xref:Xamarin.Forms.ContentView.Content) 。 `Content`属性可以设置为任何 `View` 派生，包括其他 `Layout` 派生。 `ContentView` 主要用作结构元素，用作的基类 [`Frame`](xref:Xamarin.Forms.Frame) 。<br /><br />[API 文档](xref:Xamarin.Forms.ContentView)  / [指南](~/xamarin-forms/user-interface/layouts/contentview.md)  / [示例](/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/) | [![ContentView 示例](layouts-images/ContentView.png "ContentView 示例")](layouts-images/ContentView-Large.png#lightbox "ContentView 示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs)  /  的 c # 代码[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
| `Frame` | [`Frame`](xref:Xamarin.Forms.Frame)类派生自 [`ContentView`](xref:Xamarin.Forms.ContentView) ，并在其子级周围显示一个边框或框架。 `Frame`类的默认 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 值为20，还定义 [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor) 、 [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius) 和 [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) 属性。<br /><br />[API 文档](xref:Xamarin.Forms.Frame)  / [指南](~/xamarin-forms/user-interface/layouts/frame.md)  / [示例](/samples/xamarin/xamarin-forms-samples/userinterface-frame/) | [![框架示例](layouts-images/Frame.png "框架示例")](layouts-images/Frame-Large.png#lightbox "框架示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs)  /  的 c # 代码[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
| `ScrollView` | [`ScrollView`](xref:Xamarin.Forms.ScrollView) 能否滚动其内容。 将 [`Content`](xref:Xamarin.Forms.ScrollView.Content) 属性设置为视图或布局太大，无法在屏幕上容纳。  (的内容 `ScrollView` 通常是一个 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 ) 设置 [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) 属性以指示滚动应为垂直、水平还是同时为两者。<br /><br />[API 文档](xref:Xamarin.Forms.ScrollView)  / [指南](~/xamarin-forms/user-interface/layouts/scrollview.md)  / [示例](/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![ScrollView 示例](layouts-images/ScrollView.png "ScrollView 示例")](layouts-images/ScrollView-Large.png#lightbox "ScrollView 示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs)  /  的 c # 代码[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
| `TemplatedView` | [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) 显示具有控件模板的内容，并且是的基类 [`ContentView`](xref:Xamarin.Forms.ContentView) 。<br /><br />[API 文档](xref:Xamarin.Forms.TemplatedView)  / [指南](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![TemplatedView 示例](layouts-images/TemplatedView.png "TemplatedView 示例")](layouts-images/TemplatedView.png#lightbox "TemplatedView 示例") |
| `ContentPresenter` | [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) 是一个用于模板化视图的布局管理器，用于在中 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 标记要显示的内容。<br /><br />[API 文档](xref:Xamarin.Forms.ContentPresenter)  / [指南](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![System.windows.controls.contentpresenter> 示例](layouts-images/ContentPresenter.png "System.windows.controls.contentpresenter> 示例")](layouts-images/ContentPresenter.png#lightbox "System.windows.controls.contentpresenter> 示例") |
|     |     |     |

## <a name="layouts-with-multiple-children"></a>具有多个子元素的布局

这些类派生自 [`Layout<View>`](xref:Xamarin.Forms.Layout`1) ：

| 类型 | 说明 | 外观 |
| --- | --- | --- |
| `StackLayout` | [`StackLayout`](xref:Xamarin.Forms.StackLayout) 根据属性水平或垂直地定位堆栈中的子元素 [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) 。 [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing)属性控制子级之间的间距，默认值为6。<br /><br />[API 文档](xref:Xamarin.Forms.StackLayout)  / [指南](~/xamarin-forms/user-interface/layouts/stacklayout.md)  / [示例](/samples/xamarin/xamarin-forms-samples/userinterface-layout)| [![StackLayout 示例](layouts-images/StackLayout.png "StackLayout 示例")](layouts-images/StackLayout-Large.png#lightbox "StackLayout 示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs)  /  的 c # 代码[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) |
| `Grid` | [`Grid`](xref:Xamarin.Forms.Grid) 将其子元素置于行和列的网格中。 使用 [附加的属性](~/xamarin-forms/xaml/attached-properties.md)、、和指示子的位置 [`Row`](xref:Xamarin.Forms.Grid.RowProperty) [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty) [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) 。<br /><br />[API 文档](xref:Xamarin.Forms.Grid)  / [指南](~/xamarin-forms/user-interface/layouts/grid.md)  / [示例](/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![网格示例](layouts-images/Grid.png "网格示例")](layouts-images/Grid-Large.png#lightbox "网格示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs)  /  的 c # 代码[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) |
| `AbsoluteLayout` | [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 将子元素定位到相对于其父级的特定位置。 使用 [附加属性](~/xamarin-forms/xaml/attached-properties.md)和指示子的位置 [`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) [`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 。 可 `AbsoluteLayout` 用于对视图的位置进行动画处理。<br /><br />[API 文档](xref:Xamarin.Forms.AbsoluteLayout)  / [指南](~/xamarin-forms/user-interface/layouts/absolutelayout.md)  / [示例](/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![AbsoluteLayout 示例](layouts-images/AbsoluteLayout.png "AbsoluteLayout 示例")](layouts-images/AbsoluteLayout-Large.png#lightbox "AbsoluteLayout 示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutDemoPage.cs)  /  的 c # 代码带有[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs)的[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) |
| `RelativeLayout` | [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 相对于其 `RelativeLayout` 自身或同级定位子元素。 使用设置为类型和的对象的 [附加属性](~/xamarin-forms/xaml/attached-properties.md) 来指示子的位置 [`Constraint`](xref:Xamarin.Forms.Constraint) [`BoundsConstraint`](xref:Xamarin.Forms.Constraint) 。<br /><br />[API 文档](xref:Xamarin.Forms.RelativeLayout)  / [指南](~/xamarin-forms/user-interface/layouts/relativelayout.md)  / [示例](/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![RelativeLayout 示例](layouts-images/RelativeLayout.png "RelativeLayout 示例")](layouts-images/RelativeLayout-Large.png#lightbox "RelativeLayout 示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs)  /  的 c # 代码[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) |
| `FlexLayout` | [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) 基于 CSS [挠性 Box 布局模块](https://www.w3.org/TR/css-flexbox-1/)，通常称为 " _flex 布局_ " 或 _"弹性"_。 `FlexLayout` 定义六个可绑定属性和五个附加的可绑定属性，这些属性允许用许多对齐方式和方向选项来堆叠或包装子元素。<br /><br />[API 文档](xref:Xamarin.Forms.FlexLayout)  / [指南](~/xamarin-forms/user-interface/layouts/flex-layout.md)  / [示例](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos) | [![FlexLayout 示例](layouts-images/FlexLayout.png "FlexLayout 示例")](layouts-images/FlexLayout-Large.png#lightbox "FlexLayout 示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs)  /  的 c # 代码[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) |
|     |     |     |

## <a name="related-links"></a>相关链接

- [Xamarin.Forms FormsGallery 示例](/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 示例](/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 文档](/dotnet/api/xamarin.forms?view=xamarin-forms)