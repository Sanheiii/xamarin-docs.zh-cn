---
title: 布局压缩
description: 布局压缩从可视化树中删除指定的布局，以尝试改善页面呈现性能。 本文介绍如何启用布局压缩以及它可以引入的优点。
ms.prod: xamarin
ms.assetid: da9e1b26-9d31-4762-94c3-4039f306b7f2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d5e1cb9709c224ed5059de24bb45eed882ef216b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563531"
---
# <a name="layout-compression"></a>布局压缩

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)

_布局压缩从可视化树中删除指定的布局，以尝试改善页面呈现性能。本文介绍如何启用布局压缩以及它可以引入的优点。_

## <a name="overview"></a>概述

Xamarin.Forms 使用两序列递归方法调用执行布局：

- 布局从可视化树的顶部到页面开始，并在可视化树的所有分支中进行，以涵盖页面上的每个可视元素。 作为其他元素的父级的元素负责调整其子级的相对于其自身的大小和位置。
- 失效是指页面中元素的更改会触发新的布局周期的过程。 当元素不再具有正确的大小或位置时，它们被视为无效。 可视化树中具有子级的每个元素都会在其某个子元素的大小改变时发出警报。 因此，在可视化树中对元素大小所做的更改可能会导致树上的更改出现。

有关如何执行布局的详细信息 Xamarin.Forms ，请参阅 [创建自定义布局](~/xamarin-forms/user-interface/layouts/custom.md)。

布局进程的结果是本机控件的层次结构。 但是，此层次结构包括平台呈现器的附加容器呈现器和包装器，进一步因为这样做视图层次结构嵌套。 嵌套级别越多，显示页面所需执行的工作就越多 Xamarin.Forms 。 对于复杂的布局，视图层次结构可以具有多个嵌套级别。

例如，请考虑示例应用程序中用于登录 Facebook 的以下按钮：

![Facebook 按钮](layout-compression-images/facebook-button.png)

此按钮被指定为具有以下 XAML 视图层次结构的自定义控件：

```xaml
<ContentView ...>
    <StackLayout>
        <StackLayout ...>
            <AbsoluteLayout ...>
                <Button ... />    
                <Image ... />
                <Image ... />
                <BoxView ... />
                <Label ... />
                <Button ... />
            </AbsoluteLayout>
        </StackLayout>
        <Label ... />
    </StackLayout>    
</ContentView>
```

可以通过 [Xamarin Inspector](~/tools/inspector/index.md)来检查生成的嵌套视图层次结构。 在 Android 上，嵌套视图层次结构包含17个视图：

!["为 Facebook 查看层次结构" 按钮](layout-compression-images/no-compression.png)

布局压缩（适用于 Xamarin.Forms iOS 和 Android 平台上的应用程序）旨在通过从可视化树中删除指定布局来平展视图嵌套，这可以提高页面呈现性能。 所传送的性能权益因页面的复杂性、所使用的操作系统版本以及运行应用程序的设备而异。 不过，在旧设备上实现的性能提升最大。

> [!NOTE]
> 尽管本文重点介绍在 Android 上应用布局压缩的结果，但这同样适用于 iOS。

## <a name="layout-compression"></a>布局压缩

在 XAML 中，可以通过 `CompressedLayout.IsHeadless` `true` 在布局类上将附加属性设置为来启用布局压缩：

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

或者，通过将布局实例指定为方法的第一个参数，可在 c # 中启用它 `CompressedLayout.SetIsHeadless` ：

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> 由于布局压缩从可视化树中删除了布局，因此它不适用于具有视觉外观或获取触控输入的布局。 因此，用于设置属性的布局 [`VisualElement`](xref:Xamarin.Forms.VisualElement) (如 [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 、、、 [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 、 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 或接受手势）不是布局压缩的候选项。 但是，对设置视觉外观属性或接受手势的布局启用布局压缩不会导致生成或运行时错误。 相反，将应用布局压缩，并且会以静默方式识别可视外观属性和手势。

对于 Facebook 按钮，可对三个布局类启用布局压缩：

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
    <StackLayout CompressedLayout.IsHeadless="true" ...>
        <AbsoluteLayout CompressedLayout.IsHeadless="true" ...>
            ...
        </AbsoluteLayout>
    </StackLayout>
    ...
</StackLayout>  
```

在 Android 上，这会导致嵌套视图层次结构为14个视图：

![带有布局压缩功能的 Facebook 的 "查看层次结构" 按钮](layout-compression-images/layout-compression.png)

与17个视图的原始嵌套视图层次结构相比，这表示17% 的视图数减少。 虽然这种减少可能显得不明显，但整个页面的视图缩减可能更重要。

### <a name="fast-renderers"></a>快速呈现器

快速呈现器 Xamarin.Forms 通过平展生成的本机视图层次结构，减少 Android 上控件的通货膨胀和呈现成本。 这会通过创建更少的对象来进一步提高性能，进而导致不太复杂的可视化树和内存使用。 有关快速呈现器的详细信息，请参阅 [快速呈现](~/xamarin-forms/internals/fast-renderers.md)器。

对于示例应用程序中的 "Facebook" 按钮，合并布局压缩和快速呈现器将生成一个8视图的嵌套视图层次结构：

![带有布局压缩和快速呈现器的 Facebook 的 "查看层次结构" 按钮](layout-compression-images/layout-compression-with-fast-renderers.png)

与17视图的原始嵌套视图层次结构相比，这表示缩减了52%。

示例应用程序包含从实际应用程序中提取的页面。 如果没有布局压缩和快速呈现器，该页将在 Android 上生成130视图的嵌套视图层次结构。 对相应的布局类启用快速呈现器和布局压缩会将嵌套视图层次结构减小到70视图，减少了46%。

## <a name="summary"></a>总结

布局压缩从可视化树中删除指定的布局，以尝试改善页面呈现性能。 这带来的性能优势因页面复杂性、要使用的操作系统版本以及运行应用的设备而异。 不过，在旧设备上实现的性能提升最大。

## <a name="related-links"></a>相关链接

- [创建自定义布局](~/xamarin-forms/user-interface/layouts/custom.md)
- [快速呈现器](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)