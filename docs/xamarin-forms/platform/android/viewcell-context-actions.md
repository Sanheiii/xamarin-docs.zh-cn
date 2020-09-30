---
title: Android 上的 ViewCell 上下文操作
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 Android 平台特定的，该平台启用了 ViewCell 上下文操作传统模式。
ms.prod: xamarin
ms.assetid: 8BD71B10-5037-443F-9975-B941CE393E5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6cbdea3656daa4df894a79edc70453c78e3844bc
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556745"
---
# <a name="viewcell-context-actions-on-android"></a>Android 上的 ViewCell 上下文操作

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

默认情况下 Xamarin.Forms ，在4.3 中，当 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 在 Android 应用程序中为中的每个项定义上下文操作时 [`ListView`](xref:Xamarin.Forms.ListView) ，会在更改中的选定项时更新上下文操作菜单 `ListView` 。 但是，在以前版本的 Xamarin.Forms 上下文操作菜单未更新时，此行为称为 `ViewCell` 旧模式。 如果 `ListView` 使用 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) `ItemTemplate` 从 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 定义不同上下文操作的对象设置其，则此旧模式可能导致不正确的行为。

此 Android 平台特定平台启用 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 上下文操作菜单旧版模式，以实现向后兼容性，以便在更改中的选定项时不会更新上下文操作菜单 [`ListView`](xref:Xamarin.Forms.ListView) 。 它通过将 `ViewCell.IsContextActionsLegacyModeEnabled` 可绑定的属性设置为来在 XAML 中使用 `true` ：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ItemsSource="{Binding Items}">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell android:ViewCell.IsContextActionsLegacyModeEnabled="true">
                        <ViewCell.ContextActions>
                            <MenuItem Text="{Binding Item1Text}" />
                            <MenuItem Text="{Binding Item2Text}" />
                        </ViewCell.ContextActions>
                        <Label Text="{Binding Text}" />
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

此外，还可以使用 Fluent API 从 c # 使用该方法：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

viewCell.On<Android>().SetIsContextActionsLegacyModeEnabled(true);
```

`ViewCell.On<Android>`方法指定此平台特定的仅在 Android 上运行。 `ViewCell.SetIsContextActionsLegacyModeEnabled`命名空间中的方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 用于启用 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 上下文操作菜单旧版模式，以便在更改中的选定项时不会更新上下文操作菜单 [`ListView`](xref:Xamarin.Forms.ListView) 。 此外， `ViewCell.GetIsContextActionsLegacyModeEnabled` 可以使用方法来返回是否启用上下文操作的旧模式。

以下屏幕截图显示 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 启用了旧模式的上下文操作：

![Android 上已启用 ViewCell 旧式模式的屏幕截图](viewcell-context-actions-images/legacy-mode-enabled.png "ViewCell 旧版模式已启用")

在此模式下，尽管为第2单元定义了不同的上下文菜单项，但显示的上下文操作菜单项对于单元1和单元2是相同的。

以下屏幕截图显示 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 禁用了旧模式的上下文操作，这是默认 Xamarin.Forms 行为：

![Android 上禁用的 ViewCell 旧版模式的屏幕截图](viewcell-context-actions-images/legacy-mode-disabled.png "ViewCell 旧版模式已禁用")

在此模式下，将显示单元格1和单元格2的正确上下文操作菜单项。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)