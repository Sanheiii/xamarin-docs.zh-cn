---
title: 创建 Xamarin.Forms 可视化呈现器
description: 创建 Xamarin.Forms 可有选择地应用于 VisualElement 对象的视觉对象，而无需为视图创建子类 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a8e8388a6a460b3a2958d3ab3b9a4c9c34ea1831
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556459"
---
# <a name="create-a-no-locxamarinforms-visual-renderer"></a>创建 Xamarin.Forms 可视化呈现器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

Xamarin.Forms 视觉对象允许创建呈现器并有选择地应用于 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 对象，而无需为视图创建子类 Xamarin.Forms 。 指定一个 `IVisual` 类型（作为其一部分）的呈现器 `ExportRendererAttribute` 将用于呈现所选择的视图，而不是默认呈现器。 在呈现器选择时间，将 `Visual` 检查视图的属性并将其包含在呈现器选择过程中。

> [!IMPORTANT]
> 当前，在 [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) 视图呈现后无法更改属性，但在将来的版本中将发生更改。

创建和使用可视化呈现器的过程 Xamarin.Forms 如下：

1. 为所需视图创建平台呈现器。 有关详细信息，请参阅 [创建呈现](#create-platform-renderers)器。
1. 创建一个派生自的类型 `IVisual` 。 有关详细信息，请参阅 [Create a IVisual type](#create-an-ivisual-type)。
1. `IVisual`将类型注册为修饰呈现器的的一部分 `ExportRendererAttribute` 。 有关详细信息，请参阅 [注册 IVisual 类型](#register-the-ivisual-type)。
1. 通过将 [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) 视图的属性设置为名称来使用可视化呈现器 `IVisual` 。 有关详细信息，请参阅 [使用视觉呈现](#consume-the-visual-renderer)器。
1. 可有可无注册类型的名称 `IVisual` 。 有关详细信息，请参阅 [注册 IVisual 类型的名称](#register-a-name-for-the-ivisual-type)。

## <a name="create-platform-renderers"></a>创建平台呈现器

有关创建呈现器类的信息，请参阅 [自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)器。 但请注意， Xamarin.Forms 无需为视图划分子类，即可将可视化呈现器应用于视图。

此处所述的呈现器类实现了一个 [`Button`](xref:Xamarin.Forms.Button) 使用阴影显示其文本的自定义。

### <a name="ios"></a>iOS

下面的代码示例演示了 iOS 的按钮呈现器：

```csharp
public class CustomButtonRenderer : ButtonRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.TitleShadowOffset = new CoreGraphics.CGSize(1, 1);
            Control.SetTitleShadowColor(Color.Black.ToUIColor(), UIKit.UIControlState.Normal);
        }
    }
}
```

### <a name="android"></a>Android

下面的代码示例演示适用于 Android 的按钮呈现器：

```csharp
public class CustomButtonRenderer : Xamarin.Forms.Platform.Android.AppCompat.ButtonRenderer
{
    public CustomButtonRenderer(Context context) : base(context)
    {
    }

    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.SetShadowLayer(5, 3, 3, Color.Black.ToAndroid());
        }
    }
}
```

## <a name="create-an-ivisual-type"></a>创建 IVisual 类型

在跨平台库中，创建一个派生自的类型 `IVisual` ：

```csharp
public class CustomVisual : IVisual
{
}
```

`CustomVisual`然后，可以针对呈现器类注册该类型，允许 [`Button`](xref:Xamarin.Forms.Button) 对象选择使用呈现器。

## <a name="register-the-ivisual-type"></a>注册 IVisual 类型

在平台项目中，将添加到 `ExportRendererAttribute` 程序集级别：

```csharp
[assembly: ExportRenderer(typeof(Xamarin.Forms.Button), typeof(CustomButtonRenderer), new[] { typeof(CustomVisual) })]
namespace VisualDemos.iOS
{
    public class CustomButtonRenderer : ButtonRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
        {
            // ...
        }
    }
}
```

在 iOS 平台项目的此示例中， `ExportRendererAttribute` 指定 `CustomButtonRenderer` 类将用于呈现使用 [`Button`](xref:Xamarin.Forms.Button) 对象，其 `IVisual` 类型注册为第三个参数。 指定一个 `IVisual` 类型（作为其一部分）的呈现器 `ExportRendererAttribute` 将用于呈现所选择的视图，而不是默认呈现器。

## <a name="consume-the-visual-renderer"></a>使用可视化呈现器

[`Button`](xref:Xamarin.Forms.Button)对象可以通过将呈现器类的属性设置为来选择使用呈现器类 [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) `Custom` ：

```xaml
<Button Visual="Custom"
        Text="CUSTOM BUTTON"
        BackgroundColor="{StaticResource PrimaryColor}"
        TextColor="{StaticResource SecondaryTextColor}"
        HorizontalOptions="FillAndExpand" />
```

> [!NOTE]
> 在 XAML 中，类型转换器无需在属性值中包含 "视觉对象后缀" [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) 。 但是，也可以指定完整的类型名称。

等效 C# 代码如下：

```csharp
Button button = new Button { Text = "CUSTOM BUTTON", ... };
button.Visual = new CustomVisual();
```

在呈现器选择时， [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) 的属性 [`Button`](xref:Xamarin.Forms.Button) 被检查并包含在呈现器选择过程中。 如果未找到呈现器，则 Xamarin.Forms 将使用默认呈现器。

下面的屏幕截图显示了呈现的 [`Button`](xref:Xamarin.Forms.Button) ，其中显示了具有阴影的文本：

[![在 iOS 和 Android 上带有阴影文本的自定义按钮的屏幕截图](material-visual-images/custom-button.png "带有阴影文本的按钮")](material-visual-images/custom-button-large.png#lightbox)

## <a name="register-a-name-for-the-ivisual-type"></a>注册 IVisual 类型的名称

[`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute)可用于为类型注册其他名称（可选） `IVisual` 。 此方法可用于解决不同可视化库之间的命名冲突，或者只需用不同于其类型名称的名称来引用视觉对象。

[`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute)应在跨平台库或平台项目中的程序集级别定义：

```csharp
[assembly: Visual("MyVisual", typeof(CustomVisual))]
```

`IVisual`然后，可以通过其注册的名称使用该类型：

```xaml
<Button Visual="MyVisual"
        ... />
```

> [!NOTE]
> 使用视觉对象的注册名称时，必须包含任何 "视觉" 后缀。

## <a name="related-links"></a>相关链接

- [材料视觉 (示例) ](/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [Xamarin.Forms 材料视觉对象](material-visual.md)
- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)