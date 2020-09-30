---
title: Xamarin.Forms CollectionView 简介
description: CollectionView 是一种灵活且高性能的视图，可使用不同的布局规范呈现数据列表。
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fbaa5779787efe2b7c62368080789fe767ca8021
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560333"
---
# <a name="no-locxamarinforms-collectionview-introduction"></a>Xamarin.Forms CollectionView 简介

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 是使用不同布局规范显示数据列表的视图。 它旨在提供更灵活、更高的性能替代方法 [`ListView`](xref:Xamarin.Forms.ListView) 。 例如，下面的屏幕截图显示了一个 `CollectionView` 使用两个垂直网格网格的，它允许多个选择：

[![IOS 和 Android 上的 CollectionView 垂直网格布局的屏幕截图](introduction-images/verticalgrid-multipleselection.png "具有多个选定内容的 CollectionView 垂直网格布局")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "具有多个选定内容的 CollectionView 垂直网格布局")

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 可从 Xamarin.Forms 4.3 获得。

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) 在 iOS 和 Android 上可用，但在通用 Windows 平台仅 [部分可用](https://gist.github.com/hartez/7d0edd4182dbc7de65cebc6c67f72e14) 。

## <a name="collectionview-and-listview-differences"></a>CollectionView 和 ListView 差异

尽管 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 和 [`ListView`](xref:Xamarin.Forms.ListView) api 相似，但有一些明显的区别：

- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 具有灵活的布局模型，允许在列表或网格中垂直或水平显示数据。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 支持单个和多个选择。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 没有单元的概念。 相反，数据模板用于定义列表中每个数据项的外观。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 自动利用基础本机控件提供的虚拟化。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 减少的 API 图面 [`ListView`](xref:Xamarin.Forms.ListView) 。 中的很多属性和事件 [`ListView`](xref:Xamarin.Forms.ListView) 都不存在于中 `CollectionView` 。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 不包含内置分隔符。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 如果其 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 从 UI 线程中更新，则会引发异常。

## <a name="move-from-listview-to-collectionview"></a>从 ListView 移到 CollectionView

[`ListView`](xref:Xamarin.Forms.ListView)Xamarin.Forms可以通过下表中的帮助将现有实现中的实现迁移到 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 实现：

| 概念 | ListView API | CollectionView |
|---|---|---|
| 数据 | `ItemsSource` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)通过设置的属性来填充 `ItemsSource` 。 有关详细信息，请参阅 [使用数据填充 CollectionView](populate-data.md#populate-a-collectionview-with-data)。 |
| 项外观 | `ItemTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)可以通过将属性设置为来定义中每个项的外观 `ItemTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 有关详细信息，请参阅 [定义项外观](populate-data.md#define-item-appearance)。 |
| 单元 | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 没有单元的概念，因此没有公开指示器的概念。 相反，数据模板用于定义列表中每个数据项的外观。 |
| 行分隔符 | `SeparatorColor`, `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 不包含内置分隔符。 如果需要，可在项模板中提供这些项。 |
| 选择 | `SelectionMode`, `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 支持单个和多个选择。 有关详细信息，请参阅[ Xamarin.Forms CollectionView 选择](selection.md)。 |
| 行高 | `HasUnevenRows`, `RowHeight` | 在中 `CollectionView` ，每个项的行高由 `ItemSizingStrategy` 属性确定。 有关详细信息，请参阅 [项大小调整](layout.md#item-sizing)。|
| Caching | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 自动使用基础本机控件提供的虚拟化。 |
| 页眉和页脚 | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以通过 `Header` 、、 `Footer` `HeaderTemplate` 和属性，提供滚动列表中的项的页眉和页脚 `FooterTemplate` 。 有关详细信息，请参阅 [页眉和页脚](layout.md#headers-and-footers)。 |
| 分组 | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 通过将其属性设置为，显示正确分组的数据 `IsGrouped` `true` 。 可以通过将 `GroupHeaderTemplate` 和 `GroupFooterTemplate` 属性设置为对象来自定义组头和组尾  [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 有关详细信息，请参阅[ Xamarin.Forms CollectionView 分组](grouping.md)。 |
| 下拉刷新 | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | 通过将设置为的子级，支持拉取到刷新功能 [`CollectionView`](xref:Xamarin.Forms.CollectionView) `RefreshView` 。 有关详细信息，请参阅 [请求刷新](populate-data.md#pull-to-refresh)。 |
| 上下文菜单项 | `ContextActions` | 通过将设置 `SwipeView` 为中的根视图来支持上下文菜单项，该视图 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 定义中每个数据项的外观 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 有关详细信息，请参阅[上下文菜单](populate-data.md#context-menus)。 |
| 滚动 | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 定义将 `ScrollTo` 项滚动到视图中的方法。 有关详细信息，请参阅 [滚动](scrolling.md)。 |

## <a name="related-links"></a>相关链接

- [CollectionView (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)