---
title: Xamarin.Forms Pages
description: Xamarin.Forms 页面表示跨平台的移动应用程序屏幕。 本文列出了中包含的页 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9164275b75c021a79b6ea5ea64194997ad804ece
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563206"
---
# <a name="no-locxamarinforms-pages"></a>Xamarin.Forms Pages

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Xamarin.Forms 页面表示跨平台的移动应用程序屏幕。_

下面描述的所有页面类型都派生自 Xamarin.Forms [`Page`](xref:Xamarin.Forms.Page) 类。 这些视觉对象占据全部或大部分屏幕。 `Page`对象表示 `ViewController` 在 iOS 和 `Page` 通用 Windows 平台中的。 在 Android 上，每个页面都占据屏幕 `Activity` ，但 Xamarin.Forms 页面并 *不*是 `Activity` 对象。

[![：： no (Xamarin) ：：：页类型](pages-images/pages-sml.png)](pages-images/pages.png#lightbox "：： no (Xamarin) ：：：页类型")

## <a name="pages"></a>Pages

Xamarin.Forms 支持以下页类型：

| 类型 | 说明 | 外观 |
| --- | --- | --- |
| `ContentPage` | [`ContentPage`](xref:Xamarin.Forms.ContentPage) 是最简单且最常见的页面类型。 将 [`Content`](xref:Xamarin.Forms.ContentPage.Content) 属性设置为一个 [`View`](views.md) 对象，该对象最常为 [`Layout`](layouts.md) ，如 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 、 [`Grid`](xref:Xamarin.Forms.Grid) 或 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。<br /><br />[API 文档](xref:Xamarin.Forms.ContentPage) | [![ContentPage 示例](pages-images/ContentPage.png "ContentPage 示例")](pages-images/ContentPage-Large.png#lightbox "ContentPage 示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs)  /  的 c # 代码[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
| `MasterDetailPage` | [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)管理两个信息窗格。 将 [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 属性设置为通常显示列表或菜单的页面。 将 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 属性设置为显示母版页中选定项的页面。 [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented)属性控制主页面或详细信息页面是否可见。<br /><br />[API 文档](xref:Xamarin.Forms.MasterDetailPage)  / [指南](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)  / [示例](/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) | [![MasterDetailPage 示例](pages-images/MasterDetailPage.png "MasterDetailPage 示例")](pages-images/MasterDetailPage-Large.png#lightbox "MasterDetailPage 示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs)  /  的 c # 代码带有[代码隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs)的[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) |
| `NavigationPage` | [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)使用基于堆栈的体系结构管理其他页面中的导航。 在应用程序中使用页面导航时，主页的实例应传递给对象的构造函数 `NavigationPage` 。<br /><br />[API 文档](xref:Xamarin.Forms.NavigationPage)  / [指南](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)  / [示例 1](/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)、 [2](/samples/xamarin/xamarin-forms-samples/navigation-passingdata)和[3](/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![NavigationPage 示例](pages-images/NavigationPage.png "NavigationPage 示例")](pages-images/NavigationPage-Large.png#lightbox "NavigationPage 示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs)  /  的 c # 代码[代码为 "隐藏](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs)" 的[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) |
| `TabbedPage` | [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 派生自抽象 [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) 类，允许使用选项卡在子页间导航。 将 [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) 属性设置为页的集合，或将属性设置 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 为数据对象的集合，并将属性设置为 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 描述如何以可视方式表示每个对象的属性。<br /><br />[API 文档](xref:Xamarin.Forms.TabbedPage)  / [指南](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)  / [示例 1](/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)和[2](/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![TabbedPage 示例](pages-images/TabbedPage.png "TabbedPage 示例")](pages-images/TabbedPage-Large.png#lightbox "TabbedPage 示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs)  /  的 c # 代码[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
| `CarouselPage` | [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 派生自抽象 [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) 类，允许通过手指轻扫在子页面之间导航。 将 [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) 属性设置为对象的集合 [`ContentPage`](xref:Xamarin.Forms.ContentPage) ，或将属性设置 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 为数据对象的集合，并将 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 属性设置为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 描述如何以直观方式表示每个对象的属性。<br /><br />[API 文档](xref:Xamarin.Forms.CarouselPage)  / [指南](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md)  / [示例 1](/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)和[2](/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![CarouselPage 示例](pages-images/CarouselPage.png "CarouselPage 示例")](pages-images/CarouselPage-Large.png#lightbox "CarouselPage 示例")<br />[此页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs)  /  的 c # 代码[XAML 页](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
| `TemplatedPage` | [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage) 使用控件模板显示全屏内容，是的基类 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。<br /><br />[API 文档](xref:Xamarin.Forms.TemplatedPage)  / [指南](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![TemplatedPage 示例](pages-images/TemplatedPage.png "TemplatedPage 示例")](pages-images/TemplatedPage.png "TemplatedPage 示例") |
|     |     |     |

## <a name="related-links"></a>相关链接

- [Xamarin.Forms FormsGallery 示例](/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 示例](/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 文档](/dotnet/api/xamarin.forms?view=xamarin-forms)